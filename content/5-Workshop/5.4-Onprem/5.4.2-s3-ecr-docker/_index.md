---
title: "S3, ECR, and Docker"
date: 2026-08-03
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

In this section, the team creates Amazon S3 buckets for each data type, configures Amazon ECR to store Docker images, and tests frontend and backend images before deploying to ECS. IAM and OIDC permissions are covered in [5.3 System Architecture](../../5.3-Structure/).

## Create and configure S3 buckets

### Create project buckets

1. Sign in to the AWS Console in `ap-southeast-1`, open **Amazon S3**, and select **Buckets → Create bucket**.
2. Use the name format `neonfoodmap-<purpose>-dev`. Create `neonfoodmap-frontend-dev`, `neonfoodmap-media-dev`, and `neonfoodmap-logs-dev`. Use `ap-southeast-1`, the same Region as VPC, RDS, and ECS, to reduce latency and transfer costs.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picCreateS3_1.jpg" alt="Create a NeonFoodMap bucket" >}}

3. Keep **Block all public access** and default **SSE-S3** encryption enabled. Buckets must not be public; only authorized IAM roles and AWS services may access them.
4. Keep the remaining settings and select **Create bucket**.

| Bucket | Purpose | Initial versioning |
| --- | --- | --- |
| `neonfoodmap-frontend-dev` | Static assets and frontend build | Disabled |
| `neonfoodmap-media-dev` | Images and media files | Enabled in the next step |
| `neonfoodmap-logs-dev` | Long-term system logs | Disabled |

Check the Buckets page to confirm that all project buckets were created in the correct Region.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picCreateS3.jpg" alt="NeonFoodMap bucket list" >}}

### Enable versioning for the media bucket

1. Go to **S3 → Buckets** and select `neonfoodmap-media-dev`.
2. Open **Properties**, find **Bucket Versioning**, select **Edit → Enable → Save changes**.

Frontend and log buckets keep versioning disabled because they do not yet require multiple versions. Media versioning supports recovery after accidental overwrite or deletion while avoiding unnecessary storage costs.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picS3Versioning.jpg" alt="S3 versioning configuration" >}}

### Check public access and object ownership

Open each bucket's **Permissions** tab. Under **Block public access**, ensure all four public-access block options are enabled. Do not make a bucket public to distribute the frontend through CloudFront; use **Origin Access Control (OAC)** and a bucket policy so CloudFront can read private objects.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picCheckPublicAccess.jpg" alt="Check Block Public Access" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picCheckPublicAccess2.jpg" alt="Object Ownership and ACL settings" >}}

## Create ECR repositories

Create two private Amazon ECR repositories for backend and frontend Docker images before deployment to ECS Fargate.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/AllStageECRTask.jpg" alt="ECR process" >}}

### Create repositories

1. Open PowerShell in the project's `aws_04_deploy` folder.
2. Run:

```powershell
.\01_create_ecr_repos.ps1
```

3. Enter MFA when requested. The script gets an STS session token, checks whether each repository already exists, and creates only missing repositories, so it is safe to run again.
4. Repositories use scan-on-push, AES-256 encryption, and project-management tags. Open **Amazon ECR → Private repositories** to check them.

| Repository | Expected URI |
| --- | --- |
| Backend | `<AWS_ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com/neonfoodmap-backend` |
| Frontend | `<AWS_ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com/neonfoodmap-frontend` |

### Check AWS credentials

After configuring AWS CLI and getting an MFA session token, verify the identity:

```powershell
aws sts get-caller-identity
```

The command must return the AWS account being deployed and a valid IAM user or role ARN.

### Get an ECR password and sign Docker in

The authentication chain is AWS profile → MFA code → STS session token → `aws ecr get-login-password` → Docker login. Set the team registry and pipe the AWS CLI password directly to Docker:

```powershell
$registry = "<AWS_ACCOUNT_ID>.dkr.ecr.ap-southeast-1.amazonaws.com"
aws ecr get-login-password --region ap-southeast-1 |
  docker login --username AWS --password-stdin $registry
```

### Test ECR sign-in

Run the script that checks the full authentication chain:

```powershell
.\02_test_ecr_login.ps1
```

| Check | PASS result |
| --- | --- |
| Docker daemon | Docker Desktop/Engine is running |
| AWS CLI | Profile is configured and `aws sts get-caller-identity` succeeds |
| MFA and STS | MFA is valid and returns a temporary session token |
| ECR password | `aws ecr get-login-password` returns a valid password |
| Docker login | Docker successfully signs in to ECR |

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picECRSuccess.jpg" alt="Successful ECR sign-in" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picECRDetail.jpg" alt="ECR repository details" >}}

## Build Docker images

### Backend: Django and Gunicorn

The backend uses a multi-stage Dockerfile. A `python:3.12-slim` builder installs dependencies and the runtime keeps only required libraries. The container runs as `appuser` and serves Gunicorn on port `8000`. Run migrations once in an ECS one-off task before deployment to prevent multiple replicas from migrating at the same time.

```powershell
.\03_build_backend.ps1
```

The script creates `neonfoodmap-backend:latest` and a timestamp tag, then checks Django import, Gunicorn, the app structure, `mysqlclient`/Pillow, and Django management commands.

### Frontend: React/Vite and Nginx

The frontend uses `node:22-alpine` to build the Vite bundle. Nginx uses `try_files $uri $uri/ /index.html` so SPA routes still work after a refresh.

```powershell
.\04_build_frontend.ps1 -ViteApiUrl "http://localhost:8000/api"
```

## Test locally, push, and apply lifecycle rules

1. Run the complete test for both images:

```powershell
.\05_test_builds_local.ps1
```

The backend runs as non-root, the frontend returns HTTP 200 on port 80, unneeded libraries are not in the final image, and SPA routing works.

2. Push tested images:

```powershell
.\06_push_ecr.ps1
```

The script tags and pushes `latest` and a timestamp/commit tag, then compares digests. Run `.\07_test_pull_ecr.ps1` to clear the cache, pull from ECR again, and run smoke tests.

3. Apply the ECR lifecycle policy:

```powershell
.\08_ecr_lifecycle.ps1
```

The policy keeps up to 10 date-tagged images, removes untagged images after one day, and retains `latest`. Use `-DryRun` to review it before applying.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/PicECRLifecyclePolicy.jpg" alt="ECR lifecycle policy" >}}

**Verify that the images were pushed to ECR successfully.**

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.2-s3-ecr-docker/picECRImagesPush.jpg" alt="Images pushed to ECR" >}}
