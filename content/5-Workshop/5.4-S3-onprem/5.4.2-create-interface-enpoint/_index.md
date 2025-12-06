---
title: "Deploy Infrastructure"
date: "2024-01-01"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

## Deploy Infrastructure

The infrastructure is automatically deployed by the GitLab CI/CD pipeline. The `terraform_deploy` job runs Terraform to create all AWS resources.

### Automatic Deployment via Pipeline

When you push code to the main branch, the pipeline automatically:

1. **Lint Stage**: Runs Semgrep security scan
2. **Build Stage**: Builds Docker image, scans with Trivy, pushes to ECR
3. **Deploy Stage**: Runs Terraform to deploy infrastructure

The `terraform_deploy` job uses the GitLab CI/CD variables you configured earlier.

### Infrastructure Created

The Terraform deployment creates:

- **DynamoDB Tables**:
  - `products` - Product catalog
  - `orders` - Order information
  - `users` - User accounts and authentication

- **Lambda Function**:
  - Container-based Lambda using image from ECR
  - Environment variables from Secrets Manager
  - IAM role with least privilege permissions

- **API Gateway HTTP API**:
  - Routes all requests to Lambda function
  - Automatic deployment on changes

- **IAM Roles**:
  - Lambda execution role
  - Permissions for DynamoDB, Secrets Manager, CloudWatch

- **CloudWatch Resources**:
  - Log groups for Lambda
  - Metric alarms for errors
  - SNS topic for alerts

### Terraform Module Structure

The infrastructure is organized into reusable modules:

```
infra/
├── main.tf                 # Orchestrates all modules
├── variables.tf            # Input variables
├── outputs.tf              # Output values
├── providers.tf            # Provider configuration
└── modules/
    ├── dynamodb/           # DynamoDB tables
    ├── iam/                # IAM roles and policies
    ├── lambda_container/  # Lambda function
    ├── apigw/              # API Gateway HTTP API
    └── observability/      # CloudWatch and SNS
```

### DynamoDB Tables

The DynamoDB module creates three tables:

#### 1. Products Table

```hcl
resource "aws_dynamodb_table" "products" {
  name         = var.products_table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "product_id"
  
  attribute {
    name = "product_id"
    type = "S"
  }
  
  attribute {
    name = "category"
    type = "S"
  }
  
  global_secondary_index {
    name            = "category-index"
    hash_key        = "category"
    projection_type = "ALL"
  }
}
```

**Attributes:**
- `product_id` (String) - Primary key
- `name` (String) - Product name
- `price` (Number) - Product price
- `stock` (Number) - Stock quantity
- `category` (String) - Product category
- `updated_at` (String) - Last update timestamp

**Global Secondary Index:**
- `category-index` - Query products by category

#### 2. Orders Table

```hcl
resource "aws_dynamodb_table" "orders" {
  name         = var.orders_table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "order_id"
  
  attribute {
    name = "order_id"
    type = "S"
  }
  
  attribute {
    name = "user_id"
    type = "S"
  }
  
  global_secondary_index {
    name            = "user-index"
    hash_key        = "user_id"
    projection_type = "ALL"
  }
}
```

**Attributes:**
- `order_id` (String) - Primary key
- `user_id` (String) - User who placed the order
- `items` (List) - Order items
- `total_amount` (Number) - Total order amount
- `status` (String) - Order status
- `created_at` (String) - Order creation timestamp

**Global Secondary Index:**
- `user-index` - Query orders by user

#### 3. Users Table

```hcl
resource "aws_dynamodb_table" "users" {
  name         = var.users_table_name
  billing_mode = "PAY_PER_REQUEST"
  hash_key     = "user_id"
  
  attribute {
    name = "user_id"
    type = "S"
  }
  
  attribute {
    name = "email"
    type = "S"
  }
  
  global_secondary_index {
    name            = "email-index"
    hash_key        = "email"
    projection_type = "ALL"
  }
}
```

**Attributes:**
- `user_id` (String) - Primary key
- `email` (String) - User email (unique)
- `password_hash` (String) - Hashed password
- `role` (String) - User role (admin/user)
- `created_at` (String) - Account creation timestamp

**Global Secondary Index:**
- `email-index` - Query users by email

![DynamoDB Tables](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.2-Deploy-Infrastructure/dynamodb-tables.png)

### Lambda Function

The Lambda module creates a container-based function:

```hcl
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

**Configuration:**
- **Package Type**: Container Image
- **Image URI**: From ECR (set via GitLab variable `ECR_URI`)
- **Timeout**: 15 seconds
- **Memory**: 512 MB
- **Environment Variables**: 
  - `AWS_REGION`
  - `PRODUCTS_TABLE`
  - `ORDERS_TABLE`
  - `JWT_SECRET` (fetched from Secrets Manager)

![Lambda Function Configuration](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.2-Deploy-Infrastructure/lambda-function.png)

### API Gateway HTTP API

The API Gateway module creates an HTTP API:

```hcl
resource "aws_apigatewayv2_api" "http" {
  name          = "${var.project_name}-http"
  protocol_type = "HTTP"
}

resource "aws_apigatewayv2_integration" "lambda" {
  api_id           = aws_apigatewayv2_api.http.id
  integration_type = "AWS_PROXY"
  integration_uri  = var.lambda_arn
}

resource "aws_apigatewayv2_route" "any" {
  api_id    = aws_apigatewayv2_api.http.id
  route_key = "$default"
  target    = "integrations/${aws_apigatewayv2_integration.lambda.id}"
}
```

**Features:**
- HTTP API (v2) - Lower latency and cost
- `$default` route - All requests go to Lambda
- Automatic deployment on changes

![API Gateway Configuration](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.2-Deploy-Infrastructure/api-gateway.png.png)

### IAM Roles

The IAM module creates a Lambda execution role:

```hcl
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
- CloudWatch Logs: Create log groups and streams
- DynamoDB: Full CRUD operations on all three tables and their indexes
- Secrets Manager: Read JWT secret value

### Monitoring and Observability

The observability module creates:

- **CloudWatch Log Group**: `/aws/lambda/${lambda_name}`
- **CloudWatch Alarm**: Triggers when error count > 0
- **SNS Topic**: Receives alarm notifications

### Get Deployment Outputs

After the pipeline completes successfully, get the API Gateway URL:

#### Option 1: From GitLab CI/CD Job Logs

1. Go to **CI/CD** → **Pipelines**
2. Click on the completed pipeline
3. Click on `terraform_deploy` job
4. Scroll to the end of logs to find Terraform outputs:

```
Outputs:

api_url = "https://abc123xyz.execute-api.ap-southeast-1.amazonaws.com"
lambda_arn = "arn:aws:lambda:ap-southeast-1:123456789012:function:fastapi-lambda-fn"
```

#### Option 2: From AWS Console

1. Navigate to **API Gateway** → **APIs**
2. Find your API (name: `fastapi-lambda-http`)
3. Copy the **Invoke URL**

#### Option 3: Using AWS CLI

```bash
# Get API Gateway URL
aws apigatewayv2 get-apis \
    --region ap-southeast-1 \
    --query 'Items[?Name==`fastapi-lambda-http`].ApiEndpoint' \
    --output text
```

### Verify Deployment

Test the health endpoint:

```bash
API_URL="https://abc123xyz.execute-api.ap-southeast-1.amazonaws.com"
curl $API_URL/health
```

Expected response:
```json
{"status": "ok"}
```

### Manual Deployment (Optional)

If you need to deploy manually (not recommended):

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/infra

# Initialize Terraform
terraform init

# Plan deployment
terraform plan \
    -var "project_name=fastapi-lambda" \
    -var "region=ap-southeast-1" \
    -var "jwt_secret_arn=arn:aws:secretsmanager:..." \
    -var "image_uri=123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fastapi-lambda:latest"

# Apply deployment
terraform apply \
    -var "project_name=fastapi-lambda" \
    -var "region=ap-southeast-1" \
    -var "jwt_secret_arn=arn:aws:secretsmanager:..." \
    -var "image_uri=123456789012.dkr.ecr.ap-southeast-1.amazonaws.com/fastapi-lambda:latest"
```

{{% notice tip %}}
The pipeline automatically handles deployment. Manual deployment is only needed for troubleshooting or initial setup before the pipeline is configured.
{{% /notice %}}
