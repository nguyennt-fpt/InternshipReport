---
title: "Workshop"
date: "2024-01-01"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building a Secure FastAPI Backend on AWS Lambda with DevSecOps

#### Overview

This is a **step-by-step deployment guide** that will help you deploy a complete **FastAPI Backend API** running on **AWS Lambda** (container-based) with a fully automated CI/CD pipeline, integrated security scanning, and Infrastructure as Code using Terraform.

**Project Repository:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

Follow this guide to deploy a production-ready serverless application with automated security scanning, monitoring, and infrastructure management.

You will learn how to:
- Build a **FastAPI** application with clean layered architecture (API → Service → Repository)
- Package FastAPI as a **Lambda container image** using Mangum adapter
- Set up CI/CD
- Integrate security scanning with **Semgrep** (SAST) and **Trivy** (container vulnerability scanning)
- Deploy infrastructure using **Terraform** modules
- Store data in **DynamoDB** tables (products, orders, users)
- Implement **JWT authentication** with AWS Secrets Manager
- Set up monitoring with **CloudWatch** and **SNS** alerts

#### Architecture

![System Architecture](/images/5-Workshop/workshop-architecture.png)

The architecture is divided into three main domains:

1. **CI/CD Pipeline Domain**: GitLab → (Semgrep → Docker Build → Trivy) → ECR → Terraform Deploy
2. **Application Domain**: API Gateway HTTP API → Lambda (Container) → FastAPI → DynamoDB + Secrets Manager
3. **Monitoring Domain**: CloudWatch Logs → CloudWatch Alarms → SNS Alerts

**Request Flow:**
```
User Request
    ↓
API Gateway (HTTP API)
    ↓
Lambda Function (Container Image from ECR)
    ↓
Mangum (ASGI Adapter)
    ↓
FastAPI Application
    ├── /auth    → JWT Authentication
    ├── /products → Product CRUD Operations
    └── /orders   → Order CRUD Operations
    ↓
DynamoDB Tables
    ├── products (product_id, name, price, stock, category)
    ├── orders   (order_id, user_id, products, status)
    └── users    (user_id, email, password_hash)
```

#### Project Structure

```
Backend-FastAPI-Docker_Build-Pipeline/
├── backend/                    # FastAPI Application
│   ├── app/
│   │   ├── api/
│   │   │   ├── routers/        # API endpoints (auth, products, orders)
│   │   │   ├── deps.py         # Dependency injection
│   │   │   └── middleware.py  # Correlation ID middleware
│   │   ├── core/               # Core configuration
│   │   │   ├── config.py       # Environment variables
│   │   │   ├── security.py     # JWT, password hashing
│   │   │   └── logging.py      # Structured logging
│   │   ├── models/             # Pydantic models
│   │   │   ├── product.py
│   │   │   ├── order.py
│   │   │   └── user.py
│   │   ├── repositories/       # Data access layer
│   │   │   ├── ddb_products.py
│   │   │   ├── ddb_orders.py
│   │   │   └── ddb_users.py
│   │   ├── services/           # Business logic layer
│   │   │   ├── product_service.py
│   │   │   ├── order_service.py
│   │   │   └── auth_service.py
│   │   ├── utils/              # Utility functions
│   │   │   └── time.py
│   │   ├── main.py             # FastAPI app entry point
│   │   └── lambda_handler.py   # Lambda handler (Mangum)
│   ├── tests/                  # Unit tests
│   ├── Dockerfile              # Lambda container image
│   ├── requirements.txt
│   ├── semgrep.yml            # Security scanning rules
│   └── trivyignore.txt        # Trivy ignore list
├── infra/                      # Terraform Infrastructure
│   ├── main.tf                 # Main orchestration
│   ├── variables.tf
│   ├── outputs.tf
│   ├── providers.tf
│   ├── backend.tf
│   └── modules/
│       ├── dynamodb/           # DynamoDB tables
│       ├── iam/                # IAM roles & policies
│       ├── lambda_container/  # Lambda function
│       ├── apigw/              # API Gateway HTTP API
│       ├── observability/      # CloudWatch & SNS
│       └── route53/            # Custom domain (optional)
└── pipeline/                   # CI/CD Pipeline
    ├── buildspec-build.yml    # Build stage (Semgrep, Docker, Trivy)
    ├── buildspec-deploy.yml   # Deploy stage (Terraform)
    ├── codepipeline.tf        # CodePipeline configuration
    └── README.md
```

#### Content

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Setting up CI/CD Pipeline](5.3-S3-vpc/)
4. [Building FastAPI Backend](5.4-S3-onprem/)
5. [Monitoring & Observability](5.5-Policy/)
6. [Clean up Resources](5.6-Cleanup/)

#### AWS Services Used

| Category | Services |
| --- | --- |
| **Compute** | AWS Lambda (Container Image) |
| **API** | API Gateway HTTP API (v2) |
| **Database** | Amazon DynamoDB (3 tables) |
| **Security** | AWS Secrets Manager, IAM |
| **CI/CD** | Amazon ECR |
| **Monitoring** | Amazon CloudWatch (Logs, Metrics, Alarms), SNS |
| **DNS** | Amazon Route 53 (optional) |
| **IaC** | Terraform |
| **Security Scanning** | Semgrep (SAST), Trivy (Container Scan) |

#### Estimated Time & Cost

| Item | Details |
| --- | --- |
| **Duration** | 4-5 hours |
| **Level** | Intermediate to Advanced |
| **Cost** | ~$5-10 (if cleaned up after workshop) |


