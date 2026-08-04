---
title: "Checking ECS, CI/CD Pipeline, ALB, and CloudWatch"
date: 2026-08-03
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## Check ECR and ECS status

### Check Amazon ECR repositories

The two repositories for the frontend and backend have been created, contain images that ECS can pull, and are working normally.

- **neonfoodmap-backend:** stores images pulled when backend ECS tasks are deployed.

{{< event-image src="images/5-Workshop/5.5-Policy/picECR1.jpg" alt="Status of the NeonFoodMap backend ECR repository" >}}

- **neonfoodmap-frontend:** stores images pulled when the frontend service is deployed.

{{< event-image src="images/5-Workshop/5.5-Policy/picECR2.jpg" alt="Status of the NeonFoodMap frontend ECR repository" >}}

### Check the cluster and ECS services

In **NeonFoodmap-cluster**, confirm the following:

- **Cluster status:** `Active`.
- **Services:** `2 active` services: `svc-neonfoodmap-be` and `svc-neonfoodmap-fe`.
- **Tasks:** `4 running`; each service keeps `2/2 Tasks running`.

{{< event-image src="images/5-Workshop/5.5-Policy/picECSCheck2.jpg" alt="NeonFoodMap ECS cluster and service status" >}}

### Check tasks and private IP addresses

Open the **Tasks** tab to confirm that Fargate containers pulled their images from ECR and started successfully.

- **Launch type:** `Fargate`, platform version `1.4.0`.
- **Last / Desired status:** all four tasks are `Running / Running`.
- **Frontend:** `svc-neonfoodmap-fe` uses task definition revision `:17`, with private IPs `10.0.24.199` and `10.0.12.222`.
- **Backend:** `svc-neonfoodmap-be` uses task definition revision `:36`, with private IPs `10.0.9.254` and `10.0.30.15`.

{{< event-image src="images/5-Workshop/5.5-Policy/picECSCheck3.jpg" alt="NeonFoodMap ECS tasks and services" >}}

### Check container logs and health checks

Review CloudWatch Logs for `svc-neonfoodmap-be` to confirm that the application responds normally to health checks:

- **Target path:** `GET /health` HTTP/1.1.
- **HTTP status:** continuous `200 OK` responses from private IP addresses.
- **Result:** the application receives requests and responds normally; no service interruption has been recorded.

{{< event-image src="images/5-Workshop/5.5-Policy/picECSCheck1.jpg" alt="CloudWatch logs and backend ECS service health checks" >}}

## Check CI/CD deployment status

After configuring the automated pipeline, verify the deployment path from GitHub Actions to Amazon ECS.

### Check the GitHub Actions CI/CD pipeline

Review the workflow run for `NeonFoodmap CI/CD Pipeline`.

{{< event-image src="images/5-Workshop/5.5-Policy/picCICD3.jpg" alt="NeonFoodMap CI/CD pipeline" >}}

### Check the backend deployment

Verify `svc-neonfoodmap-be` after it receives the deployment request from GitHub Actions:

- **Deployment status:** `Success` (`Active`).
- **Circuit breaker / health check:** `Monitoring complete` and `Configured`; no rollback error was recorded.

{{< event-image src="images/5-Workshop/5.5-Policy/picCICD1.jpg" alt="Backend ECS deployment result" >}}

### Check the frontend deployment

Verify `svc-neonfoodmap-fe`:

- **Deployment status:** `Success` (`Active`).
- **Circuit breaker / health check:** `Monitoring complete` and `Configured`, so service remains available during the update.

{{< event-image src="images/5-Workshop/5.5-Policy/picCICD2.jpg" alt="Frontend ECS deployment result" >}}

## Check Application Load Balancing

Confirm that `ALB-NeonFoodMap` routes traffic correctly with five path-based rules and that all four task IPs are `Healthy`.

{{< event-image src="images/5-Workshop/5.5-Policy/picALB.jpg" alt="ALB routing and healthy targets" >}}

## Check the CloudWatch dashboard and monitoring

Amazon CloudWatch provides centralized monitoring for the ECS cluster, `ALB-NeonFoodMap`, and automated alerts.

### 1. CloudWatch alarms

**ECS CPU alarm (`ECS-Backend-HighCPU-Alarm`):** watches `TaskCpuUtilization` at `>= 80%` for one five-minute data point. Its current state is **OK** because CPU use is low.

**ALB 5XX error alarm (`ALB-5XX-Error-Alarm`):** watches `HTTPCode_Target_5XX_Count` at `>= 10` for one one-minute data point. Its state is **Insufficient data** because no server-side 5XX errors occurred during the observed period.

{{< event-image src="images/5-Workshop/5.5-Policy/CloudWatchOverview.jpg" alt="CloudWatch alarms overview" >}}

### 2. ECS cluster metrics

Monitoring `NeonFoodmap-cluster` shows stable resource use:

- **CPU utilization:** low on average, peaking at `48.96%`, below the 80% alert threshold.
- **Memory utilization:** around `25.49%`.
- **Disk utilization:** highest value `1.56%`.
- **Network traffic:** peak `32.41 KB/s`.
- **Service and task count:** `2` active services and `4` running tasks; task count reached `8` during deployments or scaling.

{{< event-image src="images/5-Workshop/5.5-Policy/CloudWatchECS.jpg" alt="CloudWatch ECS metrics" >}}

### 3. Application Load Balancer health and performance

The `ALB-NeonFoodMap` dashboard reports:

- **Availability and errors:** `100%` availability and no server-side `5XX` errors.
- **Requests:** peak traffic of `252 requests/period`.
- **Latency:** very low, from `3.6 × 10⁻⁴ ms` to `5.2 × 10⁻³ ms`, under `0.01 ms`.
- **Client errors:** some `4XX` errors, peaking at `45 requests`, mainly from invalid endpoints or expired tokens.

{{< event-image src="images/5-Workshop/5.5-Policy/CloudWatchALB.jpg" alt="CloudWatch ALB metrics" >}}

### 4. Summary

The ECS Fargate and ALB infrastructure runs steadily. CPU (about 25–48%) and memory (about 25%) have enough capacity. ALB latency is low, and no backend/server 5XX errors appeared during the test. CloudWatch alarms send SNS notifications when metrics exceed their thresholds, helping the team detect operational issues early.
