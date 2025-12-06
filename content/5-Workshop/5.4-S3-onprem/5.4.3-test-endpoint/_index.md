---
title: "Testing the API"
date: "2024-01-01"
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

## Testing the API

After the pipeline deploys the backend, you can test all API endpoints.

### Step 1: Get API Gateway URL

```bash
# Option 1: From GitLab job logs (terraform_deploy job)
# Look for: api_url = "https://..."

# Option 2: From Terraform output
cd Backend-FastAPI-Docker_Build-Pipeline/infra
API_URL=$(terraform output -raw api_url)
echo $API_URL
```

Example: `https://0bb7ewqnga.execute-api.ap-southeast-1.amazonaws.com`

### Step 2: Test Health Endpoint

```bash
curl $API_URL/health
```

Expected response:
```json
{"status": "ok"}
```

### Step 3: Access Swagger UI

Open in browser:
```
https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com/docs
```

The Swagger UI allows you to:
- View all available endpoints
- Test endpoints directly from the browser
- See request/response schemas
- Try out authentication

![Swagger UI](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/swagger-ui.png)

### Step 4: Register a User

```bash
curl -X POST $API_URL/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecurePassword123!"
  }'
```

Expected response:
```json
{"message": "registered"}
```

### Step 5: Login and Get JWT Token

```bash
curl -X POST $API_URL/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "user@example.com",
    "password": "SecurePassword123!"
  }'
```

Expected response:
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "bearer"
}
```

Save the token:
```bash
TOKEN=$(curl -s -X POST $API_URL/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "SecurePassword123!"}' \
  | jq -r '.access_token')
echo "Token: $TOKEN"
```

![API Test Results](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/api-test-results.png)

### Step 6: List Products (Public)

```bash
curl $API_URL/products
```

Expected response:
```json
[]
```

### Step 7: Create Product (Admin Only)

**Note:** Regular users cannot create products. You need an admin user. For testing, you can manually create an admin user in DynamoDB or modify the registration logic.

```bash
curl -X POST $API_URL/products \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{
    "name": "Laptop",
    "price": 999.99,
    "stock": 10,
    "category": "electronics"
  }'
```

If user is not admin, you'll get:
```json
{"detail": "Admin only"}
```

### Step 8: Get Product by ID

```bash
PRODUCT_ID="your-product-id"
curl $API_URL/products/$PRODUCT_ID
```

### Step 9: List Products by Category

```bash
curl "$API_URL/products?category=electronics"
```

### Step 10: Create Order

```bash
curl -X POST $API_URL/orders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{
    "items": [
      {
        "product_id": "product-id-here",
        "quantity": 2
      }
    ]
  }'
```

Expected response:
```json
{
  "order_id": "uuid-here",
  "user_id": "user-id-here",
  "items": [...],
  "total_amount": 1999.98,
  "status": "pending",
  "created_at": "2024-01-01T00:00:00Z"
}
```

### Step 11: List User Orders

```bash
curl -X GET $API_URL/orders \
  -H "Authorization: Bearer $TOKEN"
```

### Step 12: Get Order by ID

```bash
ORDER_ID="your-order-id"
curl -X GET $API_URL/orders/$ORDER_ID \
  -H "Authorization: Bearer $TOKEN"
```

### Testing in Browser

You can test public endpoints directly in browser:

- Health: `https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com/health`
- Products: `https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com/products`
- Swagger: `https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com/docs`

### View Resources in AWS Console

#### Lambda Function

1. Navigate to [Lambda Console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions)
2. Find: `fastapi-lambda-fn`
3. View configuration, logs, and metrics

![Lambda Console](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/lambda-console.png)

#### API Gateway

1. Navigate to [API Gateway Console](https://ap-southeast-1.console.aws.amazon.com/apigateway/main/apis?region=ap-southeast-1)
2. Find your API (name: `fastapi-lambda-http`)
3. View routes, stages, and logs

![API Gateway Console](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/apigw-console.png)

#### DynamoDB Tables

1. Navigate to [DynamoDB Console](https://ap-southeast-1.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-1#tables)
2. View tables: `products`, `orders`, `users`
3. Explore table items and indexes

![DynamoDB Console](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/dynamodb-console.png)

#### CloudWatch Logs

1. Navigate to [CloudWatch Logs](https://ap-southeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-southeast-1#logsV2:log-groups)
2. Find: `/aws/lambda/fastapi-lambda-fn`
3. View logs with correlation IDs

![CloudWatch Logs](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/cloudwatch-logs.png)

### Common Test Scenarios

#### Authentication Flow

```bash
# Register → Login → Use Token
curl -X POST $API_URL/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"pass123"}'

TOKEN=$(curl -s -X POST $API_URL/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"pass123"}' \
  | jq -r '.access_token')

curl -H "Authorization: Bearer $TOKEN" $API_URL/products
```

#### Error Handling

```bash
# Invalid credentials
curl -X POST $API_URL/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"wrong@test.com","password":"wrong"}'

# Missing token
curl $API_URL/orders

# Invalid token
curl -H "Authorization: Bearer invalid-token" $API_URL/orders
```

{{% notice tip %}}
Use correlation IDs from response headers (`X-Correlation-ID`) to trace requests across CloudWatch logs.
{{% /notice %}}
