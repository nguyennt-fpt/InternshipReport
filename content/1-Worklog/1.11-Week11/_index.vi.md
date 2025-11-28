---
title: "Worklog tuần 11"
date: "2025-11-17"
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu Tuần 11:

* Luyện tập triển khai ứng dụng Node.js container hóa bằng Docker và Docker Compose
* Thực hành Infrastructure as Code (IaC) với Terraform để triển khai dịch vụ AWS
* Học về prompt engineering cho các ứng dụng AI

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tạo một dự án Node.js nhỏ chạy trong Docker <br>&emsp; + Tạo Docker network <br>&emsp; + Chạy container database <br>&emsp; + Chạy container app và kết nối với database <br>&emsp; + Xóa container app và tạo lại <br>&emsp; + Kiểm tra dữ liệu vẫn tồn tại | 11/17/2025 | 11/17/2025      | https://docs.docker.com/get-started/workshop/               |
| 3   | - Triển khai ứng dụng Node.js bằng Docker Compose <br>&emsp; + Viết file docker-compose.yml định nghĩa dịch vụ app và database <br>&emsp; + Chạy docker-compose up và kiểm tra deployment | 11/18/2025 | 11/18/2025      | https://docs.docker.com/get-started/workshop/                      |
| 4   | - Luyện tập IaC với Terraform <br>&emsp; + Viết script tạo VPC, subnets, security groups trên AWS <br>&emsp; + Tạo NAT gateway, ALB, target groups <br>&emsp; + Tạo RDS và IAM policies <br>&emsp; + Tạo ECS cluster, task definition và service | 11/19/2025 | 11/21/2025      |               |
| 5   | - Triển khai dịch vụ AWS bằng Terraform <br>&emsp; + Apply script Terraform để tạo các resource AWS <br>&emsp; + Kiểm tra các resource được tạo đúng <br>&emsp; + Kiểm tra ECS service và RDS hoạt động ổn định | 11/21/2025 | 11/21/2025      |                         |
| 6   | - Học prompt engineering <br>&emsp; + Nghiên cứu cách viết prompt hiệu quả cho AI <br>&emsp; + Thử nghiệm các dạng prompt khác nhau | 11/22/2025 | 11/22/2025      | https://youtu.be/_ZvnD73m40o?si=ud6xi6-S2NsvA7dr    |

### Kết quả đạt được tuần 11:

* Thành công container hóa ứng dụng Node.js bằng Docker  
    * Tạo Docker network, container database và container app  
    * Kiểm tra dữ liệu vẫn tồn tại sau khi tạo lại container app  
    * Test kết nối giữa các container  

* Triển khai ứng dụng Node.js bằng Docker Compose  
    * Viết hoàn chỉnh docker-compose.yml cho dịch vụ app và database  
    * Chạy docker-compose up và xác nhận ứng dụng hoạt động đúng  

* Luyện tập IaC bằng Terraform  
    * Tạo VPC, subnets, security groups, NAT gateway, ALB và target groups  
    * Triển khai RDS và cấu hình IAM policies  
    * Tạo ECS cluster, task definition và service  

* Triển khai dịch vụ AWS bằng Terraform thành công  
    * Xác nhận tất cả các resource AWS được tạo đúng  
    * Kiểm tra ECS service và RDS hoạt động ổn định  
