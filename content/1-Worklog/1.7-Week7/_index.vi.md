---
title: "Worklog tuần 7"
date: "2025-10-20"
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

### Mục tiêu Tuần 7:

* Trực quan hóa và giám sát số liệu hệ thống bằng Grafana  
* Quản lý và tự động hóa hoạt động AWS bằng Systems Manager
* Triển khai Infrastructure as Code bằng CloudFormation và Terraform

### Các công việc cần triển khai trong tuần này:
| Ngày | Công việc                                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu về các kiến thức cơ bản của Grafana <br> - **Thực hành:** <br>&emsp; + Cài đặt Grafana <br>&emsp; + Trực quan hóa số liệu trong dashboards                                                                                                 | 20/10/2025 | 20/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu cách Systems Manager tập trung hóa các tác vụ vận hành<br> - **Thực hành:** <br>&emsp; + Sử dụng Patch Manager để tự động cập nhật bản vá hệ điều hành và phần mềm <br>&emsp; + Thực thi lệnh từ xa trên nhiều EC2 instances bằng Run Command                                               | 21/10/2025 | 21/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Hiểu cú pháp YAML và cấu trúc của AWS CloudFormation template <br> - Viết một template cơ bản để tạo EC2 instance | 22/10/2025 | 23/10/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu về các khái niệm cơ bản của Terraform để tự động hóa việc triển khai hạ tầng AWS <br> - **Thực hành:** <br>&emsp; + Viết file main.tf để triển khai các tài nguyên AWS cơ bản <br>&emsp; + Sử dụng variables để bảo vệ dữ liệu nhạy cảm trong runtime <br>&emsp; + Tạo nhiều workspaces để tách biệt môi trường                       | 23/10/2025 | 26/10/2025      | https://youtu.be/7xngnjfIlK4?si=VhwAI1v4UCCSJQZF |
| 6   | - Tìm hiểu các khái niệm cơ bản về Continuous Integration và Deployment (CI/CD) <br> - **Thực hành:** <br>&emsp; + Sử dụng GitHub Actions để tự động hóa quy trình build và deploy                                                                                     | 26/10/2025 | 26/10/2025      | https://www.youtube.com/watch?v=ZKaDy0mNHGs |

### Kết quả đạt được tuần 7:

* Làm quen với các kiến thức cơ bản và tính năng chính của Grafana, bao gồm:
  * Cài đặt và cấu hình Grafana Server
  * Tạo dashboards để trực quan hóa số liệu hệ thống theo thời gian thực

* Hiểu cách Systems Manager tập trung hóa việc quản lý vận hành trên AWS

* Nắm được khái niệm Infrastructure as Code (Terraform và CloudFormation):
  * Học cú pháp YAML và cấu trúc của CloudFormation template
  * Tạo một CloudFormation template đơn giản để triển khai EC2 instance
  * Hiểu các thành phần chính: parameters, resources, và outputs
  * Nghiên cứu các khái niệm cơ bản của Terraform để tự động hóa triển khai hạ tầng AWS
  * Viết file main.tf để khởi tạo các tài nguyên AWS cơ bản
  * Sử dụng variables để quản lý dữ liệu nhạy cảm một cách an toàn trong runtime
  * ...

* Hiểu các khái niệm về Continuous Integration và Deployment (CI/CD)

* Xây dựng quy trình tự động build và deploy bằng GitHub Actions

* ...
