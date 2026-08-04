---
title: "System Architecture"
date: 2026-08-03
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---
## VPC design

All NeonFoodMap infrastructure is in a dedicated VPC. The VPC spans two Availability Zones, distributing the Application Load Balancer, ECS services, and database subnet group to support availability and application scaling.

| Component                       | Details                                                                                                              |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| VPC                             | `10.0.0.0/16`, Region `ap-southeast-1`                                                                           |
| Availability Zones              | `ap-southeast-1a`, `ap-southeast-1b`                                                                             |
| Public subnets (2)              | Hold the Application Load Balancer and NAT Gateway; enable auto-assigned public IPv4 when needed                     |
| Private application subnets (2) | Hold frontend/backend ECS Fargate tasks; tasks have no public IP and accept traffic only from the ALB security group |
| Private database subnets (2)    | Used by the RDS DB subnet group; RDS MySQL is private and accepts MySQL/3306 only from the ECS task security group   |
| Additional private subnets (2)  | Complete the four-private-subnet Sprint model for workload separation or future scaling by AZ                        |
| Internet Gateway                | Attached to the VPC for the ALB and NAT Gateway outbound route; it does not expose private subnets directly          |
| NAT Gateway                     | Public NAT Gateway with an Elastic IP; private subnets route`0.0.0.0/0` to it for controlled outbound access       |

Create the VPC through **VPC Console → Create VPC → VPC and more**, rather than a script. This creates the VPC, subnets, route tables, and Internet Gateway together. Then allocate an Elastic IP, create the NAT Gateway, and add private-subnet routes. Detailed steps and checks are in [5.4.1 – VPC and RDS MySQL Foundation](../5.4-Onprem/5.4.1-foundation/).

## IAM and permission management

### Main components

| Component type | Name                          | Purpose                                                                                                                                       |
| -------------- | ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| Policy         | `ForceMFAPolicy`            | When MFA is not enabled, allows only account viewing and MFA self-setup; blocks other AWS actions                                             |
| Group          | `NeonFoodmap-DevOps-Admins` | Manages project infrastructure and cost configuration                                                                                         |
| Group          | `NeonFoodmap-Backend-Devs`  | Uses ECS, ECR, RDS, S3, and CloudWatch for backend work                                                                                       |
| Group          | `NeonFoodmap-Frontend-Devs` | Manages S3/CloudFront and has template-defined read-only ECR/CloudWatch access                                                                |
| OIDC provider  | `GitHubOIDCProvider`        | Connects`token.actions.githubusercontent.com` to audience `sts.amazonaws.com` so GitHub Actions can receive temporary AWS STS credentials |

{{< event-image src="images/5-Workshop/5.3-Structure/picUserGroup.jpg" alt="IAM user groups" >}}

## IAM roles

| Role                                   | Attached to                                | Main permissions                                                                                                    |
| -------------------------------------- | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| `NeonFoodmap-ECS-Backend-Role`       | Backend ECS task                           | Amazon S3, CloudWatch Logs, and RDS Data API through the template's managed policy; used by the running application |
| `NeonFoodmap-ECS-TaskExecution-Role` | ECS task platform                          | Pulls ECR images and sends container logs to CloudWatch through`AmazonECSTaskExecutionRolePolicy`                 |
| `NeonFoodmap-GitHub-Actions-Role`    | GitHub Actions through OIDC, no access key | Pushes images to ECR and updates ECS services; its trust policy accepts only`HaoWasabi/NeonFoodmap`               |
| `NeonFoodmap-EC2-Backend-Role`       | EC2 backend/instance profile (fallback)    | Amazon S3 and CloudWatch Logs when the backend runs on EC2 instead of ECS                                           |

{{< event-image src="images/5-Workshop/5.3-Structure/picDetailRole.jpg" alt="IAM role details" >}}

{{< event-image src="images/5-Workshop/5.3-Structure/picIAMRole.jpg" alt="IAM roles" >}}

`ECS-Backend-Role` and `ECS-TaskExecution-Role` are **separate**. The execution role lets the ECS platform pull images and write logs; the backend role grants permissions to the code running inside the container. GitHub Actions has its own OIDC role, so the pipeline does not use the application's runtime permissions. This separation reduces the impact of an incorrect permission grant.
