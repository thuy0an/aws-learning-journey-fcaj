---
title: "Các bước triển khai"
date: 2026-08-04
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---
Phần này hướng dẫn triển khai NeonFoodMap trên AWS theo đúng thứ tự: nền tảng mạng và cơ sở dữ liệu, lưu trữ cùng Docker image, GitHub Actions, ứng dụng ECS Fargate sau ALB, rồi Auto Scaling và CloudFront.

## Nội dung triển khai

1. [VPC và RDS MySQL](5.4.1-vpc-rds/) — tạo VPC trên hai Availability Zone, public/private subnet, NAT Gateway, route table và Amazon RDS MySQL trong private subnet.
2. [S3, ECR và Docker](5.4.2-s3-ecr-docker/) — tạo bucket cho tài nguyên ứng dụng, thiết lập repository ECR, build và kiểm tra Docker image của frontend và backend.
3. [GitHub Actions, OIDC và ECS](5.4.3-oidc-ecs-alb/) — cấu hình GitHub OIDC, IAM role, Secrets, Variables và pipeline kiểm thử, build, deploy.
4. [ECS Fargate và ALB](5.4.4-ecs-autoscaling/) — tạo Cloud Map, ECS cluster, task definition, ECS service, target group và ALB để phân phối lưu lượng.
5. [ECS Auto Scaling và CloudFront](5.4.5-cloudfront-delivery/) — thiết lập chính sách tự động mở rộng cho ECS, sau đó cấu hình CloudFront, S3 Origin Access Control và ALB origin.
6. [CloudWatch, Logs và Alarms](5.4.6-monitoring-alerting/) — cấu hình log, truy vấn Log Insights, dashboard, alarm và thông báo SNS.

{{< event-image src="images/2-Proposal/platform_architecture.jpg" alt="Kiến trúc tổng thể nền tảng trên AWS" >}}
