---
title: "ECS Auto Scaling"
date: 2026-08-03
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---
## Configure ECS Service Auto Scaling

Auto Scaling is applied to the backend service and uses the task definition, ALB, and target group created in section 5.4.3.

### Enable Auto Scaling and the CPU policy

1. Open `svc-neonfoodmap-be`, go to **Service auto scaling**, and select **Update**.
2. Enable **Use service auto scaling**. Set the minimum task count to `2` and the maximum to `6` so the backend always has two ready tasks while controlling cost. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picECSScaling2.jpg" alt="picECSScaling2" >}}
3. Create a **Target tracking** policy named `cpu-70-target-tracking` with the **ECSServiceAverageCPUUtilization** metric.
4. Set the target value to `70`. When average CPU exceeds 70%, ECS adds tasks. When it drops, ECS can remove tasks but never below two.
5. Set the scale-out cooldown to `60 seconds` and the scale-in cooldown to `300 seconds`, then save the policy. This lets new tasks register before the next scale-out and prevents rapid scale changes. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picECSScaling45.jpg" alt="picECSScaling45" >}}

After saving, the `cpu-70-target-tracking` policy will monitor the service's CPU usage within a range of 2 to 6 tasks.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picECSScaling7.jpg" alt="picECSScaling7" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picECSScaling6.jpg" alt="picECSScaling6" >}}

## Configure CloudFront for the frontend and audio

CloudFront delivers the frontend through a CDN while the S3 bucket remains private. This environment uses the default `*.cloudfront.net` domain and does not use Route 53 or a custom domain.

### Create the CloudFront distribution

Open the CloudFront Console → Distributions and select **Create distribution**, then enter the following parameters:

**Distribution name:** Enter the name `neonfoodmap-frontend-cdn`.

**Description – optional:** Leave blank or enter `CloudFront CDN for NeonFoodmap Frontend and API`.

**Distribution type:** Keep the **Single website or app** option selected.

**Domain (Route 53 managed domain – optional):** Leave blank, as the project uses the default `*.cloudfront.net` URL provided by AWS.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picCDN1.jpg" alt="picCDN1" >}}

### Configure the S3 origin and OAC

**Origin type:** Select **Amazon S3**.

**S3 origin:** Select the bucket `neonfoodmap-frontend-dev.s3.ap-southeast-1.amazonaws.com`.

**Origin path – optional:** Leave this blank; do not enter `/path` because the frontend is stored in the bucket's root directory.

**Allow private S3 bucket access to CloudFront:** Keep the **Allow private S3 bucket access to CloudFront – Recommended** option selected. This is the **Origin Access Control (OAC)** feature, which allows CloudFront to read the private bucket while preventing users from accessing S3 directly.

**Origin settings:** Keep the **Use recommended origin settings** option.

**Cache settings:** Keep the **Use recommended cache settings tailored to serving S3 content** option.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picCDN2.jpg" alt="picCDN2" >}}

### Update the ALB origin

After initialization, go to **Distributions**, select the newly created distribution, open the **Origins** tab, and edit the associated Elastic Load Balancing origin. Set the *Protocol* to **HTTP only** to match the current ALB/API configuration and avoid communication errors or `400 Bad Request` responses caused by protocol mismatches.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picCDN3.jpg" alt="picCDN3" >}}

Wait for the distribution status to become **Enabled** and the update to complete, then open the actual deployment URL.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.4-ecs-autoscaling/picUI.jpg" alt="picUI" >}}
