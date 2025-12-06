---
title: "Quick Start: DevSecOps with FastAPI Backend"
date: "2024-01-01" 
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Quick Start: DevSecOps with FastAPI Backend

This workshop guides you through **quickly setting up a DevSecOps pipeline** for a FastAPI Backend on AWS Lambda. Just **configure a few basic things** and the pipeline will automatically build, scan, and deploy.

**Repository:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

### Quick Start Checklist

Just follow these 5 steps:

1. **Configure AWS CLI** - `aws configure`
2. **Create JWT Secret** - `aws secretsmanager create-secret`
3. **Create ECR Repository** - `aws ecr create-repository`
4. **Configure GitLab Variables** - Add 7 variables in GitLab
5. **Push Code** - `git push` and the pipeline will automatically run

**Time:** ~15-20 minutes

### What You'll Get?

After completion, you'll have:

- **FastAPI Backend** running on AWS Lambda (container)
- **GitLab CI/CD Pipeline** automatically:
  - **Semgrep** - Security scan (SAST)
  - **Docker Build** - Build container image
  - **Trivy** - Vulnerability scan
  - **Terraform Deploy** - Automatically deploy infrastructure
- **API Gateway** - REST API endpoint
- **DynamoDB** - 3 tables (products, orders, users)
- **CloudWatch** - Monitoring & alerting

### Detailed Steps

#### Step 1: Clone & Configure AWS CLI

```bash
# Clone repository
git clone https://gitlab.com/m.quang/devsecops-aws-ver2.git
cd devsecops-aws-ver2/Backend-FastAPI-Docker_Build-Pipeline

# Configure AWS CLI
aws configure
# Enter: Access Key, Secret Key, Region (ap-southeast-1), Format (json)
```

#### Step 2: Create JWT Secret

```bash
JWT_SECRET=$(openssl rand -hex 32)
aws secretsmanager create-secret \
    --name fastapi-jwt-secret \
    --secret-string "$JWT_SECRET" \
    --region ap-southeast-1

# Get ARN (save it)
JWT_SECRET_ARN=$(aws secretsmanager describe-secret \
    --secret-id fastapi-jwt-secret \
    --region ap-southeast-1 \
    --query 'ARN' \
    --output text)
echo "JWT_SECRET_ARN: $JWT_SECRET_ARN"
```

#### Step 3: Create ECR Repository

```bash
aws ecr create-repository \
    --repository-name fastapi-lambda \
    --image-scanning-configuration scanOnPush=true \
    --region ap-southeast-1

# Get ECR URI (save it)
ECR_URI=$(aws ecr describe-repositories \
    --repository-names fastapi-lambda \
    --region ap-southeast-1 \
    --query 'repositories[0].repositoryUri' \
    --output text)
echo "ECR_URI: $ECR_URI"
```

#### Step 4: Configure GitLab CI/CD Variables

Go to GitLab project → **Settings** → **CI/CD** → **Variables** → **Expand**

Add 7 variables:

| Variable | Value | Protected | Masked |
|----------|-------|-----------|--------|
| `AWS_ACCESS_KEY_ID` | Your AWS Access Key | ✅ | ❌ |
| `AWS_SECRET_ACCESS_KEY` | Your AWS Secret Key | ✅ | ✅ |
| `AWS_DEFAULT_REGION` | `ap-southeast-1` | ❌ | ❌ |
| `JWT_SECRET_ARN` | `arn:aws:secretsmanager:...` | ❌ | ❌ |
| `ECR_URI` | `123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fastapi-lambda` | ❌ | ❌ |
| `PROJECT_NAME` | `fastapi-lambda` | ❌ | ❌ |
| `LAMBDA_FUNCTION_NAME` | `fastapi-lambda-fn` | ❌ | ❌ |

#### Step 5: Setup GitLab CI/CD & Push

```bash
# Copy GitLab CI file (from repository root)
cd ..
cp Backend-FastAPI-Docker_Build-Pipeline/.gitlab-ci.yml.example .gitlab-ci.yml

# Commit and push
git add .gitlab-ci.yml
git commit -m "Add GitLab CI/CD pipeline"
git push origin main
```

**Pipeline automatically runs:**
1. **lint_and_scan** - Semgrep security scan
2. **build_and_push** - Docker build + Trivy scan + ECR push
3. **terraform_deploy** - Deploy infrastructure

### Architecture Overview

![Platform Architecture](/images/5-Workshop/5.1-Workshop-overview/platform_architecture.png)


### Detailed Sections

The workshop is divided into sections:

1. **[Prerequisites](5.2-Prerequiste/)** - Setup AWS CLI, GitLab account
2. **[Setup GitLab CI/CD](5.3-S3-vpc/5.3.1-create-gwe/)** - Configure pipeline and variables
3. **[Security Scanning](5.3-S3-vpc/5.3.2-test-gwe/)** - Semgrep & Trivy
4. **[Deploy Backend](5.4-S3-onprem/)** - Infrastructure automatically deployed

### Tech Stack

| Component | Technology |
|-----------|-----------|
| **Backend** | FastAPI (Python) |
| **Runtime** | AWS Lambda (Container) |
| **Database** | DynamoDB |
| **CI/CD** | GitLab CI/CD |
| **Security** | Semgrep (SAST), Trivy (Container Scan) |
| **Infrastructure** | Terraform |
| **Monitoring** | CloudWatch, SNS |

### Verification

After the pipeline finishes running:

```bash
# Get API URL from GitLab job logs or:
cd Backend-FastAPI-Docker_Build-Pipeline/infra
terraform output api_url

# Test API
curl https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com/health
# Expected: {"status": "ok"}
```

### Cost Estimate

- **Lambda**: ~$1-3/month (250K requests)
- **DynamoDB**: ~$5-10/month (on-demand)
- **API Gateway**: ~$1-3/month
- **ECR**: ~$0.50-1.50/month
- **Total**: ~$10-20/month (low traffic)

### Next Steps

1. Read [Prerequisites](5.2-Prerequiste/) to setup the environment
2. Follow [Setup GitLab CI/CD](5.3-S3-vpc/5.3.1-create-gwe/) to configure the pipeline
3. View [Security Scanning](5.3-S3-vpc/5.3.2-test-gwe/) to understand how scanning works
4. Check [Deploy Backend](5.4-S3-onprem/) to see the infrastructure being created

{{% notice tip %}}
This workshop is based on a **real project folder** `Backend-FastAPI-Docker_Build-Pipeline`. All code and configuration are ready, you just need to configure and run!
{{% /notice %}}

