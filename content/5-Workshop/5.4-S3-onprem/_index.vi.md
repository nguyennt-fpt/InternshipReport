---
title: "Deploy FastAPI Backend"
date: "2024-01-01"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## Deploy FastAPI Backend

FastAPI backend được tự động deploy bởi GitLab CI/CD pipeline. Phần này giải thích cấu trúc ứng dụng và cách test.

### Tổng quan Kiến trúc

Ứng dụng tuân theo kiến trúc phân lớp:

| Layer | Location | Trách nhiệm |
| --- | --- | --- |
| **API** | `app/api/routers/` | Xử lý HTTP requests, routing |
| **Service** | `app/services/` | Business logic, orchestration |
| **Repository** | `app/repositories/` | Data access, DynamoDB operations |
| **Models** | `app/models/` | Data structures, validation |
| **Core** | `app/core/` | Configuration, security, logging |

### Luồng Request

```
HTTP Request
    ↓
API Gateway HTTP API
    ↓
Lambda Function (Container)
    ↓
Mangum (ASGI Adapter)
    ↓
FastAPI Application
    ├── /auth (register, login)
    ├── /products (CRUD operations)
    └── /orders (create, list, get)
    ↓
Service Layer (Business Logic)
    ↓
Repository Layer (DynamoDB)
    ↓
DynamoDB Tables
```

### Nội dung

1. [Cấu trúc FastAPI Application](5.4.1-prepare/) - Cấu trúc code và kiến trúc
2. [Deploy Infrastructure](5.4.2-create-interface-enpoint/) - Terraform modules và AWS resources
3. [Test API](5.4.3-test-endpoint/) - Test endpoints với curl và Swagger UI
4. [Load Testing](5.4.4-dns-simulation/) - Performance testing với Artillery

### Deploy Tự động

Job `terraform_deploy` trong GitLab CI/CD tự động:

1. Initialize Terraform
2. Plan infrastructure changes
3. Apply changes (tạo/cập nhật resources)
4. Output API Gateway URL

**Không cần deploy thủ công!** Chỉ cần push code và pipeline xử lý mọi thứ.

### Infrastructure được Tạo

- **DynamoDB Tables**: `products`, `orders`, `users`
- **Lambda Function**: Container-based FastAPI application
- **API Gateway**: HTTP API (v2) với Lambda integration
- **IAM Roles**: Least privilege permissions
- **CloudWatch**: Log groups và alarms
- **SNS**: Alert notifications

![Lambda Function Configuration](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/lambda-function.png)

### Lấy API URL

Sau khi pipeline hoàn thành:

```bash
# Từ GitLab job logs (terraform_deploy)
# Hoặc từ Terraform output:
cd Backend-FastAPI-Docker_Build-Pipeline/infra
terraform output api_url
```

### Test Health Endpoint

```bash
API_URL="https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com"
curl $API_URL/health
# Expected: {"status": "ok"}
```

### API Endpoints

| Method | Endpoint | Mô tả | Auth |
|--------|----------|-------|------|
| `GET` | `/health` | Health check | Public |
| `POST` | `/auth/register` | Đăng ký user | Public |
| `POST` | `/auth/login` | Login và lấy JWT | Public |
| `GET` | `/products` | List products | Public |
| `POST` | `/products` | Tạo product | Admin |
| `GET` | `/products/{id}` | Lấy product | Public |
| `PUT` | `/products/{id}` | Cập nhật product | Admin |
| `DELETE` | `/products/{id}` | Xóa product | Admin |
| `GET` | `/orders` | List user orders | User |
| `POST` | `/orders` | Tạo order | User |
| `GET` | `/orders/{id}` | Lấy order | User |

### Swagger Documentation

Truy cập interactive API documentation:

```
https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com/docs
```

![FastAPI Swagger Documentation](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/api-endpoints.png)

{{% notice info %}}
Backend được tự động deploy bởi pipeline. Bạn chỉ cần test sau khi deployment hoàn thành.
{{% /notice %}}
