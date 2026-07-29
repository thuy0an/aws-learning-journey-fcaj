---
title: "Worklog Tuần 3"
date: 2026-07-12
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---
### Mục tiêu tuần 3:

- Nắm vững cách quản trị bảo mật và phân quyền truy cập hệ thống an toàn thông qua AWS IAM.
- Hiểu và triển khai cơ sở dữ liệu quan hệ (Amazon RDS) với cấu hình sẵn sàng cao và khả năng sao lưu phục hồi.
- Làm quen với các giải pháp lưu trữ cốt lõi thông qua Amazon S3 và kết nối môi trường lai (hybrid) bằng AWS Storage Gateway.
- Chọn đề tài và nghiên cứu vẽ các mẫu kiến trúc để triển khai dự án.

### Các công việc cần triển khai trong tuần này:

| Thứ | Công việc                                                                                                                                                                                                    | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu                                                                                                                                                                                                                                               |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 2    | -Học và tìm hiểu sâu hơn về Amazon EC2                                                                                                                                                                | 06/07/2026       | 06/07/2026         | [docs.aws.amazon.com/ec2](https://docs.aws.amazon.com/ec2/)                                                                                                                                                                                                      |
| 3    | -Tìm hiểu về S3<br />-Tìm hiểu về Cloudfront<br />-**Thực hành:** Tạo dự án sử dụng S3 với static website                                                                                | 07/07/2026       | 07/07/2026         | [docs.aws.amazon.com/AmazonS3/latest/developerguide](https://docs.aws.amazon.com/AmazonS3/latest/developerguide)<br /><br />[000024.awsstudygroup.com](https://000024.awsstudygroup.com/)<br /><br />[000057.awsstudygroup.com](https://000057.awsstudygroup.com/) |
| 4    | -Tìm hiểu về RDS<br />-Họp nhóm chọn đề tài chính cho dự <br />-**Thực hành:** Khởi tạo và kết nối cơ sở dữ liệu RDS                                                             | 08/07/2026       | 08/07/2026         | [docs.aws.amazon.com/rds](https://docs.aws.amazon.com/rds)<br /><br />[000005.awsstudygroup.com](https://000005.awsstudygroup.com/)                                                                                                                               |
| 5    | -Nghiên cứu IAM: Principal least privilege, Policies, IAM<br />-Tìm hiểu AWS Organizations, AWS Identity Center & mã hóa AWS KMS<br />-Họp nhóm tìm hiểu và xác định hướng dự án triển khai | 09/07/2026       | 09/07/2026         | [000002.awsstudygroup.com](https://000002.awsstudygroup.com/)<br /><br />[000044.awsstudygroup.com](https://000044.awsstudygroup.com/)                                                                                                                            |
| 6    | -Nghiên cứu và chuẩn bị doc các service, vẽ kiến trúc cho dự án                                                                                                                                     | 10/07/2026       | 10/07/2026         |                                                                                                                                                                                                                                                                 |

### Kết quả đạt được tuần 3:

* Hiểu và sử dụng được các dịch vụ Compute EC2, S3, AWS Storage Gateway, Amazon VPC, CloudFront và RDS
* Amazon EC2:

  * Hiểu cách lựa chọn cấu hình (Instance Type) phù hợp, sử dụng AMI (ảnh mẫu) để khởi tạo máy chủ nhanh chóng và bảo mật đăng nhập bằng Key Pair.
  * Phân biệt và áp dụng thực tế các loại lưu trữ: Dùng EBS (lưu trữ dạng khối mạng) cho dữ liệu bền vững và Instance Store cho dữ liệu tạm thời cần tốc độ cao
  * Hiểu cơ chế EC2 Auto Scaling giúp hệ thống tự động tăng/giảm máy chủ theo tải thực tế để tối ưu chi phí
* Bảo mật & Quản lý danh tính (Security):

  * Quản trị thông tin bảo mật bằng IAM nguyên tắc "đặc quyền tối thiểu", quản lý Root/IAM User, thiết lập Policy phân quyền chặt chẽ và sử dụng IAM Role để cấp quyền an toàn cho máy chủ EC2 mà không lộ Access Key.
  * Quản lý đa tài khoản bằng AWS Organizations, đăng nhập một lần bằng Identity Center và mã hóa dữ liệu với AWS KMS.
* Cơ sở dữ liệu (Database):

  * Hệ thống hóa sự khác biệt giữa CSDL quan hệ (RDBMS) và phi quan hệ (NoSQL).
  * Nắm vững kiến trúc dự phòng Multi-AZ và Read Replica của Amazon RDS/Aurora, cùng với các giải pháp phân tích dữ liệu lớn (Redshift) và bộ nhớ đệm (ElastiCache).
* S3 & Storage Gateway:

  * Hiểu bản chất lưu trữ không thư mục, các tính năng như S3 Access Point và cách phân bổ dữ liệu vào các Storage Class (Standard, Infrequent Access, Glacier) để tối ưu chi phí
  * Thực hành triển khai thành công trang web tĩnh (Static Website) trên S3, cấu hình CORS cho phép truy cập chéo tên miền và bật S3 Versioning để bảo vệ dữ liệu
* Tổng hợp các thông kiến trúc dự án sẽ triển khai, các service dự tính sẽ dùng và họp thống nhất kiến trúc dự án sẽ làm.
