---
title: "Thiết lập GitLab CI/CD Pipeline"
date: "2024-01-01"
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Thiết lập GitLab CI/CD Pipeline

Phần này hướng dẫn bạn thiết lập GitLab CI/CD pipeline. Pipeline sẽ tự động build, scan và deploy ứng dụng FastAPI của bạn sử dụng Terraform.

### Bước 1: Clone Repository

Clone repository dự án:

```bash
git clone https://gitlab.com/m.quang/devsecops-aws-ver2.git
cd devsecops-aws-ver2/Backend-FastAPI-Docker_Build-Pipeline
```

**Repository:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

### Bước 2: Cấu hình AWS CLI

Cấu hình AWS CLI với credentials của bạn:

```bash
aws configure
```

Nhập các thông tin sau khi được hỏi:
- **AWS Access Key ID**: Access key của IAM user
- **AWS Secret Access Key**: Secret key của IAM user
- **Default region name**: `ap-southeast-1` (hoặc region bạn muốn)
- **Default output format**: `json`

Xác minh cấu hình:

```bash
aws sts get-caller-identity
```

Bạn sẽ thấy AWS account ID và user ARN.

### Bước 3: Tạo JWT Secret trong AWS Secrets Manager

Tạo JWT secret cho authentication:

```bash
# Tạo secret ngẫu nhiên (hoặc dùng secret của bạn)
JWT_SECRET=$(openssl rand -hex 32)

# Tạo secret trong Secrets Manager
aws secretsmanager create-secret \
    --name fastapi-jwt-secret \
    --secret-string "$JWT_SECRET" \
    --region ap-southeast-1

# Lấy Secret ARN (lưu lại để dùng cho GitLab variables)
aws secretsmanager describe-secret \
    --secret-id fastapi-jwt-secret \
    --region ap-southeast-1 \
    --query 'ARN' \
    --output text
```

**Lưu Secret ARN** - bạn sẽ cần nó cho GitLab variables (ví dụ: `arn:aws:secretsmanager:ap-southeast-1:123456789012:secret:fastapi-jwt-secret-xxxxx`)

### Bước 4: Cấu hình GitLab CI/CD Variables

Điều hướng đến GitLab project và cấu hình CI/CD variables:

1. Vào GitLab project: `https://gitlab.com/YOUR_USERNAME/devsecops-aws-ver2`
2. Điều hướng đến **Settings** → **CI/CD** → **Variables**
3. Click **Expand** trên section "Variables"
4. Thêm các variables sau:

| Variable Key | Value | Protected | Masked | Mô tả |
|-------------|-------|-----------|--------|-------|
| `AWS_ACCESS_KEY_ID` | AWS Access Key của bạn | ✅ | ❌ | AWS access key cho CI/CD |
| `AWS_SECRET_ACCESS_KEY` | AWS Secret Key của bạn | ✅ | ✅ | AWS secret key (masked) |
| `AWS_DEFAULT_REGION` | `ap-southeast-1` | ❌ | ❌ | AWS region |
| `JWT_SECRET_ARN` | `arn:aws:secretsmanager:...` | ❌ | ❌ | Secrets Manager ARN cho JWT |
| `ECR_REPO_NAME` | `fastapi-lambda` | ❌ | ❌ | Tên ECR repository |
| `LAMBDA_FUNCTION_NAME` | `fastapi-lambda-fn` | ❌ | ❌ | Tên Lambda function |
| `PROJECT_NAME` | `fastapi-lambda` | ❌ | ❌ | Tên project cho resources |

**Quan trọng:**
- Đánh dấu `AWS_ACCESS_KEY_ID` và `AWS_SECRET_ACCESS_KEY` là **Protected** (chỉ có trong protected branches)
- Đánh dấu `AWS_SECRET_ACCESS_KEY` là **Masked** (ẩn trong logs)
- Sử dụng nút **Expand** để thêm từng variable

![GitLab CI/CD Variables](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/gitlab-variables.png)

### Bước 5: Tạo GitLab CI/CD Pipeline File

Dự án sử dụng GitLab CI/CD với 3 stages. Tạo hoặc kiểm tra file `.gitlab-ci.yml` ở root của repository:

```yaml
stages:
  - lint
  - build
  - deploy

variables:
  IMAGE_NAME: "fastapi-lambda"
  IMAGE_TAG: "${CI_COMMIT_SHORT_SHA}"

lint_and_scan:
  stage: lint
  image: docker:24.0.7
  services:
    - docker:24.0.7-dind
  before_script:
    - apk add --no-cache python3 py3-pip bash curl
    - pip3 install semgrep
  script:
    - echo "Running Semgrep SAST scan..."
    - semgrep --config Backend-FastAPI-Docker_Build-Pipeline/backend/semgrep.yml Backend-FastAPI-Docker_Build-Pipeline/backend/
  artifacts:
    reports:
      codequality: semgrep-report.json
    paths:
      - semgrep-report.json
    expire_in: 1 week
  only:
    - main
    - merge_requests

build_and_push:
  stage: build
  image: docker:24.0.7
  services:
    - docker:24.0.7-dind
  before_script:
    - apk add --no-cache python3 py3-pip bash curl jq aws-cli
    - curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin
    - aws --version
    - aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_URI
  script:
    - echo "Building Docker image..."
    - cd Backend-FastAPI-Docker_Build-Pipeline
    - docker build -t $IMAGE_NAME:$IMAGE_TAG backend
    - echo "Scanning filesystem with Trivy..."
    - trivy fs --exit-code 1 --severity HIGH,CRITICAL --ignorefile backend/trivyignore.txt .
    - echo "Scanning image with Trivy..."
    - trivy image --exit-code 1 --severity HIGH,CRITICAL $IMAGE_NAME:$IMAGE_TAG
    - echo "Tagging and pushing to ECR..."
    - docker tag $IMAGE_NAME:$IMAGE_TAG $ECR_URI:$IMAGE_TAG
    - docker push $ECR_URI:$IMAGE_TAG
    - echo "$ECR_URI:$IMAGE_TAG" > image-uri.txt
  artifacts:
    paths:
      - Backend-FastAPI-Docker_Build-Pipeline/image-uri.txt
    expire_in: 1 week
  only:
    - main

terraform_deploy:
  stage: deploy
  image: hashicorp/terraform:1.9.5
  before_script:
    - apk add --no-cache aws-cli
    - aws --version
    - cd Backend-FastAPI-Docker_Build-Pipeline
  script:
    - echo "Initializing Terraform..."
    - cd infra
    - terraform init -upgrade
    - echo "Planning Terraform changes..."
    - terraform plan -input=false -out=tfplan \
        -var "project_name=$PROJECT_NAME" \
        -var "region=$AWS_DEFAULT_REGION" \
        -var "jwt_secret_arn=$JWT_SECRET_ARN" \
        -var "image_uri=$ECR_URI:$IMAGE_TAG"
    - echo "Applying Terraform changes..."
    - terraform apply -auto-approve tfplan
    - echo "Deployment completed successfully!"
  dependencies:
    - build_and_push
  only:
    - main
```

**Lưu ý:** Thay `$ECR_URI` bằng ECR repository URI thực tế của bạn. Bạn sẽ có URI này sau khi tạo ECR repository.

### Bước 6: Tạo ECR Repository

Tạo ECR repository để lưu trữ Docker images:

```bash
# Tạo ECR repository
aws ecr create-repository \
    --repository-name fastapi-lambda \
    --image-scanning-configuration scanOnPush=true \
    --encryption-configuration encryptionType=AES256 \
    --region ap-southeast-1

# Lấy ECR repository URI
ECR_URI=$(aws ecr describe-repositories \
    --repository-names fastapi-lambda \
    --region ap-southeast-1 \
    --query 'repositories[0].repositoryUri' \
    --output text)

echo "ECR URI: $ECR_URI"
```

Thêm `ECR_URI` vào GitLab CI/CD variables:
- **Variable Key**: `ECR_URI`
- **Value**: `123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fastapi-lambda` (URI thực tế của bạn)

![ECR Repository](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/ecr-repository.png)

### Bước 7: Push Code và Trigger Pipeline

Commit và push code để trigger pipeline:

```bash
# Thêm .gitlab-ci.yml nếu bạn vừa tạo
git add .gitlab-ci.yml
git commit -m "Add GitLab CI/CD pipeline"
git push origin main
```

Pipeline sẽ tự động:
1. **Lint Stage**: Chạy Semgrep SAST scan
2. **Build Stage**: Build Docker image, scan với Trivy, push lên ECR
3. **Deploy Stage**: Chạy Terraform để deploy infrastructure

![GitLab Pipeline](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/gitlab-pipeline.png)

### Bước 8: Giám sát Pipeline Execution

1. Vào GitLab project của bạn
2. Điều hướng đến **CI/CD** → **Pipelines**
3. Click vào pipeline đang chạy để xem job logs
4. Giám sát từng stage:
   - `lint_and_scan`: Semgrep security scan
   - `build_and_push`: Docker build và Trivy scan
   - `terraform_deploy`: Infrastructure deployment

![Pipeline Execution](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/pipeline-execution.png)

Sau khi pipeline chạy thành công, bạn sẽ thấy tất cả các stages với dấu tích xanh:

![Pipeline Success](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/pipeline-success.png)

### Bước 9: Lấy API Gateway URL

Sau khi deploy thành công, lấy API Gateway URL:

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/infra
terraform output api_url
```

Hoặc từ GitLab CI/CD job logs, tìm Terraform output.

### Xử lý sự cố

#### Pipeline Fail ở Lint Stage
- Kiểm tra Semgrep findings trong job logs
- Sửa security issues trong code
- Cập nhật `backend/semgrep.yml` nếu cần

#### Pipeline Fail ở Build Stage
- Xác minh variable `ECR_URI` đúng
- Kiểm tra AWS credentials có quyền ECR
- Xác minh Docker image build được local: `docker build -t test backend`

#### Pipeline Fail ở Deploy Stage
- Kiểm tra Terraform logs trong job output
- Xác minh tất cả GitLab variables đã được set đúng
- Đảm bảo IAM user có quyền cho Lambda, API Gateway, DynamoDB

#### Vấn đề AWS Credentials
- Xác minh `AWS_ACCESS_KEY_ID` và `AWS_SECRET_ACCESS_KEY` đúng
- Kiểm tra IAM user có đủ permissions
- Test credentials: `aws sts get-caller-identity`

{{% notice tip %}}
Pipeline tự động trigger trên mỗi push vào main branch. Bạn cũng có thể trigger thủ công từ GitLab CI/CD → Pipelines → Run Pipeline.
{{% /notice %}}
