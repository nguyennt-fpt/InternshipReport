---
title: "Worklog tuần 5"
date: "2025-06-10"
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---


### Mục tiêu tuần 5:

* Hiểu về AWS Organizations, IAM roles, và permission boundaries.

* Thực hành với KMS, CloudTrail và phân tích logs bằng Athena.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc                                                                                                                                                                                                  | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                        |
| --- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------- | --------------- | ----------------------------------------- |
| 2   | - Học về AWS Organizations và multi-account strategy <br> - Hiểu mục đích của các core accounts <br> - **Thực hành:** <br>&emsp; + Tạo AWS accounts <br>&emsp; + Thiết lập organizational units <br>&emsp; + Áp dụng customer managed policies <br>&emsp; + Mời AWS accounts vào organization     | 10/06/2025 | 10/06/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 3   | - Học về IAM permission boundary <br> - Học về IAM role và cách viết IAM policies <br> - **Thực hành:** <br>&emsp; + Tạo restriction policy và kiểm thử permission boundaries <br>&emsp; + Tạo IAM roles <br>&emsp; + Viết và gắn JSON-based Policies <br>&emsp; + Kiểm thử role assumptions và xác minh permissions   | 10/07/2025 | 10/07/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 4   | - Học cách quản lý quyền truy cập EC2 bằng Resource Tags <br> - Học về AWS Security Hub  <br> - **Thực hành:** <br>&emsp; + Kích hoạt Security Hub <br>&emsp; + Xem xét các security standards | 10/08/2025 | 10/08/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 5   | - Học về AWS Key Management Service (KMS) <br> - Hiểu sự khác nhau giữa symmetric và asymmetric keys và các use cases của chúng <br> - Học cách mã hóa dữ liệu trong Amazon S3 bằng KMS keys <br> - ... | 10/09/2025 | 10/09/2025      | <https://cloudjourney.awsstudygroup.com/> |
| 6   | - **Thực hành:** <br>&emsp; + Tạo AWS CloudTrail để ghi lại các hoạt động của tài khoản <br>&emsp; + Kích hoạt logging và lưu Llgs vào Amazon S3 <br>&emsp; + Tạo Amazon Athena để phân tích CloudTrail logs <br>&emsp; + Truy xuất và truy vấn dữ liệu trong Athena | 10/10/2025 | 10/10/2025      | <https://cloudjourney.awsstudygroup.com/> |


### Kết quả đạt được tuần 5:

* Khám phá AWS Organizations và multi-account strategy:

  * Hiểu được lợi ích của multi-account model

  * Nắm rõ mục đích của các core accounts

  * Thiết lập Organizational Units

  * Áp dụng customer managed policies

  * ...

* Làm quen với IAM roles và permission boundaries.

* Thực hành quản lý quyền truy cập EC2 bằng Resource Tags.

* Thành thạo viết IAM policies bằng JSON.

* Cấu hình và kích hoạt AWS Security Hub để giám sát bảo mật tập trung.

* Trải nghiệm thực tế với AWS Key Management Service (KMS):

  * Hiểu sự khác biệt giữa symmetric và asymmetric encryption keys.

  * Học cách KMS tích hợp với các AWS services khác.

  * Sử dụng KMS keys để mã hóa và bảo vệ dữ liệu trong Amazon S3.

  * ...

* Triển khai CloudTrail logging và phân tích bằng Athena để tạo audit insights.

* Nắm được khả năng giám sát và kiểm toán các hoạt động trong AWS environment một cách hiệu quả.

* ...