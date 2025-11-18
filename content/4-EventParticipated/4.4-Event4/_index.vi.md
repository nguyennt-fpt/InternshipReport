---
title: "Event 4"
date: "2025-11-17"
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---

# Bài thu hoạch: DevOps on AWS

### Mục đích của sự kiện
- Trình bày hệ thống CI/CD trên AWS với CodeCommit, CodeBuild, CodeDeploy và CodePipeline  
- Giới thiệu Infrastructure as Code với CloudFormation và AWS CDK  
- Làm rõ cách triển khai container bằng Docker, ECR, ECS, EKS và App Runner  
- Hướng dẫn thiết lập hệ thống Monitoring và Observability với CloudWatch và AWS X-Ray  
- Chia sẻ DevOps best practices, case studies và lộ trình nghề nghiệp  

### Danh sách diễn giả

- **Bao Huynh** - AWS Community Builders 
- **Thinh Nguyen** - AWS Community Builders 
- **Vi Tran** - AWS Community Builders 

### Nội dung nổi bật

#### AWS CI/CD Pipeline

- Source Control: CodeCommit
- Build và Test: CodeBuild pipelines, unit test, integration test  
- Deployment: CodeDeploy hỗ trợ Blue/Green, Canary, Rolling updates  
- Automation: CodePipeline orchestration tự động hóa toàn bộ luồng CI/CD 

#### Infrastructure as Code (IaC)

- AWS CloudFormation: template, stack lifecycle, drift detection  
- AWS CDK: constructs, reusable patterns, hỗ trợ đa ngôn ngữ  
- Demo: deploy resource bằng CloudFormation + CDK  
- Thảo luận lựa chọn IaC phù hợp cho từng dự án  

#### Container Services on AWS

- Docker fundamentals: containerization và microservices  
- Amazon ECR: image scanning, lifecycle policies  
- Amazon ECS và EKS: orchestration, scaling, service discovery  
- AWS App Runner: triển khai container đơn giản không cần quản lý hạ tầng  

#### Monitoring và Observability

- CloudWatch: metrics, logs, alarms, dashboards  
- AWS X-Ray: distributed tracing, phân tích hiệu năng end-to-end  

### Những gì học được

#### DevOps giúp rút ngắn thời gian triển khai

- CI/CD tự động giảm lỗi và tăng tốc độ release  
- Blue/Green & Canary giúp giảm rủi ro release  

#### IaC là nền tảng của DevOps hiện đại

- Hạ tầng phải được quản lý bằng code để dễ kiểm soát, audit  
- CDK tăng tốc nhờ khả năng tái sử dụng  

#### Containerization thúc đẩy microservices

- ECS/EKS phù hợp cho hệ thống lớn  
- App Runner lý tưởng cho team cần tốc độ triển khai  

#### Observability là bắt buộc

- CloudWatch + X-Ray cho phép theo dõi toàn bộ hệ thống  
- Alert tốt giúp giảm MTTR rõ rệt  

### Applying to Work

- Áp dụng CodePipeline cho build–test–deploy tự động  
- Sử dụng Blue/Green deployment giảm downtime  
- Dùng CloudFormation để đảm bảo hạ tầng nhất quán  
- Tạo CloudWatch dashboards để monitor performance  
- Triển khai microservices bằng ECS kết hợp CodePineline cho tự động hóa

### Trải nghiệm trong event

Tham dự sự kiện “DevOps on AWS” mang đến nhiều góc nhìn thực tiễn về DevOps trong môi trường cloud-native.

#### Học được những nội dung chuyên sâu

- Demo CI/CD và IaC thực tiễn  
- Hiểu sự khác biệt giữa ECS, EKS và App Runner  

#### Lessons learned

- DevOps = văn hóa + quy trình + công cụ  
- IaC và CI/CD là nền tảng  
- Observability phải được xây từ đầu  
- Containers là tương lai của deployment  

> Một sự kiện giàu kiến thức, thực chiến và định hướng rõ ràng cho lộ trình DevOps chuyên nghiệp.
