---
title: "Thiết lập GitLab CI/CD Pipeline"
date: "2024-01-01"
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Thiết lập GitLab CI/CD Pipeline

Trong phần này, bạn sẽ thiết lập GitLab CI/CD pipeline để tự động build, quét bảo mật, và deploy ứng dụng FastAPI.

![CI/CD Pipeline Architecture](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/cicd-architecture.png)

### Tổng quan Pipeline

GitLab CI/CD pipeline bao gồm 3 stages:

| Stage | Job | Công cụ | Mục đích |
| --- | --- | --- | --- |
| **lint** | `lint_and_scan` | Semgrep | Static Application Security Testing (SAST) |
| **build** | `build_and_push` | Docker + Trivy | Build container image và scan vulnerabilities |
| **deploy** | `terraform_deploy` | Terraform | Provision/update infrastructure |

### Nội dung

1. [Setup GitLab CI/CD Pipeline](5.3.1-create-gwe/) - Config AWS CLI, GitLab variables, và pipeline
2. [Cấu hình Security Scanning](5.3.2-test-gwe/) - Tích hợp Semgrep và Trivy

### Luồng Pipeline

![GitLab CI/CD Pipeline Flow](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/gitlab-pipeline.png)

Sau khi pipeline chạy thành công, bạn sẽ thấy tất cả các stages với dấu tích xanh:

![Pipeline Success](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/pipeline-success.png)

### Cấu hình GitLab CI/CD

Pipeline được định nghĩa trong `.gitlab-ci.yml`:

```yaml
stages:
  - lint
  - build
  - deploy

lint_and_scan:
  stage: lint
  script:
    - semgrep --config backend/semgrep.yml backend/

build_and_push:
  stage: build
  script:
    - docker build -t fastapi-lambda:$CI_COMMIT_SHORT_SHA backend
    - trivy fs --severity HIGH,CRITICAL .
    - trivy image --severity HIGH,CRITICAL fastapi-lambda:$CI_COMMIT_SHORT_SHA
    - docker push $ECR_URI:$CI_COMMIT_SHORT_SHA

terraform_deploy:
  stage: deploy
  script:
    - cd infra
    - terraform init
    - terraform apply -auto-approve
```

### Security Scanning

Pipeline bao gồm 2 loại security scanning:

1. **Semgrep (SAST)**: Static code analysis cho security vulnerabilities
2. **Trivy**: Container vulnerability scanning (filesystem + image)

### Tính năng chính

- ✅ **Tự động Trigger**: Chạy trên mỗi push vào `main` branch
- ✅ **Security First**: Fail build trên critical security findings
- ✅ **Container Image**: Build và scan Docker image
- ✅ **Infrastructure as Code**: Terraform tự động deploy infrastructure
- ✅ **GitLab Variables**: Tất cả secrets lưu trong GitLab CI/CD variables

### Quick Setup

1. **Config AWS CLI** (local)
2. **Tạo JWT Secret** (AWS Secrets Manager)
3. **Tạo ECR Repository** (AWS ECR)
4. **Config GitLab Variables** (7 variables)
5. **Push Code** → Pipeline tự động chạy

{{% notice tip %}}
Pipeline tự động xử lý mọi thứ: build, scan, và deploy. Bạn chỉ cần config AWS CLI và GitLab variables một lần.
{{% /notice %}}

