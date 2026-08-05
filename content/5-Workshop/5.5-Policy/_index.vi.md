---
title: "Xác minh triển khai và giám sát hệ thống"
date: 2026-08-03
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---
## Kiểm tra trạng thái ECR, ECS

### Kiểm tra trạng thái Amazon ECR Repositories

Thông tin hai repository phục vụ frontend và backend đã khởi tạo, có image để ECS pull và hoạt động bình thường:

- **neonfoodmap-backend:** Lưu lượng pull image phục vụ triển khai ECS task.

{{< event-image src="images/5-Workshop/5.5-Policy/picECR1.jpg" alt="Trạng thái ECR repository neonfoodmap backend" >}}

- **neonfoodmap-frontend:** Các đợt pull image tương ứng với lịch trình deployment của frontend service.

{{< event-image src="images/5-Workshop/5.5-Policy/picECR2.jpg" alt="Trạng thái ECR repository neonfoodmap frontend" >}}

### Kiểm tra trạng thái Cluster và ECS Services

Tại **NeonFoodmap-cluster**, xác nhận hệ thống đủ các dịch vụ với:

- **Cluster status:** `Active`.
- **Services:** `2 active` gồm `svc-neonfoodmap-be` và `svc-neonfoodmap-fe`.
- **Tasks:** `4 running`; mỗi service duy trì ổn định `2/2 Tasks running`.

{{< event-image src="images/5-Workshop/5.5-Policy/picECSCheck2.jpg" alt="Trạng thái NeonFoodmap ECS cluster và services" >}}

### Kiểm tra Tasks và IP nội bộ

Truy cập tab **Tasks** để xác minh các Fargate container đã pull image từ ECR và khởi chạy thành công:

- **Launch type:** `Fargate`, platform version `1.4.0`.
- **Last / Desired status:** Cả bốn task đều là `Running / Running`.
- **Frontend:** `svc-neonfoodmap-fe` sử dụng task definition revision `:17`, có private IP `10.0.24.199` và `10.0.12.222`.
- **Backend:** `svc-neonfoodmap-be` sử dụng task definition revision `:36`, có private IP `10.0.9.254` và `10.0.30.15`.

{{< event-image src="images/5-Workshop/5.5-Policy/picECSCheck3.jpg" alt="Trạng thái NeonFoodmap ECS cluster và services" >}}

### Kiểm tra Container Logs và Health Check

Kiểm tra CloudWatch Logs của backend service `svc-neonfoodmap-be` để xác nhận ứng dụng phản hồi health check bình thường:

- **Target path:** `GET /health` HTTP/1.1.
- **HTTP status:** `200 OK` liên tục từ các IP nội bộ.
- **Kết quả:** Application nhận request và phản hồi bình thường, chưa ghi nhận lỗi gián đoạn service.

{{< event-image src="images/5-Workshop/5.5-Policy/picECSCheck1.jpg" alt="CloudWatch logs và health check backend ECS service" >}}

## Kiểm tra trạng thái CI/CD Deployment

Sau khi cấu hình pipeline tự động hóa, tiến hành kiểm tra quy trình triển khai từ GitHub Actions đến Amazon ECS.

### Kiểm tra luồng CI/CD Pipeline trên GitHub Actions

Kiểm tra kết quả chạy của workflow `NeonFoodmap CI/CD Pipeline`:

{{< event-image src="images/5-Workshop/5.5-Policy/picCICD3.jpg" alt="picCICDPipeline3" >}}

### Kiểm tra kết quả Deployment của Backend Service (ECS)

Xác minh trạng thái triển khai tại service `svc-neonfoodmap-be` sau khi nhận tín hiệu từ GitHub Actions:

- **Deployment Status:** `Success` (Trạng thái `Active`).
- **Circuit breaker / Health check:** `Monitoring complete` và `Configured` (Không ghi nhận lỗi rollback).

{{< event-image src="images/5-Workshop/5.5-Policy/picCICD1.jpg" alt="picCICDPipeline2" >}}

### Kiểm tra kết quả Deployment của Frontend Service (ECS)

Xác minh trạng thái triển khai tại service `svc-neonfoodmap-fe`:

- **Deployment Status:** `Success` (Trạng thái `Active`).
- **Circuit breaker / Health check:** `Monitoring complete` và `Configured`, đảm bảo không gián đoạn dịch vụ trong quá trình cập nhật.

{{< event-image src="images/5-Workshop/5.5-Policy/picCICD2.jpg" alt="picCICDPipeline2" >}}

## Kiểm tra trạng thái Application Load Balancing

Xác nhận `ALB-NeonFoodMap` phân phối traffic chính xác qua Path-based routing với 5 rules và toàn bộ 4 task IP đều đạt trạng thái `Healthy`.

{{< event-image src="images/5-Workshop/5.5-Policy/picALB.jpg" alt="picALB" >}}

## Kiểm tra CloudWatch Dashboard và giám sát hệ thống

Hệ thống giám sát được cấu hình tập trung qua Amazon CloudWatch để theo dõi tài nguyên ECS Cluster, hiệu năng Application Load Balancer `ALB-NeonFoodMap` và các cảnh báo tự động.

### 1. Cảnh báo tự động CloudWatch Alarms

**ECS CPU Alarm (`ECS-Backend-HighCPU-Alarm`):** Alarm theo dõi `TaskCpuUtilization` với ngưỡng `>= 80%` trong một datapoint, chu kỳ 5 phút. Trạng thái hiện tại là **OK** vì CPU dao động thấp và chưa vượt ngưỡng cảnh báo.

**ALB 5XX Error Alarm (`ALB-5XX-Error-Alarm`):** Alarm theo dõi `HTTPCode_Target_5XX_Count` với ngưỡng `>= 10` trong một datapoint, chu kỳ 1 phút. Trạng thái **Insufficient data** do hệ thống không phát sinh lỗi 5XX từ phía server trong thời gian quan sát.

{{< event-image src="images/5-Workshop/5.5-Policy/CloudWatchOverview.jpg" alt="CloudWatch Alarms Overview" >}}

### 2. Chỉ số vận hành ECS Cluster

Theo dõi `NeonFoodmap-cluster` trong khoảng thời gian vận hành cho thấy tài nguyên được sử dụng ổn định:

- **CPU Utilization:** Trung bình thấp, đỉnh điểm `48.96%`, vẫn dưới ngưỡng cảnh báo 80%.
- **Memory Utilization:** Duy trì quanh `25.49%`.
- **Disk Utilization:** Mức cao nhất `1.56%`.
- **Network Traffic:** Đỉnh lưu lượng đạt `32.41 KB/s`.
- **Service & Task Count:** Hệ thống có `2` service active và duy trì `4` task; số task tối đa đạt `8` trong các đợt deployment/scaling.

{{< event-image src="images/5-Workshop/5.5-Policy/CloudWatchECS.jpg" alt="CloudWatchECS" >}}

### 3. Sức khỏe và hiệu năng Application Load Balancer

Dashboard của `ALB-NeonFoodMap` ghi nhận các chỉ số sau:

- **Availability & Errors:** Độ sẵn sàng `100%`, không ghi nhận server fault `5XX`.
- **Requests:** Lưu lượng có đỉnh `252 requests/chu kỳ`.
- **Latency:** Duy trì rất thấp, từ `3.6 × 10⁻⁴ ms` đến tối đa `5.2 × 10⁻³ ms`, dưới `0.01 ms`.
- **Client Errors:** Có một số lỗi `4XX` từ phía client, đỉnh `45 requests`, chủ yếu do truy cập sai endpoint hoặc token hết hạn.

{{< event-image src="images/5-Workshop/5.5-Policy/CloudWatchALB.jpg" alt="CloudWatchALB" >}}

### 4. Đánh giá tổng quan

Hạ tầng ECS Fargate và ALB vận hành ổn định. CPU (khoảng 25–48%) và memory (khoảng 25%) đủ khả năng chịu tải. Độ trễ ALB thấp, không xuất hiện lỗi 5XX từ backend/server trong thời gian kiểm tra. Các CloudWatch Alarm đã được cấu hình để gửi thông báo qua SNS khi chỉ số vượt ngưỡng, giúp nhóm phát hiện sớm sự cố vận hành.
