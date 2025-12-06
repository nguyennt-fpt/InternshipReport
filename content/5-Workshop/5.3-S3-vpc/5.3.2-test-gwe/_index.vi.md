---
title: "DevSecOps Security Testing & Configuration"
date: "2024-01-01" 
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## DevSecOps Security Testing & Configuration

Phần này tập trung vào **security testing** và **cấu hình tay** các công cụ security scanning cho FastAPI Backend. Bạn sẽ học cách cấu hình và test Semgrep, Trivy, và ECR scanning thủ công.

### Tổng quan DevSecOps Testing

FastAPI Backend triển khai **defense-in-depth** security qua nhiều lớp testing:

| Layer | Tool | Type | Mục đích |
|-------|------|------|----------|
| **Code** | Semgrep | SAST | Static code analysis |
| **Dependencies** | Trivy FS | Dependency Scan | Scan filesystem cho vulnerabilities |
| **Container** | Trivy Image | Container Scan | Scan Docker image |
| **Registry** | ECR Scan | Image Scan | Automatic scan khi push |

### Bước 1: Cấu hình & Test Semgrep Thủ công

#### Cài đặt Semgrep

```bash
# Cài đặt Semgrep
pip install semgrep

# Hoặc dùng Docker
docker pull returntocorp/semgrep
```

#### Cấu hình Custom Rules

Chỉnh sửa `Backend-FastAPI-Docker_Build-Pipeline/backend/semgrep.yml`:

```yaml
rules:
  - id: jwt-hardcoded-secret
    message: Avoid hardcoding JWT secrets; use Secrets Manager/env.
    severity: ERROR
    languages: [python]
    pattern: |
      jwt.encode($P, $S, ...)
    metavariable-pattern:
      metavariable: $S
      pattern: "'$SECRET'"
      
  - id: fastapi-debug
    message: Do not run uvicorn with reload or debug in production.
    severity: WARNING
    languages: [python]
    pattern-either:
      - pattern: uvicorn.run(..., reload=True, ...)
      - pattern: uvicorn.run(..., debug=True, ...)
```

#### Chạy Semgrep Scan Thủ công

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/backend

# Basic scan
semgrep --config semgrep.yml .

# Scan với JSON output
semgrep --config semgrep.yml . --json -o semgrep-results.json

# Scan với HTML report
semgrep --config semgrep.yml . --html -o semgrep-report.html
```

![Semgrep Results](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.2-DevSecOps-Security-Testing&Configuration/semgrep-results.png)

### Bước 2: Cấu hình & Test Trivy Thủ công

#### Cài đặt Trivy

```bash
# Cài đặt Trivy
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

# Verify
trivy --version
```

#### Chạy Filesystem Scan

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/backend

# Basic filesystem scan
trivy fs --severity HIGH,CRITICAL .

# Scan với JSON output
trivy fs --severity HIGH,CRITICAL . --format json -o trivy-fs-results.json
```

#### Build và Scan Container Image

```bash
# Build Docker image
cd Backend-FastAPI-Docker_Build-Pipeline
docker build -t fastapi-lambda:test backend/

# Scan container image
trivy image --severity HIGH,CRITICAL fastapi-lambda:test

# Scan với exit code (fails on findings)
trivy image --exit-code 1 --severity HIGH,CRITICAL fastapi-lambda:test
```

### Bước 3: Cấu hình ECR Image Scanning Thủ công

#### Enable ECR Image Scanning

```bash
# Enable scanning trên existing repository
aws ecr put-image-scanning-configuration \
    --repository-name fastapi-lambda \
    --image-scanning-configuration scanOnPush=true \
    --region ap-southeast-1
```

#### View ECR Scan Results

```bash
# Get scan findings
aws ecr describe-image-scan-findings \
    --repository-name fastapi-lambda \
    --image-id imageTag=test \
    --region ap-southeast-1
```

![ECR Scan Results](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.2-DevSecOps-Security-Testing&Configuration/ecr-scan-results.png)

### Security Testing Workflow

Tạo script `security-test.sh` để chạy tất cả security tests:

```bash
#!/bin/bash
set -e

echo "Starting DevSecOps Security Testing..."

# 1. Semgrep SAST Scan
echo "Running Semgrep SAST scan..."
cd Backend-FastAPI-Docker_Build-Pipeline/backend
semgrep --config semgrep.yml . --json -o ../semgrep-results.json

# 2. Trivy Filesystem Scan
echo "Running Trivy filesystem scan..."
trivy fs --severity HIGH,CRITICAL . --format json -o ../trivy-fs-results.json

# 3. Build and Scan Container
echo "Building Docker image..."
docker build -t fastapi-lambda:security-test backend/

echo "Running Trivy container scan..."
trivy image --severity HIGH,CRITICAL --format json -o ../trivy-image-results.json fastapi-lambda:security-test

echo "Security testing complete!"
```

### Xem Kết quả trong GitLab CI/CD

Sau khi push code, xem kết quả security scan:

1. Điều hướng đến **CI/CD** → **Pipelines**
2. Click vào pipeline run
3. Xem `lint_and_scan` job logs cho Semgrep results
4. Xem `build_and_push` job logs cho Trivy results

![GitLab Pipeline Logs](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.2-DevSecOps-Security-Testing&Configuration/semgrep-results-gitlab.png)

![GitLab Pipeline Logs](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.2-DevSecOps-Security-Testing&Configuration/codebuild-logs.png)

### Best Practices

1. **Run Before Push**: Test local trước khi push
2. **Fail Fast**: Dùng `--exit-code 1` để fail builds trên critical findings
3. **Severity Threshold**: Chỉ block trên HIGH và CRITICAL findings
4. **Regular Updates**: Cập nhật Semgrep rules và Trivy database thường xuyên

