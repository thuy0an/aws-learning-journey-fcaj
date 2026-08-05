---
title : "CloudWatch Logs và Log Insights"
date : 2024-01-01
weight : 5
chapter : false
pre : " <b> 5.5.5. </b> "
---

### 5.5.5. CloudWatch Logs và Log Insights

Sau khi hoàn thành phần này, hệ thống sẽ đáp ứng các yêu cầu sau:

- Thiết lập thời gian lưu trữ Log là **30 ngày**
- Tạo Log Group cho **ALB Access Logs**
- Tạo Log Group cho **Application Logs**
- Cấu hình Log Subscription phục vụ phát hiện bất thường (Anomaly Detection)
- Tạo các truy vấn CloudWatch Log Insights thường dùng
- Kích hoạt **VPC Flow Logs** để hỗ trợ xử lý sự cố mạng
- Kiểm thử các truy vấn Log Insights

---

## Các bước thực hiện

### Bước 1. Thiết lập thời gian lưu trữ Log (Retention Policy)

1. Đăng nhập **AWS Management Console**.
2. Truy cập dịch vụ **CloudWatch**.
3. Chọn **Log Groups**.
Hình 77 ![Hình 77.](/images/5-Workshop/5.5-Neon-Operations/image077.png)

4. Chọn Log Group cần cấu hình.
Hình 78 ![Hình 78.](/images/5-Workshop/5.5-Neon-Operations/image078.png)

5. Chọn **Actions → Edit retention setting**.
Hình 79 ![Hình 79.](/images/5-Workshop/5.5-Neon-Operations/image079.png)

6. Chọn thời gian lưu trữ là **30 Days**.
7. Nhấn **Save** để áp dụng.
Hình 80 ![Hình 80.](/images/5-Workshop/5.5-Neon-Operations/image080.png)

> Lặp lại thao tác này cho tất cả Log Group của hệ thống.

---

### Bước 2. Tạo Log Group cho ALB Access Logs

1. Trong **CloudWatch**, chọn **Log Groups**.

2. Nhấn **Create log group**.
Hình 81 ![Hình 81.](/images/5-Workshop/5.5-Neon-Operations/image081.png)

3. Đặt tên Log Group, ví dụ:

```
/aws/alb/access-logs
```
Hình 82 ![Hình 82.](/images/5-Workshop/5.5-Neon-Operations/image082.png)

4. Chọn cấu hình mặc định.
Hình 83 ![Hình 83.](/images/5-Workshop/5.5-Neon-Operations/image083.png)

5. Nhấn **Create** để hoàn tất.

> Nếu ALB đang ghi Access Logs về Amazon S3, có thể sử dụng dịch vụ bổ sung để đưa Log vào CloudWatch khi cần phân tích tập trung.

---

Hình 84 ![Hình 84.](/images/5-Workshop/5.5-Neon-Operations/image084.png)
Hình 85 ![Hình 85.](/images/5-Workshop/5.5-Neon-Operations/image085.png)
Hình 86 ![Hình 86.](/images/5-Workshop/5.5-Neon-Operations/image086.png)
Hình 87 ![Hình 87.](/images/5-Workshop/5.5-Neon-Operations/image087.png)

### Bước 3. Tạo Log Group cho Application Logs

1. Trong **CloudWatch Log Groups**, chọn **Create log group**.
2. Đặt tên Log Group, ví dụ:

```
/ecs/neonfoodmap-backend
```

hoặc

```
/ecs/neonfoodmap-frontend
```

3. Nhấn **Create**.
Hình 104 ![Hình 104.](/images/5-Workshop/5.5-Neon-Operations/image104.png)

4. Cấu hình ECS Task Definition sử dụng **awslogs Log Driver**.
5. Khởi động lại ECS Service nếu cần để Log bắt đầu được ghi vào CloudWatch.

---

Hình 106 ![Hình 106.](/images/5-Workshop/5.5-Neon-Operations/image106.png)

### Bước 4. Cấu hình Log Subscription phục vụ Anomaly Detection

1. Mở Log Group cần theo dõi.
2. Chọn tab **Subscriptions**.
3. Nhấn **Create subscription filter**.
4. Chọn đích gửi Log, ví dụ:
   - AWS Lambda
   - Amazon Kinesis Data Firehose
   - Amazon Kinesis Data Streams
5. Cấu hình Filter Pattern theo yêu cầu.
6. Kiểm tra Subscription hoạt động thành công.

> Log Subscription giúp tự động xử lý Log hoặc tích hợp với các hệ thống phát hiện bất thường theo thời gian thực.

---

### Bước 5. Tạo các truy vấn CloudWatch Log Insights

1. Truy cập **CloudWatch → Logs Insights**.
Hình 105 ![Hình 105.](/images/5-Workshop/5.5-Neon-Operations/image105.png)

2. Chọn Log Group cần phân tích.
3. Tạo và lưu các truy vấn thường dùng, ví dụ:

- Thống kê số lượng lỗi theo thời gian
- Tìm các HTTP Status Code 500
- Tìm Request có thời gian xử lý lớn
- Tìm Exception theo từ khóa
- Thống kê các IP truy cập nhiều nhất
Hình 107 ![Hình 107.](/images/5-Workshop/5.5-Neon-Operations/image107.png)

4. Đặt tên cho từng truy vấn.
5. Chọn **Save query** để sử dụng lại sau này.
Hình 108 ![Hình 108.](/images/5-Workshop/5.5-Neon-Operations/image108.png)

---

### Bước 6. Cấu hình VPC Flow Logs

1. Truy cập dịch vụ **Amazon VPC**.
2. Chọn **Your VPCs**.
3. Chọn VPC đang triển khai hệ thống.
Hình 98 ![Hình 98.](/images/5-Workshop/5.5-Neon-Operations/image098.png)

4. Chọn tab **Flow Logs**.
Hình 99 ![Hình 99.](/images/5-Workshop/5.5-Neon-Operations/image099.png)

5. Nhấn **Create Flow Log**.
Hình 100 ![Hình 100.](/images/5-Workshop/5.5-Neon-Operations/image100.png)

6. Thiết lập:
   - Filter: **All**
   - Destination: **CloudWatch Logs**
   - Chọn hoặc tạo Log Group mới
   - Chọn IAM Role phù hợp
Hình 101 ![Hình 101.](/images/5-Workshop/5.5-Neon-Operations/image101.png)
Hình 102 ![Hình 102.](/images/5-Workshop/5.5-Neon-Operations/image102.png)

7. Nhấn **Create Flow Log**.
Hình 103 ![Hình 103.](/images/5-Workshop/5.5-Neon-Operations/image103.png)

> VPC Flow Logs hỗ trợ phân tích lưu lượng mạng và xử lý các vấn đề kết nối giữa các tài nguyên trong VPC.

---

### Bước 7. Kiểm thử các truy vấn Log Insights

1. Truy cập **CloudWatch → Logs Insights**.
2. Chọn Log Group cần kiểm tra.
3. Chạy các truy vấn đã lưu.
4. Xác nhận:
   - Log được thu thập đầy đủ.
   - Truy vấn trả về kết quả chính xác.
   - Có thể lọc theo thời gian và từ khóa.
5. Kiểm tra Dashboard (nếu có) đã hiển thị dữ liệu Log đúng như mong đợi.
6. Ghi nhận kết quả kiểm thử và hoàn tất cấu hình.

