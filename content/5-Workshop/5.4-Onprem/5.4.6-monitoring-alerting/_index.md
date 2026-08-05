---
title: "CloudWatch Monitoring and Alerting"
date: 2026-08-05
weight: 6
chapter: false
pre: " <b> 5.4.6. </b> "
---
After the application is deployed, configure CloudWatch to monitor logs and resource health, and to receive alerts when an abnormal condition occurs. This section covers the main operations; section 5.5 verifies the resulting data after deployment.

## 1. Collect logs and use Logs Insights

### Configure ECS log groups

1. In the frontend and backend task definitions, select the **awslogs** log driver, use Region `ap-southeast-1`, and configure these log groups.

| Service      | Log group                     |
| ------------ | ----------------------------- |
| Backend ECS  | `/ecs/neonfoodmap-backend`  |
| Frontend ECS | `/ecs/neonfoodmap-frontend` |

2. Open **CloudWatch → Log groups**, open each group, choose **Actions → Edit retention setting**, and set retention to `30 days`.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image079.png" alt="CloudWatch Log Groups list" >}}

3. After the ECS service starts tasks, check the log streams to confirm that the containers are writing logs.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image106.png" alt="ECS backend log streams in CloudWatch" >}}

### Save Logs Insights queries

1. Open **CloudWatch → Logs Insights** and select the backend log group.
2. Create, run, and save operational queries to find errors/exceptions, check health checks, and filter 5XX requests. Adapt fields and patterns to the application log format.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image105.png" alt="Open CloudWatch Logs Insights" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image107.png" alt="Save a CloudWatch Logs Insights query" >}}

## 2. Create an operational dashboard

1. Open **CloudWatch → Dashboards → Create dashboard** and name it `NeonFoodMap-Operational-Dashboard`.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image067.png" alt="Create a CloudWatch dashboard" >}}

2. Choose **Add widget**, then select an appropriate widget type such as **Line** or **Number**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image068.png" alt="Add a CloudWatch dashboard widget" >}}

3. Add key metrics: ECS CPU/memory and task count; ALB request count, target response time, healthy hosts, and 5XX errors; RDS CPU, connection count, and free storage.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image070.png" alt="Select Application Load Balancer metrics" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image076.png" alt="CloudWatch dashboard with ECS, ALB, and Logs Insights metrics" >}}

## 3. Create alarms and SNS notifications

1. Create an SNS topic named `NeonFoodMap-Alerts` in **Amazon SNS → Topics**, then create and confirm an **Email** subscription.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image047.png" alt="Create an SNS topic" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image048.png" alt="Create an SNS email subscription" >}}

2. Open **CloudWatch → Alarms → Create alarm**, choose a metric, set its threshold, and select `NeonFoodMap-Alerts` as the notification action.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image050.png" alt="Create a CloudWatch alarm" >}}

3. Create these two alarms: {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image055.png" alt="Tạo Alarm 1" >}} {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image056.png" alt="Tạo Alarm 2" >}}

| Alarm                         | Metric                              | Threshold                    |
| ----------------------------- | ----------------------------------- | ---------------------------- |
| `ECS-Backend-HighCPU-Alarm` | `ECSServiceAverageCPUUtilization` | `>= 80%` for `5 minutes` |
| `ALB-5XX-Error-Alarm`       | `HTTPCode_Target_5XX_Count`       | `>= 10` for `1 minute`   |

4. Confirm that each alarm is **OK** or **Insufficient data**. Test alarms in a test environment only; do not create artificial errors in production.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image065.png" alt="Configured CloudWatch alarms" >}}

## 4. Enable VPC Flow Logs (optional)

1. Open **Amazon VPC → Your VPCs**, select the system VPC, and open the **Flow Logs** tab.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image098.png" alt="Select a VPC to configure Flow Logs" >}}

2. Choose **Create flow log**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image100.png" alt="Create a VPC Flow Log" >}}

3. Set **Traffic type** to `All`, set **Destination** to `CloudWatch Logs`, then choose a dedicated log group and an appropriate IAM role.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image102.png" alt="Configure CloudWatch Logs as the VPC Flow Logs destination" >}}

4. Create the Flow Log and confirm that its state is `Active`. Enable Flow Logs only when needed because log storage and queries incur costs.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image103.png" alt="VPC Flow Log created with CloudWatch Logs as the destination" >}}
