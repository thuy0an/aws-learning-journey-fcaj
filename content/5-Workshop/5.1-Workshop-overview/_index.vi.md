---
title: "Tổng quan workshop"
date: 2026-08-03
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---
## Bối cảnh

**NeonFoodMap** là nền tảng thuyết minh tự động và khám phá du lịch số dành cho **Phố ẩm thực Vĩnh Khánh, Quận 4, Thành phố Hồ Chí Minh**. Ứng dụng giúp du khách khám phá các điểm ẩm thực và văn hóa thông qua bản đồ, thông tin điểm đến (POI), hình ảnh và nội dung thuyết minh âm thanh. Trải nghiệm có thể được kích hoạt theo vị trí địa lý hoặc QR code.

Hệ thống phục vụ ba nhóm chính: du khách cần tra cứu và nghe thuyết minh; đối tác kinh doanh địa phương cần cập nhật thực đơn/ưu đãi; và quản trị viên cần quản lý POI, nội dung, người dùng và tình trạng vận hành. Ứng dụng gồm React frontend, Django backend và cơ sở dữ liệu MySQL.

**Repository:** [github.com/HaoWasabi/NeonFoodmap](https://github.com/HaoWasabi/NeonFoodmap)

## Vấn đề giải quyết

Doanh nghiệp ẩm thực & du lịch thường gặp khó khăn khi thông tin địa điểm, thực đơn và media bị phân tán, tốn nhiều nguồn lực vận hành nhưng khó mở rộng cho lượng lớn du khách. Về hạ tầng, hệ thống dễ gặp rủi ro bảo mật (lộ database, đính kèm access key), quy trình deploy thủ công tốn thời gian và thiếu công cụ kiểm soát chi phí hay sự cố tức thì.

**NeonFoodMap** giải quyết bằng cách số hóa toàn bộ nội dung thành trải nghiệm đa phương tiện tập trung, kết hợp hạ tầng AWS bảo mật Multi-AZ, CI/CD tự động qua GitHub OIDC và hệ thống giám sát vận hành.

## Kiến trúc tổng quan

Hệ thống được tổ chức theo mô hình multi-tier trong VPC, trải trên hai Availability Zone để hỗ trợ tính sẵn sàng:

* **Frontend:** React SPA, lưu trữ trên S3 Static Website, phân phối qua Amazon CloudFront.
* **Backend:** Backend Django/Gunicorn chạy trên Amazon ECS Fargate (trong private subnet, tự động tăng/giảm số task theo tải), đứng sau Application Load Balancer.
* **Dữ liệu:** MySQL trên RDS (trong private database subnet), S3 lưu trữ tách biệt theo mục đích (frontend, media, audio, logs).
* **Bảo mật & vận hành:** IAM/CloudFormation quản lý quyền và hạ tầng, CloudWatch theo dõi log/metric, cảnh báo tự động qua Amazon SNS, AWS Budgets và Cost Anomaly Detection.
* **CI/CD:** GitHub Actions xác thực qua OIDC/AWS STS, tự động build/push Docker image lên Amazon ECR và cập nhật ECS service.

{{< event-image src="images/2-Proposal/platform_architecture.jpg" alt="Kiến trúc tổng thể nền tảng trên AWS" >}}

## Tech stack

| Lớp            | Công nghệ/Dịch vụ sử dụng                                                             | Vai trò trong NeonFoodMap                                                                                  |
| --------------- | ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| Frontend        | React, Vite, Nginx, Docker                                                                  | Xây dựng giao diện SPA, build asset và phục vụ ứng dụng web                                         |
| Backend         | Django, Gunicorn, Python, Docker                                                            | Cung cấp API, xử lý nghiệp vụ, kết nối database và S3                                               |
| Network         | Amazon VPC, public/private subnet, Internet Gateway, NAT Gateway, Application Load Balancer | Tách lớp mạng, cho phép truy cập Internet cần thiết và định tuyến request đến ứng dụng       |
| Database        | Amazon RDS MySQL                                                                            | Lưu dữ liệu nghiệp vụ; dùng private access, backup, encryption và cấu hình đa AZ theo kiến trúc |
| Storage/CDN     | Amazon S3, Amazon CloudFront, Origin Access Control                                         | Lưu frontend, media, audio, logs và phân phối nội dung tĩnh an toàn                                  |
| Container       | Amazon ECR, Amazon ECS Fargate                                                              | Lưu Docker image và vận hành container frontend/backend                                                 |
| CI/CD           | GitHub Actions, GitHub OIDC, AWS STS, IAM Role                                              | Kiểm tra, build, push image và deploy không cần AWS access key dài hạn                                |
| Monitoring/Cost | Amazon CloudWatch, Amazon SNS, AWS Budgets, Cost Anomaly Detection                          | Thu log/metric, cảnh báo kỹ thuật và theo dõi chi phí                                                |

## Kết quả đạt được

- Hoàn thiện nền tảng AWS gồm VPC Multi-AZ, RDS MySQL private, S3, IAM và cơ chế theo dõi chi phí.
- Đóng gói frontend/backend bằng Docker, quản lý image trên Amazon ECR và kiểm tra image trước khi triển khai.
- Triển khai ECS Fargate phía sau ALB/Target Group; kiểm tra kết nối frontend–backend, health check, log và lỗi cấu hình qua CloudWatch.
- Tự động hóa quy trình build/push/deploy bằng GitHub Actions với OIDC; theo dõi ECS rollout sau mỗi lần cập nhật.
- Hoàn thiện phân phối nội dung qua CloudFront, kiểm thử các luồng chính của ứng dụng và bổ sung checklist vận hành/cleanup.
