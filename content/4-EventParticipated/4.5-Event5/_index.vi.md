---
title: "Sự kiện 5"
date: "2025-11-29"
weight: 5
chapter: false
pre: " <b> 4.5. </b> "
---

# Báo cáo tóm tắt: AWS Well-Architected Security Pillar

### Mục tiêu sự kiện 
- Quản lý quyền truy cập và phân quyền an toàn (IAM), bao gồm SSO, roles và policies
- Triển khai Detection và Monitoring để phát hiện mối đe dọa sớm và tự động hóa cảnh báo
- Bảo vệ hạ tầng và workloads bằng thiết kế mạng, Security Groups, NACLs, WAF và Shield
- Mã hóa dữ liệu và quản lý secrets (Data Protection)
- Chuẩn hóa quy trình Incident Response và tự động hóa các hành động phản hồi

### Danh sách diễn giả

- **Tran Duc Anh** - Cloud Security Engineer Trainee
- **Nguyen Tuan Thinh** - Cloud Engineer Trainee
- **Nguyen Do Thanh Dat** - Cloud Engineer Trainee
- **Thinh Lam** - FCJer
- **Viet Nguyen** - FCJer
- **Mendel Grabski** - Head of Security and DevOps

### Nội dung nổi bật

#### Identity and Access Management (IAM) 

- IAM: Users, Roles, Policies – loại bỏ long-term credentials  
- IAM Identity Center: SSO, permission sets  
- Best practices đa tài khoản:
  - Service Control Policies (SCP) cho organization guardrails  
  - Permission boundaries để hạn chế privilege escalation  
- Cải tiến bảo mật:
  - MFA bắt buộc  
  - Credential rotation  

#### Detection và Continuous Monitoring  

- AWS CloudTrail ở cấp organization  
- Amazon GuardDuty cho phát hiện mối đe dọa nâng cao  
- Logging:
  - VPC Flow Logs  
  - ALB Logs  
  - S3 Access Logs  
- Alerting & Automation:
  - EventBridge + SNS  
  - Lambda remediation  
- Áp dụng Detection-as-Code sử dụng IaC  

#### Infrastructure Protection  

- Thiết kế mạng:
  - Public/Private subnets  
  - VPC segmentation  
- Security Groups (stateful) vs NACLs (stateless)  
- Bảo vệ mạng:
  - AWS WAF  
  - AWS Shield  
  - AWS Network Firewall  

#### Data Protection  

- AWS KMS 
- Mã hóa at-rest & in-transit 
- Quản lý secrets:
  - Secrets Manager  
  - Parameter Store  
- Phân loại dữ liệu và access guardrails  

## Incident Response  

- Chu trình AWS Incident Response lifecycle  
- Các tình huống phổ biến:
  - IAM key bị xâm phạm  
  - S3 bị public exposure  
  - Phát hiện malware trên EC2  
- Quy trình phản hồi:
  - Snapshot resources  
  - Cô lập workloads  
  - Thu thập bằng chứng  
- Tự động hóa:
  - Lambda  
  - Step Functions  
  - Auto-remediation  

### Những điều học được

#### IAM là nền tảng  
- Loại bỏ long-term credentials  
- Multi-account + SCP tăng khả năng kiểm soát  

#### Detection là thiết yếu  
- CloudTrail + GuardDuty cho phép phát hiện sớm  
- Logging đầy đủ hỗ trợ phân tích pháp y  

#### Hạ tầng cần defense in depth  
- Kết hợp SGs, NACLs, WAF, Shield, Network Firewall  

#### Dữ liệu phải được bảo vệ và quản lý  
- Sử dụng KMS cho mã hóa  
- Secrets Manager thay thế hard-coded secrets  

#### Incident Response cần quy trình rõ ràng  
- Chuẩn bị playbooks trước  

### Áp dụng vào công việc

- Kích hoạt GuardDuty và Security Hub trên tất cả tài khoản  
- Cấu hình EventBridge + Lambda cho auto-remediation  
- Bật mã hóa mặc định cho S3/EBS/RDS  

### Trải nghiệm sự kiện

Tham dự sự kiện "AWS Well-Architected Security Pillar" mang lại những hiểu biết thực tiễn và toàn diện về các thực hành bảo mật cloud trên AWS.

### Điểm nổi bật
- Hiểu sâu về Zero Trust & IAM  
- Hướng dẫn logging & alerting cấp doanh nghiệp  
- Demo thực hành incident response  

### Bài học rút ra
- Bảo mật cloud = quy trình + con người + công cụ  
- IAM và Detection là ưu tiên hàng đầu  
- Tự động hóa IR là chìa khóa giảm MTTR  
- Defense in depth là bắt buộc  

> Sự kiện rất bổ ích, cung cấp kiến thức có thể áp dụng ngay cho cloud engineers, chuyên gia bảo mật và system administrators để cải thiện hoạt động bảo mật AWS