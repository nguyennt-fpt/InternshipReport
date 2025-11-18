---
title: "Worklog tuần 10"
date: "2025-11-10"
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---



### Mục tiêu Tuần 10:

* Luyện tập quy trình CI/CD bằng CodeBuild, CodePipeline, ECR và ECS
* Thực hành xây dựng và triển khai API bằng API Gateway và Lambda

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Sử dụng CodeBuild để tự động push image lên ECR <br>&emsp; + Tạo CodeBuild project và kết nối với GitHub <br>&emsp; + Tạo IAM roles cần thiết cho CodeBuild <br>&emsp; + Viết file buildspec.yml để build và push Docker image lên Amazon ECR <br>&emsp; + Chạy test build để kiểm tra image được push thành công                                                                                                 | 11/10/2025 | 11/10/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 3   | - Xây dựng CI/CD Pipeline sử dụng AWS CodePipeline <br> - **Luyện tập:** <br>&emsp; + Tạo CodePipeline pipeline lắng nghe GitHub pushes <br>&emsp; + Tạo IAM roles cần thiết cho CodePipeline và phần deploy <br>&emsp; + Tự động deploy container updates lên ECS <br>&emsp; + Push code lên GitHub và kiểm tra ECS service được update thành công                                             | 11/11/2025 | 11/11/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 4   | - Học về Amazon API Gateway <br>&emsp; + Tạo API resources <br>&emsp; + Tạo và tích hợp Lambda functions với API Gateway <br>&emsp; + Tạo methods và test invoke Lambda function | 11/12/2025 | 11/12/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 5   | - Thực hành Input Validation trong API Gateway <br> - **Luyện tập:** <br>&emsp; + Tạo API Gateway models để validate request headers và body <br>&emsp; + Thêm mapping templates cho method request <br>&emsp; + Test logic validation bằng API Gateway console                      | 11/13/2025 | 11/13/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 6   | - Sử dụng stage variables cho triển khai multi-environment <br> - **Luyện tập:** <br>&emsp; + Tạo Lambda function và versions <br>&emsp; + Tạo aliases và map chúng vào các stage khác nhau <br>&emsp; + Thêm stage variables và reference chúng trong Lambda integration <br>&emsp; + Test và verify triển khai thành công                                                                                   | 11/14/2025 | 11/14/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |


### Kết quả đạt được tuần 10:

* Tạo được một quy trình CI/CD bằng CodeBuild, CodePipeline, ECR và ECS
    * Tạo CodeBuild project
    * Viết và test hoàn chỉnh buildspec.yml để build và push image lên Amazon ECR
    * Tạo CodePipeline pipeline kích hoạt từ GitHub pushes
    * Tích hợp các bước source, build, deploy để tự động cập nhật container trên ECS
    * Test toàn bộ pipeline và xác nhận ECS service được update sau mỗi lần push

* Làm quen với kiến trúc API Gateway
    * Tạo resources, methods và tích hợp với Lambda
    * Xây dựng backend API đơn giản với API Gateway và Lambda
    * Test method responses và Lambda invocation

* Triển khai request validation trong API Gateway
    * Tạo models để validate headers và request body
    * Thêm và cấu hình mapping templates cho method request
    * Xác thực logic validation bằng API Gateway console

* Thực hành triển khai multi-environment bằng stage variables
    * Tạo Lambda versions và aliases cho từng environment
    * Cấu hình stage variables để chọn Lambda version tương ứng
    * Test hành vi multi-stage cho dev, staging và prod