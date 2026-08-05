---
title : "CloudWatch Dashboard"
date : 2024-01-01
weight : 3
chapter : false
pre : " <b> 5.5.3. </b> "
---

### 5.5.3. CloudWatch Dashboard 

### Mục tiêu

- Dashboard hiển thị trong CloudWatch
- Tất cả các chỉ số (Metrics) được cập nhật theo thời gian thực
- Các cảnh báo (Alarms) đã được cấu hình và kiểm thử
- Thông báo Email hoạt động bình thường
- Các truy vấn CloudWatch Log Insights đã được chuẩn bị

### Các bước thực hiện
#### Bước 1. Tạo CloudWatch Dashboard (JSON Template)

1. Đăng nhập **AWS Management Console**.
2. Truy cập dịch vụ **CloudWatch**.
3. Chọn **Dashboards**.
4. Nhấn **Create dashboard**.
![Hình 66.](/images/5-Workshop/5.5-Neon-Operations/image066.png)

5. Nhập tên Dashboard (ví dụ: `NeonFoodMap-Operational-Dashboard`).
![Hình 67.](/images/5-Workshop/5.5-Neon-Operations/image067.png)

6. Chọn tạo Dashboard mới.
7. Nếu đã chuẩn bị sẵn Dashboard dạng JSON:
   - Chọn **Actions** → **View/Edit source**.
   - Dán nội dung JSON template.
   - Chọn **Save**.
![Hình 69.](/images/5-Workshop/5.5-Neon-Operations/image069.png)

---

#### Bước 2. Thêm Widget hiển thị Metrics của ECS

1. Trong Dashboard chọn **Add widget**.
Hình 68 ![Hình 68.](/images/5-Workshop/5.5-Neon-Operations/image068.png)

2. Chọn loại **Line** hoặc **Number**.
Hình 46 ![Hình 46.](/images/5-Workshop/5.5-Neon-Operations/image046.png)

3. Chọn nguồn dữ liệu **CloudWatch Metrics**.
Hình 45 ![Hình 45.](/images/5-Workshop/5.5-Neon-Operations/image045.png)

4. Điều hướng đến:
   - **ECS → Cluster Metrics**
Hình 53 ![Hình 53.](/images/5-Workshop/5.5-Neon-Operations/image053.png)

5. Thêm các Metrics:
   - CPU Utilization
   - Memory Utilization
   - Network In
   - Network Out
Hình 55 ![Hình 55.](/images/5-Workshop/5.5-Neon-Operations/image055.png)

6. Đặt tên Widget phù hợp.
7. Nhấn **Create widget**.

---

#### Bước 3. Thêm Widget Metrics của Application Load Balancer (ALB)

1. Chọn **Add widget**.
Hình 70 ![Hình 70.](/images/5-Workshop/5.5-Neon-Operations/image070.png)

2. Chọn **CloudWatch Metrics**.
3. Điều hướng đến:
   - **ApplicationELB**
Hình 60 ![Hình 60.](/images/5-Workshop/5.5-Neon-Operations/image060.png)

4. Thêm các Metrics:
   - Healthy Host Count
   - UnHealthy Host Count
   - Target Response Time
   - Request Count
   - HTTPCode_Target_5XX_Count
5. Lưu Widget.

<!-- Hình 59 ![Hình 59.](/images/5-Workshop/5.5-Neon-Operations/image059.png) -->
Hình 70 ![Hình 70.](/images/5-Workshop/5.5-Neon-Operations/image070.png)
Hình 60 ![Hình 60.](/images/5-Workshop/5.5-Neon-Operations/image060.png)
<!-- Hình 72 ![Hình 72.](/images/5-Workshop/5.5-Neon-Operations/image072.png) -->

---

#### Bước 4. Thêm Widget Metrics của Amazon S3

1. Chọn **Add widget**.
2. Chọn Metrics của dịch vụ **Amazon S3**.
Hình 88 ![Hình 88.](/images/5-Workshop/5.5-Neon-Operations/image088.png)

3. Chọn Bucket cần theo dõi.
Hình 89 ![Hình 89.](/images/5-Workshop/5.5-Neon-Operations/image089.png)

4. Thêm các Metrics:
   - NumberOfObjects
   - BucketSizeBytes
   - AllRequests
   - BytesDownloaded
   - BytesUploaded
Hình 90 ![Hình 90.](/images/5-Workshop/5.5-Neon-Operations/image090.png)
Hình 91 ![Hình 91.](/images/5-Workshop/5.5-Neon-Operations/image091.png)
Hình 92 ![Hình 92.](/images/5-Workshop/5.5-Neon-Operations/image092.png)
Hình 93 ![Hình 93.](/images/5-Workshop/5.5-Neon-Operations/image093.png)
Hình 94 ![Hình 94.](/images/5-Workshop/5.5-Neon-Operations/image094.png)
Hình 95 ![Hình 95.](/images/5-Workshop/5.5-Neon-Operations/image095.png)

5. Lưu Widget.
Hình 96 ![Hình 96.](/images/5-Workshop/5.5-Neon-Operations/image096.png)

---

####   Bước 5. Thêm Widget CloudWatch Log Insights

1. Chọn **Add widget**.
Hình 73 ![Hình 73.](/images/5-Workshop/5.5-Neon-Operations/image073.png)

2. Chọn **Log query**.
Hình 74 ![Hình 74.](/images/5-Workshop/5.5-Neon-Operations/image074.png)

3. Chọn Log Group của:
   - ECS
   - Application
   - ALB
4. Nhập câu lệnh CloudWatch Log Insights.
Hình 75 ![Hình 75.](/images/5-Workshop/5.5-Neon-Operations/image075.png)

5. Kiểm tra kết quả trả về.

6. Lưu Widget vào Dashboard.
Hình 76 ![Hình 76.](/images/5-Workshop/5.5-Neon-Operations/image076.png)

---

#### Bước 6. Thêm Widget Metrics của Amazon RDS

1. Chọn **Add widget**.
2. Chọn Metrics của **Amazon RDS**.
3. Chọn Database Instance.
4. Thêm các Metrics:
   - CPU Utilization
   - Database Connections
   - Read Latency
   - Write Latency
   - Free Storage Space
5. Lưu Widget.

---

#### Bước 7. Tạo CloudWatch Alarms

1. Truy cập **CloudWatch → Alarms**.
2. Chọn **Create Alarm**.
Hình 50 ![Hình 50.](/images/5-Workshop/5.5-Neon-Operations/image050.png)

3. Tạo Alarm cho:
   - CPU Utilization > 80%
   - HTTP 5XX Errors > 10 lần/phút
Hình 51 ![Hình 51.](/images/5-Workshop/5.5-Neon-Operations/image051.png)

4. Thiết lập:
   - Evaluation Period
   - Threshold
   - Alarm Name
Hình 52 ![Hình 52.](/images/5-Workshop/5.5-Neon-Operations/image052.png)

5. Chọn hành động gửi thông báo khi Alarm kích hoạt.
Hình 56 ![Hình 56.](/images/5-Workshop/5.5-Neon-Operations/image056.png)
Hình 57 ![Hình 57.](/images/5-Workshop/5.5-Neon-Operations/image057.png)
Hình 58 ![Hình 58.](/images/5-Workshop/5.5-Neon-Operations/image058.png)
Hình 61 ![Hình 61.](/images/5-Workshop/5.5-Neon-Operations/image061.png)
Hình 62 ![Hình 62.](/images/5-Workshop/5.5-Neon-Operations/image062.png)
Hình 63 ![Hình 63.](/images/5-Workshop/5.5-Neon-Operations/image063.png)
Hình 64 ![Hình 64.](/images/5-Workshop/5.5-Neon-Operations/image064.png)
Hình 65 ![Hình 65.](/images/5-Workshop/5.5-Neon-Operations/image065.png)

---

#### Bước 8. Tạo SNS Topic để gửi thông báo

1. Truy cập dịch vụ **Amazon SNS**.
2. Chọn **Topics**.
3. Nhấn **Create topic**.
![Hình 47.](/images/5-Workshop/5.5-Neon-Operations/image047.png)

4. Chọn loại **Standard**.
5. Đặt tên Topic (ví dụ: `NeonFoodMap-Alerts`).
6. Hoàn tất việc tạo Topic.
![Hình 48.](/images/5-Workshop/5.5-Neon-Operations/image048.png)

---

#### Bước 9. Đăng ký Email nhận thông báo

1. Mở Topic vừa tạo.
2. Chọn **Create subscription**.
![Hình 49.](/images/5-Workshop/5.5-Neon-Operations/image049.png)

3. Chọn:
   - Protocol: **Email**
4. Nhập địa chỉ Email của nhóm vận hành.
5. Gửi Subscription.
6. Mở Email và nhấn **Confirm Subscription** để kích hoạt.
![Hình 97.](/images/5-Workshop/5.5-Neon-Operations/image097.png)

---

####  Bước 10. Kiểm thử Alarm

1. Tạo điều kiện để Alarm được kích hoạt, ví dụ:
   - Tăng tải CPU của ECS.
   - Sinh nhiều HTTP 5XX Error.
2. Quan sát trạng thái Alarm chuyển sang **ALARM**.
3. Kiểm tra Email thông báo được gửi qua SNS.
4. Xác nhận Dashboard cập nhật Metrics theo thời gian thực.
5. Ghi nhận kết quả kiểm thử.
Hình 71 ![Hình 71.](/images/5-Workshop/5.5-Neon-Operations/image071.png)
