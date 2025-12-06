---
title: "Worklog tuần 8"
date: "2025-10-27"
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---



### Mục tiêu Tuần 8:

* Củng cố kiến trúc AWS và các dịch vụ cốt lõi
* Học về Docker
* Xây dựng CI/CD pipelines sử dụng Docker và GitHub Actions

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Ôn tập kiến trúc AWS <br>&emsp; +  Ôn tập các khái niệm kiến trúc AWS nền tảng <br>&emsp; + Hiểu best practices về kiến trúc bảo mật AWS <br>&emsp; + Tìm hiểu thiết kế kiến trúc High Availability và High Performance <br>&emsp; + Tối ưu chi phí sử dụng dịch vụ <br> - Ôn tập các dịch vụ cốt lõi của AWS: EC2, VPC, IAM, S3, RDS, CloudWatch, …                                                                                                 | 10/27/2025 | 10/28/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Học về Docker <br> - **Thực hành:** <br>&emsp; + Sử dụng các lệnh cơ bản của Docker <br>&emsp; + Chạy một ứng dụng web đơn giản <br>&emsp; + Viết Dockerfile để build custom image                                              | 10/28/2025 | 10/28/2025      | https://www.youtube.com/watch?v=Y3zqsFpUzMk&list=PLncHg6Kn2JT4kLKJ_7uy0x4AdNrCHbe0n |
| 4   | - Học về CI/CD với Docker và GitHub Actions <br> - **Thực hành:** <br>&emsp; + Viết GitHub Actions workflow để build và test code khi push <br>&emsp; + Viết và tối ưu Dockerfile cho CI/CD pipeline <br> - Cấu hình GitHub Actions để tự động: <br>&emsp; + Build Docker image <br>&emsp; + Deploy ứng dụng khi có code mới được push | 10/29/2025 | 10/29/2025      | https://www.youtube.com/watch?v=Y3zqsFpUzMk&list=PLncHg6Kn2JT4kLKJ_7uy0x4AdNrCHbe0n |
| 5   | - Làm việc với Amazon ECR và triển khai ứng dụng <br> - **Thực hành:** <br>&emsp; + Tạo EC2 instance để chạy Docker containers <br>&emsp; + Tạo và cấu hình RDS instance để sử dụng database <br>&emsp; + Build Docker image và chạy container local trước khi deploy <br>&emsp; + Tạo ECR repository và push Docker images lên ECR                      | 10/30/2025 | 10/30/2025      | https://www.youtube.com/watch?v=Y3zqsFpUzMk&list=PLncHg6Kn2JT4kLKJ_7uy0x4AdNrCHbe0n |
| 6   | - Học về Amazon ECS <br> - **Thực hành:** <br>&emsp; + Tạo ECS cluster <br>&emsp; + Tạo task definition dựa trên image lưu trong ECR <br>&emsp; + Deploy ứng dụng sử dụng ECS service <br>&emsp; + Kiểm tra và xác nhận triển khai thành công                                                                                   | 10/31/2025 | 10/31/2025      | https://youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2&si=baiJ4O6XbfmMEFvH |


### Kết quả đạt được tuần 8:

* Củng cố kiến thức về AWS Core architecture và các dịch vụ
    * Hiểu mô hình kiến trúc cloud cơ bản
    * Ôn tập các dịch vụ AWS cốt lõi như EC2, S3, IAM, RDS và CloudWatch
    * Xác định vai trò của từng dịch vụ trong việc xây dựng ứng dụng có khả năng mở rộng

* Làm quen với cách sử dụng Docker
    * Thực hành các lệnh Docker phổ biến (build, run, ps, logs, exec)
    * Học cách containerize một ứng dụng web đơn giản bằng Docker
    * Viết một Dockerfile cơ bản

* Trải nghiệm thực hành CI/CD bằng Docker và GitHub Actions
    * Thiết lập GitHub Actions workflow để tự động build ứng dụng
    * Cấu hình pipeline GitHub Actions để tự động build Docker images
    * Hiểu cách CI/CD giúp deploy phiên bản mới mỗi khi có code push

* Trải nghiệm làm việc với Amazon ECR và triển khai ứng dụng
    * Tạo và cấu hình EC2 instance để chạy Docker containers
    * Deploy ứng dụng web container và kiểm tra vận hành thành công
    * Tạo ECR repository và push Docker image lên ECR

* Hiểu cách ECS cluster tổ chức tài nguyên tính toán

* Nắm được vai trò của Task Definition và cách mô tả cấu hình container
