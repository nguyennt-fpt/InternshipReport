---
title: "Triển khai Infrastructure với Terraform"
date: "2024-01-01"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Triển khai Infrastructure với Terraform

Trong phần này, bạn sẽ triển khai infrastructure hoàn chỉnh sử dụng Terraform modules, bao gồm DynamoDB tables, Lambda function, API Gateway, và IAM roles.

### Bước 1: Tạo JWT Secret trong Secrets Manager

Trước khi deploy, tạo JWT secret trong AWS Secrets Manager:

```bash
aws secretsmanager create-secret \
    --name fastapi-jwt-secret \
    --secret-string "your-super-secret-jwt-key-min-32-chars" \
    --region us-east-1
```

Lưu ARN để sử dụng trong Terraform:

```bash
aws secretsmanager describe-secret \
    --secret-id fastapi-jwt-secret \
    --query 'ARN' \
    --output text
```

![Secrets Manager](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.2-Deploy-Infrastructure/secrets-manager.png)

### Bước 2: Terraform Variables

Tạo file `terraform.tfvars`:

```hcl
# infra/terraform.tfvars
project_name        = "fastapi-lambda"
region              = "us-east-1"
jwt_secret_arn      = "arn:aws:secretsmanager:us-east-1:123456789012:secret:fastapi-jwt-secret-xxxxx"
products_table_name = "products"
orders_table_name   = "orders"
users_table_name    = "users"
enable_route53      = false
```

Hoặc truyền variables qua command line:

```bash
terraform apply \
    -var "jwt_secret_arn=arn:aws:secretsmanager:..." \
    -var "image_uri=123456789012.dkr.ecr.us-east-1.amazonaws.com/fastapi-lambda:latest"
```

### Bước 3: Cấu trúc Terraform Module

Infrastructure được tổ chức thành các modules có thể tái sử dụng:

```
infra/
├── main.tf                 # Điều phối tất cả modules
├── variables.tf            # Input variables
├── outputs.tf              # Output values
├── providers.tf            # Provider configuration
├── backend.tf              # Terraform backend (tùy chọn)
└── modules/
    ├── dynamodb/           # DynamoDB tables
    ├── iam/                # IAM roles and policies
    ├── lambda_container/  # Lambda function
    ├── apigw/              # API Gateway HTTP API
    ├── observability/      # CloudWatch and SNS
    └── route53/            # Custom domain (tùy chọn)
```

### Bước 4: DynamoDB Tables

Module DynamoDB tạo ba bảng:

```hcl
# infra/modules/dynamodb/main.tf
resource "aws_dynamodb_table" "products" {
  name         = var.products_table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "product_id"

  attribute { name = "product_id" type = "S" }
  attribute { name = "category" type = "S" }

  global_secondary_index {
    name            = "category-index"
    hash_key        = "category"
    projection_type = "ALL"
  }
}

resource "aws_dynamodb_table" "orders" {
  name         = var.orders_table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "order_id"

  attribute { name = "order_id" type = "S" }
  attribute { name = "user_id" type = "S" }

  global_secondary_index {
    name            = "user-index"
    hash_key        = "user_id"
    projection_type = "ALL"
  }
}

resource "aws_dynamodb_table" "users" {
  name         = var.users_table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "user_id"

  attribute { name = "user_id" type = "S" }
  attribute { name = "email" type = "S" }

  global_secondary_index {
    name            = "email-index"
    hash_key        = "email"
    projection_type = "ALL"
  }
}
```

**Cấu trúc bảng:**

1. **Products Table**
   - Primary Key: `product_id` (String)
   - GSI: `category-index` trên `category`
   - Attributes: name, price, stock, category, description

2. **Orders Table**
   - Primary Key: `order_id` (String)
   - GSI: `user-index` trên `user_id`
   - Attributes: user_id, items, total_amount, status, created_at

3. **Users Table**
   - Primary Key: `user_id` (String)
   - GSI: `email-index` trên `email`
   - Attributes: email, password_hash, role, created_at

![DynamoDB Tables](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.2-Deploy-Infrastructure/dynamodb-tables.png)

### Bước 5: IAM Roles

Module IAM tạo Lambda execution role với least privilege:

```hcl
# infra/modules/iam/main.tf
resource "aws_iam_role" "lambda" {
  name               = "${var.project_name}-lambda-role"
  assume_role_policy = data.aws_iam_policy_document.lambda_assume.json
}

data "aws_iam_policy_document" "lambda_policy" {
  statement { 
    actions = ["logs:CreateLogGroup", "logs:CreateLogStream", "logs:PutLogEvents"]
    resources = ["*"]
  }
  statement { 
    actions = ["dynamodb:PutItem", "dynamodb:GetItem", "dynamodb:UpdateItem", 
               "dynamodb:DeleteItem", "dynamodb:Query", "dynamodb:Scan"]
    resources = [
      var.products_table_arn, 
      var.orders_table_arn, 
      var.users_table_arn,
      "${var.products_table_arn}/index/*",
      "${var.orders_table_arn}/index/*",
      "${var.users_table_arn}/index/*"
    ]
  }
  statement { 
    actions = ["secretsmanager:GetSecretValue"]
    resources = [var.jwt_secret_arn]
  }
}
```

**Permissions:**
- CloudWatch Logs: Tạo log groups và streams
- DynamoDB: Full CRUD operations trên cả ba bảng và indexes của chúng
- Secrets Manager: Đọc JWT secret value

### Bước 6: Lambda Function

Module Lambda tạo container-based function:

```hcl
# infra/modules/lambda_container/main.tf
resource "aws_lambda_function" "this" {
  function_name = "${var.project_name}-fn"
  package_type  = "Image"
  image_uri     = var.image_uri
  role          = var.lambda_role_arn
  timeout       = 15
  memory_size   = 512

  environment {
    variables = var.environment
  }
}
```

**Cấu hình:**
- Package Type: Container Image
- Image URI: Từ ECR (ví dụ: `123456789012.dkr.ecr.us-east-1.amazonaws.com/fastapi-lambda:latest`)
- Timeout: 15 giây
- Memory: 512 MB
- Environment Variables: AWS_REGION, PRODUCTS_TABLE, ORDERS_TABLE, JWT_SECRET

![Lambda Function](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.2-Deploy-Infrastructure/lambda-function.png)

### Bước 7: API Gateway HTTP API

Module API Gateway tạo HTTP API (v2):

```hcl
# infra/modules/apigw/main.tf
resource "aws_apigatewayv2_api" "http" {
  name          = "${var.project_name}-http"
  protocol_type = "HTTP"
}

resource "aws_apigatewayv2_integration" "lambda" {
  api_id                 = aws_apigatewayv2_api.http.id
  integration_type       = "AWS_PROXY"
  integration_uri        = var.lambda_arn
  payload_format_version = "2.0"
}

resource "aws_apigatewayv2_route" "any" {
  api_id    = aws_apigatewayv2_api.http.id
  route_key = "$default"
  target    = "integrations/${aws_apigatewayv2_integration.lambda.id}"
}

resource "aws_apigatewayv2_stage" "default" {
  api_id      = aws_apigatewayv2_api.http.id
  name        = "$default"
  auto_deploy = true
}
```

**Tính năng:**
- HTTP API (v2) - độ trễ và chi phí thấp hơn REST API
- AWS_PROXY integration với Lambda
- Payload format version 2.0
- Auto-deploy enabled
- Default route (`$default`) bắt tất cả paths

![API Gateway](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.2-Deploy-Infrastructure/api-gateway.png.png)

### Bước 8: Deploy Infrastructure

Initialize và apply Terraform:

```bash
cd infra
terraform init
terraform plan -var "jwt_secret_arn=..." -var "image_uri=..."
terraform apply -var "jwt_secret_arn=..." -var "image_uri=..."
```

### Bước 9: Xác minh Deployment

Kiểm tra outputs:

```bash
terraform output
```

Expected outputs:
- `api_url`: API Gateway endpoint URL
- `lambda_arn`: Lambda function ARN
- `products_table_arn`: Products table ARN
- `orders_table_arn`: Orders table ARN
- `users_table_arn`: Users table ARN

Test health endpoint:

```bash
curl $(terraform output -raw api_url)/health
```

Expected response:
```json
{"status": "ok"}
```

### Bước 10: Cập nhật Lambda Environment Variables

Lambda function cần fetch JWT secret từ Secrets Manager. Application code nên đọc nó:

```python
# Trong Lambda function, đọc từ Secrets Manager
import boto3
import json

def get_jwt_secret():
    client = boto3.client('secretsmanager')
    response = client.get_secret_value(SecretId=os.environ['JWT_SECRET_ARN'])
    return json.loads(response['SecretString'])
```

Tuy nhiên, trong dự án này, JWT secret được truyền như một environment variable placeholder và nên được fetch tại runtime.

{{% notice tip %}}
Chế độ thanh toán `PAY_PER_REQUEST` cho DynamoDB tiết kiệm chi phí cho ứng dụng có traffic thấp. Đối với production với traffic dự đoán được, hãy cân nhắc chế độ `PROVISIONED`.
{{% /notice %}}
