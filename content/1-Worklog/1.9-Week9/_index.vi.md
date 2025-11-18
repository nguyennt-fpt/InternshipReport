---
title: "Worklog tuần 9"
date: "2025-11-03"
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---



### Mục tiêu Tuần 9:

* Triển khai containers trên ECS sử dụng EC2 và Fargate
* Hiểu các chế độ networking của ECS
* Triển khai load balancing và path-based routing với ALB

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Thực hành chạy containers trên ECS với EC2: <br>&emsp; + Tạo ECR repository <br>&emsp; + Build và upload container image lên ECR <br>&emsp; + Tạo ECS cluster <br>&emsp; + Tạo task definition sử dụng EC2 <br>&emsp; + Tạo service để chạy task <br>&emsp; + Kiểm tra triển khai thành công                                                                                               | 11/03/2025 | 11/03/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 3   | - Thực hành chạy containers trên ECS với Fargate: <br>&emsp; + Push container image lên Docker Hub <br>&emsp; + Tạo task definition cho Fargate <br>&emsp; + Tạo service và chạy task <br>&emsp; + Scale service bằng cách tăng desired task count <br>&emsp; + Kiểm tra triển khai thành công                                                                                               | 11/04/2025 | 11/04/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 4   | - Tìm hiểu các chế độ network của ECS <br> - Hiểu về sự khác nhau giữa các chế độ: <br>&emsp; + bridge <br>&emsp; + host <br>&emsp; + awsvpc  | 11/05/2025 | 11/05/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 5   | - Triển khai containers với Application Load Balancer: <br>&emsp; + Tạo Application Load Balancer <br>&emsp; + Tạo target group <br>&emsp; + Tạo service tích hợp với ALB <br>&emsp; + Kiểm tra và xác nhận traffic được route đúng                      | 11/06/2025 | 11/06/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |
| 6   | - Chạy nhiều service phía sau cùng một ALB: <br>&emsp; + Tạo các target group riêng cho từng path (/email, /user) <br>&emsp; + Tạo services tương ứng với từng target group <br>&emsp; + Kiểm tra triển khai để đảm bảo routing đúng                                                                                   | 11/07/2025 | 11/07/2025      | https://www.youtube.com/playlist?list=PL4NoNM0L1m70lB1qL6sJ9Xr43PVbua3u2 |


###  Kết quả đạt được tuần 9:

* Thực hành triển khai ứng dụng containerized trên Amazon ECS:
    * Build và push container images lên cả ECR và Docker Hub
    * Tạo ECS cluster với cả launch type EC2 và Fargate
    * Tạo task definition và triển khai services
    * Scale services bằng cách điều chỉnh desired task count
    * Xác nhận deployment thành công và ứng dụng truy cập ổn định

* Tìm hiểu các chế độ network của ECS:
    * So sánh đặc điểm và trường hợp sử dụng của bridge, host và awsvpc
    * Hiểu cách awsvpc cấp phát ENI và cải thiện isolation cho service

* Thực hành triển khai containers với Application Load Balancer (ALB):
    * Tạo ALB và cấu hình target groups
    * Tích hợp ECS services với ALB
    * Kiểm tra routing và xác nhận traffic forwarding hoạt động đúng

* Triển khai nhiều services phía sau một ALB:
    * Tạo nhiều target groups cho các đường dẫn ứng dụng khác nhau (/email, /user, /)
    * Liên kết ECS services với target groups tương ứng
    * Kiểm tra và xác nhận hành vi path-based routing hoạt động chính xác