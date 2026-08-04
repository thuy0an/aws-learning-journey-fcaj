---
title: "Clean Up Resources"
date: 2026-08-03
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## Clean up resources

Many AWS services charge for running time or stored data even when nobody uses the application. For NeonFoodMap, watch Amazon ECS Fargate, Application Load Balancer, NAT Gateway, Amazon RDS MySQL, Elastic IP, Amazon S3, Amazon ECR, and CloudWatch Logs closely.

## Save costs during development

During development, the team can stop time-based resources after each work session. This **does not delete data or configuration**, but the application will not be available until resources are started again.

| NeonFoodMap resource | Cost-saving action | Notes |
| --- | --- | --- |
| ECS backend `svc-neonfoodmap-be` | Set desired task count to `0` and Auto Scaling minimum to `0` | The backend has Auto Scaling. If only desired count is reduced while minimum remains `2`, ECS recreates tasks. |
| ECS frontend `svc-neonfoodmap-fe` | Set desired task count to `0` | The frontend and API are unavailable through the ALB without healthy tasks. |
| RDS MySQL `neonfoodmap-mysql-db` | Stop the DB instance when the app is not needed | RDS still charges for storage and backups while stopped and may restart after up to seven days. Do not use this with RDS configurations that do not support stopping, such as Multi-AZ. |

AWS CLI commands to reduce costs while developing:

```powershell
# Pause at the end of the day: lower both the backend Auto Scaling minimum and desired count.
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

# Start again before a work session: restore Auto Scaling limits and task counts.
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

## Full cleanup

Deletion order **matters** because resources depend on one another. Deleting in the wrong order can cause dependency errors.

| Step | Resource | Action and reason |
| --- | --- | --- |
| 2 | CloudFront `neonfoodmap-frontend-cdn` | Disable the distribution and wait for deployment to finish before deletion. It references the S3 origin. |
| 3 | ECS services and Auto Scaling | Delete `svc-neonfoodmap-be` and `svc-neonfoodmap-fe`; stop one-off tasks, deregister scalable targets and policies, and wait for tasks to drain. Delete `NeonFoodmap-cluster` when no services or tasks remain. |
| 4 | ALB and target groups | Delete listeners/rules, `ALB-NeonFoodMap`, then `TG-NeonFoodMap-FE` and `TG-NeonFoodMap-BE`. The ALB must be deleted before its security groups. |
| 5 | S3 buckets | Empty and delete `neonfoodmap-frontend-dev` and all project media/log buckets. For versioned buckets, delete current and noncurrent versions plus delete markers. |
| 6 | ECR repositories | When rollback images are no longer needed, delete all images and then `neonfoodmap-backend` and `neonfoodmap-frontend`. |
| 7 | RDS MySQL | Delete the DB instance, then unused snapshots/backups, `neonfoodmap-rds-subnet-group`, and parameter groups. |
| 8 | Secrets, logs, and alarms | Delete configuration secrets after ECS/RDS stops. Delete `/ecs/neonfoodmap-backend`, `/ecs/neonfoodmap-frontend`, alarms, SNS topic `NeonFoodmap-billing-alerts`, Budget, and Cost Anomaly Detection. |
| 9 | NAT Gateway and Elastic IP | Delete `NAT-Gateway-AZ1a`, wait for `deleted`, then release `EIP-NAT-AZ1a`. |
| 10 | Security groups and network | Delete ECS/ALB/RDS security groups, then route tables, subnets, Internet Gateway, and VPC. |
| 11 | IAM and CloudFormation | Delete the IAM CloudFormation stack or unused roles and policies. |

### Cleanup commands

Replace `<...>` with ARNs or IDs from the AWS Console/CLI. Confirm the correct AWS account and Region before running these commands.

```powershell
$region = "ap-southeast-1"

# 1. Delete ECS services before deleting the cluster and ALB.
aws ecs delete-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-be --force --region $region
aws ecs delete-service --cluster NeonFoodmap-cluster `
  --service svc-neonfoodmap-fe --force --region $region

# 2. Delete the ALB; delete target groups only after the ALB is gone.
aws elbv2 delete-load-balancer --load-balancer-arn <ALB_ARN> --region $region
aws elbv2 delete-target-group --target-group-arn <BACKEND_TARGET_GROUP_ARN> --region $region
aws elbv2 delete-target-group --target-group-arn <FRONTEND_TARGET_GROUP_ARN> --region $region

# 3. Empty a non-versioned bucket. Versioned buckets also need versions/delete markers removed.
aws s3 rm s3://neonfoodmap-frontend-dev --recursive --region $region
aws s3api delete-bucket --bucket neonfoodmap-frontend-dev --region $region

# 4. Delete RDS only after confirming that a final snapshot is not needed.
aws rds delete-db-instance --db-instance-identifier neonfoodmap-mysql-db `
  --skip-final-snapshot --region $region

# 5. Delete images and ECR repositories after rollback is no longer needed.
aws ecr delete-repository --repository-name neonfoodmap-backend --force --region $region
aws ecr delete-repository --repository-name neonfoodmap-frontend --force --region $region

# 6. Delete the NAT Gateway; wait for deletion before releasing the Elastic IP.
aws ec2 delete-nat-gateway --nat-gateway-id <NAT_GATEWAY_ID> --region $region
aws ec2 release-address --allocation-id <ELASTIC_IP_ALLOCATION_ID> --region $region
```

## Verify that no chargeable resources remain

After cleanup, check each resource in `ap-southeast-1`. The expected result is an empty list, or `ResourceNotFoundException` for a resource deleted by its specific name.

```powershell
$region = "ap-southeast-1"

# ECS and ALB
aws ecs list-services --cluster NeonFoodmap-cluster --region $region
aws elbv2 describe-load-balancers --region $region `
  --query "LoadBalancers[?LoadBalancerName=='ALB-NeonFoodMap'].LoadBalancerName"

# RDS, NAT Gateway, and Elastic IP
aws rds describe-db-instances --region $region `
  --query "DBInstances[?DBInstanceIdentifier=='neonfoodmap-mysql-db'].DBInstanceIdentifier"
aws ec2 describe-nat-gateways --region $region `
  --filter "Name=state,Values=available" `
  --query "NatGateways[?Tags[?Key=='Name' && Value=='NAT-Gateway-AZ1a']].NatGatewayId"
aws ec2 describe-addresses --region $region `
  --query "Addresses[?Tags[?Key=='Name' && Value=='EIP-NAT-AZ1a']].AllocationId"

# ECR and CloudWatch Logs
aws ecr describe-repositories --region $region `
  --query "repositories[?repositoryName=='neonfoodmap-backend' || repositoryName=='neonfoodmap-frontend'].repositoryName"
aws logs describe-log-groups --region $region `
  --log-group-name-prefix /ecs/neonfoodmap `
  --query "logGroups[].logGroupName"
```
