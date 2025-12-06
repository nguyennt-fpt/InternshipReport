---
title: "DevSecOps Security Testing & Configuration"
date: "2024-01-01" 
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

## DevSecOps Security Testing & Configuration

This section focuses on **security testing** and **manual configuration** of security scanning tools for the FastAPI Backend. You will learn how to configure and test Semgrep, Trivy, and ECR scanning manually.

### DevSecOps Testing Overview

The FastAPI Backend implements **defense-in-depth** security through multiple testing layers:

| Layer | Tool | Type | Purpose |
|-------|------|------|---------|
| **Code** | Semgrep | SAST | Static code analysis |
| **Dependencies** | Trivy FS | Dependency Scan | Scan filesystem for vulnerabilities |
| **Container** | Trivy Image | Container Scan | Scan Docker image |
| **Registry** | ECR Scan | Image Scan | Automatic scan on push |

### Step 1: Manual Semgrep Configuration & Testing

#### Install Semgrep

```bash
# Install Semgrep
pip install semgrep

# Or using Docker
docker pull returntocorp/semgrep
```

#### Configure Custom Rules

Edit `Backend-FastAPI-Docker_Build-Pipeline/backend/semgrep.yml`:

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

#### Run Semgrep Scan Manually

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/backend

# Basic scan
semgrep --config semgrep.yml .

# Scan with JSON output
semgrep --config semgrep.yml . --json -o semgrep-results.json

# Scan with HTML report
semgrep --config semgrep.yml . --html -o semgrep-report.html
```

![Semgrep Results](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.2-DevSecOps-Security-Testing&Configuration/semgrep-results.png)

### Step 2: Manual Trivy Configuration & Testing

#### Install Trivy

```bash
# Install Trivy
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sh -s -- -b /usr/local/bin

# Verify installation
trivy --version
```

#### Run Filesystem Scan

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/backend

# Basic filesystem scan
trivy fs --severity HIGH,CRITICAL .

# Scan with JSON output
trivy fs --severity HIGH,CRITICAL . --format json -o trivy-fs-results.json
```

#### Build and Scan Container Image

```bash
# Build Docker image
cd Backend-FastAPI-Docker_Build-Pipeline
docker build -t fastapi-lambda:test backend/

# Scan container image
trivy image --severity HIGH,CRITICAL fastapi-lambda:test

# Scan with exit code (fails on findings)
trivy image --exit-code 1 --severity HIGH,CRITICAL fastapi-lambda:test
```

**Example Output:**
```
Scanning filesystem with Trivy...
2025-11-18T03:44:45Z	INFO	Detected OS	family="amazon" version="2"
2025-11-18T03:45:14Z	INFO	[python-pkg] Detecting vulnerabilities...

Report Summary
┌──────────────────────────────────────────────────────────┬────────────┬─────────────────┬─────────┐
│ Target                                                   │ Type       │ Vulnerabilities │ Secrets │
├──────────────────────────────────────────────────────────┼────────────┼─────────────────┼─────────┤
│ fastapi-lambda:test (amazon 2)                          │ amazon     │        0        │    -    │
│ var/task/fastapi-0.115.0.dist-info/METADATA             │ python-pkg │        1        │    -    │
└──────────────────────────────────────────────────────────┴────────────┴─────────────────┴─────────┘

CRITICAL: 1 (CVE-2024-XXXXX in fastapi==0.115.0)
```



### Step 3: Manual ECR Image Scanning Configuration

#### Enable ECR Image Scanning

```bash
# Enable scanning on existing repository
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

Create `security-test.sh` script to run all security tests:

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

### View Results in GitLab CI/CD

After pushing code, view security scan results:

1. Navigate to **CI/CD** → **Pipelines**
2. Click on pipeline run
3. View `lint_and_scan` job logs for Semgrep results
4. View `build_and_push` job logs for Trivy results

![GitLab Pipeline Logs](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.2-DevSecOps-Security-Testing&Configuration/semgrep-results-gitlab.png)

![GitLab Pipeline Logs](/images/5-Workshop/5.3-Setting-up-GitLab-CI-CD-Pipeline/5.3.2-DevSecOps-Security-Testing&Configuration/codebuild-logs.png)

### Best Practices

1. **Run Before Push**: Test locally before pushing
2. **Fail Fast**: Use `--exit-code 1` to fail builds on critical findings
3. **Severity Threshold**: Only block on HIGH and CRITICAL findings
4. **Regular Updates**: Update Semgrep rules and Trivy database regularly
