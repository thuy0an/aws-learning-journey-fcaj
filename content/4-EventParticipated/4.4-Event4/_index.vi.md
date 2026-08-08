---
title: "AWS FCAJ Agent Forge – Deepdive"
date: 2026-08-01
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---
# Bài thu hoạch: AWS FCAJ Agent Forge – Deepdive (Ngày 1)

### Tổng quan sự kiện

Sự kiện là buổi workshop chuyên sâu cấp độ nâng cao (**L300 – Advanced Level**) hợp tác cùng cộng đồng First Cloud AI Journey (FCAJ). Buổi học chia sẻ tài liệu và kiến thức thực tế từ các nhân viên AWS, hướng dẫn kỹ sư và doanh nghiệp xây dựng hệ thống Agentic AI hoàn chỉnh từ giai đoạn thử nghiệm (*Proof of Concept*) đến khi sẵn sàng vận hành trên môi trường production.

### Mục đích của sự kiện

- **Tiếp cận kiến thức nâng cao:** hiểu các thách thức về hiệu năng (*performance*), khả năng mở rộng (*scalability*), bảo mật (*security*) và quản trị (*governance*) khi đưa Agent vào thực tế.
- **Làm chủ bộ ba tính năng cốt lõi:** tiếp cận lý thuyết chuyên sâu về Runtime, Identity và Gateway trong kiến trúc Amazon Bedrock AgentCore.
- **Thực hành thực chiến:** cài đặt công cụ, cấu hình mã nguồn qua trợ lý AI Kiro và triển khai Agent trên môi trường đám mây AWS.

### Danh sách diễn giả

- **Anh Nghĩa:** Senior Speaker, điều phối chương trình và chia sẻ khung lý thuyết nền tảng về Agentic AI cùng kiến trúc doanh nghiệp.
- **Anh Hải Anh:** Host phần hands-on lab, hướng dẫn cấu hình dự án, cài đặt dependency và kiểm thử Agent.

### Workshop

Đây là chuỗi workshop kéo dài ba ngày, được thiết kế theo lộ trình từ kiến thức nền tảng đến triển khai Agent production với Amazon Bedrock AgentCore.

1. **Ngày 1 (01/08): AgentCore Foundations** — kiến trúc tổng quan, Runtime, Gateway và Identity.
2. **Ngày 2 (08/08): Memory, Evaluations, Observability & Optimization** — quản lý memory, đánh giá Agent, giám sát và tối ưu hiệu suất.
3. **Ngày 3 (15/08): DevOps, Policies & Production Best Practices** — DevOps, policies, bảo mật và thực hành production.

### Nội dung nổi bật

#### Agentic AI và mức độ tự chủ

**Agentic AI** là hệ thống có khả năng tự chủ hoàn thành mục tiêu thay vì chỉ phản hồi từng câu lệnh. Sau khi nhận mục tiêu, Agent có thể lập kế hoạch, chọn công cụ, thực hiện từng bước, đánh giá kết quả và điều chỉnh kế hoạch.

Ví dụ, với yêu cầu triển khai ứng dụng lên AWS, Agent có thể build ứng dụng, tạo Docker image, đẩy image lên registry, triển khai dịch vụ cloud, kiểm tra trạng thái và báo cáo kết quả.

Workshop phân loại Agent theo phổ tự chủ:

1. **Deterministic agents** — hoạt động theo quy tắc cố định, ví dụ format mã nguồn hoặc chạy workflow CI có sẵn.
2. **Reactive agents** — phản hồi đầu vào mà không lập kế hoạch trước, ví dụ GitHub Copilot sinh mã từ yêu cầu.
3. **Goal-oriented agents** — lập kế hoạch để đạt mục tiêu, ví dụ phân tích yêu cầu, viết mã, tạo API và kiểm thử một chức năng.
4. **Learning agents** — học từ kinh nghiệm để cải thiện cách xử lý ở những lần sau.
5. **Multi-agent systems** — các Agent về coding, testing, security và DevOps phối hợp hoàn thành dự án.

#### Amazon Bedrock AgentCore

**Amazon Bedrock AgentCore** là dịch vụ AWS hỗ trợ xây dựng, triển khai và vận hành AI Agent trong production. Dịch vụ cung cấp hạ tầng được quản lý hoàn toàn, giúp nhà phát triển tập trung vào logic của Agent.

- **Serverless Runtime**: môi trường thực thi không cần quản lý máy chủ.
- **Tự động mở rộng**: điều chỉnh tài nguyên theo lưu lượng truy cập.
- **Bảo mật tích hợp**: hỗ trợ xác thực, phân quyền và tích hợp dịch vụ bảo mật AWS.
- **Quản lý vòng đời Agent**: hỗ trợ phát triển, kiểm thử, triển khai và vận hành.

Kiến trúc serverless cùng mô hình thanh toán theo mức sử dụng giúp giảm chi phí vận hành, rút ngắn thời gian phát triển và đáp ứng lưu lượng thay đổi.

#### Runtime, memory và streaming

Amazon Bedrock AgentCore Runtime là môi trường thực thi được quản lý để chạy Agent trong production. Agent khởi chạy theo yêu cầu; mỗi lần thực thi diễn ra trong một **Firecracker MicroVM** cô lập, giúp tăng bảo mật và duy trì môi trường nhất quán. Runtime tự động mở rộng theo số lượng yêu cầu và hỗ trợ quản lý trạng thái xuyên suốt phiên làm việc.

Các cơ chế quản lý ngữ cảnh gồm:

- **Session Memory** lưu ngữ cảnh trong một phiên hoặc cuộc hội thoại.
- **Long-term Memory** lưu thông tin lâu dài để tái sử dụng ở các phiên sau.
- **Context Management** tối ưu lượng ngữ cảnh truyền đến mô hình ngôn ngữ.

Runtime còn hỗ trợ **Streaming Response** và **Progress Updates**, cho phép trả kết quả từng phần và hiển thị tiến độ sớm hơn đối với các tác vụ mất nhiều thời gian.

#### Identity và bảo mật

AgentCore hỗ trợ xác thực và phân quyền để Agent truy cập tài nguyên an toàn thông qua JSON Web Token (JWT), Amazon Cognito, AWS IAM và xác thực giữa các dịch vụ. Khi gọi công cụ hoặc API bên ngoài, Agent chỉ được cấp quyền cần thiết theo nguyên tắc **least privilege**; hoạt động được ghi nhận bằng AWS CloudTrail và dữ liệu truyền qua HTTPS/TLS.

Các thực hành bảo mật cần áp dụng:

- Triển khai trong Amazon VPC khi cần cô lập mạng.
- Lưu API key và thông tin nhạy cảm trong AWS Secrets Manager.
- Áp dụng least privilege cho mọi IAM role và policy.
- Theo dõi hoạt động bằng Amazon CloudWatch và AWS CloudTrail để phát hiện sự cố, hỗ trợ kiểm toán.

#### Gateway, middleware và human-in-the-loop

**Amazon Bedrock AgentCore Gateway** là lớp trung gian kết nối AI Agent với công cụ, API và các dịch vụ bên ngoài. Gateway hỗ trợ định tuyến yêu cầu, quản lý API, xác thực, phân quyền và giám sát các kết nối của Agent.

Middleware bổ sung các chức năng chuyển đổi dữ liệu, caching, retry/error handling, logging và monitoring. Với tác vụ quan trọng như phê duyệt giao dịch, gửi thông báo hàng loạt hoặc công khai nội dung, cơ chế **human-in-the-loop (HITL)** yêu cầu con người phê duyệt trước khi Agent tiếp tục.

#### Phần thực hành

Người tham gia quan sát vòng đời xử lý của Agent qua bốn bước:

1. **Reason** — phân tích yêu cầu và xác định mục tiêu.
2. **Plan** — lập kế hoạch, chia nhỏ nhiệm vụ.
3. **Execute** — gọi công cụ hoặc API để thực hiện.
4. **Reflect & Adapt** — đánh giá kết quả, điều chỉnh kế hoạch khi cần.

Các tình huống thực hành gồm chatbot hỗ trợ khách hàng, trợ lý phân tích dữ liệu, tự động hóa quy trình nghiệp vụ nhiều bước và Agent hỗ trợ phát triển phần mềm. Workshop cũng giới thiệu prompt engineering: viết hướng dẫn rõ ràng, cung cấp ngữ cảnh, xác định vai trò/giới hạn, quy định định dạng đầu ra và tinh chỉnh prompt theo kết quả.

Để tối ưu workflow, cần hạn chế lần gọi API không cần thiết, chạy song song khi phù hợp, tận dụng caching, thiết lập timeout và xử lý lỗi để tăng độ ổn định.

### Những gì học được

#### Kiến thức chuyên môn

- Hiểu Agentic AI và sự khác biệt với ứng dụng AI truyền thống.
- Nắm các cấp độ tự chủ từ Deterministic Agent đến Multi-Agent System.
- Hiểu kiến trúc Amazon Bedrock AgentCore: Runtime, Gateway và Identity.
- Biết cách Agent lập kế hoạch, sử dụng công cụ và hoàn thành nhiệm vụ nhiều bước.
- Hiểu các cơ chế bảo mật JWT, Amazon Cognito, IAM và nguyên tắc least privilege.

#### Kiến thức triển khai

- Hiểu quy trình xây dựng và triển khai AI Agent trong production.
- Biết cách tích hợp Agent với API và công cụ bên ngoài.
- Nhận thức vai trò của HITL với tác vụ cần phê duyệt.
- Nắm kỹ thuật prompt engineering và tối ưu workflow cơ bản.

#### Bài học kinh nghiệm

- Thiết kế Agent theo từng chức năng nhỏ trước khi xây dựng hệ thống phức tạp.
- Luôn ưu tiên bảo mật và phân quyền khi Agent truy cập tài nguyên.
- Theo dõi, đánh giá và tối ưu Agent dựa trên kết quả thực tế.
- Xây dựng hệ thống theo hướng dễ mở rộng và dễ bảo trì.

### Trải nghiệm trong workshop

Ngày 1 của **AWS FCAJ Agent Forge – Deepdive** mang lại cái nhìn tổng quan về cách xây dựng và vận hành AI Agent trong môi trường doanh nghiệp. Nội dung minh họa giúp em hiểu quy trình từ phân tích yêu cầu, lập kế hoạch, sử dụng công cụ đến hoàn thành mục tiêu; đồng thời tiếp cận các thành phần Runtime, Gateway và Identity của Amazon Bedrock AgentCore.

Workshop kết hợp lý thuyết với các ví dụ về tự động hóa quy trình, hỗ trợ khách hàng và phát triển phần mềm. Kiến thức về prompt engineering, tối ưu workflow, bảo mật và triển khai production tạo nền tảng để tiếp tục nghiên cứu các chủ đề chuyên sâu trong những buổi workshop tiếp theo.

#### Một số hình ảnh khi tham gia sự kiện

{{< event-image src="images/4-EventParticipated/Event_4_8_pic1.jpg" alt="Event_4_8_pic1" >}}

{{< event-image src="images/4-EventParticipated/Event_4_8_pic2.jpg" alt="Event_4_8_pic2" >}}

{{< event-image src="images/4-EventParticipated/Event_4_8_pic3.jpg" alt="Event_4_8_pic3" >}}

{{< event-image src="images/4-EventParticipated/Event_4_8_pic4.jpg" alt="Event_4_8_pic4" >}}
