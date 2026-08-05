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

NeonFoodMap dùng kiến trúc **multi-tier** trong Amazon VPC tại `ap-southeast-1`, trải trên hai Availability Zone để tăng tính sẵn sàng. Frontend tĩnh được phân phối qua CloudFront/S3; các container ứng dụng chạy trên ECS Fargate trong private subnet; dữ liệu nằm trên RDS MySQL private.

### Sơ đồ kiến trúc tổng thể

{{< event-image src="images/2-Proposal/platform_architecture.jpg" alt="Kiến trúc tổng thể nền tảng trên AWS" >}}

### Kiến trúc kết nối dịch vụ

{{< event-image src="images/2-Proposal/edge_architecture.jpg" alt="Kiến trúc kết nối dịch vụ trên AWS" >}}

- Người dùng truy cập nội dung tĩnh qua **CloudFront**; các request ứng dụng đi qua **Application Load Balancer (ALB)**.
- ALB nhận HTTP/HTTPS, thực hiện health check và dùng rule theo đường dẫn để chuyển request API đến Backend Service.
- ECS task chỉ nằm trong private subnet. **AWS Cloud Map** cung cấp service discovery nội bộ, nhờ đó container giao tiếp bằng DNS ổn định thay vì IP thay đổi theo từng task revision.

### Năm lớp kiến trúc

| Lớp | Thành phần | Vai trò chính |
| --- | --- | --- |
| CI/CD | GitHub Repository, GitHub Actions, Docker Build, AWS STS, Amazon ECR | Tự động kiểm thử, build image, xác thực OIDC và triển khai phiên bản mới. |
| Presentation | Amazon CloudFront, Amazon S3 Static Website | Phân phối frontend với độ trễ thấp, tăng tốc truy cập và giảm tải cho backend. |
| Application | ALB, ECS Cluster, Backend Service, Frontend Service | Chạy ứng dụng trên Fargate, định tuyến request, rolling update và khởi động lại task khi lỗi. |
| Data | Amazon RDS MySQL Multi-AZ | Lưu dữ liệu nghiệp vụ trong private database subnet; tăng chịu lỗi và hỗ trợ failover. |
| Monitoring | Amazon CloudWatch, Amazon SNS | Thu thập log/metric và gửi email alert khi phát hiện bất thường. |

### ECS Cluster và điều hướng lưu lượng

ECS Cluster gồm hai service độc lập, giúp mỗi thành phần có thể được cập nhật và mở rộng theo nhu cầu riêng:

| Service | Triển khai | Trách nhiệm |
| --- | --- | --- |
| **Backend Service** | Django REST API trong Docker Container trên ECS Fargate | Authentication, business logic, truy cập database và upload media. |
| **Frontend Service** | React Application trong Docker Container trên ECS Fargate | Phục vụ giao diện containerized và gọi backend qua ALB. |

**Service Discovery:** AWS Cloud Map quản lý DNS nội bộ giữa các container trong ECS Cluster.

**Load Balancing:** ALB tiếp nhận HTTP/HTTPS, kiểm tra sức khỏe target và chuyển request API đến Backend Service.

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

## Quy trình triển khai và vận hành

Quy trình phát hành tự động bắt đầu khi developer push source code lên nhánh chính:

1. **Developer push source code** → **GitHub Actions trigger workflow**.
2. Workflow kiểm thử, **build Docker image** và xác thực với AWS bằng OIDC qua **AWS STS**.
3. AWS STS cấp temporary credential; image được **push lên Amazon ECR**.
4. ECS **pull image** mới và thực hiện **rolling update** để thay thế task mà không làm gián đoạn service.
5. ALB tiếp tục chuyển request đến các task healthy; Backend Service truy cập **RDS MySQL** và upload media/audio lên **Amazon S3**.
6. **CloudWatch** thu thập ECS/container/application logs và metrics; **Amazon SNS** gửi email khi có cảnh báo hoặc sự cố.

Luồng rút gọn:

```text
Developer → GitHub Actions → Docker Build → OIDC / AWS STS → Amazon ECR
          → ECS Pull Image → Rolling Update → ALB → Backend → RDS / S3
                                                   └→ CloudWatch → SNS Email
```

## Kết quả đạt được

- Hoàn thiện nền tảng AWS gồm VPC Multi-AZ, RDS MySQL private, S3, IAM và cơ chế theo dõi chi phí.
- Đóng gói frontend/backend bằng Docker, quản lý image trên Amazon ECR và kiểm tra image trước khi triển khai.
- Triển khai ECS Fargate phía sau ALB/Target Group; kiểm tra kết nối frontend–backend, health check, log và lỗi cấu hình qua CloudWatch.
- Tự động hóa quy trình build/push/deploy bằng GitHub Actions với OIDC; theo dõi ECS rollout sau mỗi lần cập nhật.
- Hoàn thiện phân phối nội dung qua CloudFront, kiểm thử các luồng chính của ứng dụng và bổ sung checklist vận hành/cleanup.
