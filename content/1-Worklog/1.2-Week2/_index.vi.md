---
title: "Worklog Tuần 2"
date: "2025-09-15"
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---



### Mục tiêu tuần 2:

* Học cách sử dụng session manager để quán lý và kết nối đến EC2.
* Luyện tập về VPC peering, áp dụng ACL và security group rules khi giao tiếp cross-VPC.
* Hiểu được sự khác nhau giữa VPC peering và transit gateway.
* Tìm hiểu về route 53 và AWS managed microsoft AD.
* Triển khai ứng dụng trên EC2.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Học về session manager <br> - **Thực hành:** <br>&emsp; + Tạo VPC endpoint cho session manager <br>&emsp; + Tạo S3 bucket để lưu session logs                                                                | 09/15/2025 | 09/15/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Học về VPC peering <br> - Cách áp dụng rules trong ACLs và security groups cho cross-VPC traffic <br> - **Thực hành:** <br>&emsp; + Tạo 2 VPCs <br>&emsp; + Thiết lập VPC peering <br>&emsp; + Áp dụng ACLs và security groups cho cross-VPC traffic                                         | 09/16/2025 | 09/16/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Học về transit gateway <br> - Hiểu sự khác biệt giữa VPC peering và transit gateway <br> - **Thực hành:** <br>&emsp; + Tạo 4 VPCs <br>&emsp; + Tạo transit gateway và attachments <br> &emsp; + Cấu hình transit gateway route table và VPC route tables | 09/17/2025 | 09/17/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Học về hybrid DNS với route 53 resolver <br> - Học về AWS directory service và AWS managed microsoft AD <br> - **Thực hành:** <br>&emsp; + Thiết lập network infrastructure bằng AWS cloudFormation  <br>&emsp; + Cấu hình inbound/outbound endpoints cho DNS resolution                            | 09/18/2025 | 09/18/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - Học về cách triển khai ứng dụng trên EC2 (Windows and Linux) <br> - **Thực hành:** <br>&emsp; + Triển khai cơ sở dữ liệu và web services thông qua CLI trên linux <br>&emsp; + Triển khai cơ sở dữ liệu và web services thông GUI và CLI trên windows                                                                                     | 09/18/2025 | 09/20/2025      | <https://cloudjourney.awsstudygroup.com/> |


### Kết quả đạt được tuần 2:

* Quản lý và kết nối thành công tới các EC2 instances trong cả public và private subnets bằng AWS session manager.

* Thiết lập VPC peering giữa hai VPCs và áp dụng đúng ACL và security group rules để cho phép giao tiếp cross-VPC.

* Xác minh và khắc phục lỗi kết nối VPC peering bằng reachability analyzer để phát hiện và sửa lỗi trong route table và security group.

* Xây dựng kết nối multi-VPC bằng AWS transit gateway, bao gồm:
  * Transit gateway attachments
  * Cấu hình transit gateway route table
  * Cấu hình VPC route table
  * ...

* Triển khai hybrid DNS resolution bằng route 53 resolver.

* Thiết lập AWS managed Microsoft AD thông qua AWS directory service và triển khai network infrastructure bằng CloudFormation.

* Triển khai applications trên EC2 instances (Windows và Linux) bằng cả CLI và GUI.

* ...