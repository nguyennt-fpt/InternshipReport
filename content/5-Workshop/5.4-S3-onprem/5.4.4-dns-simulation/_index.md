---
title: "Load Testing with Artillery"
date: "2024-01-01"
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

## Load Testing with Artillery

In this section, you will perform load testing on the deployed API using Artillery to simulate user traffic and measure performance.

### Prerequisites

Install Artillery:

```bash
npm install -g artillery
```

Or using Docker:

```bash
docker run -it --rm -v $(pwd):/work artilleryio/artillery:latest
```

### Step 1: Get API URL and Token

```bash
# Get API URL
cd Backend-FastAPI-Docker_Build-Pipeline/infra
API_URL=$(terraform output -raw api_url)

# Get admin token (you need to login first)
TOKEN="YOUR_ADMIN_TOKEN_HERE"
```

**Note:** Replace with your actual admin token from `/auth/login` endpoint.

### Step 2: Create Load Test Configuration

Create `load-test.yml`:

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

**Important:** Replace:
- `target` with your actual API Gateway URL
- `Authorization` header with your admin token

### Step 3: Run Load Test

```bash
artillery run load-test.yml
```

### Step 4: Analyze Results

Artillery will output statistics like:

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

### Step 5: Generate HTML Report

```bash
artillery run --output report.json load-test.yml
artillery report report.json
```

This generates an HTML report with:
- Response time graphs
- Request rate charts
- Error rates
- Scenario breakdown

![Load Test Results](/images/5-Workshop/5.4-Deploy-FastAPI-Backend/5.4.4-Load%20Testing-with-Artillery/load-test.png)

### Advanced Load Test Configuration

For more realistic testing:

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
  processor: "./processor.js"  # Custom processor for dynamic data

scenarios:
  - name: "Product browsing"
    weight: 60
    flow:
      - get:
          url: "/products"
      - think: 2  # Wait 2 seconds
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

### Monitor During Load Test

#### CloudWatch Metrics

1. Navigate to **CloudWatch** → **Metrics** → **AWS/Lambda**
2. Select `fastapi-lambda-fn`
3. Monitor:
   - **Invocations**: Request count
   - **Duration**: Response time
   - **Errors**: Error rate
   - **Throttles**: Throttled requests

#### CloudWatch Logs

```bash
# Watch logs in real-time
aws logs tail /aws/lambda/fastapi-lambda-fn --follow --region ap-southeast-1
```

### Performance Benchmarks

Expected performance for Lambda container:

| Metric | Target | Notes |
|--------|--------|-------|
| **P50 (median)** | < 200ms | Most requests |
| **P95** | < 500ms | 95% of requests |
| **P99** | < 1000ms | 99% of requests |
| **Error Rate** | < 0.1% | Very low errors |
| **Throughput** | 50+ req/s | Depends on concurrency |

### Troubleshooting

#### High Response Times

- Check Lambda cold starts
- Monitor DynamoDB throttling
- Review CloudWatch metrics
- Check API Gateway latency

#### High Error Rates

- Check CloudWatch logs for errors
- Verify DynamoDB table capacity
- Check Lambda timeout settings
- Review API Gateway limits

#### Throttling

- Increase Lambda concurrency limit
- Check DynamoDB on-demand capacity
- Review API Gateway throttling settings

### Best Practices

1. **Start Small**: Begin with low arrival rates and gradually increase
2. **Monitor Resources**: Watch CloudWatch metrics during testing
3. **Test Realistic Scenarios**: Mix read and write operations
4. **Use Correlation IDs**: Track requests across logs
5. **Test Error Handling**: Include negative test cases

{{% notice tip %}}
Run load tests during off-peak hours and monitor AWS costs. Load testing can generate significant traffic and incur costs.
{{% /notice %}}
