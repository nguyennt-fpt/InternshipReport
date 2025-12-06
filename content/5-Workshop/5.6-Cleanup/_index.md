---
title: "Clean Up Resources"
date: "2024-01-01"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Clean Up Resources

Clean up all AWS resources created during the workshop to avoid unnecessary costs.

### Quick Cleanup

The easiest way is using Terraform:

```bash
cd Backend-FastAPI-Docker_Build-Pipeline/infra
terraform destroy
```

![Terraform Destroy](/images/5-Workshop/5.6-Clean-Up-Resources/terraform-destroy.png)

This destroys:
- Lambda function
- API Gateway HTTP API
- DynamoDB tables
- IAM roles
- CloudWatch log groups
- SNS topics
- CloudWatch alarms

### Manual Cleanup Steps

If Terraform destroy doesn't work or you need to clean up manually:

#### 1. Delete ECR Repository

```bash
# Delete all images first
aws ecr list-images \
    --repository-name fastapi-lambda \
    --region ap-southeast-1 \
    --query 'imageIds[*]' \
    --output json > image-ids.json

aws ecr batch-delete-image \
    --repository-name fastapi-lambda \
    --image-ids file://image-ids.json \
    --region ap-southeast-1

# Delete repository
aws ecr delete-repository \
    --repository-name fastapi-lambda \
    --force \
    --region ap-southeast-1
```

#### 2. Delete Secrets Manager Secret

```bash
aws secretsmanager delete-secret \
    --secret-id fastapi-jwt-secret \
    --force-delete-without-recovery \
    --region ap-southeast-1
```

#### 3. Delete CloudWatch Log Groups

```bash
aws logs delete-log-group \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --region ap-southeast-1
```

#### 4. Delete SNS Subscriptions

```bash
# List subscriptions
aws sns list-subscriptions-by-topic \
    --topic-arn arn:aws:sns:ap-southeast-1:123456789012:fastapi-lambda-alerts \
    --region ap-southeast-1

# Unsubscribe (replace with actual subscription ARN)
aws sns unsubscribe \
    --subscription-arn arn:aws:sns:ap-southeast-1:123456789012:fastapi-lambda-alerts:subscription-id \
    --region ap-southeast-1
```

### Verify Deletion

#### Check Lambda Functions

```bash
aws lambda list-functions \
    --region ap-southeast-1 \
    --query 'Functions[?contains(FunctionName, `fastapi`)].FunctionName'
```

Should return empty.

#### Check API Gateway

```bash
aws apigatewayv2 get-apis \
    --region ap-southeast-1 \
    --query 'Items[?contains(Name, `fastapi`)].Name'
```

#### Check DynamoDB Tables

```bash
aws dynamodb list-tables \
    --region ap-southeast-1 \
    --query 'TableNames[?contains(@, `products`) || contains(@, `orders`) || contains(@, `users`)]'
```

#### Check ECR Repositories

```bash
aws ecr describe-repositories \
    --region ap-southeast-1 \
    --query 'repositories[?contains(repositoryName, `fastapi`)].repositoryName'
```

### Complete Cleanup Script

Create `cleanup.sh`:

```bash
#!/bin/bash

REGION="ap-southeast-1"

echo "Destroying Terraform infrastructure..."
cd Backend-FastAPI-Docker_Build-Pipeline/infra
terraform destroy -auto-approve

echo "Deleting ECR repository..."
aws ecr delete-repository \
    --repository-name fastapi-lambda \
    --force \
    --region $REGION 2>/dev/null || true

echo "Deleting Secrets Manager secret..."
aws secretsmanager delete-secret \
    --secret-id fastapi-jwt-secret \
    --force-delete-without-recovery \
    --region $REGION 2>/dev/null || true

echo "Deleting CloudWatch log group..."
aws logs delete-log-group \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --region $REGION 2>/dev/null || true

echo "Cleanup completed!"
```

Run:
```bash
chmod +x cleanup.sh
./cleanup.sh
```

### Cost Verification

After cleanup:

1. Navigate to **AWS Cost Explorer**
2. Check recent charges
3. Verify no resources are running

Expected costs after cleanup: **$0** (except free tier usage)

### Common Issues

#### Issue: Terraform destroy fails

**Solution:** Delete resources manually in order, or use `terraform destroy -target` for specific resources.

#### Issue: ECR repository not empty

**Solution:** Delete all images first, then delete repository.

#### Issue: DynamoDB table deletion takes time

**Solution:** Wait for deletion to complete. Check status with `aws dynamodb describe-table`.

### Best Practices

1. **Always use Terraform destroy** when possible
2. **Verify deletion** to ensure no orphaned resources
3. **Set up billing alerts** to monitor unexpected charges
4. **Use tags** to identify workshop resources
5. **Document manual steps** for resources not managed by Terraform

{{% notice warning %}}
Make sure to delete all resources to avoid incurring costs. DynamoDB tables and Lambda functions can accumulate charges if left running.
{{% /notice %}}
