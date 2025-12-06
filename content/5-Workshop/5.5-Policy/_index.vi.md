---
title: "Cấu hình Thủ công: Monitoring & Security"
date: "2024-01-01"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Cấu hình Thủ công: Monitoring & Security

Phần này cung cấp **hướng dẫn cấu hình thủ công từng bước** cho CloudWatch monitoring, alarms, SNS notifications, và security settings cho FastAPI Backend.

### Bước 1: Cấu hình CloudWatch Log Group Thủ công

#### Tạo Log Group

```bash
# Tạo CloudWatch log group cho Lambda
aws logs create-log-group \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --region ap-southeast-1

# Set retention period (14 days)
aws logs put-retention-policy \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --retention-in-days 14 \
    --region ap-southeast-1
```

#### Xem Logs

```bash
# List log streams
aws logs describe-log-streams \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --region ap-southeast-1 \
    --order-by LastEventTime \
    --descending \
    --max-items 10

# Get recent log events
aws logs tail /aws/lambda/fastapi-lambda-fn \
    --follow \
    --region ap-southeast-1
```

#### CloudWatch Insights Query

```bash
# Query logs sử dụng CloudWatch Insights
aws logs start-query \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --start-time $(date -d '1 hour ago' +%s) \
    --end-time $(date +%s) \
    --query-string 'fields @timestamp, @message, correlation_id | filter @message like /error/ | sort @timestamp desc | limit 20' \
    --region ap-southeast-1

# Get query results (thay thế QUERY_ID)
aws logs get-query-results \
    --query-id QUERY_ID \
    --region ap-southeast-1
```

![CloudWatch Logs](/images/5-Workshop/5.5-Manual-Configuration-Monitoring&Security/cloudwatch-logs.png)

### Bước 2: Cấu hình CloudWatch Metrics Thủ công

#### Xem Lambda Metrics

```bash
# List available metrics
aws cloudwatch list-metrics \
    --namespace AWS/Lambda \
    --dimensions Name=FunctionName,Value=fastapi-lambda-fn \
    --region ap-southeast-1

# Get metric statistics
aws cloudwatch get-metric-statistics \
    --namespace AWS/Lambda \
    --metric-name Invocations \
    --dimensions Name=FunctionName,Value=fastapi-lambda-fn \
    --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
    --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
    --period 300 \
    --statistics Sum \
    --region ap-southeast-1
```

#### Tạo Custom Metric

```python
# Trong FastAPI application của bạn
import boto3

cloudwatch = boto3.client('cloudwatch', region_name='ap-southeast-1')

# Publish custom metric
cloudwatch.put_metric_data(
    Namespace='FastAPI/Products',
    MetricData=[
        {
            'MetricName': 'ProductsCreated',
            'Value': 1,
            'Unit': 'Count',
            'Timestamp': datetime.utcnow()
        }
    ]
)
```

![CloudWatch Metrics](/images/5-Workshop/5.5-Manual-Configuration-Monitoring&Security/cloudwatch-metrics.png)

### Bước 3: Cấu hình CloudWatch Alarm Thủ công

#### Tạo SNS Topic Trước

```bash
# Tạo SNS topic cho alerts
aws sns create-topic \
    --name fastapi-lambda-alerts \
    --region ap-southeast-1

# Get topic ARN
SNS_TOPIC_ARN=$(aws sns list-topics \
    --region ap-southeast-1 \
    --query 'Topics[?contains(TopicArn, `fastapi-lambda-alerts`)].TopicArn' \
    --output text)
echo "SNS Topic ARN: $SNS_TOPIC_ARN"
```

#### Tạo CloudWatch Alarm cho Lambda Errors

```bash
# Tạo alarm cho Lambda errors
aws cloudwatch put-metric-alarm \
    --alarm-name fastapi-lambda-5xx \
    --alarm-description "Alert when Lambda errors exceed threshold" \
    --metric-name Errors \
    --namespace AWS/Lambda \
    --statistic Sum \
    --period 60 \
    --evaluation-periods 1 \
    --threshold 1 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=FunctionName,Value=fastapi-lambda-fn \
    --alarm-actions $SNS_TOPIC_ARN \
    --region ap-southeast-1
```

#### Tạo Alarm cho High Duration

```bash
# Tạo alarm cho Lambda duration
aws cloudwatch put-metric-alarm \
    --alarm-name fastapi-lambda-duration \
    --alarm-description "Alert when Lambda duration is high" \
    --metric-name Duration \
    --namespace AWS/Lambda \
    --statistic Average \
    --period 300 \
    --evaluation-periods 2 \
    --threshold 5000 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=FunctionName,Value=fastapi-lambda-fn \
    --alarm-actions $SNS_TOPIC_ARN \
    --region ap-southeast-1
```

#### Tạo Alarm cho Throttles

```bash
# Tạo alarm cho Lambda throttles
aws cloudwatch put-metric-alarm \
    --alarm-name fastapi-lambda-throttles \
    --alarm-description "Alert when Lambda is throttled" \
    --metric-name Throttles \
    --namespace AWS/Lambda \
    --statistic Sum \
    --period 60 \
    --evaluation-periods 1 \
    --threshold 1 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=FunctionName,Value=fastapi-lambda-fn \
    --alarm-actions $SNS_TOPIC_ARN \
    --region ap-southeast-1
```

#### Xem Alarms

```bash
# List tất cả alarms
aws cloudwatch describe-alarms \
    --alarm-name-prefix fastapi-lambda \
    --region ap-southeast-1

# Get alarm state
aws cloudwatch describe-alarms \
    --alarm-names fastapi-lambda-5xx \
    --region ap-southeast-1 \
    --query 'MetricAlarms[0].StateValue' \
    --output text
```

![CloudWatch Alarm](/images/5-Workshop/5.5-Manual-Configuration-Monitoring&Security/cloudwatch-alarm.png)

### Bước 4: Cấu hình SNS Subscription Thủ công

#### Subscribe Email

```bash
# Subscribe email to SNS topic
aws sns subscribe \
    --topic-arn $SNS_TOPIC_ARN \
    --protocol email \
    --notification-endpoint your-email@example.com \
    --region ap-southeast-1

# Kiểm tra email và confirm subscription
```

#### Subscribe SMS

```bash
# Subscribe SMS to SNS topic
aws sns subscribe \
    --topic-arn $SNS_TOPIC_ARN \
    --protocol sms \
    --notification-endpoint +1234567890 \
    --region ap-southeast-1
```

#### List Subscriptions

```bash
# List tất cả subscriptions
aws sns list-subscriptions-by-topic \
    --topic-arn $SNS_TOPIC_ARN \
    --region ap-southeast-1
```

#### Test Notification

```bash
# Publish test message
aws sns publish \
    --topic-arn $SNS_TOPIC_ARN \
    --message "Test alert from FastAPI Lambda" \
    --subject "Test Alert" \
    --region ap-southeast-1
```

![SNS Subscription](/images/5-Workshop/5.5-Manual-Configuration-Monitoring&Security/sns-subscription.png)

### Bước 5: Cấu hình Security Thủ công

#### Enable ECR Image Scanning

```bash
# Enable scanning trên existing repository
aws ecr put-image-scanning-configuration \
    --repository-name fastapi-lambda \
    --image-scanning-configuration scanOnPush=true \
    --region ap-southeast-1

# Verify scanning is enabled
aws ecr describe-repositories \
    --repository-names fastapi-lambda \
    --region ap-southeast-1 \
    --query 'repositories[0].imageScanningConfiguration'
```

#### Configure Secrets Manager Encryption

```bash
# Tạo KMS key cho Secrets Manager
KMS_KEY_ID=$(aws kms create-key \
    --description "FastAPI JWT Secret Key" \
    --region ap-southeast-1 \
    --query 'KeyMetadata.KeyId' \
    --output text)

# Update secret để sử dụng KMS encryption
aws secretsmanager update-secret \
    --secret-id fastapi-jwt-secret \
    --kms-key-id $KMS_KEY_ID \
    --region ap-southeast-1
```

#### Enable API Gateway Logging

```bash
# Get API Gateway ID
API_ID=$(aws apigatewayv2 get-apis \
    --region ap-southeast-1 \
    --query 'Items[?Name==`fastapi-lambda-http`].ApiId' \
    --output text)

# Tạo CloudWatch log group cho API Gateway
aws logs create-log-group \
    --log-group-name /aws/apigateway/fastapi-lambda \
    --region ap-southeast-1

# Enable access logging (cần IAM role với logs:CreateLogStream permission)
aws apigatewayv2 update-stage \
    --api-id $API_ID \
    --stage-name '$default' \
    --access-log-settings DestinationArn=arn:aws:logs:ap-southeast-1:ACCOUNT_ID:log-group:/aws/apigateway/fastapi-lambda,Format='{"requestId":"$context.requestId","ip":"$context.identity.sourceIp","requestTime":"$context.requestTime","httpMethod":"$context.httpMethod","routeKey":"$context.routeKey","status":"$context.status","protocol":"$context.protocol","responseLength":"$context.responseLength"}' \
    --region ap-southeast-1
```

### Bước 6: Tạo Monitoring Dashboard

#### Tạo CloudWatch Dashboard

```bash
# Tạo dashboard JSON
cat > dashboard.json <<EOF
{
  "widgets": [
    {
      "type": "metric",
      "properties": {
        "metrics": [
          ["AWS/Lambda", "Invocations", {"stat": "Sum", "label": "Invocations"}],
          [".", "Errors", {"stat": "Sum", "label": "Errors"}],
          [".", "Duration", {"stat": "Average", "label": "Duration (ms)"}],
          [".", "Throttles", {"stat": "Sum", "label": "Throttles"}]
        ],
        "period": 300,
        "stat": "Sum",
        "region": "ap-southeast-1",
        "title": "Lambda Metrics",
        "view": "timeSeries",
        "yAxis": {
          "left": {
            "min": 0
          }
        }
      }
    }
  ]
}
EOF

# Tạo dashboard
aws cloudwatch put-dashboard \
    --dashboard-name FastAPI-Monitoring \
    --dashboard-body file://dashboard.json \
    --region ap-southeast-1
```

![Monitoring Dashboard](/images/5-Workshop/5.5-Manual-Configuration-Monitoring&Security/monitoring-dashboard.png)

### Bước 7: Xác minh Cấu hình

#### Kiểm tra Tất cả Resources

```bash
# Verify log group exists
aws logs describe-log-groups \
    --log-group-name-prefix /aws/lambda/fastapi-lambda \
    --region ap-southeast-1

# Verify alarms exist
aws cloudwatch describe-alarms \
    --alarm-name-prefix fastapi-lambda \
    --region ap-southeast-1

# Verify SNS topic exists
aws sns list-topics \
    --region ap-southeast-1 \
    --query 'Topics[?contains(TopicArn, `fastapi-lambda-alerts`)]'

# Verify SNS subscriptions
aws sns list-subscriptions-by-topic \
    --topic-arn $SNS_TOPIC_ARN \
    --region ap-southeast-1
```

### Script Cấu hình Hoàn chỉnh

Tạo `configure-monitoring.sh`:

```bash
#!/bin/bash
set -e

REGION="ap-southeast-1"
FUNCTION_NAME="fastapi-lambda-fn"
PROJECT_NAME="fastapi-lambda"

echo "Configuring Monitoring & Security..."

# 1. Tạo CloudWatch Log Group
echo "Creating CloudWatch log group..."
aws logs create-log-group \
    --log-group-name /aws/lambda/$FUNCTION_NAME \
    --region $REGION 2>/dev/null || echo "Log group already exists"

aws logs put-retention-policy \
    --log-group-name /aws/lambda/$FUNCTION_NAME \
    --retention-in-days 14 \
    --region $REGION

# 2. Tạo SNS Topic
echo "Creating SNS topic..."
SNS_TOPIC_ARN=$(aws sns create-topic \
    --name ${PROJECT_NAME}-alerts \
    --region $REGION \
    --query 'TopicArn' \
    --output text)
echo "SNS Topic ARN: $SNS_TOPIC_ARN"

# 3. Tạo CloudWatch Alarms
echo "Creating CloudWatch alarms..."
aws cloudwatch put-metric-alarm \
    --alarm-name ${PROJECT_NAME}-5xx \
    --alarm-description "Alert when Lambda errors exceed threshold" \
    --metric-name Errors \
    --namespace AWS/Lambda \
    --statistic Sum \
    --period 60 \
    --evaluation-periods 1 \
    --threshold 1 \
    --comparison-operator GreaterThanThreshold \
    --dimensions Name=FunctionName,Value=$FUNCTION_NAME \
    --alarm-actions $SNS_TOPIC_ARN \
    --region $REGION

# 4. Subscribe Email (thay thế bằng email của bạn)
read -p "Enter your email for alerts: " EMAIL
aws sns subscribe \
    --topic-arn $SNS_TOPIC_ARN \
    --protocol email \
    --notification-endpoint $EMAIL \
    --region $REGION

echo "Monitoring configuration complete!"
echo "Check your email to confirm SNS subscription"
```

Chạy:
```bash
chmod +x configure-monitoring.sh
./configure-monitoring.sh
```

### Best Practices

1. **Log Retention**: Đặt retention phù hợp (14-30 ngày) để cân bằng cost và compliance
2. **Alarm Thresholds**: Đặt dựa trên expected error rates và business requirements
3. **Multiple Channels**: Sử dụng Email, SMS, và Slack cho critical alerts
4. **Regular Review**: Xem lại alarms và điều chỉnh thresholds dựa trên actual metrics
5. **Cost Monitoring**: Thiết lập billing alerts để giám sát costs

{{% notice tip %}}
Luôn test alarms bằng cách trigger thủ công để đảm bảo notifications hoạt động đúng.
{{% /notice %}}
