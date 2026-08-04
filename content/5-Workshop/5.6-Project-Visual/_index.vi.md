---
title: "Tổng quan giao diện và chức năng ứng dụng"
date: 2026-08-03
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---
## NeonFoodMap

**NeonFoodMap** là nền tảng khám phá ẩm thực và du lịch số cho **phố ẩm thực Vĩnh Khánh, Quận 4, Thành phố Hồ Chí Minh**. Ứng dụng cho phép du khách khám phá **các điểm đến (POI) trên bản đồ, nghe thuyết minh** theo vị trí hoặc QR code, theo dõi **hành trình tour**, tải dữ liệu để dùng khi mất kết nối và mua nội dung premium. **Đối tác địa phương** có thể đăng ký để tạo và quản lý hồ sơ, POI, audio, mã QR; nội dung hiển thị của đối tác sẽ hiển thị trên bản đồ GPS sau khi đăng ký.

Trong ứng dụng **NeonFoodMap**, du khách có thể khám phá và **nghe thuyết minh theo bản đồ/QR**; người dùng trả phí mở khóa nội dung và tiếp tục trải nghiệm khi offline; đối tác cập nhật nội dung địa điểm để phân phối đến du khách. Ứng dụng có thể **thay đổi ngôn ngữ âm thanh và ngôn ngữ hiển thị** tại phần Cài đặt với **5 ngôn ngữ: tiếng Việt, tiếng Anh, tiếng Trung, tiếng Nhật và tiếng Hàn**.

## 1. Khởi động và cấp quyền vị trí

Ở lần dùng đầu tiên, ứng dụng hiển thị phần giới thiệu **“Phố kể bạn nghe”** với các điểm dừng gợi ý trên hành trình Vĩnh Khánh. Người dùng chọn **Cho phép** cho quyền **Vị trí chính xác**, sau đó chọn **Khám phá ngay** để vào bản đồ. Quyền vị trí là cơ sở để ứng dụng tìm POI lân cận, điều hướng và kích hoạt thuyết minh khi người dùng đến gần điểm dừng.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EOnboarding.jpg" alt="Màn hình giới thiệu và cấp quyền vị trí của NeonFoodMap" >}}

## 2. Khám phá POI trên bản đồ

Trang **Bản đồ** là điểm vào chính dành cho du khách. Bản đồ hiển thị vị trí hiện tại, các marker POI lân cận, công cụ phóng to/thu nhỏ và nút định vị lại. Người dùng có thể nhập tên món ăn, địa điểm hoặc nội dung liên quan vào ô tìm kiếm; chọn một kết quả hoặc marker để mở trải nghiệm chi tiết của POI.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EMap.jpg" alt="Bản đồ NeonFoodMap với các POI lân cận" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EMapSearch.jpg" alt="Tìm kiếm địa điểm ẩm thực trên bản đồ" >}}

Màn hình chi tiết POI trình bày ảnh bìa, câu chuyện địa phương, khoảng cách, danh mục, thời lượng dự kiến, địa chỉ. Đây là phần thông tin để thể hiện giá trị cốt lõi, số hóa thông tin ẩm thực/văn hóa trên nền bản đồ tương tác.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPOIDetail.jpg" alt="Màn hình chi tiết câu chuyện của một POI" >}}

## 3. Thuyết minh audio theo POI

Từ màn hình chi tiết, người dùng nhấn **Bắt đầu thuyết minh** để nghe audio gắn với POI và ngôn ngữ đang chọn. Thanh player cố định cho phép phát/tạm dừng, mở rộng trình phát, tua lùi/tới 10 giây, kéo tiến trình, đổi tốc độ phát. Khi đi vào vùng geofence của POI hoặc quét QR hợp lệ, luồng thuyết minh cũng có thể được kích hoạt cho đúng điểm đến.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EAudioExpanded.jpg" alt="Bộ điều khiển audio mở rộng của NeonFoodMap" >}}

## 4. Quét QR và khám phá đối tác địa phương

Nút QR trên trang bản đồ mở giao diện **Quét mã QR tại điểm đến**. Sau khi camera nhận diện mã QR của POI, ứng dụng mở đúng điểm thuyết minh để du khách nghe nội dung mà không cần tự tìm trên bản đồ..

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EQRScan.jpg" alt="Giao diện quét QR để mở thuyết minh POI" >}}

Trong trang POI, khu vực đối tác đề xuất các quán/địa điểm liên quan ở khoảng cách đi bộ. Người dùng chọn **QR Menu** để mở hồ sơ công khai của đối tác: thông tin quán, giờ hoạt động, khoảng giá, món nổi bật, menu/QR và audio giới thiệu.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerPublic.jpg" alt="Hồ sơ công khai và menu QR của đối tác" >}}

## 5. Hành trình tour và đánh giá

Trang **Lộ trình** cho phép người dùng chọn một hành trình. Sau khi chọn tour, giao diện hiển thị bản đồ tuyến đi, tiến độ các trạm, điểm hiện tại, điểm tiếp theo và thao tác **Bắt đầu hành trình**. Người dùng có thể xem tab **Tổng quan**, **Lộ trình** để theo dõi danh sách trạm/điểm khóa, và **Đánh giá** để đọc hoặc gửi đánh giá sao kèm nhận xét.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourChooser.jpg" alt="Hộp chọn hành trình tour" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourJourney.jpg" alt="Tour đang diễn ra với trạm hiện tại và tiến độ" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourReview.jpg" alt="Tab đánh giá của hành trình" >}}

Khi hành trình đang diễn ra, người dùng có thể chuyển trạm, mở bản đồ các trạm và phát thuyết minh cho điểm hiện tại. Nếu vị trí mô phỏng hoặc GPS lệch tuyến, giao diện đưa cảnh báo để người dùng định vị lại.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourRoute.jpg" alt="Danh sách các trạm trong lộ trình tour" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourRoute2.jpg" alt="Danh sách các trạm trong lộ trình tour" >}}

## 6. Mở khóa tour premium và hóa đơn

Tour premium hiển thị trạng thái khóa trước khi người dùng có quyền truy cập. Người dùng chọn **Mở khóa**, xem tóm tắt nội dung, thời hạn sử dụng và tổng thanh toán, rồi chọn tiếp tục qua PayPal Sandbox. Khi backend xác nhận giao dịch, giao diện hiển thị trạng thái mở khóa thành công; hóa đơn có thể xem lại từ **Tôi & Cài đặt → Hóa đơn & nhật ký giao dịch**.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ETourPremiumLocked.jpg" alt="Tour premium chưa được mở khóa" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EInvoice.jpg" alt="Chi tiết hóa đơn giao dịch của NeonFoodMap" >}}

Luồng PayPal dùng tài khoản Sandbox và kết nối ngoài để xác nhận thanh toán của người dùng với tour được thanh toán trong NeonFoodMap.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPaymentStart.jpg" alt="Xác nhận mở khóa tour và thanh toán PayPal Sandbox" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPaymentSuccess.jpg" alt="Thông báo mở khóa tour premium thành công" >}}


## 7. Tải dữ liệu và sử dụng offline

Trang **Tải Offline** cung cấp các gói dữ liệu theo khu vực/tour. Với gói chưa tải, người dùng chọn **Tải** khi đang trực tuyến; sau khi hoàn tất, ứng dụng hiển thị trạng thái đã tải, dung lượng sử dụng và cho phép xóa gói. Nội dung premium chưa mở khóa sẽ điều hướng người dùng về tour tương ứng để mua quyền truy cập.

Khi không có mạng, bản đồ ưu tiên dữ liệu đã lưu trên thiết bị và hiển thị nhãn **Chế độ Offline**. Các nhật ký còn chờ có thể đồng bộ lại khi thiết bị trực tuyến.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EOfflinePackages.jpg" alt="Danh sách gói dữ liệu offline của NeonFoodMap" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EOfflineReady.jpg" alt="Gói offline đã tải và sẵn sàng sử dụng" >}}

## 8. Tài khoản, ngôn ngữ và thiết bị

Trang **Tôi & Cài đặt** cho phép du khách chọn vùng giọng đọc, đổi ngôn ngữ giao diện, xem thông tin thiết bị/kết nối, xem hóa đơn và truy cập cổng đối tác. Du khách có thể liên kết thiết bị với email hoặc chọn **Đăng nhập tài khoản khác** để mở trang xác thực. Trang xác thực hỗ trợ đăng nhập, tạo tài khoản và tiếp tục với tư cách khách du lịch.

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2ESettings.jpg" alt="Cài đặt giọng đọc, ngôn ngữ và dữ liệu thiết bị" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EAuth.jpg" alt="Giao diện đăng nhập hoặc tạo tài khoản NeonFoodMap" >}}

## 9. Cổng dành cho đối tác

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerLogin.jpg" alt="Đăng nhập cổng quản trị đối tác" >}}

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerSignup.jpg" alt="Đăng ký cổng quản trị đối tác" >}}

1. **Hồ sơ & Audio:** cập nhật tên cơ sở, giờ hoạt động, địa chỉ, khoảng giá; tạo file âm thanh hoặc tải audio thu sẵn và gửi nội dung để duyệt. {{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerProfile.jpg" alt="Hồ sơ cơ sở và Audio Studio trong cổng đối tác" >}}
2. **POI & Món ăn:** tạo/cập nhật POI, chọn vị trí trên bản đồ, danh mục. {{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerPOI.jpg" alt="Tạo hoặc cập nhật POI của đối tác trên bản đồ" >}}
3. **Mã QR & Phân phối:** tạo hoặc làm mới QR có thời hạn để in tại điểm kinh doanh và dẫn du khách đến nội dung chính xác. {{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerQR.jpg" alt="Tạo QR và phân phối nội dung cho đối tác" >}}
4. **Thay đổi ngôn ngữ:** Ứng dụng hỗ trợ đa ngôn ngữ cho phép đối tác tạo thông tin POI thay đổi giọng đọc, hiển thị theo ngôn ngữ của mình, hệ thống sẽ tự động thay đổi ngôn ngữ thành ngôn ngữ khác dựa trên 5 ngôn ngữ hiện có trên hệ thống (Việt Nam, Tiếng Anh, Trung,Nhật và Hàn).

{{< event-image src="images/5-Workshop/5.6-Project-Visual/picE2EPartnerAnalytics.jpg" alt="Thông tin thay đổi ngôn ngữ, giọng đọc" >}}


## Tổng kết

Ứng dụng **NeonFoodMap** đã hoàn thành các chức năng phục vụ khám phá ẩm thực và du lịch số tại phố Vĩnh Khánh. Du khách có thể xem POI trên bản đồ, quét QR, nghe thuyết minh audio, trải nghiệm tour, mở khóa nội dung premium, tải dữ liệu offline và tùy chỉnh ngôn ngữ/giọng đọc. Đối tác địa phương cũng có thể quản lý hồ sơ, nội dung giới thiệu, POI, audio và mã QR.

Dự án đồng thời đã tích hợp thành công các dịch vụ AWS: Amazon S3 và CloudFront để ph phối giao diện người dùng; Amazon ECS Fargate và Amazon ECR để triển khai ứng dụng bằng container; Application Load Balancer để định tuyến truy cập; Amazon RDS MySQL để lưu trữ dữ liệu; Amazon VPC, IAM, Secrets Manager và Security Group để bảo mật hạ tầng; CloudWatch, SNS, AWS Budgets và Cost Anomaly Detection để theo dõi vận hành và chi phí. Quy trình CI/CD được tự động hóa bằng GitHub Actions kết hợp GitHub OIDC và AWS STS.
