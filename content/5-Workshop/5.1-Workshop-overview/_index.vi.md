---
title: "Quick Start: DevSecOps với FastAPI Backend"
date: "2024-01-01" 
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Quick Start: DevSecOps với FastAPI Backend

Workshop này hướng dẫn bạn **thiết lập nhanh DevSecOps pipeline** cho FastAPI Backend trên AWS Lambda. Chỉ cần **config một số thứ cơ bản** và pipeline sẽ tự động build, scan, và deploy.

**Repository:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

### Quick Start Checklist

Chỉ cần làm 5 bước này:

1. **Config AWS CLI** - `aws configure`
2. **Tạo JWT Secret** - `aws secretsmanager create-secret`
3. **Tạo ECR Repository** - `aws ecr create-repository`
4. **Config GitLab Variables** - Thêm 7 variables trong GitLab
5. **Push Code** - `git push` và pipeline tự động chạy

**Thời gian:** ~15-20 phút

### Bạn sẽ có gì?

Sau khi hoàn thành, bạn sẽ có:

- **FastAPI Backend** chạy trên AWS Lambda (container)
- **GitLab CI/CD Pipeline** tự động:
  - **Semgrep** - Security scan (SAST)
  - **Docker Build** - Build container image
  - **Trivy** - Vulnerability scan
  - **Terraform Deploy** - Tự động deploy infrastructure
- **API Gateway** - REST API endpoint
- **DynamoDB** - 3 tables (products, orders, users)
- **CloudWatch** - Monitoring & alerting

### Các bước chi tiết

#### Bước 1: Clone & Config AWS CLI

```bash
# Clone repository
git clone https://gitlab.com/m.quang/devsecops-aws-ver2.git
cd devsecops-aws-ver2/Backend-FastAPI-Docker_Build-Pipeline

# Config AWS CLI
aws configure
# Nhập: Access Key, Secret Key, Region (ap-southeast-1), Format (json)
```

#### Bước 2: Tạo JWT Secret

```bash
JWT_SECRET=$(openssl rand -hex 32)
aws secretsmanager create-secret \
    --name fastapi-jwt-secret \
    --secret-string "$JWT_SECRET" \
    --region ap-southeast-1

# Lấy ARN (lưu lại)
JWT_SECRET_ARN=$(aws secretsmanager describe-secret \
    --secret-id fastapi-jwt-secret \
    --region ap-southeast-1 \
    --query 'ARN' \
    --output text)
echo "JWT_SECRET_ARN: $JWT_SECRET_ARN"
```

#### Bước 3: Tạo ECR Repository

```bash
aws ecr create-repository \
    --repository-name fastapi-lambda \
    --image-scanning-configuration scanOnPush=true \
    --region ap-southeast-1

# Lấy ECR URI (lưu lại)
ECR_URI=$(aws ecr describe-repositories \
    --repository-names fastapi-lambda \
    --region ap-southeast-1 \
    --query 'repositories[0].repositoryUri' \
    --output text)
echo "ECR_URI: $ECR_URI"
```

#### Bước 4: Config GitLab CI/CD Variables

Vào GitLab project → **Settings** → **CI/CD** → **Variables** → **Expand**

Thêm 7 variables:

| Variable | Value | Protected | Masked |
|----------|-------|-----------|--------|
| `AWS_ACCESS_KEY_ID` | Your AWS Access Key | ✅ | ❌ |
| `AWS_SECRET_ACCESS_KEY` | Your AWS Secret Key | ✅ | ✅ |
| `AWS_DEFAULT_REGION` | `ap-southeast-1` | ❌ | ❌ |
| `JWT_SECRET_ARN` | `arn:aws:secretsmanager:...` | ❌ | ❌ |
| `ECR_URI` | `123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fastapi-lambda` | ❌ | ❌ |
| `PROJECT_NAME` | `fastapi-lambda` | ❌ | ❌ |
| `LAMBDA_FUNCTION_NAME` | `fastapi-lambda-fn` | ❌ | ❌ |

#### Bước 5: Setup GitLab CI/CD & Push

```bash
# Copy GitLab CI file (từ repository root)
cd ..
cp Backend-FastAPI-Docker_Build-Pipeline/.gitlab-ci.yml.example .gitlab-ci.yml

# Commit và push
git add .gitlab-ci.yml
git commit -m "Add GitLab CI/CD pipeline"
git push origin main
```

**Pipeline tự động chạy:**
1. **lint_and_scan** - Semgrep security scan
2. **build_and_push** - Docker build + Trivy scan + ECR push
3. **terraform_deploy** - Deploy infrastructure

### Architecture Overview

![Platform Architecture](/images/5-Workshop/5.1-Workshop-overview/platform_architecture.png)

### Chi tiết từng phần

Workshop được chia thành các phần:

1. **[Prerequisites](5.2-Prerequiste/)** - Setup AWS CLI, GitLab account
2. **[Setup GitLab CI/CD](5.3-S3-vpc/5.3.1-create-gwe/)** - Config pipeline và variables
3. **[Security Scanning](5.3-S3-vpc/5.3.2-test-gwe/)** - Semgrep & Trivy
4. **[Deploy Backend](5.4-S3-onprem/)** - Infrastructure tự động deploy

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

Sau khi pipeline chạy xong:

```bash
# Lấy API URL từ GitLab job logs hoặc:
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

1. Đọc [Prerequisites](5.2-Prerequiste/) để setup môi trường
2. Làm theo [Setup GitLab CI/CD](5.3-S3-vpc/5.3.1-create-gwe/) để config pipeline
3. Xem [Security Scanning](5.3-S3-vpc/5.3.2-test-gwe/) để hiểu cách scan hoạt động
4. Kiểm tra [Deploy Backend](5.4-S3-onprem/) để xem infrastructure được tạo

{{% notice tip %}}
Workshop này dựa trên **folder dự án thực tế** `Backend-FastAPI-Docker_Build-Pipeline`. Tất cả code và config đã sẵn sàng, bạn chỉ cần config và chạy!
{{% /notice %}}

