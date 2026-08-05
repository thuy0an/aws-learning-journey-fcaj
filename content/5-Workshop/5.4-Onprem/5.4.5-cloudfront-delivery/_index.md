---
title: "ECS Auto Scaling and CloudFront"
date: 2026-08-03
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---

## Configure ECS Service Auto Scaling

Auto Scaling is applied to the backend service, inheriting the task definition, ALB, and target group from section 5.4.4.

### Enable Auto Scaling and the CPU policy

1. Open the `svc-neonfoodmap-be` service, go to **Service auto scaling**, and select **Update**.
2. Enable **Use service auto scaling**. Under *Capacity limits*, set the minimum task count to `2` and the maximum to `6`. This keeps two backend tasks ready to receive requests while still limiting the scale-out range to control cost. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling2.jpg" alt="picECSScaling2" >}}
3. In the scaling policy section, select **Target tracking**. Name the policy `cpu-70-target-tracking` and choose the **ECSServiceAverageCPUUtilization** metric.
4. Set the *Target value* to `70`. When average CPU exceeds 70%, ECS Service Auto Scaling adds tasks to share the load. When CPU drops, ECS can remove tasks, but never below the minimum of two tasks.
5. Set *Scale-out cooldown* to `60 seconds` and *Scale-in cooldown* to `300 seconds`, then save the policy. After each scale-out event, ECS waits 60 seconds for the new task to start and register with the target group. Scale-in waits 5 minutes to avoid rapid task churn during fluctuating load. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling45.jpg" alt="picECSScaling45" >}}

After saving, the `cpu-70-target-tracking` policy monitors the service CPU within the `2` to `6` task range.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling7.jpg" alt="picECSScaling7" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling6.jpg" alt="picECSScaling6" >}}

## Configure CloudFront for the frontend and audio

CloudFront delivers the frontend through a CDN while the S3 bucket remains private. This environment uses the default CloudFront domain and does not configure Route 53 or a custom domain.

### Create the CloudFront distribution

Open the CloudFront Console → Distributions and select **Create distribution**, then fill in the following settings:

**Distribution name:** Enter `neonfoodmap-frontend-cdn`.

**Description – optional:** Leave blank or enter `CloudFront CDN for NeonFoodmap Frontend and API`.

**Distribution type:** Keep **Single website or app** selected.

**Domain (Route 53 managed domain – optional):** Leave blank because the project uses the default `*.cloudfront.net` URL provided by AWS.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picCDN1.jpg" alt="picCDN1" >}}

### Configure the S3 origin and OAC

**Origin type:** Select **Amazon S3**.

**S3 origin:** Select the `neonfoodmap-frontend-dev.s3.ap-southeast-1.amazonaws.com` bucket.

**Origin path – optional:** Leave this blank; do not enter `/path` because the frontend is stored in the root of the bucket.

**Allow private S3 bucket access to CloudFront:** Keep **Allow private S3 bucket access to CloudFront – Recommended** selected. This is the **Origin Access Control (OAC)** feature, which allows CloudFront to read the private bucket while preventing direct S3 access.

**Origin settings:** Keep **Use recommended origin settings** selected.

**Cache settings:** Keep **Use recommended cache settings tailored to serving S3 content** selected.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picCDN2.jpg" alt="picCDN2" >}}

### Update the ALB origin

After initialization, go to **Distributions**, open the new distribution, switch to the **Origins** tab, and edit the associated Elastic Load Balancing origin. Set *Protocol* to **HTTP only** to match the current ALB/API configuration and avoid communication errors or `400 Bad Request` responses caused by protocol mismatches.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picCDN3.jpg" alt="picCDN3" >}}

Wait until the distribution status becomes **Enabled** and the update completes, then open the deployed URL.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picUI.jpg" alt="picUI" >}}