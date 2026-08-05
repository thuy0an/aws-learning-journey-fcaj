---
title: "Workshop"
date: 2026-08-04
weight: 5
chapter: false
pre: " <b> 5. </b> "
---
## Workshop: Deploy NeonFoodMap on AWS

This workshop explains how to deploy **NeonFoodMap**, a digital food and tourism discovery platform, on AWS. The application includes a React frontend, Django backend, MySQL database, points-of-interest (POI) data, audio guides, tours, and sandbox payments.

The infrastructure is designed and operated in **Asia Pacific (Singapore) `ap-southeast-1`** with a multi-Availability Zone VPC. The guide covers infrastructure design, access control, application packaging, deployment, monitoring, and resource cleanup.

The main services are:

* **Compute and delivery:** Amazon ECS Fargate and Application Load Balancer run the containerized Django backend; Amazon S3 and CloudFront deliver the React SPA and static assets.
* **Data, security, and CI/CD:** Amazon RDS MySQL stores application data; GitHub Actions authenticates through OIDC/AWS STS so deployment does not need long-lived access keys; CloudWatch provides logs, metrics, and alerts.

## Contents

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [System architecture](5.3-Structure/)
4. [Deployment steps](5.4-Onprem/)
5. [Verify deployment and system monitoring](5.5-Policy/)
6. [Application interface and features](5.6-Project-Visual/)
7. [Clean up resources](5.7-Cleanup/)
