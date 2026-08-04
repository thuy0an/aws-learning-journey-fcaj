---
title: "Workshop Overview"
date: 2026-08-03
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## Context

**NeonFoodMap** is an automated guide and digital tourism discovery platform for **Vinh Khanh Food Street, District 4, Ho Chi Minh City**. It helps visitors explore food and cultural places through a map, point-of-interest (POI) information, images, and audio guides. The experience can start by geographic location or QR code.

The system serves three main groups: visitors who search for and listen to guides; local business partners who update menus and promotions; and administrators who manage POIs, content, users, and operating status. The application has a React frontend, Django backend, and MySQL database.

**Repository:** [github.com/HaoWasabi/NeonFoodmap](https://github.com/HaoWasabi/NeonFoodmap)

## Problems addressed

Food and tourism businesses often have scattered place information, menus, and media. This takes significant effort to operate and is difficult to scale for many visitors. The infrastructure can also have security risks, such as exposing a database or access key. Manual deployment takes time and lacks immediate cost and incident controls.

**NeonFoodMap** solves these issues by turning content into one central multimedia experience, using secure multi-AZ AWS infrastructure, GitHub OIDC automated CI/CD, and operational monitoring.

## High-level architecture

The system uses a multi-tier design in a VPC across two Availability Zones for high availability:

* **Frontend:** React SPA stored as an S3 static website and delivered through Amazon CloudFront.
* **Backend:** Django/Gunicorn runs on Amazon ECS Fargate in private subnets. It scales task count based on demand and sits behind an Application Load Balancer.
* **Data:** MySQL runs on RDS in private database subnets. S3 stores frontend files, media, audio, and logs in separate buckets.
* **Security and operations:** IAM and CloudFormation manage permissions and infrastructure. CloudWatch collects logs and metrics; Amazon SNS, AWS Budgets, and Cost Anomaly Detection provide alerts and cost monitoring.
* **CI/CD:** GitHub Actions authenticates with OIDC/AWS STS, builds and pushes Docker images to Amazon ECR, and updates the ECS service.

{{< event-image src="images/2-Proposal/platform_architecture.jpg" alt="Overall platform architecture on AWS" >}}

## Technology stack

| Layer | Technology/services | Role in NeonFoodMap |
| --- | --- | --- |
| Frontend | React, Vite, Nginx, Docker | Builds the SPA, creates assets, and serves the web application |
| Backend | Django, Gunicorn, Python, Docker | Provides APIs, business logic, database access, and S3 access |
| Network | Amazon VPC, public/private subnets, Internet Gateway, NAT Gateway, Application Load Balancer | Separates network layers, provides required Internet access, and routes application requests |
| Database | Amazon RDS MySQL | Stores business data with private access, backups, encryption, and a multi-AZ configuration where needed |
| Storage/CDN | Amazon S3, Amazon CloudFront, Origin Access Control | Stores frontend files, media, audio, and logs, and delivers static content securely |
| Containers | Amazon ECR, Amazon ECS Fargate | Stores Docker images and runs frontend/backend containers |
| CI/CD | GitHub Actions, GitHub OIDC, AWS STS, IAM Role | Tests, builds, pushes images, and deploys without long-lived AWS keys |
| Monitoring/cost | Amazon CloudWatch, Amazon SNS, AWS Budgets, Cost Anomaly Detection | Collects logs and metrics, sends technical alerts, and tracks costs |

## Results achieved

- Completed an AWS platform with a multi-AZ VPC, private RDS MySQL, S3, IAM, and cost monitoring.
- Packaged frontend and backend with Docker, managed images in Amazon ECR, and tested images before deployment.
- Deployed ECS Fargate behind ALB and target groups; checked frontend–backend connectivity, health checks, logs, and configuration errors in CloudWatch.
- Automated build, push, and deployment with GitHub Actions and OIDC; monitored ECS rollouts after each update.
- Completed CloudFront delivery, tested the main application flows, and added operations and cleanup checklists.
