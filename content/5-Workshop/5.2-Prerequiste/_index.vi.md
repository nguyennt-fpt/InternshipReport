---
title: "Điều kiện tiên quyết"
date: "2024-01-01"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Điều kiện tiên quyết

Trước khi bắt đầu workshop này, đảm bảo bạn đã có các yêu cầu sau.

### 1. AWS Account

Bạn cần một AWS account với quyền phù hợp. Nếu chưa có:

1. Vào [AWS Console](https://aws.amazon.com/)
2. Click **Create an AWS Account**
3. Làm theo quy trình đăng ký
4. Bật MFA cho root account (khuyến nghị)

![AWS Account Creation](/images/5-Workshop/5.2-Prerequisites/aws-account.png)

{{% notice warning %}}
Workshop này sẽ tạo các tài nguyên có phát sinh chi phí. Chi phí ước tính ~$5-10 nếu dọn dẹp trong vài giờ. Đảm bảo hoàn thành phần cleanup ở cuối.
{{% /notice %}}

### 2. IAM User với Quyền Cần Thiết

Tạo IAM user với programmatic access cho GitLab CI/CD:

#### Tạo IAM User

```bash
# Tạo IAM user
aws iam create-user --user-name gitlab-ci-user

# Tạo access key
aws iam create-access-key --user-name gitlab-ci-user
```

**Lưu Access Key ID và Secret Access Key** - bạn sẽ cần chúng cho GitLab CI/CD variables.

#### Gắn Policies Cần Thiết

IAM user cần quyền cho:
- Lambda, API Gateway, DynamoDB
- ECR (Elastic Container Registry)
- Secrets Manager
- CloudWatch, SNS
- IAM (để tạo roles)
- S3 (cho Terraform state, nếu dùng remote backend)

**Option 1: Administrator Access (cho workshop)**

```bash
aws iam attach-user-policy \
    --user-name gitlab-ci-user \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
```

**Option 2: Custom Policy (khuyến nghị cho production)**

Tạo custom policy với least privilege permissions cho các services trên.

![IAM Permissions](/images/5-Workshop/5.2-Prerequisites/iam-permissions.png)

### 3. GitLab Account

1. Đăng ký tại [GitLab](https://gitlab.com/)
2. Tạo project/repository mới
3. Fork hoặc clone workshop repository

![GitLab Setup](/images/5-Workshop/5.2-Prerequisites/gitlab-setup.png)

### 4. Cấu hình AWS CLI

Cài đặt và cấu hình AWS CLI trên máy local:

#### Cài đặt AWS CLI

```bash
# Windows (PowerShell)
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi

# macOS
brew install awscli

# Linux
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

#### Cấu hình AWS CLI

```bash
aws configure
```

Nhập các thông tin sau khi được hỏi:
- **AWS Access Key ID**: Access key của IAM user
- **AWS Secret Access Key**: Secret key của IAM user
- **Default region name**: `ap-southeast-1` (hoặc region bạn muốn)
- **Default output format**: `json`

#### Xác minh Cấu hình AWS CLI

```bash
# Kiểm tra AWS CLI version
aws --version

# Xác minh credentials
aws sts get-caller-identity
```

Bạn sẽ thấy AWS account ID và user ARN.

### 5. Clone Workshop Repository

Clone repository dự án:

```bash
git clone https://gitlab.com/m.quang/devsecops-aws-ver2.git
cd devsecops-aws-ver2/Backend-FastAPI-Docker_Build-Pipeline
```

**Repository:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

### 6. Thiết lập GitLab CI/CD Variables

Trước khi chạy pipeline, bạn cần cấu hình GitLab CI/CD variables. Phần này sẽ được hướng dẫn chi tiết ở section tiếp theo, nhưng đây là tổng quan:

**Variables Cần Thiết:**
- `AWS_ACCESS_KEY_ID` - AWS access key
- `AWS_SECRET_ACCESS_KEY` - AWS secret key (masked)
- `AWS_DEFAULT_REGION` - AWS region
- `JWT_SECRET_ARN` - Secrets Manager ARN cho JWT
- `ECR_URI` - ECR repository URI
- `PROJECT_NAME` - Tên project
- `LAMBDA_FUNCTION_NAME` - Tên Lambda function

### 7. Xác minh Setup

Chạy các lệnh sau để xác minh setup:

```bash
# Kiểm tra AWS CLI
aws --version
aws sts get-caller-identity

# Kiểm tra Git
git --version

# Kiểm tra Docker (tùy chọn, cho local testing)
docker --version
```

### Tài nguyên Workshop

Repository chứa tất cả code và cấu hình cần thiết cho workshop này:

{{% notice info %}}
Workshop repository chứa:
- **Backend**: FastAPI application code với kiến trúc phân lớp
- **Infrastructure**: Terraform modules cho tất cả AWS resources (DynamoDB, Lambda, API Gateway, IAM, Observability)
- **Pipeline**: GitLab CI/CD configuration (`.gitlab-ci.yml`)
- **Security**: Semgrep và Trivy scanning configurations
- **Docker**: Dockerfile cho Lambda container image
{{% /notice %}}

### Các bước tiếp theo

Sau khi hoàn thành prerequisites:

1. ✅ AWS account đã tạo
2. ✅ IAM user với access keys
3. ✅ AWS CLI đã cấu hình
4. ✅ Repository đã clone
5. ⏭️ Cấu hình GitLab CI/CD variables (section tiếp theo)
6. ⏭️ Thiết lập GitLab CI/CD pipeline (section tiếp theo)
