---
title: "Kiểm thử API"
date: "2024-01-01"
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

## Kiểm thử API

Trong phần này, bạn sẽ kiểm thử tất cả API endpoints sử dụng curl, Postman, hoặc FastAPI Swagger UI.

### Bước 1: Lấy API Gateway URL

```bash
cd infra
API_URL=$(terraform output -raw api_url)
echo $API_URL
```

Ví dụ output: `https://abc123xyz.execute-api.us-east-1.amazonaws.com`

### Kiểm thử trong Browser

Bạn có thể test API trực tiếp trong browser bằng cách mở các URL sau:

#### Health Check
Mở trong browser:
```
https://0bb7ewqnga.execute-api.ap-southeast-1.amazonaws.com/health
```

Expected response: `{"status": "ok"}`

#### API Documentation (Swagger UI)
Mở trong browser:
```
https://0bb7ewqnga.execute-api.ap-southeast-1.amazonaws.com/docs
```

Điều này sẽ hiển thị Swagger UI tương tác nơi bạn có thể:
- Xem tất cả endpoints có sẵn
- Test endpoints trực tiếp từ browser
- Xem request/response schemas
- Thử authentication

![Swagger UI](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/swagger-ui.png)

#### Products Endpoint
Mở trong browser:
```
https://0bb7ewqnga.execute-api.ap-southeast-1.amazonaws.com/products
```

Expected response: `[]` (mảng rỗng nếu chưa có products)

**Lưu ý:** Thay `0bb7ewqnga` bằng API Gateway ID thực tế của bạn từ Terraform outputs.

### Kiểm tra Resources trên AWS Console

Sau khi deploy, bạn có thể xác minh và kiểm tra tất cả resources trong AWS Console.

#### Lambda Function

1. Điều hướng đến: [Lambda Functions Console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions)
2. Tìm: `fastapi-lambda-fn`
3. Click vào function để xem:
   - **Configuration**: Environment variables, timeout, memory, handler
   - **Code**: Container image URI từ ECR
   - **Test**: Tạo test events để invoke function
   - **Monitor**: Xem metrics, logs, và traces
   - **Aliases**: Function versions và aliases

![Lambda Function Console](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/lambda-console.png)

#### API Gateway

1. Điều hướng đến: [API Gateway Console](https://ap-southeast-1.console.aws.amazon.com/apigateway/main/apis?region=ap-southeast-1)
2. Tìm API ID: `0bb7ewqnga` (hoặc API ID của bạn từ Terraform outputs)
3. Click vào API để xem:
   - **Routes**: Tất cả routes và integrations đã cấu hình
   - **Stages**: Deployment stages (mặc định: `$default`)
   - **Settings**: Cấu hình API, CORS, throttling
   - **Integrations**: Chi tiết Lambda integration
   - **Logs**: API Gateway access logs

![API Gateway Console](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/apigw-console.png)

#### DynamoDB Tables

1. Điều hướng đến: [DynamoDB Tables Console](https://ap-southeast-1.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-1#tables)
2. Xem ba bảng:
   - **products**: Dữ liệu catalog sản phẩm
   - **orders**: Thông tin đơn hàng
   - **users**: Tài khoản người dùng và dữ liệu xác thực

3. Với mỗi bảng, bạn có thể:
   - Click **Explore table items** để xem dữ liệu
   - Kiểm tra tab **Indexes** cho Global Secondary Indexes (GSI)
   - Xem tab **Metrics** cho read/write capacity và throttling
   - Kiểm tra tab **Backup** cho backup settings

![DynamoDB Tables Console](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/dynamodb-console.png)

#### CloudWatch Logs

1. Điều hướng đến: [CloudWatch Log Groups](https://ap-southeast-1.console.aws.amazon.com/cloudwatch/home?region=ap-southeast-1#logsV2:log-groups)
2. Tìm: `/aws/lambda/fastapi-lambda-fn`
3. Click vào log group để:
   - Xem recent log streams
   - Tìm kiếm logs theo correlation ID hoặc keywords
   - Sử dụng CloudWatch Insights cho advanced queries
   - Giám sát log retention settings

![CloudWatch Logs Console](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/cloudwatch-logs.png)

#### CloudWatch Insights Query Example

Trong CloudWatch Logs Insights, bạn có thể chạy queries như:

```
fields @timestamp, @message, correlation_id
| filter @message like /error/
| sort @timestamp desc
| limit 20
```

Điều này sẽ hiển thị các lỗi gần đây với correlation IDs để dễ debug hơn.

### Bước 2: Test Health Endpoint

```bash
curl $API_URL/health
```

Expected response:
```json
{"status": "ok"}
```

### Bước 3: Đăng ký User mới

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

### Bước 4: Đăng nhập và lấy JWT Token

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

Lưu token cho các requests tiếp theo:

```bash
TOKEN=$(curl -s -X POST $API_URL/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "user@example.com", "password": "SecurePassword123!"}' \
  | jq -r '.access_token')
```

![API Test Results](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/api-test-results.png)

### Bước 5: Tạo Admin User (Tùy chọn)

Để test admin endpoints, bạn có thể cần tạo admin user thủ công trong DynamoDB hoặc sửa logic đăng ký. Hiện tại, test với regular user endpoints.

### Bước 6: Liệt kê Products (Public Endpoint)

```bash
curl $API_URL/products
```

Expected response:
```json
[]
```

### Bước 7: Tạo Product (Chỉ Admin)

```bash
curl -X POST $API_URL/products \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{
    "name": "Laptop",
    "price": 999.99,
    "stock": 10,
    "category": "electronics",
    "description": "High-performance laptop"
  }'
```

**Lưu ý:** Điều này sẽ thất bại nếu user không phải admin. Bạn sẽ nhận được:
```json
{"detail": "Admin only"}
```

### Bước 8: Lấy Product theo ID

```bash
PRODUCT_ID="your-product-id"
curl $API_URL/products/$PRODUCT_ID
```

### Bước 9: Liệt kê Products theo Category

```bash
curl "$API_URL/products?category=electronics"
```

### Bước 10: Tạo Order

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

### Bước 11: Liệt kê Orders của User

```bash
curl -X GET $API_URL/orders \
  -H "Authorization: Bearer $TOKEN"
```

### Bước 12: Lấy Order theo ID

```bash
ORDER_ID="your-order-id"
curl -X GET $API_URL/orders/$ORDER_ID \
  -H "Authorization: Bearer $TOKEN"
```

### Kiểm thử với Postman

1. Import API collection
2. Set biến `API_URL`
3. Chạy request "Login" trước để lấy token
4. Set token trong Authorization header cho protected endpoints


### Kiểm thử với FastAPI Swagger UI

Nếu bạn đang chạy cục bộ, truy cập Swagger UI tại `http://localhost:8000/docs`.

Đối với API đã deploy, bạn có thể enable Swagger bằng cách thêm route hoặc truy cập nếu CORS được cấu hình.

### Các kịch bản kiểm thử phổ biến

#### 1. Authentication Flow
```bash
# Register → Login → Use Token
curl -X POST $API_URL/auth/register -d '{"email":"test@test.com","password":"pass123"}'
TOKEN=$(curl -s -X POST $API_URL/auth/login -d '{"email":"test@test.com","password":"pass123"}' | jq -r '.access_token')
curl -H "Authorization: Bearer $TOKEN" $API_URL/products
```

#### 2. Error Handling
```bash
# Invalid credentials
curl -X POST $API_URL/auth/login -d '{"email":"wrong@test.com","password":"wrong"}'

# Missing token
curl $API_URL/orders

# Invalid token
curl -H "Authorization: Bearer invalid-token" $API_URL/orders
```

#### 3. Product Operations
```bash
# List all products
curl $API_URL/products

# Filter by category
curl "$API_URL/products?category=electronics"

# Get specific product
curl $API_URL/products/product-id-here
```

### Giám sát Requests trong CloudWatch

1. Điều hướng đến **CloudWatch** → **Log Groups**
2. Tìm `/aws/lambda/fastapi-lambda-fn`
3. Xem recent log streams
4. Kiểm tra correlation IDs trong logs

![CloudWatch Logs](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.3-Testing-the-API/cloudwatch-logs.png)

### Ví dụ API Response

#### Success Response
```json
{
  "product_id": "123e4567-e89b-12d3-a456-426614174000",
  "name": "Laptop",
  "price": 999.99,
  "stock": 10,
  "category": "electronics",
  "description": "High-performance laptop"
}
```

#### Error Response
```json
{
  "detail": "Product not found"
}
```

#### Validation Error
```json
{
  "detail": [
    {
      "loc": ["body", "price"],
      "msg": "value is not a valid float",
      "type": "type_error.float"
    }
  ]
}
```

{{% notice tip %}}
Sử dụng correlation IDs từ response headers để trace requests qua logs. Middleware thêm `X-Correlation-ID` vào tất cả responses.
{{% /notice %}}
