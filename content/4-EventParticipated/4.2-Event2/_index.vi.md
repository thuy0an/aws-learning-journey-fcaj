---
title: "FCAJ Community Day"
date: 2026-07-11
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---
# Bài thu hoạch: FCAJ Community Day

### Tổng quan sự kiện

**FCAJ Community Day** là buổi chia sẻ thực chiến do cộng đồng **First Cloud AI Journey (FCAJ)** tổ chức. Nội dung chương trình đi từ lộ trình nền tảng dành cho người mới bắt đầu, như chứng chỉ AWS Cloud Practitioner, đến các kiến thức chuyên sâu hơn về vận hành hệ thống, bao gồm SLA, Monitoring, bảo mật và đánh giá hệ thống tự động bằng Security Agent và Pentest.

Sự kiện mang đến góc nhìn gần với môi trường doanh nghiệp, giúp người học hiểu rằng kỹ năng Cloud không chỉ là sử dụng dịch vụ mà còn là khả năng vận hành ổn định, bảo mật và đặt trải nghiệm khách hàng làm trọng tâm.

### Mục đích của sự kiện

- Cung cấp các cột mốc rõ ràng để người học đánh giá quá trình học Cloud thông qua hệ thống chứng chỉ.
- Trang bị tư duy doanh nghiệp, kỹ năng xử lý sự cố (*Incident Management*) và ý thức đảm bảo trải nghiệm khách hàng cho các kỹ sư tương lai.
- Giới thiệu các công cụ bảo mật hiện đại, hỗ trợ tự động hóa việc rà soát tài liệu thiết kế, mã nguồn và kiểm thử xâm nhập.

### Danh sách diễn giả

- **Anh Huy** — Chia sẻ lộ trình, cấu trúc bài thi và mẹo ôn thi chứng chỉ AWS Cloud Practitioner (CLF-C02).
- **Anh Sơn** — Cựu sinh viên HUFLIT, hiện là Infrastructure Reliability Engineer; chia sẻ về giám sát hạ tầng, trung tâm vận hành NOC và cam kết chất lượng dịch vụ SLA.
- **Anh Nghĩa** — Trình diễn cách triển khai Security Agent để tìm kiếm lỗ hổng bảo mật trên ứng dụng.

### Nội dung nổi bật

#### Chiến lược thi chứng chỉ AWS Cloud Practitioner

- Bài thi kéo dài **90 phút**; thí sinh Việt Nam được cộng thêm **30 phút** và cần đạt **700/1000 điểm** để vượt qua.
- Nội dung được phân bổ theo bốn nhóm chính: **Cloud Concepts (24%)**, **Security & Compliance (30%)**, **Technology & Services (34%)** và **Billing & Pricing (12%)**.
- Khi ôn tập, cần tập trung vào việc hiểu lý do các đáp án sai thay vì chỉ ghi nhớ đáp án đúng.

#### NOC, SLA và thực tế vận hành doanh nghiệp

- **NOC (Network Operations Center)** là nơi tập trung giám sát hạ tầng liên tục 24/7.
- **SLA (Service Level Agreement)** là cam kết về mức độ dịch vụ, bao gồm thời gian hoạt động của hệ thống. Vi phạm SLA có thể gây tổn thất tài chính và ảnh hưởng đến uy tín doanh nghiệp.
- Một thông điệp quan trọng là: **hạ tầng ở trạng thái “healthy” không đồng nghĩa người dùng đang có trải nghiệm tốt**. Kỹ sư cần theo dõi cả hạ tầng lẫn luồng nghiệp vụ của ứng dụng.

#### Tự động hóa bảo mật với Security Agent

- Security Agent có thể tích hợp với GitHub hoặc GitLab để hỗ trợ **Design Review**, đánh giá tài liệu thiết kế hệ thống.
- Công cụ hỗ trợ **Code Review** bằng cách quét mã nguồn, phát hiện lỗ hổng và đề xuất hướng khắc phục.
- Có thể thực hiện **Pentest** trên các endpoint để kiểm tra khả năng bảo vệ của ứng dụng.

#### Xác thực Mutual TLS

- **mTLS (Mutual TLS)** là cơ chế xác thực hai chiều, thường được sử dụng trong lĩnh vực tài chính và ngân hàng.
- Cả client và server đều phải xác thực chứng chỉ của nhau, qua đó tăng mức độ tin cậy cho kết nối.

### Những gì học được

#### Kỹ năng học tập và thi chứng chỉ

- Khi làm sai, cần thực hiện **review mistakes** để phân tích lý do những đáp án khác không phù hợp.
- Trong phòng thi, nên áp dụng kỹ năng loại trừ, tìm **keyword** trong câu hỏi và tránh suy nghĩ quá phức tạp.

#### Tư duy trách nhiệm trong vận hành

- Cần có **SOP (Standard Operating Procedure)** rõ ràng khi phản ứng với sự cố.
- Không nên dừng lại khi công việc đã ra ngoài phạm vi của đội nhóm; mục tiêu quan trọng là phối hợp để giải quyết vấn đề cho khách hàng.

#### Nguyên tắc bảo mật và độ tin cậy

- Áp dụng nguyên tắc **đặc quyền tối thiểu** (*Principle of Least Privilege*) khi cấp quyền truy cập.
- Luôn chuẩn bị phương án dự phòng theo tư duy: **“Everything fails, all the time.”**

### Ứng dụng vào công việc

- Mở rộng Monitoring: không chỉ theo dõi CPU và RAM với CloudWatch mà còn thiết lập alarm tùy chỉnh, sử dụng SNS gửi email hoặc SMS để theo dõi kết nối giữa backend và cơ sở dữ liệu.
- Tích hợp Security Agent vào pipeline CI/CD để tự động phát hiện các vấn đề như lộ thông tin xác thực, sau đó sử dụng báo cáo để khắc phục lỗ hổng.
- Cân nhắc các mô hình chi phí **On-Demand, Reserved Instances và Spot Instances** để tối ưu ngân sách cho những khối lượng công việc không cần chạy liên tục.

### Trải nghiệm trong sự kiện

#### Môi trường thực chiến và chân thật

Các chia sẻ về NOC và vận hành hệ thống giúp em hình dung rõ hơn áp lực, trách nhiệm và cách tư duy cần có trong môi trường doanh nghiệp.

#### Tính tương tác cao

Diễn giả Anh Sơn không chỉ trình bày qua slide mà còn minh họa tình huống ứng dụng không thể đăng nhập để làm rõ vai trò của hệ thống cảnh báo và giám sát chủ động.

#### Bài học rút ra

- Kỹ năng kỹ thuật cần đi kèm với tư duy hướng đến khách hàng (*customer-oriented*).
- Tinh thần hỗ trợ đồng nghiệp, chủ động chịu trách nhiệm và khả năng phối hợp là những yếu tố cần thiết để phát triển bền vững trong môi trường làm việc toàn cầu.

#### Một số hình ảnh khi tham gia sự kiện

{{< event-image src="images/4-EventParticipated/Event_11_7_pic1.jpg" alt="Khoảnh khắc trong sự kiện AWS" >}}
