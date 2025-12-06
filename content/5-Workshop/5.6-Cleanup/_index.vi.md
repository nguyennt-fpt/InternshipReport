---
title: "Dọn dẹp tài nguyên"
date: "2024-01-01"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## Dọn dẹp tài nguyên

Trong phần này, bạn sẽ dọn dẹp tất cả tài nguyên AWS được tạo trong workshop để tránh phát sinh chi phí không cần thiết.

### Thứ tự dọn dẹp

Quan trọng là xóa tài nguyên theo đúng thứ tự do dependencies:

1. **CodePipeline và CodeBuild** (nếu tạo riêng)
2. **API Gateway**
3. **Lambda Function**
4. **DynamoDB Tables**
5. **CloudWatch Log Groups**
6. **SNS Topics**
7. **IAM Roles**
8. **ECR Repository** (images)
9. **Secrets Manager Secret** (tùy chọn)

### Bước 1: Destroy Infrastructure với Terraform

Cách dễ nhất để dọn dẹp là sử dụng Terraform:

```bash
cd infra
terraform destroy
```

Điều này sẽ destroy:
- Lambda function
- API Gateway HTTP API
- DynamoDB tables
- IAM roles
- CloudWatch log groups
- SNS topics
- CloudWatch alarms

**Lưu ý:** Bạn có thể cần xác nhận thủ công việc xóa một số tài nguyên.

![Terraform Destroy](/images/5-Workshop/5.6-Clean-Up-Resources/terraform-destroy.png)

### Bước 2: Destroy CI/CD Pipeline

```bash
cd pipeline
terraform destroy
```

Điều này sẽ destroy:
- CodePipeline
- CodeBuild projects
- ECR repository
- S3 bucket cho artifacts
- IAM roles cho pipeline

### Bước 3: Dọn dẹp thủ công (nếu cần)

#### Xóa ECR Images

Nếu Terraform không xóa ECR images:

```bash
# Liệt kê images
aws ecr list-images \
    --repository-name fastapi-lambda \
    --region us-east-1

# Xóa images
aws ecr batch-delete-image \
    --repository-name fastapi-lambda \
    --image-ids imageTag=latest \
    --region us-east-1

# Xóa repository
aws ecr delete-repository \
    --repository-name fastapi-lambda \
    --force \
    --region us-east-1
```

#### Xóa Secrets Manager Secret

```bash
aws secretsmanager delete-secret \
    --secret-id fastapi-jwt-secret \
    --force-delete-without-recovery \
    --region us-east-1
```

#### Xóa S3 Artifacts Bucket

```bash
# Empty bucket trước
aws s3 rm s3://fastapi-pipeline-artifacts-YOUR-OWNER-YOUR-REPO --recursive

# Xóa bucket
aws s3 rb s3://fastapi-pipeline-artifacts-YOUR-OWNER-YOUR-REPO
```

#### Xóa CloudWatch Log Groups (nếu không bị Terraform xóa)

```bash
aws logs delete-log-group \
    --log-group-name /aws/lambda/fastapi-lambda-fn \
    --region us-east-1
```

#### Xóa SNS Subscriptions

```bash
# Liệt kê subscriptions
aws sns list-subscriptions-by-topic \
    --topic-arn arn:aws:sns:us-east-1:123456789012:fastapi-lambda-alerts

# Unsubscribe
aws sns unsubscribe \
    --subscription-arn arn:aws:sns:us-east-1:123456789012:fastapi-lambda-alerts:subscription-id
```

### Bước 4: Xác minh xóa

#### Kiểm tra Lambda Functions

```bash
aws lambda list-functions \
    --region us-east-1 \
    --query 'Functions[?contains(FunctionName, `fastapi`)].FunctionName'
```

Nên trả về empty hoặc không có functions khớp.

#### Kiểm tra API Gateway APIs

```bash
aws apigatewayv2 get-apis \
    --region us-east-1 \
    --query 'Items[?contains(Name, `fastapi`)].Name'
```

#### Kiểm tra DynamoDB Tables

```bash
aws dynamodb list-tables \
    --region us-east-1 \
    --query 'TableNames[?contains(@, `products`) || contains(@, `orders`) || contains(@, `users`)]'
```

#### Kiểm tra ECR Repositories

```bash
aws ecr describe-repositories \
    --region us-east-1 \
    --query 'repositories[?contains(repositoryName, `fastapi`)].repositoryName'
```

### Bước 5: Dọn dẹp Local Files (Tùy chọn)

```bash
# Xóa Terraform state files
rm -rf infra/.terraform infra/terraform.tfstate*

# Xóa pipeline state files
rm -rf pipeline/.terraform pipeline/terraform.tfstate*

# Xóa Docker images
docker rmi fastapi-lambda:latest
docker rmi $(docker images | grep fastapi | awk '{print $3}')
```

### Các vấn đề thường gặp và giải pháp

#### Vấn đề: Terraform destroy thất bại do dependencies

**Giải pháp:** Xóa tài nguyên thủ công theo đúng thứ tự, hoặc sử dụng `terraform destroy -target` để xóa các tài nguyên cụ thể trước.

#### Vấn đề: ECR repository không rỗng

**Giải pháp:** Xóa tất cả images trước, sau đó xóa repository.

#### Vấn đề: S3 bucket không rỗng

**Giải pháp:** Empty bucket trước, sau đó xóa nó.

#### Vấn đề: DynamoDB table deletion mất thời gian

**Giải pháp:** Đợi table deletion hoàn tất. Sử dụng `aws dynamodb describe-table` để kiểm tra status.

### Xác minh chi phí

Sau khi dọn dẹp, xác minh không có chi phí bất ngờ:

1. Điều hướng đến **AWS Cost Explorer**
2. Kiểm tra recent charges
3. Xác minh không có tài nguyên nào đang chạy

Chi phí mong đợi sau khi dọn dẹp: **$0** (trừ bất kỳ free tier usage nào)

### Best Practices

1. **Luôn sử dụng Terraform destroy** khi có thể để xóa sạch
2. **Xác minh xóa** để đảm bảo không có orphaned resources
3. **Thiết lập billing alerts** để giám sát chi phí bất ngờ
4. **Sử dụng tags** để xác định tài nguyên workshop để dễ dọn dẹp
5. **Document manual steps** cho tài nguyên không được quản lý bởi Terraform

{{% notice warning %}}
Đảm bảo xóa tất cả tài nguyên để tránh phát sinh chi phí. Một số tài nguyên như DynamoDB tables và Lambda functions có thể tích lũy chi phí nếu để chạy.
{{% /notice %}}
