# Hướng dẫn chỉnh sửa nội dung báo cáo

Tài liệu này ghi lại các file cần sửa trong `FCJ-WORKSHOP-TEMPLATE` khi bạn muốn thay nội dung mẫu bằng thông tin thực tập của mình.

## 1. Nguyên tắc chung

- Nội dung chính của báo cáo nằm trong thư mục `content/`.
- Ảnh minh hoạ để trong `static/images/`.
- Nếu muốn đổi tiêu đề site, ngôn ngữ mặc định hoặc menu, sửa `config.toml`.
- Nếu muốn đổi giao diện riêng của site, ưu tiên sửa file trong `layouts/` thay vì sửa trực tiếp theme trong `themes/`.

## 2. Trang bìa và thông tin sinh viên

Sửa các file sau:

- `content/_index.md`
- `content/_index.vi.md`

Các phần nên thay:

- Tên báo cáo
- Họ và tên
- Số điện thoại
- Email
- Trường
- Ngành
- Lớp
- Công ty thực tập
- Vị trí thực tập
- Thời gian thực tập

Ảnh đại diện nằm ở:

- `static/images/avatar.png`

Nếu muốn đổi ảnh, chỉ cần thay file này bằng ảnh mới và giữ nguyên tên `avatar.png`.

## 3. Mục Worklog

Trang tổng của mục Worklog nằm ở:

- `content/1-Worklog/_index.md`
- `content/1-Worklog/_index.vi.md`

Các tuần chi tiết nằm trong từng thư mục con:

- `content/1-Worklog/1.1-Week1/_index.md`
- `content/1-Worklog/1.2-Week2/_index.md`
- `content/1-Worklog/1.3-Week3/_index.md`
- ...
- `content/1-Worklog/1.12-Week12/_index.md`

Nếu dùng bản tiếng Việt, chỉnh thêm các file tương ứng:

- `content/1-Worklog/1.1-Week1/_index.vi.md`
- `content/1-Worklog/1.2-Week2/_index.vi.md`
- ...
- `content/1-Worklog/1.12-Week12/_index.vi.md`

Mỗi file tuần thường có các phần:

- Mục tiêu của tuần
- Công việc đã làm theo từng ngày
- Kết quả đạt được

## 4. Mục Proposal

Trang đề xuất nằm ở:

- `content/2-Proposal/_index.md`
- `content/2-Proposal/_index.vi.md`

Đây là phần mô tả dự án hoặc hướng thực tập mà bạn chọn. Các nội dung thường cần thay:

- Tên đề tài
- Mô tả vấn đề
- Giải pháp
- Kiến trúc
- Công nghệ sử dụng
- Kế hoạch triển khai
- Ước tính chi phí

Ảnh trong mục này thường nằm ở:

- `static/images/2-Proposal/`

## 5. Mục Blogs Posted

Trang tổng của blog nằm ở:

- `content/3-BlogsPosted/_index.md`
- `content/3-BlogsPosted/_index.vi.md`

Các blog con nằm ở:

- `content/3-BlogsPosted/3.1-Blog1/_index.md`
- `content/3-BlogsPosted/3.2-Blog2/_index.md`
- `content/3-BlogsPosted/3.3-Blog3/_index.md`

Và bản tiếng Việt tương ứng:

- `content/3-BlogsPosted/3.1-Blog1/_index.vi.md`
- `content/3-BlogsPosted/3.2-Blog2/_index.vi.md`
- `content/3-BlogsPosted/3.3-Blog3/_index.vi.md`

Bạn chỉ cần sửa nội dung bài viết theo blog thật của mình, thay tiêu đề, mô tả, ảnh chụp màn hình, và liên kết nếu có.

## 6. Mục Events Participated

Trang tổng của phần sự kiện nằm ở:

- `content/4-EventParticipated/_index.md`
- `content/4-EventParticipated/_index.vi.md`

Các sự kiện con nằm ở:

- `content/4-EventParticipated/4.1-Event1/_index.md`
- `content/4-EventParticipated/4.2-Event2/_index.md`

Và bản tiếng Việt tương ứng:

- `content/4-EventParticipated/4.1-Event1/_index.vi.md`
- `content/4-EventParticipated/4.2-Event2/_index.vi.md`

Trong phần này, bạn nên thay:

- Tên sự kiện
- Thời gian tham gia
- Nội dung đã học
- Cảm nhận cá nhân
- Hình ảnh minh hoạ

## 7. Mục Workshop

Trang tổng của workshop nằm ở:

- `content/5-Workshop/_index.md`
- `content/5-Workshop/_index.vi.md`

Các phần con gồm:

- `content/5-Workshop/5.1-Workshop-overview/_index.md`
- `content/5-Workshop/5.2-Prerequiste/_index.md`
- `content/5-Workshop/5.3-S3-vpc/_index.md`
- `content/5-Workshop/5.4-S3-onprem/_index.md`
- `content/5-Workshop/5.5-Policy/_index.md`
- `content/5-Workshop/5.6-Cleanup/_index.md`

Mỗi phần đều có file tiếng Việt tương ứng `*_index.vi.md`.

Phần này thường dùng để mô tả:

- Workshop bạn đã tham gia
- Môi trường chuẩn bị
- Các bước thực hiện
- Kết quả kiểm tra
- Dọn dẹp tài nguyên

## 8. Mục Self-evaluation

Trang tự đánh giá nằm ở:

- `content/6-Self-evaluation/_index.md`
- `content/6-Self-evaluation/_index.vi.md`

Đây là nơi bạn sửa:

- Tên công ty
- Thời gian thực tập
- Phần mô tả ngắn về quá trình học tập
- Bảng tự chấm điểm
- Mục cần cải thiện

## 9. Mục Feedback

Trang góp ý nằm ở:

- `content/7-Feedback/_index.md`
- `content/7-Feedback/_index.vi.md`

Phần này dùng để viết:

- Nhận xét về môi trường làm việc
- Mức độ hỗ trợ của mentor hoặc admin
- Những điều học được
- Góp ý cải thiện

## 10. Các file cấu hình và giao diện

Nếu bạn muốn sửa toàn bộ site, các file sau cũng rất quan trọng:

- `config.toml`: đổi tiêu đề site, ngôn ngữ mặc định, menu, base URL.
- `layouts/partials/custom-footer.html`: sửa footer riêng của site.
- `layouts/partials/logo.html`: đổi logo nếu site có dùng.
- `layouts/shortcodes/`: sửa hoặc thêm shortcode đặc biệt.
- `static/css/theme-mine.css`: CSS riêng nếu bạn muốn tinh chỉnh giao diện.

## 11. Cách kiểm tra sau khi sửa

Mỗi lần chỉnh xong, chạy:

```powershell
hugo server -D
```

Nếu muốn xuất bản bản tĩnh, chạy:

```powershell
hugo --minify
```

## 12. Gợi ý thứ tự nên sửa

1. Sửa trang bìa trong `content/_index.md` và `content/_index.vi.md`.
2. Sửa ảnh đại diện trong `static/images/avatar.png`.
3. Sửa `Proposal` để đổi đề tài cho khớp với thực tập của bạn.
4. Sửa từng tuần Worklog để phản ánh đúng việc đã làm.
5. Sửa `Workshop`, `Events`, `Blogs`, `Self-evaluation`, `Feedback`.
6. Chạy lại `hugo server -D` để xem kết quả.

## 13. Tóm tắt ngắn

Nếu chỉ cần nhớ nhanh, bạn gần như chỉ phải sửa các file trong `content/`, thay ảnh trong `static/images/`, và kiểm tra cấu hình trong `config.toml`. Phần giao diện chỉ cần đụng tới `layouts/` hoặc `static/css/` khi muốn chỉnh sâu hơn.