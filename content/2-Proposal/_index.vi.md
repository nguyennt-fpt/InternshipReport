---
title: "Bản đề xuất"
date: "2024-01-01"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Dự án Serverless Bảo Mật Cho Ứng Dụng Phục Vụ Toàn Cầu

## 1. Tóm tắt dự án
Dự án vận hành một ứng dụng serverless trên AWS với quy trình CI/CD tự động hoàn toàn. Khi mã nguồn được đẩy lên GitLab, pipeline tự động quét bảo mật (Semgrep, Trivy), build container, triển khai hạ tầng bằng Terraform và đẩy image lên Amazon ECR. Người dùng truy cập qua Route 53 → CloudFront → WAF → API Gateway, được xác thực bởi Cognito và gọi Lambda để xử lý nghiệp vụ, lưu trữ dữ liệu trong DynamoDB. Hệ thống giám sát liên tục qua CloudWatch, CloudTrail, GuardDuty và gửi cảnh báo qua EventBridge → SNS khi phát hiện sự cố hoặc mối đe dọa. 

## 2. Tuyên bố vấn đề

### Vấn đề hiện tại
Nhiều tổ chức gặp khó khăn khi triển khai ứng dụng serverless do quy trình thủ công dễ sai sót, thiếu kiểm soát bảo mật và khó mở rộng. Các vấn đề cụ thể bao gồm:

- Quy trình triển khai thủ công thiếu kiểm soát bảo mật, dễ bỏ sót bước quét lỗ hổng và kiểm thử tự động
- Hệ thống hiện tại thiếu cơ chế phân phối nội dung hiệu năng cao (CDN) và bảo vệ lớp ứng dụng trước tấn công DDoS, bot
- Khó khăn trong việc quản lý bí mật, mã hóa dữ liệu và đảm bảo nguyên tắc ít quyền nhất cho toàn bộ pipeline CI/CD
- Thiếu hệ thống giám sát, cảnh báo tập trung, dẫn đến phản ứng chậm với sự cố và mối đe dọa bảo mật

### Giải pháp
Dự án cung cấp một kiến trúc serverless hoàn chỉnh với CI/CD tự động, bảo mật tích hợp và giám sát toàn diện. Quy trình triển khai tự động từ GitLab qua CodePipeline, CodeBuild, Semgrep (SAST), Trivy (container scan), Terraform (IaC) và CodeDeploy, đảm bảo mỗi thay đổi đều được kiểm tra và quét bảo mật. Lớp phân phối sử dụng Route 53, CloudFront và WAF để tăng tốc và bảo vệ ứng dụng. Backend serverless với Cognito (xác thực), API Gateway, Lambda, DynamoDB, Secrets Manager và KMS đảm bảo bảo mật dữ liệu. Hệ thống giám sát đa lớp (CloudWatch, CloudTrail, GuardDuty, EventBridge, SNS) phát hiện và cảnh báo sự cố tức thời.

### Lợi ích và hoàn vốn đầu tư (ROI)
Giải pháp mang lại lợi ích kỹ thuật và tài chính đáng kể: tự động hóa hoàn toàn giảm 80-90% thời gian triển khai và loại bỏ lỗi thủ công, bảo mật tích hợp giảm rủi ro lỗ hổng, CDN và serverless đảm bảo hiệu năng cao với khả năng mở rộng tự động, giám sát tập trung phát hiện sự cố nhanh hơn 70-80%. Về tài chính, chi phí vận hành ước tính ~$20-40/tháng cho quy mô nhỏ đến trung bình, thấp hơn đáng kể so với hạ tầng truyền thống, không cần đầu tư phần cứng ban đầu và giảm 60-70% chi phí nhân lực vận hành. Thời gian hoàn vốn dự kiến 1-2 tháng, đồng thời cung cấp nền tảng mở rộng cho các dự án tương lai.

## 3. Kiến trúc hệ thống
![System Architecture](/images/2-Proposal/platform_architecture.jpeg)

Kiến trúc gồm ba phần chính:

1. **Pipeline CI/CD**: Chạy trên GitLab và AWS CodePipeline để build container, quét bảo mật (Semgrep, Trivy), triển khai hạ tầng bằng Terraform và đẩy image lên Amazon ECR
2. **Lớp phân phối nội dung**: Sử dụng Route 53, AWS WAF và CloudFront nhằm bảo vệ và tăng tốc truy cập người dùng
3. **Lõi ứng dụng serverless**: Đặt tại khu vực ap-southeast-1 với Cognito (xác thực), API Gateway, Lambda (xử lý nghiệp vụ), DynamoDB (lưu trữ), KMS và Secrets Manager (bảo mật), cùng bộ công cụ giám sát (CloudWatch, CloudTrail, GuardDuty, EventBridge, SNS)

**AWS Services sử dụng**
- GitLab Actions, AWS CodePipeline, CodeBuild, CodeDeploy
- Semgrep, Trivy
- Terraform, Amazon ECR
- Amazon Cognito, API Gateway, AWS Lambda, DynamoDB
- AWS WAF, Amazon CloudFront, Amazon Route 53
- AWS Secrets Manager, AWS Key Management Service (KMS)
- Amazon CloudWatch, AWS CloudTrail, Amazon GuardDuty, Amazon EventBridge, Amazon SNS

**Thiết kế thành phần**
- *CI/CD*: Khi code đẩy lên GitLab, pipeline kích hoạt Semgrep (SAST), CodeBuild build container, Trivy quét, Terraform Plan/Apply, CodeDeploy cập nhật hạ tầng.
- *Phân phối & bảo vệ*: Route 53 quản lý DNS → CloudFront cache nội dung → WAF lọc lưu lượng độc hại trước khi tới API.
- *Dịch vụ ứng dụng*: Cognito cấp token cho người dùng; API Gateway xác thực và chuyển request đến Lambda; Lambda xử lý nghiệp vụ, đọc/ghi DynamoDB.
- *Bí mật & mã hóa*: Secrets Manager lưu thông tin nhạy cảm, KMS mã hóa khóa và dữ liệu.
- *Giám sát & cảnh báo*: CloudWatch thu thập metric/log, CloudTrail ghi audit trail, GuardDuty phát hiện đe dọa, EventBridge kích hoạt SNS gửi cảnh báo.

## 4. Triển khai kỹ thuật
1. Thiết kế IaC với Terraform cho VPC logic, IAM role ít quyền nhất, API Gateway, Lambda, DynamoDB, bảo mật cổng.
2. Thiết lập GitLab CI/CD pipeline, tích hợp AWS CodePipeline/CodeBuild/CodeDeploy qua role liên tài khoản.
3. Cấu hình Semgrep và Trivy chạy tự động; pipeline fail nếu phát hiện vấn đề nghiêm trọng.
4. Định nghĩa Terraform module để quản lý Lambda (mã nguồn lưu trong ECR), API Gateway, CloudFront, WAF, Cognito.
5. Bật CloudWatch log group, metric filter, GuardDuty, CloudTrail; cấu hình EventBridge rule gửi SNS khi có sự kiện bất thường.
6. Thiết kế quy trình rollback tự động dựa trên CodeDeploy và versioning của Terraform state.

## 5. Lộ trình & Mốc triển khai
| Giai đoạn | Thời gian | Nội dung chính |
| --- | --- | --- |
| Khởi động | Tuần 1 | Thu thập yêu cầu, hoàn thiện thiết kế chi tiết, xác định role IAM |
| Thiết lập IaC & CI/CD | Tuần 2 | Viết Terraform, tạo GitLab pipeline, kết nối CodePipeline |
| Tích hợp bảo mật | Tuần 3 | Cấu hình Semgrep, Trivy, WAF rule, Cognito, Secrets Manager |
| Hoàn thiện backend | Tuần 4 | Viết Lambda, API Gateway, DynamoDB, test đơn vị |
| Triển khai & kiểm thử | Tuần 5 | Chạy pipeline, kiểm thử tích hợp, hiệu năng CDN |
| Vận hành & bàn giao | Tuần 6 | Tối ưu hệ thống giám sát, thiết lập cảnh báo thông minh, hoàn thiện tài liệu |
| Sau triển khai | Tuần 7+ | Giám sát hiệu năng và chi phí liên tục, tối ưu hóa cấu hình, mở rộng tính năng theo nhu cầu, cập nhật bảo mật định kỳ |

## 6. Ước tính ngân sách
| Dịch vụ AWS | Yếu tố Tính phí Chính | Chi phí Ước tính (USD) |
| --- | --- | --- |
| AWS Lambda & API Gateway | 250.000 yêu cầu/tháng | ~$1.00 - $3.00 |
| Amazon DynamoDB | 5 GB lưu trữ, 5 RCU/5 WCU (Provisioned) | ~$5.00 - $10.00 |
| CI/CD (CodePipeline/CodeBuild) | 5 lần triển khai (250 phút build) | ~$2.25 - $4.00 |
| Amazon ECR | 5 GB lưu trữ Image (Giá: $0.10/GB) | ~$0.50 - $1.50 |
| Route 53 | 1 Hosted Zone + truy vấn | ~$0.54 |
| CloudFront / WAF | 15 GB Data Transfer Out, WAF cơ bản | ~$9.50 - $15.50 |
| Security & Monitoring | GuardDuty, KMS, Secrets Manager, Logs tối thiểu | ~$2.00 - $5.00 |
| **Tổng Chi Phí** | | **~$20.79 - $39.54 USD/tháng** |

*Lưu ý: Chi phí thực tế phụ thuộc lưu lượng và cấu hình từng môi trường; có thể giảm thêm khi tắt môi trường dev/test. Không phát sinh chi phí phần cứng.*

## 7. Đánh giá rủi ro
| Rủi ro | Mức độ | Xác suất | Biện pháp giảm thiểu |
| --- | --- | --- | --- |
| Lỗ hổng bảo mật không phát hiện | Cao | Trung bình | Bắt buộc Semgrep/Trivy ở mỗi pipeline, bật AWS Inspector, review định kỳ |
| Sai lệch cấu hình Terraform gây downtime | Cao | Thấp | Sử dụng Terraform plan review, môi trường staging, backup state |
| Chi phí CloudFront/Terraform tăng đột biến | Trung bình | Trung bình | Thiết lập AWS Budgets, bật cảnh báo, dùng CloudFront Cache policy hiệu quả |
| Lỗi xác thực Cognito | Trung bình | Thấp | Thiết lập multi-region backup user pool, tự động test API auth |

### Chiến lược giảm thiểu
- **Bảo mật**: Tích hợp quét bảo mật tự động (Semgrep, Trivy) vào mọi pipeline, kích hoạt AWS Inspector định kỳ, thực hiện code review và security audit hàng quý
- **Cấu hình hạ tầng**: Áp dụng Infrastructure as Code (Terraform) với version control, peer review cho mọi thay đổi, sử dụng môi trường staging để kiểm thử trước khi production, backup Terraform state thường xuyên
- **Quản lý chi phí**: Thiết lập AWS Budgets với ngưỡng cảnh báo 80% và 100%, cấu hình CloudFront cache policies tối ưu, giám sát chi phí hàng ngày qua Cost Explorer, tự động tắt tài nguyên dev/test khi không sử dụng
- **Xác thực và truy cập**: Triển khai automated testing cho API authentication, duy trì backup Cognito user pool ở region khác, thiết lập multi-factor authentication (MFA) cho tài khoản quản trị

### Kế hoạch dự phòng
- **Sự cố bảo mật**: Nếu phát hiện lỗ hổng nghiêm trọng, ngay lập tức rollback về version trước đó qua CodeDeploy, cách ly tài nguyên bị ảnh hưởng, thông báo cho đội bảo mật và tiến hành patch
- **Downtime do cấu hình sai**: Sử dụng Terraform state backup để khôi phục cấu hình trước đó, chuyển traffic sang môi trường staging tạm thời nếu cần, có sẵn runbook để rollback nhanh
- **Vượt quá ngân sách**: Tự động tắt các dịch vụ không cần thiết khi đạt ngưỡng cảnh báo, tối ưu CloudFront cache hit ratio, xem xét chuyển sang Reserved Capacity cho DynamoDB nếu sử dụng lâu dài
- **Lỗi xác thực**: Chuyển sang backup Cognito user pool ở region khác, sử dụng API Gateway caching để giảm tải, có sẵn fallback authentication mechanism

## 8. Kết quả kỳ vọng
- Quy trình CI/CD tự động và an toàn, đảm bảo mỗi thay đổi đều được kiểm thử và quét bảo mật trước khi triển khai.
- Hệ thống backend serverless phục vụ người dùng qua CDN và API Gateway với độ trễ thấp, luôn mã hóa dữ liệu.
- Bộ giám sát tập trung phát hiện sự cố, gửi cảnh báo tức thì, hỗ trợ đáp ứng SLA cao.
- Quyền truy cập và chi phí được tối ưu nhờ áp dụng nguyên tắc ít quyền nhất, kiến trúc serverless và cơ chế autoscaling.
- Cơ sở hạ tầng dễ mở rộng cho các tính năng tương lai, sẵn sàng nhân rộng sang các khu vực AWS khác.



