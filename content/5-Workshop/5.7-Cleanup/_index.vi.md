---
title: "Dọn dẹp tài nguyên"
date: 2026-08-03
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---
## Dọn dẹp tài nguyên

Nhiều dịch vụ AWS tính phí theo thời gian hoạt động hoặc dung lượng lưu trữ dù ứng dụng không có người dùng truy cập. Với NeonFoodMap, các nguồn chi phí cần theo dõi sát gồm Amazon ECS Fargate, Application Load Balancer, NAT Gateway, Amazon RDS MySQL, Elastic IP, Amazon S3, Amazon ECR và CloudWatch Logs.

## Tiết kiệm chi phí trong quá trình phát triển

Trong thời gian phát triển, nhóm có thể dừng các tài nguyên tính phí theo thời gian chạy sau mỗi phiên làm việc. Việc này **không xóa dữ liệu hay cấu hình**, nhưng ứng dụng sẽ không phục vụ được cho đến khi tài nguyên được khởi động lại.

| Tài nguyên NeonFoodMap           | Hành động tiết kiệm                                                                           | Lưu ý                                                                                                                                                                                                          |
| ---------------------------------- | -------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ECS backend`svc-neonfoodmap-be`  | Đặt số task mong muốn về`0`; đồng thời hạ giới hạn Auto Scaling tối thiểu về `0` | Service backend đang có Auto Scaling. Nếu chỉ giảm desired count mà vẫn để minimum là`2`, ECS sẽ tự tạo lại task.                                                                                |
| ECS frontend`svc-neonfoodmap-fe` | Đặt số task mong muốn về`0`                                                                 | Frontend và API không còn phục vụ qua ALB khi không có task healthy.                                                                                                                                      |
| RDS MySQL`neonfoodmap-mysql-db`  | Dừng DB instance khi không cần chạy ứng dụng                                                 | RDS vẫn tính phí phần storage và backup khi dừng; DB instance có thể tự khởi động lại sau tối đa 7 ngày. Không dùng thao tác này với cấu hình RDS không hỗ trợ stop, ví dụ Multi-AZ. |

Lệnh AWS CLI để tiết kiệm chi phí trong quá làm ứng dụng.

```powershell
# Tạm ngưng cuối ngày: backend phải hạ cả Auto Scaling minimum và desired count.
$region = "ap-southeast-1"

aws application-autoscaling register-scalable-target `
  --service-namespace ecs `
  --resource-id service/NeonFoodmap-cluster/svc-neonfoodmap-be `
  --scalable-dimension ecs:service:DesiredCount `
  --min-capacity 0 --max-capacity 0 --region $region

aws ecs update-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-be --desired-count 0 --region $region

aws ecs update-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-fe --desired-count 0 --region $region

aws rds stop-db-instance --db-instance-identifier neonfoodmap-mysql-db --region $region

# Khởi động lại trước phiên làm việc: khôi phục giới hạn Auto Scaling và số task.
aws rds start-db-instance --db-instance-identifier neonfoodmap-mysql-db --region $region

aws application-autoscaling register-scalable-target `
  --service-namespace ecs `
  --resource-id service/NeonFoodmap-cluster/svc-neonfoodmap-be `
  --scalable-dimension ecs:service:DesiredCount `
  --min-capacity 2 --max-capacity 6 --region $region

aws ecs update-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-be --desired-count 2 --region $region

aws ecs update-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-fe --desired-count 2 --region $region
```

## Dọn dẹp toàn bộ

Thứ tự xóa **quan trọng** vì nhiều tài nguyên phụ thuộc lẫn nhau (xóa sai thứ tự sẽ báo lỗi dependency):

| Bước | Tài nguyên                           | Thao tác và lý do về thứ tự                                                                                                                                                                                  |
| ------ | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 2      | CloudFront`neonfoodmap-frontend-cdn` | Disable distribution và chờ trạng thái triển khai hoàn tất trước khi xóa. CloudFront đang tham chiếu S3 origin nên cần xử lý trước.                                                              |
| 3      | ECS services và Auto Scaling          | Xóa`svc-neonfoodmap-be` và `svc-neonfoodmap-fe`; dừng one-off task, deregister scalable target/policy rồi đợi task drain. Sau khi không còn service/task, xóa `NeonFoodmap-cluster`.                |
| 4      | ALB và Target Group                   | Xóa listener/rule, Application Load Balancer`ALB-NeonFoodMap`, rồi xóa `TG-NeonFoodMap-FE` và `TG-NeonFoodMap-BE`. ALB phải được xóa trước các security group mà nó đang dùng.               |
| 5      | S3 buckets                             | Làm rỗng rồi xóa`neonfoodmap-frontend-dev` và mọi bucket media, log của dự án. Với bucket bật Versioning, phải xóa current version, non-current version và delete marker; đảm bảo bucket rỗng. |
| 6      | ECR repositories                       | Không cần rollback image, xóa toàn bộ image rồi xóa`neonfoodmap-backend` và `neonfoodmap-frontend`.                                                                                                    |
| 7      | RDS MySQL                              | Xóa DB instance và sau đó xóa snapshot/backup, DB subnet group`neonfoodmap-rds-subnet-group` và parameter group không còn dùng.                                                                         |
| 8      | Secrets, log và cảnh báo            | Xóa secret cấu hình khi ECS/RDS đã dừng. Xóa CloudWatch log group`/ecs/neonfoodmap-backend`, `/ecs/neonfoodmap-frontend`, alarm, SNS topic `NeonFoodmap-billing-alerts`, Budget và Cost Anomaly.    |
| 9      | NAT Gateway và Elastic IP             | Xóa`NAT-Gateway-AZ1a`, chờ trạng thái deleted rồi release Elastic IP `EIP-NAT-AZ1a`.                                                                                                                      |
| 10     | Security group và mạng               | Xóa security group của ECS/ALB/RDS. Sau đó xóa route table, subnet, Internet Gateway và VPC.                                                                                                                 |
| 11     | IAM và CloudFormation                 | Xóa CloudFormation stack IAM hoặc các role/policy không còn dùng.                                                                                                                                            |

### Nhóm lệnh dọn dẹp

Thay `<...>` bằng ARN hoặc ID lấy từ AWS Console/CLI và kiểm tra đúng account, Region trước khi chạy.

```powershell
$region = "ap-southeast-1"

# 1. Dừng/xóa ECS service trước khi xóa cluster và ALB.
aws ecs delete-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-be --force --region $region
aws ecs delete-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-fe --force --region $region

# 2. Xóa ALB, sau khi ALB đã biến mất mới xóa các target group liên quan.
aws elbv2 delete-load-balancer --load-balancer-arn <ALB_ARN> --region $region
aws elbv2 delete-target-group --target-group-arn <BACKEND_TARGET_GROUP_ARN> --region $region
aws elbv2 delete-target-group --target-group-arn <FRONTEND_TARGET_GROUP_ARN> --region $region

# 3. Làm rỗng bucket không versioning; bucket có Versioning cần xóa thêm versions/delete markers.
aws s3 rm s3://neonfoodmap-frontend-dev --recursive --region $region
aws s3api delete-bucket --bucket neonfoodmap-frontend-dev --region $region

# 4. Chỉ xóa RDS khi final snapshot đã được xác nhận là không cần thiết.
aws rds delete-db-instance --db-instance-identifier neonfoodmap-mysql-db `
  --skip-final-snapshot --region $region

# 5. Xóa image và ECR repository sau khi không cần rollback.
aws ecr delete-repository --repository-name neonfoodmap-backend --force --region $region
aws ecr delete-repository --repository-name neonfoodmap-frontend --force --region $region

# 6. Xóa NAT Gateway, đợi trạng thái deleted rồi mới release Elastic IP.
aws ec2 delete-nat-gateway --nat-gateway-id <NAT_GATEWAY_ID> --region $region
aws ec2 release-address --allocation-id <ELASTIC_IP_ALLOCATION_ID> --region $region
```

## Kiểm chứng không còn tài nguyên tính phí sót lại

Sau cleanup, kiểm tra lại từng tài nguyên tại Region `ap-southeast-1`. Kết quả mong đợi là danh sách rỗng, hoặc `ResourceNotFoundException` đối với tài nguyên đã bị xóa theo tên cụ thể.

```powershell
$region = "ap-southeast-1"

# ECS và ALB
aws ecs list-services --cluster NeonFoodmap-cluster --region $region
aws elbv2 describe-load-balancers --region $region `
  --query "LoadBalancers[?LoadBalancerName=='ALB-NeonFoodMap'].LoadBalancerName"

# RDS, NAT Gateway và Elastic IP
aws rds describe-db-instances --region $region `
  --query "DBInstances[?DBInstanceIdentifier=='neonfoodmap-mysql-db'].DBInstanceIdentifier"
aws ec2 describe-nat-gateways --region $region `
  --filter "Name=state,Values=available" `
  --query "NatGateways[?Tags[?Key=='Name' && Value=='NAT-Gateway-AZ1a']].NatGatewayId"
aws ec2 describe-addresses --region $region `
  --query "Addresses[?Tags[?Key=='Name' && Value=='EIP-NAT-AZ1a']].AllocationId"

# ECR và CloudWatch Logs
aws ecr describe-repositories --region $region `
  --query "repositories[?repositoryName=='neonfoodmap-backend' || repositoryName=='neonfoodmap-frontend'].repositoryName"
aws logs describe-log-groups --region $region `
  --log-group-name-prefix /ecs/neonfoodmap `
  --query "logGroups[].logGroupName"
```
