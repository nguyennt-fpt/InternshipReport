---
title: "Worklog tuần 6"
date: "2025-10-13"
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---



### Mục tiêu tuần 6:

* Hiểu về các dịch vụ AWS nâng cao cho việc data migration, bảo mật và automation.
* Học cách bảo mật, giám sát và quản lý tài nguyên AWS bằng GuardDuty, macie, và secrets manager.
* Thực hành lambda và EventBridge để tự động hóa việc quản lý EC2.
### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                                   | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Tìm hiểu về AWS data migration service <br> - **Thực hành:** <br>&emsp; + Thiết lập môi trường lab <br>&emsp; + Tạo replication instance <br>&emsp; + Cấu hình source và target endpoints <br>&emsp; + Tạo và chạy migration task                                                                                                  | 10/13/2025 | 10/13/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Tìm hiểu về Amazon macie<br> - **Thực hành:** <br>&emsp; + Tạo custom data identifiers <br>&emsp; + Tạo và chạy macie job để phân tích dữ liệu nhạy cảm                                               | 10/14/2025 | 10/14/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Tìm hiểu về AWS secrets manager <br> - **Thực hành:** <br>&emsp; + Lưu trữ thông tin đăng nhập RDS một cách an toàn bằng secrets manager <br>&emsp; + Viết bash script để kết nối tới RDS bằng secret đã lưu | 10/15/2025 | 10/15/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Tìm hiểu về Amazon GuardDuty <br> - Học cách sử dụng EventBridge và lambda để tự động cô lập EC2 bị xâm nhập khi phát hiện hoạt động đáng ngờ                            | 10/16/2025 | 10/16/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Tìm hiểu về AWS lambda <br> - **Thực hành:** <br>&emsp; + Tạo lambda function để start EC2 instance thông qua EventBridge <br>&emsp; + Tạo lambda function để stop EC2 instance thông qua EventBridge                                                                                     | 10/17/2025 | 10/17/2025      | <https://cloudjourney.awsstudygroup.com/> |


### Kết quả đạt được tuần 6:

* Làm quen với AWS data migration service và biết cách:
  * Tạo replication instance
  * Cấu hình source và target endpoints
  * Tạo và chạy migration task

* Biết được cách phát hiện và phân loại dữ liệu nhạy cảm bằng Amazon macie.

* Hiểu cách sử dụng AWS secrets manager và thực hành:
  * Lưu trữ thông tin đăng nhập RDS an toàn bằng secrets manager
  * Viết bash script để kết nối tới RDS

* Làm quen với Amazon GuardDuty và tăng cường bảo mật bằng cách sử dụng EventBridge và lambda để tự động bảo vệ hệ thống

* Học cách sử dụng AWS Lambda và thực hành:
  * Tạo lambda function để khởi động EC2 instance thông qua EventBridge
  * Tạo lambda function để tắt EC2 instance thông qua EventBridge

* ...
