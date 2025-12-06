---
title: "Load Testing với Artillery"
date: "2024-01-01"
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

## Load Testing với Artillery

Trong phần này, bạn sẽ thực hiện load testing trên API đã deploy sử dụng Artillery để mô phỏng user traffic và đo lường hiệu suất.

### Yêu cầu

Cài đặt Artillery:

```bash
npm install -g artillery
```

Hoặc sử dụng Docker:

```bash
docker run -it --rm -v $(pwd):/work artilleryio/artillery:latest
```

### Bước 1: Lấy API URL và Token

```bash
# Lấy API URL
cd Backend-FastAPI-Docker_Build-Pipeline/infra
API_URL=$(terraform output -raw api_url)

# Lấy admin token (bạn cần login trước)
TOKEN="YOUR_ADMIN_TOKEN_HERE"
```

**Lưu ý:** Thay thế bằng admin token thực tế của bạn từ endpoint `/auth/login`.

### Bước 2: Tạo Cấu hình Load Test

Tạo `load-test.yml`:

```yaml
config:
  target: "https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com"
  phases:
    - duration: 60
      arrivalRate: 10
      name: "Warm up"
    - duration: 120
      arrivalRate: 50
      name: "Ramp up"
    - duration: 60
      arrivalRate: 10
      name: "Cool down"
  defaults:
    headers:
      Authorization: "Bearer YOUR_ADMIN_TOKEN_HERE"
      Content-Type: "application/json"

scenarios:
  - name: "Get products"
    weight: 70
    flow:
      - get:
          url: "/products"
          
  - name: "Create product"
    weight: 30
    flow:
      - post:
          url: "/products"
          json:
            name: "Load test product {{ $randomString() }}"
            price: 29.99
            stock: 100
            category: "electronics"
```

**Quan trọng:** Thay thế:
- `target` bằng API Gateway URL thực tế của bạn
- Header `Authorization` bằng admin token của bạn

### Bước 3: Chạy Load Test

```bash
artillery run load-test.yml
```

### Bước 4: Phân tích Kết quả

Artillery sẽ xuất thống kê như:

```
Summary report @ 14:30:00(+0000) 2024-01-01
  Scenarios launched:  1000
  Scenarios completed: 1000
  Requests completed:  2000
  Mean response/sec:   45.2
  Response time (msec):
    min: 45
    max: 1200
    median: 120
    p95: 350
    p99: 800
  Scenario counts:
    Get products: 700 (70%)
    Create product: 300 (30%)
  Codes:
    200: 1950
    201: 50
    500: 0
```

### Bước 5: Tạo HTML Report

```bash
artillery run --output report.json load-test.yml
artillery report report.json
```

Điều này tạo ra một HTML report với:
- Biểu đồ response time
- Biểu đồ request rate
- Error rates
- Phân tích scenario

![Load Test Results](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.4-Load%20Testing-with-Artillery/load-test.png)

### Cấu hình Load Test Nâng cao

Để test thực tế hơn:

```yaml
config:
  target: "https://YOUR_API_ID.execute-api.ap-southeast-1.amazonaws.com"
  phases:
    - duration: 120
      arrivalRate: 5
      name: "Warm up"
    - duration: 300
      arrivalRate: 20
      name: "Sustained load"
    - duration: 60
      arrivalRate: 5
      name: "Cool down"
  defaults:
    headers:
      Authorization: "Bearer YOUR_TOKEN"
      Content-Type: "application/json"
  processor: "./processor.js"  # Custom processor cho dynamic data

scenarios:
  - name: "Product browsing"
    weight: 60
    flow:
      - get:
          url: "/products"
      - think: 2  # Đợi 2 giây
      - get:
          url: "/products?category=electronics"
          
  - name: "Product management"
    weight: 30
    flow:
      - post:
          url: "/products"
          json:
            name: "Product {{ $randomString() }}"
            price: {{ $randomNumber(10, 1000) }}
            stock: {{ $randomNumber(1, 100) }}
            category: "{{ $pick(['electronics', 'clothing', 'books']) }}"
      - think: 1
      - get:
          url: "/products"
          
  - name: "Order flow"
    weight: 10
    flow:
      - get:
          url: "/products"
      - post:
          url: "/orders"
          json:
            items:
              - product_id: "{{ $pick(['id1', 'id2', 'id3']) }}"
                quantity: {{ $randomNumber(1, 5) }}
```

### Giám sát Trong Khi Load Test

#### CloudWatch Metrics

1. Điều hướng đến **CloudWatch** → **Metrics** → **AWS/Lambda**
2. Chọn `fastapi-lambda-fn`
3. Giám sát:
   - **Invocations**: Số lượng requests
   - **Duration**: Response time
   - **Errors**: Error rate
   - **Throttles**: Requests bị throttle

#### CloudWatch Logs

```bash
# Xem logs real-time
aws logs tail /aws/lambda/fastapi-lambda-fn --follow --region ap-southeast-1
```

### Performance Benchmarks

Hiệu suất mong đợi cho Lambda container:

| Metric | Target | Ghi chú |
|--------|--------|---------|
| **P50 (median)** | < 200ms | Hầu hết requests |
| **P95** | < 500ms | 95% requests |
| **P99** | < 1000ms | 99% requests |
| **Error Rate** | < 0.1% | Rất ít lỗi |
| **Throughput** | 50+ req/s | Phụ thuộc vào concurrency |

### Troubleshooting

#### Response Time Cao

- Kiểm tra Lambda cold starts
- Giám sát DynamoDB throttling
- Xem lại CloudWatch metrics
- Kiểm tra API Gateway latency

#### Error Rate Cao

- Kiểm tra CloudWatch logs cho errors
- Xác minh DynamoDB table capacity
- Kiểm tra Lambda timeout settings
- Xem lại API Gateway limits

#### Throttling

- Tăng Lambda concurrency limit
- Kiểm tra DynamoDB on-demand capacity
- Xem lại API Gateway throttling settings

### Best Practices

1. **Bắt đầu nhỏ**: Bắt đầu với arrival rate thấp và tăng dần
2. **Giám sát Resources**: Xem CloudWatch metrics trong khi test
3. **Test Scenarios thực tế**: Kết hợp read và write operations
4. **Sử dụng Correlation IDs**: Track requests qua logs
5. **Test Error Handling**: Bao gồm negative test cases

{{% notice tip %}}
Chạy load tests trong giờ off-peak và giám sát AWS costs. Load testing có thể tạo ra traffic đáng kể và phát sinh chi phí.
{{% /notice %}}
