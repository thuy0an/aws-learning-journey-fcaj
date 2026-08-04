---
title: "Các bước triển khai"
date: 2026-08-04
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---
Phần này hướng dẫn triển khai NeonFoodMap trên AWS theo đúng thứ tự. Bắt đầu từ mạng và cơ sở dữ liệu, nhóm lần lượt chuẩn bị lưu trữ cùng Docker image, triển khai ứng dụng lên ECS Fargate, rồi bổ sung Auto Scaling và CloudFront.

## Nội dung triển khai

1. [Nền tảng VPC và RDS MySQL](5.4.1-foundation/) — tạo VPC trên hai Availability Zone, public/private subnet, NAT Gateway, route table và Amazon RDS MySQL trong private subnet.
2. [S3, ECR và Docker](5.4.2-storage-identity-containers/) — tạo bucket cho tài nguyên ứng dụng, thiết lập repository ECR, build và kiểm tra Docker image của frontend và backend.
3. [GitHub Actions, ECS và Application Load Balancer](5.4.3-delivery-application/) — cấu hình GitHub OIDC, IAM role, ECS cluster, task definition, service discovery, ECS service và ALB để phân phối lưu lượng đến ứng dụng.
4. [ECS Auto Scaling và CloudFront](5.4.4-scaling-cdn-operations/) — thiết lập chính sách tự động mở rộng cho ECS, sau đó cấu hình CloudFront, S3 Origin Access Control và ALB origin.

{{< event-image src="images/2-Proposal/platform_architecture.jpg" alt="Kiến trúc tổng thể nền tảng trên AWS" >}}
