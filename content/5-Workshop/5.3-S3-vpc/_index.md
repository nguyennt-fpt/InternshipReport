---
title: "Setting up GitLab CI/CD Pipeline"
date: "2024-01-01"
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## Setting up GitLab CI/CD Pipeline

In this section, you will set up a GitLab CI/CD pipeline to automate building, security scanning, and deploying your FastAPI application.

![CI/CD Pipeline Architecture](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/cicd-architecture.png)

### Pipeline Overview

The GitLab CI/CD pipeline consists of three stages:

| Stage | Job | Tool | Purpose |
| --- | --- | --- | --- |
| **lint** | `lint_and_scan` | Semgrep | Static Application Security Testing (SAST) |
| **build** | `build_and_push` | Docker + Trivy | Build container image and scan for vulnerabilities |
| **deploy** | `terraform_deploy` | Terraform | Provision/update infrastructure |

### Content

1. [Setup GitLab CI/CD Pipeline](5.3.1-create-gwe/) - Config AWS CLI, GitLab variables, and pipeline
2. [Security Scanning Configuration](5.3.2-test-gwe/) - Semgrep and Trivy integration

### Pipeline Flow

![GitLab CI/CD Pipeline Flow](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/gitlab-pipeline.png)

After the pipeline completes successfully, you should see all stages with green checkmarks:

![Pipeline Success](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/pipeline-success.png)

### GitLab CI/CD Configuration

The pipeline is defined in `.gitlab-ci.yml`:

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

The pipeline includes two types of security scanning:

1. **Semgrep (SAST)**: Static code analysis for security vulnerabilities
2. **Trivy**: Container vulnerability scanning (filesystem + image)

### Key Features

- ✅ **Automatic Trigger**: Runs on every push to `main` branch
- ✅ **Security First**: Fails build on critical security findings
- ✅ **Container Image**: Builds and scans Docker image
- ✅ **Infrastructure as Code**: Terraform automatically deploys infrastructure
- ✅ **GitLab Variables**: All secrets stored in GitLab CI/CD variables

### Quick Setup

1. **Config AWS CLI** (local)
2. **Create JWT Secret** (AWS Secrets Manager)
3. **Create ECR Repository** (AWS ECR)
4. **Config GitLab Variables** (7 variables)
5. **Push Code** → Pipeline runs automatically

{{% notice tip %}}
The pipeline automatically handles everything: build, scan, and deploy. You only need to configure AWS CLI and GitLab variables once.
{{% /notice %}}

