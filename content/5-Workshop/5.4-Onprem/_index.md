---
title: "Deployment Steps"
date: 2026-08-04
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

This section deploys NeonFoodMap to AWS in the required order. It starts with networking and the database, then prepares storage and Docker images, deploys the application to ECS Fargate, and finally adds Auto Scaling, CloudFront, and observability.

## Deployment sections

1. [VPC and RDS MySQL foundation](5.4.1-vpc-rds/) — create a VPC across two Availability Zones, public and private subnets, NAT Gateway, route tables, and Amazon RDS MySQL in private subnets.
2. [S3, ECR, and Docker](5.4.2-s3-ecr-docker/) — create buckets for application assets, configure ECR repositories, and build and test frontend and backend Docker images.
3. [GitHub Actions, ECS, and Application Load Balancer](5.4.3-oidc-ecs-alb/) — configure GitHub OIDC, IAM roles, ECS cluster, task definitions, service discovery, ECS services, and the ALB to route traffic to the application.
4. [Amazon ECS Fargate and Application Load Balancer](5.4.4-ecs-autoscaling/) — create Cloud Map service discovery, ECS task definitions and services, target groups, and an ALB.
5. [ECS Auto Scaling and CloudFront](5.4.5-cloudfront-delivery/) — configure ECS scaling policies, CloudFront, S3 Origin Access Control, and the ALB origin.
6. [Set up system observability with CloudWatch](5.4.6-monitoring-alerting/) — configure logs, Log Insights queries, a dashboard, alarms, and SNS notifications.

{{< event-image src="images/2-Proposal/platform_architecture.jpg" alt="Overall platform architecture on AWS" >}}
