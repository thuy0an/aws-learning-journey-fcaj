---
title: "Kiến trúc hệ thống"
date: 2026-08-03
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---
## Thiết kế VPC

Toàn bộ hạ tầng NeonFoodMap nằm trong một VPC riêng. VPC trải trên hai Availability Zone để phân tán Application Load Balancer, ECS service và database subnet group, từ đó hỗ trợ tính sẵn sàng và khả năng mở rộng của ứng dụng.

| **Thành phần**            | **Chi tiết**                                                                                                                   |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| VPC                               | `10.0.0.0/16`, Region `ap-southeast-1`                                                                                            |
| Availability Zones                | `ap-southeast-1a`, `ap-southeast-1b`                                                                                              |
| Public Subnet (2)                 | Chứa Application Load Balancer và NAT Gateway; public subnet bật auto-assign public IPv4 khi cần                                  |
| Private Subnet — Application (2) | Chứa ECS Fargate task frontend/backend; task không nhận public IP và chỉ nhận traffic từ ALB security group                    |
| Private Subnet — Database (2)    | Dùng cho RDS DB subnet group; RDS MySQL không public, chỉ nhận MySQL/3306 từ ECS task security group                             |
| Private Subnet bổ sung (2)       | Hoàn thiện mô hình 4 private subnet theo cấu hình`VPC and more` của Sprint; dành cho phân tách workload/mở rộng theo AZ |
| Internet Gateway                  | Gắn ở cấp VPC, phục vụ ALB và tuyến outbound của NAT Gateway; không mở trực tiếp private subnet ra Internet               |
| NAT Gateway                       | Public NAT Gateway dùng Elastic IP; private subnet có route`0.0.0.0/0` đến NAT để outbound có kiểm soát                    |

VPC được tạo bằng **VPC Console → Create VPC → VPC and more**, thay vì script. Quy trình này tạo đồng bộ VPC, subnet, route table và Internet Gateway; sau đó nhóm cấp Elastic IP, tạo NAT Gateway và bổ sung route cho private subnet. Chi tiết thao tác và bước kiểm tra nằm tại [5.4.1 – Nền tảng VPC và RDS MySQL](../5.4-Onprem/5.4.1-foundation/).

## IAM và quản lý quyền

### Các thành phần chính

| **Loại thành phần** | **Tên**                | **Mục đích**                                                                                                                             |
| ---------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Policy                       | `ForceMFAPolicy`            | Chỉ cho phép xem thông tin tài khoản/tự thiết lập MFA khi người dùng chưa bật MFA; các AWS action khác bị chặn.                  |
| Group                        | `NeonFoodmap-DevOps-Admins` | Quản trị hạ tầng và cấu hình chi phí của dự án.                                                                                        |
| Group                        | `NeonFoodmap-Backend-Devs`  | Thao tác ECS, ECR, RDS, S3 và CloudWatch phục vụ backend.                                                                                     |
| Group                        | `NeonFoodmap-Frontend-Devs` | Quản lý S3/CloudFront; chỉ đọc ECR và CloudWatch theo template.                                                                             |
| OIDC Provider                | `GitHubOIDCProvider`        | Liên kết`token.actions.githubusercontent.com` với audience `sts.amazonaws.com` để GitHub Actions dùng temporary credential từ AWS STS. |

{{< event-image src="images/5-Workshop/5.3-S3-vpc/picUserGroup.jpg" alt="IAM User Group" >}}

## IAM Roles

| **Role**                         | **Gắn cho**                                     | **Quyền chính**                                                                                                   |
| -------------------------------------- | ------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| `NeonFoodmap-ECS-Backend-Role`       | Backend ECS task                                       | Amazon S3, CloudWatch Logs và RDS Data API theo managed policy trong template; dùng cho quyền runtime của ứng dụng. |
| `NeonFoodmap-ECS-TaskExecution-Role` | ECS task platform                                      | Pull image từ Amazon ECR và gửi container log đến CloudWatch thông qua`AmazonECSTaskExecutionRolePolicy`.         |
| `NeonFoodmap-GitHub-Actions-Role`    | GitHub Actions qua OIDC, không dùng access key       | Push image lên ECR và cập nhật ECS service; trust policy chỉ chấp nhận repo`HaoWasabi/NeonFoodmap`.              |
| `NeonFoodmap-EC2-Backend-Role`       | EC2 backend/Instance Profile (phương án dự phòng) | Amazon S3 và CloudWatch Logs khi backend chạy trên EC2 thay vì ECS.                                                   |

{{< event-image src="images/5-Workshop/5.3-S3-vpc/picDetailRole.jpg" alt="IAM User Group" >}}

{{< event-image src="images/5-Workshop/5.3-S3-vpc/picIAMRole.jpg" alt="IAM User Group" >}}

`ECS-Backend-Role` và `ECS-TaskExecution-Role` là hai role **tách biệt**: execution role phục vụ nền tảng ECS pull image/ghi log, còn backend role phục vụ code đang chạy trong container. GitHub Actions cũng có role riêng qua OIDC để pipeline không dùng quyền runtime của ứng dụng. Cách tách này giảm phạm vi ảnh hưởng khi một thành phần bị cấp quyền không đúng.
