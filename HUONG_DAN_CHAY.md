# Hướng dẫn chạy FCJ Workshop Template

Đây là một website tài liệu được xây dựng bằng [Hugo](https://gohugo.io/) với theme `hugo-theme-learn`. Dự án này dùng để làm báo cáo thực tập, hiển thị nội dung theo dạng menu nhiều cấp và hỗ trợ song ngữ Anh/Việt.

## 1. Yêu cầu môi trường

- Cài `Hugo Extended`.
- Nên dùng đúng phiên bản Hugo mà workflow của dự án đang dùng: `0.134.3`.
- Sau khi sửa shortcode `ghcontributors`, dự án đã build được với Hugo mới hơn trên máy local.
- Có `Git` để lấy submodule của theme.

## 2. Lấy mã nguồn

Mở PowerShell tại thư mục dự án:

```powershell
cd D:\YayCode\CodeWithPython\fcj-workshop-template
```

Nếu theme chưa được tải về, chỉ init submodule của theme:

```powershell
git submodule update --init --recursive themes/hugo-theme-learn
```

Lưu ý: nếu lệnh `git submodule update --init --recursive` báo lỗi liên quan đến `public`, thì không cần xử lý `public` bằng submodule. `public` là thư mục output khi build site, không phải phần cần init để chạy nội dung.

## 3. Chạy website ở máy local

Chạy lệnh sau để mở site ở chế độ phát triển:

```powershell
hugo server -D
```

Sau khi chạy thành công, mở trình duyệt tại địa chỉ được Hugo in ra, thường là:

```text
http://localhost:1313/
```

Tuỳ chọn `-D` cho phép hiển thị cả các trang đang để trạng thái draft.

## 4. Build bản tĩnh

Nếu muốn tạo bản tĩnh để deploy, dùng:

```powershell
hugo --minify
```

Kết quả sẽ được sinh ra trong thư mục `public/`.

## 5. Triển khai tự động

Repo có GitHub Actions ở `.github/workflows/hugo.yml` để build và deploy lên GitHub Pages khi push vào nhánh `main`.

Quy trình của workflow là:

1. Checkout source code.
2. Cài Hugo Extended `0.134.3`.
3. Chạy `hugo --minify`.
4. Đẩy thư mục `public/` lên nhánh `gh-pages`.

## 6. Lỗi thường gặp

### 6.1. Lỗi `No url found for submodule path 'public'`

Nguyên nhân thường là repo đang có trạng thái submodule không đồng bộ hoặc có gitlink cũ cho `public`.

Cách xử lý nhanh:

- Không cần init `public`.
- Chỉ init submodule theme nếu cần.
- Nếu vẫn lỗi khi clone, dùng `git status` để kiểm tra trạng thái repository và bỏ qua thư mục output `public/`.

### 6.2. Lỗi `function "getJSON" not defined`

Lỗi này xuất hiện khi dùng Hugo mới hơn phiên bản mà theme hoặc shortcode trong repo hỗ trợ. Trong repo này, lỗi đã được xử lý bằng cách thay shortcode cũ bằng `resources.GetRemote` và `transform.Unmarshal`.

Cách xử lý nhanh nếu bạn gặp lại ở bản chưa sửa:

- Dùng Hugo Extended `0.134.3` như trong workflow.
- Hoặc sửa shortcode `layouts/shortcodes/ghcontributors.html` để tương thích với phiên bản Hugo mới hơn.

## 7. Xác nhận chạy thành công

Sau khi sửa shortcode, mình đã kiểm tra lại bằng:

```powershell
hugo --minify
```

Kết quả build đã chạy thành công, nên bạn có thể tiếp tục dùng:

```powershell
hugo server -D
```

## 8. Tóm tắt ngắn để đưa vào báo cáo

Website báo cáo thực tập được xây dựng bằng Hugo với theme `hugo-theme-learn`. Dữ liệu nội dung được viết bằng Markdown trong thư mục `content/`, còn giao diện và shortcode được cấu hình trong `layouts/` và `config.toml`. Khi chạy local, dự án được khởi động bằng `hugo server -D`; khi build, dùng `hugo --minify`. Bản triển khai chính thức được tự động hoá qua GitHub Actions và xuất ra thư mục `public/`.
