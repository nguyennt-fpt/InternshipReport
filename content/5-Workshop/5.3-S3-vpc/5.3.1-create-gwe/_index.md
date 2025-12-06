---
title: "Setup GitLab CI/CD Pipeline"
date: "2024-01-01"
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

## Setup GitLab CI/CD Pipeline

This section guides you through setting up the GitLab CI/CD pipeline. The pipeline automatically builds, scans, and deploys your FastAPI application using Terraform.

### Step 1: Clone the Repository

Clone the project repository:

```bash
git clone https://gitlab.com/m.quang/devsecops-aws-ver2.git
cd devsecops-aws-ver2/Backend-FastAPI-Docker_Build-Pipeline
```

**Repository:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

### Step 2: Configure AWS CLI

Configure AWS CLI with your credentials:

```bash
aws configure
```

Enter the following when prompted:
- **AWS Access Key ID**: Your IAM user access key
- **AWS Secret Access Key**: Your IAM user secret key
- **Default region name**: `ap-southeast-1` (or your preferred region)
- **Default output format**: `json`

Verify the configuration:

```bash
aws sts get-caller-identity
```

You should see your AWS account ID and user ARN.

### Step 3: Create JWT Secret in AWS Secrets Manager

Create a JWT secret for authentication:

```bash
# Generate a random secret (or use your own)
JWT_SECRET=$(openssl rand -hex 32)

# Create secret in Secrets Manager
aws secretsmanager create-secret \
    --name fastapi-jwt-secret \
    --secret-string "$JWT_SECRET" \
    --region ap-southeast-1

# Get the secret ARN (save this for GitLab variables)
aws secretsmanager describe-secret \
    --secret-id fastapi-jwt-secret \
    --region ap-southeast-1 \
    --query 'ARN' \
    --output text
```

**Note the Secret ARN** - you'll need it for GitLab variables (e.g., `arn:aws:secretsmanager:ap-southeast-1:123456789012:secret:fastapi-jwt-secret-xxxxx`)

### Step 4: Configure GitLab CI/CD Variables

Navigate to your GitLab project and configure CI/CD variables:

1. Go to your GitLab project: `https://gitlab.com/YOUR_USERNAME/devsecops-aws-ver2`
2. Navigate to **Settings** → **CI/CD** → **Variables**
3. Click **Expand** on "Variables" section
4. Add the following variables:

| Variable Key | Value | Protected | Masked | Description |
|-------------|-------|-----------|--------|-------------|
| `AWS_ACCESS_KEY_ID` | Your AWS Access Key | ✅ | ❌ | AWS access key for CI/CD |
| `AWS_SECRET_ACCESS_KEY` | Your AWS Secret Key | ✅ | ✅ | AWS secret key (masked) |
| `AWS_DEFAULT_REGION` | `ap-southeast-1` | ❌ | ❌ | AWS region |
| `JWT_SECRET_ARN` | `arn:aws:secretsmanager:...` | ❌ | ❌ | Secrets Manager ARN for JWT |
| `ECR_REPO_NAME` | `fastapi-lambda` | ❌ | ❌ | ECR repository name |
| `LAMBDA_FUNCTION_NAME` | `fastapi-lambda-fn` | ❌ | ❌ | Lambda function name |
| `PROJECT_NAME` | `fastapi-lambda` | ❌ | ❌ | Project name for resources |

**Important:**
- Mark `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` as **Protected** (only available in protected branches)
- Mark `AWS_SECRET_ACCESS_KEY` as **Masked** (hidden in logs)
- Use **Expand** button to add each variable

![GitLab CI/CD Variables](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/gitlab-variables.png)

### Step 5: Create GitLab CI/CD Pipeline File

The project uses GitLab CI/CD with three stages. Create or verify `.gitlab-ci.yml` in the root of your repository:

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

**Note:** Replace `$ECR_URI` with your actual ECR repository URI. You'll get this after creating the ECR repository.

### Step 6: Create ECR Repository

Create the ECR repository for storing Docker images:

```bash
# Create ECR repository
aws ecr create-repository \
    --repository-name fastapi-lambda \
    --image-scanning-configuration scanOnPush=true \
    --encryption-configuration encryptionType=AES256 \
    --region ap-southeast-1

# Get ECR repository URI
ECR_URI=$(aws ecr describe-repositories \
    --repository-names fastapi-lambda \
    --region ap-southeast-1 \
    --query 'repositories[0].repositoryUri' \
    --output text)

echo "ECR URI: $ECR_URI"
```

Add `ECR_URI` to GitLab CI/CD variables:
- **Variable Key**: `ECR_URI`
- **Value**: `123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fastapi-lambda` (your actual URI)

![ECR Repository](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/ecr-repository.png)

### Step 7: Push Code and Trigger Pipeline

Commit and push your code to trigger the pipeline:

```bash
# Add .gitlab-ci.yml if you created it
git add .gitlab-ci.yml
git commit -m "Add GitLab CI/CD pipeline"
git push origin main
```

The pipeline will automatically:
1. **Lint Stage**: Run Semgrep SAST scan
2. **Build Stage**: Build Docker image, scan with Trivy, push to ECR
3. **Deploy Stage**: Run Terraform to deploy infrastructure

![GitLab Pipeline](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/gitlab-pipeline.png)

### Step 8: Monitor Pipeline Execution

1. Go to your GitLab project
2. Navigate to **CI/CD** → **Pipelines**
3. Click on the running pipeline to view job logs
4. Monitor each stage:
   - `lint_and_scan`: Semgrep security scan
   - `build_and_push`: Docker build and Trivy scan
   - `terraform_deploy`: Infrastructure deployment

![Pipeline Execution](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.1-Setup-GitLab-CI-CD-Pipeline/pipeline-execution.png)

After the pipeline completes successfully, you should see all stages with green checkmarks:

![Pipeline Success](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/pipeline-success.png)

### Step 9: Get API Gateway URL

After successful deployment, get the API Gateway URL:

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/infra
terraform output api_url
```

Or from GitLab CI/CD job logs, look for Terraform output.

### Troubleshooting

#### Pipeline Fails at Lint Stage
- Check Semgrep findings in job logs
- Fix security issues in code
- Update `backend/semgrep.yml` if needed

#### Pipeline Fails at Build Stage
- Verify `ECR_URI` variable is correct
- Check AWS credentials have ECR permissions
- Verify Docker image builds locally: `docker build -t test backend`

#### Pipeline Fails at Deploy Stage
- Check Terraform logs in job output
- Verify all GitLab variables are set correctly
- Ensure IAM user has permissions for Lambda, API Gateway, DynamoDB

#### AWS Credentials Issues
- Verify `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` are correct
- Check IAM user has required permissions
- Test credentials: `aws sts get-caller-identity`

{{% notice tip %}}
The pipeline automatically triggers on every push to the main branch. You can also manually trigger it from GitLab CI/CD → Pipelines → Run Pipeline.
{{% /notice %}}
