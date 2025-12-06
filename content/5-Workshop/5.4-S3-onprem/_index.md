---
title: "FastAPI Backend Deployment"
date: "2024-01-01"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## FastAPI Backend Deployment

The FastAPI backend is automatically deployed by the GitLab CI/CD pipeline. This section explains the application structure and how to test it.

### Architecture Overview

The application follows a clean layered architecture:

| Layer | Location | Responsibility |
| --- | --- | --- |
| **API** | `app/api/routers/` | HTTP request handling, routing |
| **Service** | `app/services/` | Business logic, orchestration |
| **Repository** | `app/repositories/` | Data access, DynamoDB operations |
| **Models** | `app/models/` | Data structures, validation |
| **Core** | `app/core/` | Configuration, security, logging |

### Request Flow

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

### Content

1. [FastAPI Application Structure](5.4.1-prepare/) - Code structure and architecture
2. [Infrastructure Deployment](5.4.2-create-interface-enpoint/) - Terraform modules and AWS resources
3. [Testing the API](5.4.3-test-endpoint/) - Test endpoints with curl and Swagger UI
4. [Load Testing](5.4.4-dns-simulation/) - Performance testing with Artillery

### Automatic Deployment

The `terraform_deploy` job in GitLab CI/CD automatically:

1. Initializes Terraform
2. Plans infrastructure changes
3. Applies changes (creates/updates resources)
4. Outputs API Gateway URL

**No manual deployment needed!** Just push code and the pipeline handles everything.

### Infrastructure Created

- **DynamoDB Tables**: `products`, `orders`, `users`
- **Lambda Function**: Container-based FastAPI application
- **API Gateway**: HTTP API (v2) with Lambda integration
- **IAM Roles**: Least privilege permissions
- **CloudWatch**: Log groups and alarms
- **SNS**: Alert notifications

![Lambda Function Configuration](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/lambda-function.png)

### Get API URL

After pipeline completes:

```bash
# From GitLab job logs (terraform_deploy)
# Or from Terraform output:
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

| Method | Endpoint | Description | Auth |
|--------|----------|-------------|------|
| `GET` | `/health` | Health check | Public |
| `POST` | `/auth/register` | Register user | Public |
| `POST` | `/auth/login` | Login and get JWT | Public |
| `GET` | `/products` | List products | Public |
| `POST` | `/products` | Create product | Admin |
| `GET` | `/products/{id}` | Get product | Public |
| `PUT` | `/products/{id}` | Update product | Admin |
| `DELETE` | `/products/{id}` | Delete product | Admin |
| `GET` | `/orders` | List user orders | User |
| `POST` | `/orders` | Create order | User |
| `GET` | `/orders/{id}` | Get order | User |

### Swagger Documentation

Access interactive API documentation:

```
https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com/docs
```

![FastAPI Swagger Documentation](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/api-endpoints.png)

{{% notice info %}}
The backend is automatically deployed by the pipeline. You only need to test it after deployment completes.
{{% /notice %}}
