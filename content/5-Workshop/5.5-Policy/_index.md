---
title: "Manual Configuration: Monitoring & Security"
date: "2024-01-01"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Manual Configuration: Monitoring & Security

This section provides **step-by-step manual configuration** of CloudWatch monitoring, alarms, SNS notifications, and security settings for the FastAPI Backend.

### Step 1: Manual CloudWatch Log Group Configuration

#### Create Log Group

```bash
# Create CloudWatch log group for Lambda
aws logs create-log-group \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --region ap-southeast-1

# Set retention period (14 days)
aws logs put-retention-policy \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --retention-in-days 14 \
    --region ap-southeast-1
```

#### View Logs

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
# Query logs using CloudWatch Insights
aws logs start-query \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --start-time $(date -d '1 hour ago' +%s) \
    --end-time $(date +%s) \
    --query-string 'fields @timestamp, @message, correlation_id | filter @message like /error/ | sort @timestamp desc | limit 20' \
    --region ap-southeast-1

# Get query results (replace QUERY_ID)
aws logs get-query-results \
    --query-id QUERY_ID \
    --region ap-southeast-1
```

![CloudWatch Logs](/images/5-Workshop/5.5-Manual-Configuration-Monitoring&Security/cloudwatch-logs.png)

### Step 2: Manual CloudWatch Metrics Configuration

#### View Lambda Metrics

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

#### Create Custom Metric

```python
# In your FastAPI application
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

### Step 3: Manual CloudWatch Alarm Configuration

#### Create SNS Topic First

```bash
# Create SNS topic for alerts
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

#### Create CloudWatch Alarm for Lambda Errors

```bash
# Create alarm for Lambda errors
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

#### Create Alarm for High Duration

```bash
# Create alarm for Lambda duration
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

#### Create Alarm for Throttles

```bash
# Create alarm for Lambda throttles
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

#### View Alarms

```bash
# List all alarms
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

### Step 4: Manual SNS Subscription Configuration

#### Subscribe Email

```bash
# Subscribe email to SNS topic
aws sns subscribe \
    --topic-arn $SNS_TOPIC_ARN \
    --protocol email \
    --notification-endpoint your-email@example.com \
    --region ap-southeast-1

# Check your email and confirm subscription
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
# List all subscriptions
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

### Step 5: Manual Security Configuration

#### Enable ECR Image Scanning

```bash
# Enable scanning on existing repository
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
# Create KMS key for Secrets Manager
KMS_KEY_ID=$(aws kms create-key \
    --description "FastAPI JWT Secret Key" \
    --region ap-southeast-1 \
    --query 'KeyMetadata.KeyId' \
    --output text)

# Update secret to use KMS encryption
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

# Create CloudWatch log group for API Gateway
aws logs create-log-group \
    --log-group-name /aws/apigateway/fastapi-lambda \
    --region ap-southeast-1

# Enable access logging (requires IAM role with logs:CreateLogStream permission)
aws apigatewayv2 update-stage \
    --api-id $API_ID \
    --stage-name '$default' \
    --access-log-settings DestinationArn=arn:aws:logs:ap-southeast-1:ACCOUNT_ID:log-group:/aws/apigateway/fastapi-lambda,Format='{"requestId":"$context.requestId","ip":"$context.identity.sourceIp","requestTime":"$context.requestTime","httpMethod":"$context.httpMethod","routeKey":"$context.routeKey","status":"$context.status","protocol":"$context.protocol","responseLength":"$context.responseLength"}' \
    --region ap-southeast-1
```

### Step 6: Create Monitoring Dashboard

#### Create CloudWatch Dashboard

```bash
# Create dashboard JSON
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

# Create dashboard
aws cloudwatch put-dashboard \
    --dashboard-name FastAPI-Monitoring \
    --dashboard-body file://dashboard.json \
    --region ap-southeast-1
```

![Monitoring Dashboard](/images/5-Workshop/5.5-Manual-Configuration-Monitoring&Security/monitoring-dashboard.png)

### Step 7: Verify Configuration

#### Check All Resources

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

### Complete Configuration Script

Create `configure-monitoring.sh`:

```bash
#!/bin/bash
set -e

REGION="ap-southeast-1"
FUNCTION_NAME="fastapi-lambda-fn"
PROJECT_NAME="fastapi-lambda"

echo "Configuring Monitoring & Security..."

# 1. Create CloudWatch Log Group
echo "Creating CloudWatch log group..."
aws logs create-log-group \
    --log-group-name /aws/lambda/$FUNCTION_NAME \
    --region $REGION 2>/dev/null || echo "Log group already exists"

aws logs put-retention-policy \
    --log-group-name /aws/lambda/$FUNCTION_NAME \
    --retention-in-days 14 \
    --region $REGION

# 2. Create SNS Topic
echo "Creating SNS topic..."
SNS_TOPIC_ARN=$(aws sns create-topic \
    --name ${PROJECT_NAME}-alerts \
    --region $REGION \
    --query 'TopicArn' \
    --output text)
echo "SNS Topic ARN: $SNS_TOPIC_ARN"

# 3. Create CloudWatch Alarms
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

# 4. Subscribe Email (replace with your email)
read -p "Enter your email for alerts: " EMAIL
aws sns subscribe \
    --topic-arn $SNS_TOPIC_ARN \
    --protocol email \
    --notification-endpoint $EMAIL \
    --region $REGION

echo "Monitoring configuration complete!"
echo "Check your email to confirm SNS subscription"
```

Run:
```bash
chmod +x configure-monitoring.sh
./configure-monitoring.sh
```

### Best Practices

1. **Log Retention**: Set appropriate retention (14-30 days) to balance cost and compliance
2. **Alarm Thresholds**: Set based on expected error rates and business requirements
3. **Multiple Channels**: Use Email, SMS, and Slack for critical alerts
4. **Regular Review**: Review alarms and adjust thresholds based on actual metrics
5. **Cost Monitoring**: Set up billing alerts to monitor costs

{{% notice tip %}}
Always test alarms by triggering them manually to ensure notifications work correctly.
{{% /notice %}}
