---
title: "ECS Auto Scaling and CloudFront"
date: 2026-08-03
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

## Configure ECS Service Auto Scaling

Auto Scaling is applied to the backend service and uses the task definition, ALB, and target group created in section 5.4.3.

### Enable Auto Scaling and the CPU policy

1. Open `svc-neonfoodmap-be`, go to **Service auto scaling**, and select **Update**.
2. Enable **Use service auto scaling**. Set the minimum task count to `2` and the maximum to `6` so the backend always has two ready tasks while controlling cost.
3. Create a **Target tracking** policy named `cpu-70-target-tracking` with the **ECSServiceAverageCPUUtilization** metric.
4. Set the target value to `70`. When average CPU exceeds 70%, ECS adds tasks. When it drops, ECS can remove tasks but never below two.
5. Set the scale-out cooldown to `60 seconds` and the scale-in cooldown to `300 seconds`, then save the policy. This lets new tasks register before the next scale-out and prevents rapid scale changes.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picECSScaling7.jpg" alt="ECS Auto Scaling policy" >}}

## Configure CloudFront for the frontend and audio

CloudFront delivers the frontend through a CDN while the S3 bucket remains private. This environment uses the default `*.cloudfront.net` domain and does not use Route 53 or a custom domain.

### Create the CloudFront distribution

Open **CloudFront Console → Distributions** and select **Create distribution**. Use `neonfoodmap-frontend-cdn` as the distribution name, keep **Single website or app**, and leave the Route 53 managed domain empty.

### Configure the S3 origin and OAC

Choose **Amazon S3** as the origin type and select `neonfoodmap-frontend-dev.s3.ap-southeast-1.amazonaws.com`. Leave the origin path empty because the frontend is at the bucket root. Keep **Allow private S3 bucket access to CloudFront – Recommended** to use Origin Access Control (OAC), and keep the recommended origin and cache settings.

### Update the ALB origin

After creating the distribution, open its **Origins** tab and edit the Elastic Load Balancing origin. Set the protocol to **HTTP only** to match the current ALB/API and avoid a protocol mismatch or `400 Bad Request` response. Wait for the distribution to be **Enabled** and fully deployed, then open its URL.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picUI.jpg" alt="Deployed NeonFoodMap interface" >}}
{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picECSScaling2.jpg" alt="ECS Auto Scaling capacity limits" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picECSScaling45.jpg" alt="ECS CPU target-tracking policy" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picECSScaling6.jpg" alt="ECS Auto Scaling configuration" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picCDN1.jpg" alt="Create CloudFront distribution" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picCDN2.jpg" alt="CloudFront S3 origin and OAC configuration" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4/picCDN3.jpg" alt="CloudFront ALB origin configuration" >}}
