---
title: "Prerequisites"
date: "2024-01-01"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## Prerequisites

Before starting this workshop, ensure you have the following requirements ready.

### 1. AWS Account

You need an AWS account with appropriate permissions. If you don't have one:

1. Go to [AWS Console](https://aws.amazon.com/)
2. Click **Create an AWS Account**
3. Follow the registration process
4. Enable MFA for the root account (recommended)

![AWS Account Creation](/images/5-Workshop/5.2-Prerequisites/aws-account.png)

{{% notice warning %}}
This workshop will create resources that incur costs. Estimated cost is ~$5-10 if cleaned up within a few hours. Make sure to complete the cleanup section at the end.
{{% /notice %}}

### 2. IAM User with Required Permissions

Create an IAM user with programmatic access for GitLab CI/CD:

#### Create IAM User

```bash
# Create IAM user
aws iam create-user --user-name gitlab-ci-user

# Create access key
aws iam create-access-key --user-name gitlab-ci-user
```

**Save the Access Key ID and Secret Access Key** - you'll need them for GitLab CI/CD variables.

#### Attach Required Policies

The IAM user needs permissions for:
- Lambda, API Gateway, DynamoDB
- ECR (Elastic Container Registry)
- Secrets Manager
- CloudWatch, SNS
- IAM (for creating roles)
- S3 (for Terraform state, if using remote backend)

**Option 1: Administrator Access (for workshop)**

```bash
aws iam attach-user-policy \
    --user-name gitlab-ci-user \
    --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
```

**Option 2: Custom Policy (recommended for production)**

Create a custom policy with least privilege permissions for the services listed above.

![IAM Permissions](/images/5-Workshop/5.2-Prerequisites/iam-permissions.png)

### 3. GitLab Account

1. Sign up at [GitLab](https://gitlab.com/)
2. Create a new project/repository
3. Fork or clone the workshop repository

![GitLab Setup](/images/5-Workshop/5.2-Prerequisites/gitlab-setup.png)

### 4. AWS CLI Configuration

Install and configure AWS CLI on your local machine:

#### Install AWS CLI

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

#### Configure AWS CLI

```bash
aws configure
```

Enter the following when prompted:
- **AWS Access Key ID**: Your IAM user access key
- **AWS Secret Access Key**: Your IAM user secret key
- **Default region name**: `ap-southeast-1` (or your preferred region)
- **Default output format**: `json`

#### Verify AWS CLI Configuration

```bash
# Check AWS CLI version
aws --version

# Verify credentials
aws sts get-caller-identity
```

You should see your AWS account ID and user ARN.

### 5. Clone Workshop Repository

Clone the project repository:

```bash
git clone https://gitlab.com/m.quang/devsecops-aws-ver2.git
cd devsecops-aws-ver2/Backend-FastAPI-Docker_Build-Pipeline
```

**Repository:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

### 6. GitLab CI/CD Variables Setup

Before running the pipeline, you need to configure GitLab CI/CD variables. This will be covered in detail in the next section, but here's a quick overview:

**Required Variables:**
- `AWS_ACCESS_KEY_ID` - AWS access key
- `AWS_SECRET_ACCESS_KEY` - AWS secret key (masked)
- `AWS_DEFAULT_REGION` - AWS region
- `JWT_SECRET_ARN` - Secrets Manager ARN for JWT
- `ECR_URI` - ECR repository URI
- `PROJECT_NAME` - Project name
- `LAMBDA_FUNCTION_NAME` - Lambda function name

### 7. Verify Setup

Run the following commands to verify your setup:

```bash
# Check AWS CLI
aws --version
aws sts get-caller-identity

# Check Git
git --version

# Check Docker (optional, for local testing)
docker --version
```

### Workshop Resources

The repository contains all the code and configurations needed for this workshop:

{{% notice info %}}
The workshop repository contains:
- **Backend**: FastAPI application code with layered architecture
- **Infrastructure**: Terraform modules for all AWS resources (DynamoDB, Lambda, API Gateway, IAM, Observability)
- **Pipeline**: GitLab CI/CD configuration (`.gitlab-ci.yml`)
- **Security**: Semgrep and Trivy scanning configurations
- **Docker**: Dockerfile for Lambda container image
{{% /notice %}}

### Next Steps

After completing prerequisites:

1. ✅ AWS account created
2. ✅ IAM user with access keys
3. ✅ AWS CLI configured
4. ✅ Repository cloned
5. ⏭️ Configure GitLab CI/CD variables (next section)
6. ⏭️ Set up GitLab CI/CD pipeline (next section)
