---
title: "Deployment Steps"
date: 2026-08-04
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

This section deploys NeonFoodMap to AWS in the required order. It starts with networking and the database, then prepares storage and Docker images, deploys the application to ECS Fargate, and finally adds Auto Scaling and CloudFront.

## Deployment sections

1. [VPC and RDS MySQL foundation](5.4.1-foundation/) — create a VPC across two Availability Zones, public and private subnets, NAT Gateway, route tables, and Amazon RDS MySQL in private subnets.
2. [S3, ECR, and Docker](5.4.2-storage-identity-containers/) — create buckets for application assets, configure ECR repositories, and build and test frontend and backend Docker images.
3. [GitHub Actions, ECS, and Application Load Balancer](5.4.3-delivery-application/) — configure GitHub OIDC, IAM roles, ECS cluster, task definitions, service discovery, ECS services, and the ALB to route traffic to the application.
4. [ECS Auto Scaling and CloudFront](5.4.4-scaling-cdn-operations/) — configure ECS scaling policies, CloudFront, S3 Origin Access Control, and the ALB origin.

{{< event-image src="images/2-Proposal/platform_architecture.jpg" alt="Overall platform architecture on AWS" >}}
