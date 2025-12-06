---
title: "Workshop"
date: "2024-01-01"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng FastAPI Backend Bảo mật trên AWS Lambda với DevSecOps

#### Tổng quan

Đây là một **hướng dẫn triển khai từng bước** sẽ giúp bạn triển khai một **FastAPI Backend API** hoàn chỉnh chạy trên **AWS Lambda** (container-based) với pipeline CI/CD tự động hoàn toàn, tích hợp quét bảo mật, và Infrastructure as Code sử dụng Terraform.

**Repository dự án:** [https://gitlab.com/m.quang/devsecops-aws-ver2](https://gitlab.com/m.quang/devsecops-aws-ver2)

Làm theo hướng dẫn này để triển khai một ứng dụng serverless production-ready với quét bảo mật tự động, monitoring, và quản lý infrastructure.

Bạn sẽ học cách:
- Xây dựng ứng dụng **FastAPI** với kiến trúc phân lớp sạch (API → Service → Repository)
- Đóng gói FastAPI thành **Lambda container image** sử dụng Mangum adapter
- Thiết lập **AWS CodePipeline** với **CodeBuild** cho CI/CD tự động
- Tích hợp quét bảo mật với **Semgrep** (SAST) và **Trivy** (container vulnerability scanning)
- Triển khai infrastructure sử dụng **Terraform** modules
- Lưu trữ dữ liệu trong các bảng **DynamoDB** (products, orders, users)
- Triển khai **JWT authentication** với AWS Secrets Manager
- Thiết lập monitoring với **CloudWatch** và cảnh báo **SNS**

#### Kiến trúc

![System Architecture](/images/5-Workshop/workshop-architecture.png)

Kiến trúc được chia thành ba domain chính:

1. **Domain CI/CD Pipeline**: GitLab → CodePipeline → CodeBuild (Semgrep → Docker Build → Trivy) → ECR → Terraform Deploy
2. **Domain Application**: API Gateway HTTP API → Lambda (Container) → FastAPI → DynamoDB + Secrets Manager
3. **Domain Monitoring**: CloudWatch Logs → CloudWatch Alarms → SNS Alerts

**Luồng Request:**
```
User Request
    ↓
API Gateway (HTTP API)
    ↓
Lambda Function (Container Image từ ECR)
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

#### Cấu trúc dự án

```
Backend-FastAPI-Docker_Build-Pipeline/
├── backend/                    # Ứng dụng FastAPI
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
│   ├── main.tf                 # Điều phối chính
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
│       └── route53/            # Custom domain (tùy chọn)
└── pipeline/                   # CI/CD Pipeline
    ├── buildspec-build.yml    # Build stage (Semgrep, Docker, Trivy)
    ├── buildspec-deploy.yml   # Deploy stage (Terraform)
    ├── codepipeline.tf        # CodePipeline configuration
    └── README.md
```

#### Nội dung

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequiste/)
3. [Thiết lập Pipeline CI/CD](5.3-S3-vpc/)
4. [Xây dựng FastAPI Backend](5.4-S3-onprem/)
5. [Monitoring & Observability](5.5-Policy/)
6. [Dọn dẹp tài nguyên](5.6-Cleanup/)

#### Các dịch vụ AWS được sử dụng

| Danh mục | Dịch vụ |
| --- | --- |
| **Compute** | AWS Lambda (Container Image) |
| **API** | API Gateway HTTP API (v2) |
| **Database** | Amazon DynamoDB (3 tables) |
| **Bảo mật** | AWS Secrets Manager, IAM |
| **CI/CD** | AWS CodePipeline, CodeBuild, Amazon ECR |
| **Monitoring** | Amazon CloudWatch (Logs, Metrics, Alarms), SNS |
| **DNS** | Amazon Route 53 (tùy chọn) |
| **IaC** | Terraform |
| **Security Scanning** | Semgrep (SAST), Trivy (Container Scan) |

#### Thời gian & Chi phí ước tính

| Mục | Chi tiết |
| --- | --- |
| **Thời gian** | 4-5 giờ |
| **Cấp độ** | Trung cấp đến Nâng cao |
| **Chi phí** | ~$5-10 (nếu dọn dẹp sau workshop) |

