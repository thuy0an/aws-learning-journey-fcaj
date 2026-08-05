---
title: "Workshop"
date: 2026-08-04
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

## Workshop: Triển khai NeonFoodMap trên AWS

Workshop này hướng dẫn triển khai **NeonFoodMap** — nền tảng khám phá ẩm thực và du lịch số — trên AWS. Ứng dụng gồm React frontend, Django backend, cơ sở dữ liệu MySQL, dữ liệu điểm đến (POI), thuyết minh âm thanh, tour và thanh toán sandbox.

Hạ tầng được thiết kế và vận hành tại Region **Asia Pacific (Singapore) — `ap-southeast-1`** theo mô hình VPC đa Availability Zone. Nội dung đi từ thiết kế hạ tầng, quản lý quyền, đóng gói ứng dụng đến triển khai, giám sát và dọn dẹp tài nguyên.

Các dịch vụ chính được sử dụng gồm:

* **Compute & delivery** — Amazon ECS Fargate và Application Load Balancer chạy Django backend dạng container; Amazon S3 và CloudFront phân phối React SPA cùng tài nguyên tĩnh.
* **Dữ liệu, bảo mật & CI/CD** — Amazon RDS MySQL lưu dữ liệu nghiệp vụ; GitHub Actions xác thực qua OIDC/AWS STS để triển khai không cần access key dài hạn; CloudWatch hỗ trợ theo dõi log, metric và cảnh báo.

## Nội dung

1. [Tổng quan workshop](5.1-Workshop-overview/)
2. [Các bước chuẩn bị](5.2-Prerequiste/)
3. [Kiến trúc hệ thống](5.3-Structure/)
4. [Các bước triển khai](5.4-Onprem/)
5. [Xác minh triển khai và giám sát hệ thống](5.5-Policy/)
6. [Tổng quan giao diện và chức năng ứng dụng](5.6-Project-Visual/)
7. [Dọn dẹp tài nguyên](5.7-Cleanup/)
