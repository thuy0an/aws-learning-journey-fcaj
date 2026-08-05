---
title: "CloudWatch, Logs và Alarms"
date: 2026-08-05
weight: 6
chapter: false
pre: " <b> 5.4.6. </b> "
---
## 1. Thu thập log và dùng Logs Insights

### Cấu hình log group cho ECS

1. Trong task definition của frontend và backend, chọn **awslogs** log driver, Region `ap-southeast-1` và các log group sau.

| Service      | Log group                     |
| ------------ | ----------------------------- |
| Backend ECS  | `/ecs/neonfoodmap-backend`  |
| Frontend ECS | `/ecs/neonfoodmap-frontend` |

2. Vào **CloudWatch → Log groups**, mở từng log group, chọn **Actions → Edit retention setting** và đặt thời gian lưu trữ `30 days`.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image079.png" alt="Danh sách CloudWatch Log Groups" >}}

3. Sau khi ECS service khởi tạo task, kiểm tra log stream để xác nhận container đang ghi log.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image106.png" alt="Log stream của ECS backend trong CloudWatch" >}}

### Lưu truy vấn Logs Insights

1. Chọn **CloudWatch → Logs Insights** và chọn log group backend.
2. Tạo, chạy và lưu các truy vấn phục vụ vận hành: tìm lỗi/exception, kiểm tra health check và lọc request 5XX. Điều chỉnh field hoặc pattern theo định dạng log của ứng dụng.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image105.png" alt="Mở CloudWatch Logs Insights" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image107.png" alt="Lưu truy vấn CloudWatch Logs Insights" >}}

## 2. Tạo dashboard vận hành

1. Vào **CloudWatch → Dashboards → Create dashboard**, đặt tên `NeonFoodMap-Operational-Dashboard`.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image067.png" alt="Tạo CloudWatch Dashboard" >}}

2. Chọn **Add widget** và dùng loại biểu đồ phù hợp như **Line** hoặc **Number**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image068.png" alt="Thêm widget vào CloudWatch Dashboard" >}}

3. Thêm các chỉ số quan trọng: ECS CPU/memory và số task; ALB request count, target response time, healthy host và 5XX; RDS CPU, số kết nối và dung lượng trống.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image070.png" alt="Chọn Application Load Balancer metrics" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image076.png" alt="CloudWatch Dashboard tổng hợp chỉ số ECS, ALB và Logs Insights" >}}

## 3. Tạo cảnh báo và thông báo SNS

1. Tạo SNS topic `NeonFoodMap-Alerts` trong **Amazon SNS → Topics**, sau đó tạo subscription kiểu **Email** và xác nhận email nhận được.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image047.png" alt="Tạo SNS topic" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image048.png" alt="Tạo SNS email subscription" >}}

2. Vào **CloudWatch → Alarms → Create alarm**, chọn metric, đặt ngưỡng và chọn `NeonFoodMap-Alerts-Topic` làm notification action.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image050.png" alt="Tạo CloudWatch Alarm" >}}

3. Tạo hai alarm sau: {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image055.png" alt="Tạo Alarm 1" >}} {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image056.png" alt="Tạo Alarm 2" >}}

| Alarm                         | Metric                              | Ngưỡng                       |
| ----------------------------- | ----------------------------------- | ------------------------------ |
| `ECS-Backend-HighCPU-Alarm` | `ECSServiceAverageCPUUtilization` | `>= 80%` trong `5 minutes` |
| `ALB-5XX-Error-Alarm`       | `HTTPCode_Target_5XX_Count`       | `>= 10` trong `1 minute`   |

4. Kiểm tra alarm hiển thị trạng thái **OK** hoặc **Insufficient data**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image065.png" alt="Danh sách CloudWatch Alarm đã cấu hình" >}}

## 4. Bật VPC Flow Logs

1. Vào **Amazon VPC → Your VPCs**, chọn VPC của hệ thống và mở tab **Flow Logs**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image098.png" alt="Chọn VPC để cấu hình Flow Logs" >}}

2. Chọn **Create flow log**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image100.png" alt="Tạo VPC Flow Log" >}}

3. Đặt **Traffic type** là `All`, **Destination** là `CloudWatch Logs`, chọn log group riêng và IAM role phù hợp.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image102.png" alt="Cấu hình đích CloudWatch Logs cho VPC Flow Logs" >}}

4. Tạo Flow Log và kiểm tra trạng thái `Active`. Chỉ bật khi cần vì Flow Logs phát sinh chi phí lưu trữ và truy vấn.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.6-monitoring-alerting/image103.png" alt="VPC Flow Log đã được tạo và ghi vào CloudWatch Logs" >}}
