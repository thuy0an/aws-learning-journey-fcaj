---
title: "ECS Auto Scaling và CloudFront"
date: 2026-08-03
weight: 5
chapter: false
pre: " <b> 5.4.5. </b> "
---
## Cấu hình ECS Service Auto Scaling

Auto Scaling được áp dụng cho backend service, kế thừa task definition, ALB và target group ở mục 5.4.4.

### Bật Auto Scaling và CPU Policy

1. Mở service `svc-neonfoodmap-be`, vào tab **Service auto scaling** và chọn **Update**.
2. Bật **Use service auto scaling**. Trong phần *Capacity limits*, đặt số task tối thiểu là `2` và tối đa là `6`. Nhờ đó backend luôn có hai task sẵn sàng nhận request, nhưng chỉ được mở rộng tối đa sáu task để kiểm soát chi phí. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling2.jpg" alt="picECSScaling2" >}}
3. Trong phần scaling policy, chọn **Target tracking**. Đặt tên policy là `cpu-70-target-tracking` và chọn metric **ECSServiceAverageCPUUtilization**. Metric này là mức sử dụng CPU trung bình của tất cả task đang chạy trong service.
4. Đặt *Target value* là `70`. Khi CPU trung bình vượt 70%, ECS Service Auto Scaling sẽ tăng thêm task để chia tải. Khi CPU giảm, ECS có thể giảm task nhưng không thấp hơn giới hạn tối thiểu là hai task.
5. Đặt *Scale-out cooldown* là `60 seconds` và *Scale-in cooldown* là `300 seconds`, sau đó lưu policy. Sau mỗi lần scale-out, ECS chờ 60 giây để task mới khởi động và đăng ký vào target group. Scale-in chờ 5 phút để tránh tình trạng tải dao động làm task bị tăng/giảm liên tục. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling45.jpg" alt="picECSScaling45" >}}

Sau khi lưu, policy `cpu-70-target-tracking` sẽ theo dõi CPU của service trong giới hạn từ `2` đến `6` task.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling7.jpg" alt="picECSScaling7" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picECSScaling6.jpg" alt="picECSScaling6" >}}

## Cấu hình CloudFront cho frontend và audio

CloudFront phân phối frontend qua CDN, trong khi S3 vẫn giữ private. Môi trường hiện tại sử dụng domain mặc định do CloudFront cấp, chưa cấu hình Route 53 hoặc custom domain.

### Tạo CloudFront Distribution

Mở CloudFront Console → Distributions và chọn Create distribution, sau đó điền các thông số sau:

**Distribution name:** Nhập tên `neonfoodmap-frontend-cdn`.

**Description – optional:** Có thể để trống hoặc nhập `CloudFront CDN for NeonFoodmap Frontend and API`.

**Distribution type:** Giữ tùy chọn **Single website or app**.

**Domain (Route 53 managed domain – optional):** Để trống vì dự án dùng URL mặc định `*.cloudfront.net` do AWS cấp.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picCDN1.jpg" alt="picCDN1" >}}

### Cấu hình S3 Origin và OAC

**Origin type:** Chọn **Amazon S3**.

**S3 origin:** Chọn bucket `neonfoodmap-frontend-dev.s3.ap-southeast-1.amazonaws.com`.

**Origin path – optional:** Để trống, không nhập `/path` vì frontend được lưu tại thư mục gốc của bucket.

**Allow private S3 bucket access to CloudFront:** Giữ chọn **Allow private S3 bucket access to CloudFront – Recommended**. Đây là tính năng **Origin Access Control (OAC)**, cho phép CloudFront đọc bucket private và ngăn người dùng truy cập trực tiếp vào S3.

**Origin settings:** Giữ tùy chọn **Use recommended origin settings**.

**Cache settings:** Giữ tùy chọn **Use recommended cache settings tailored to serving S3 content**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picCDN2.jpg" alt="picCDN2" >}}

### Điều chỉnh ALB Origin

Sau khi khởi tạo, vào **Distributions**, chọn distribution vừa tạo, mở tab **Origins** và chỉnh sửa origin Elastic Load Balancing đã liên kết. Đặt *Protocol* là **HTTP only** để phù hợp với ALB/API hiện tại, tránh lỗi giao tiếp hoặc phản hồi `400 Bad Request` do không khớp giao thức.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picCDN3.jpg" alt="picCDN3" >}}

Đợi distribution có trạng thái **Enabled** và cập nhật hoàn tất, sau đó mở URL triển khai thực tế.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.5-cloudfront-delivery/picUI.jpg" alt="picUI" >}}


