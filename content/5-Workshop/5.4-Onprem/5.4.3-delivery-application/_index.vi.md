---
title: "GitHub Actions, ECS và Application Load Balancer"
date: 2026-08-03
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---
Sau khi đã có image Docker trên Amazon ECR, giai đoạn này thiết lập quy trình đưa ứng dụng **NeonFoodMap** lên Amazon ECS Fargate. GitHub Actions kiểm tra mã nguồn, build/push image và yêu cầu AWS cấp quyền qua OIDC; ECS chạy hai container frontend và backend trong private subnet; Application Load Balancer (ALB) tiếp nhận lưu lượng Internet và định tuyến dịch vụ.

## Tạo GitHub OIDC Identity Provider

GitHub Actions cần một **Identity Provider** để đổi token do GitHub phát hành thành temporary credential của **AWS STS**.

1. Truy cập dịch vụ **Identity and Access Management (IAM)**. Trong thanh điều hướng bên trái, chọn **Access management → Identity providers**, sau đó chọn **Add provider**.
2. Tại **Provider type**, chọn **OpenID Connect**. Điền hai giá trị sau:

| Trường     | Giá trị                                       |
| ------------ | ----------------------------------------------- |
| Provider URL | `https://token.actions.githubusercontent.com` |
| Audience     | `sts.amazonaws.com`                           |

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateOIDC.jpg" alt="picCreateOIDC" >}}

Sau khi tạo, kiểm tra provider xuất hiện với URL `https://token.actions.githubusercontent.com`. Provider này chỉ xác thực danh tính token; quyền triển khai thực tế được cấp thông qua IAM Role ở bước kế tiếp.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picOIDCSuccess.jpg" alt="picOIDCSuccess" >}}

## Tạo IAM Role cho GitHub Actions

Role `NeonFoodmap-GitHub-Actions-Role` cho phép workflow lấy quyền tạm thời khi chạy. Role dùng **Web identity**, không sử dụng access key/secret key của IAM User.

1. Trong IAM, mở **Access management → Roles** và chọn **Create role**.
2. Chọn **Web identity** tại *Trusted entity type*; chọn GitHub OIDC Provider vừa tạo và audience `sts.amazonaws.com`. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateRole1.jpg" alt="picCreateRole1" >}}
3. Gắn các quyền cần thiết để push image ECR và cập nhật ECS. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picRolePermission1.jpg" alt="picRoleFinal" >}} {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picRolePermission2.jpg" alt="picRoleFinal" >}}
4. Mở role vừa tạo, vào **Trust relationships → Edit trust policy**. Điều kiện tin cậy phải kiểm tra cả audience và định danh repository. Với workflow production, giới hạn token ở nhánh `main` theo mẫu sau:

```json
{
  "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
  "token.actions.githubusercontent.com:sub": "repo:HaoWasabi/NeonFoodmap:ref:refs/heads/main"
}
```

5. Lưu policy và kiểm tra lại Trusted Entity. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picSetupPolicy.jpg" alt="picSetupPolicy" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picRoleFinal.jpg" alt="picRoleFinal" >}}

## Quy trình CI/CD với GitHub Actions

### CI — Kiểm tra mã nguồn

CI chạy khi có pull request vào `main` hoặc push vào `main`, `develop`, `feature/**` có thay đổi ở frontend/backend:

- **Backend:** chạy `flake8` và Django unit test.
- **Frontend:** chạy `npm ci`, ESLint và build.
- **E2E:** sau khi frontend check thành công, Playwright chạy các kịch bản `critical`; báo cáo được lưu 7 ngày.

Chỉ khi backend test và E2E test thành công, pipeline mới được phép tạo image để triển khai.

### CD — Build và triển khai

CD chỉ chạy khi **push vào `main`** và toàn bộ kiểm tra CI đã pass:

- GitHub Actions dùng OIDC để xác thực AWS, build/push backend và frontend image lên Amazon ECR với tag `latest` và `sha-<git-short-sha>`.
- Hai job deploy chạy song song: cập nhật task definition rồi deploy `svc-neonfoodmap-be` và `svc-neonfoodmap-fe` lên ECS. Backend chạy thêm migration và kiểm tra kết nối RDS.
- Sau khi hai service ổn định, smoke test gọi API gốc cùng các endpoint `/api/pois/`, `/api/tours/`, `/api/categories/`; pipeline báo lỗi nếu nhận HTTP 5xx.

**Workflow: `deploy.yml` — tự động triển khai khi push vào `main`**

```text
backend-test ─────┐
                  ├──▶ build-and-push ──┬──▶ deploy-backend  ──┐
frontend-check ──▶ e2e-tests ───────────┘                      ├──▶ smoke-tests
                                             └──▶ deploy-frontend ─┘
```

## Tạo ECS Cluster và Task Definition

NeonFoodMap sử dụng **Amazon ECS Fargate**, do đó đội dự án chỉ quản lý container và cấu hình task, không phải vận hành EC2 host. Backend và frontend được chạy bằng hai task definition riêng để có thể cập nhật, theo dõi log và phân quyền độc lập.

### Tạo Service Discovery Namespace

Trước khi tạo cluster, đăng ký namespace nội bộ trên **AWS Cloud Map**. Namespace cung cấp DNS private để các service trong VPC trao đổi và làm việc với nhau bằng tên ổn định thay vì phải dùng IP thay đổi theo từng Fargate task.

1. Mở **AWS Cloud Map Console** và chọn **Create namespace**.
2. Nhập *Namespace name* là `NeonFoodmap.internal`; mô tả là `Use for internal API Calls and DNS`.
3. Tại *Instance discovery*, chọn **API calls and DNS queries in VPCs**. Chọn VPC `NeonFoodmap` đã tạo cho dự án.
4. Giữ TTL mặc định `20 seconds`, sau đó nhấn **Create namespace**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCloudMap.jpg" alt="picCloudMap" >}}

### Tạo Cloud Map Services cho Backend và Frontend

Trong namespace `NeonFoodmap.internal`, chọn **Create service**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCLoudMapService.jpg" alt="picCLoudMapService" >}}

| Cấu hình     | Backend                                         | Frontend                                          |
| -------------- | ----------------------------------------------- | ------------------------------------------------- |
| Service name   | `backend` → `backend.neonfoodmap.internal` | `frontend` → `frontend.neonfoodmap.internal` |
| Description    | Neon Foodmap Backend Service Discovery Name     | Neon Foodmap Frontend Service Discovery Name      |
| Routing policy | `WEIGHTED`                                    | `WEIGHTED`                                      |
| Record type    | `A`                                           | `A`                                             |
| DNS TTL        | `300` seconds                                 | `300` seconds                                   |
| Health check   | No health check                                 | No health check                                   |

Các service discovery record này sẽ được ECS service đăng ký/hủy đăng ký theo vòng đời task. Health check phục vụ traffic công khai vẫn do Target Group của ALB đảm nhiệm ở các bước sau.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picFinalSetupCloudMap.jpg" alt="picFinalSetupCloudMap" >}}

### Tạo ECS Cluster

1. Mở **Amazon ECS Console**, chọn **Clusters** ở thanh điều hướng bên trái và nhấn **Create cluster**.
2. Nhập tên cluster `NeonFoodmap-cluster`; chọn hạ tầng serverless **AWS Fargate**.
3. Chọn service connect là service discovery đã được chuẩn bị ở bước 1–2.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateCluster1.jpg" alt="picCreateCluster1" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateCluster2.jpg" alt="picCreateCluster2" >}}

### Tạo Backend Task Definition và Frontend Task Definition

1. Trong ECS, chọn **Task definitions → Create new task definition**.
2. Chọn **AWS Fargate**, *Operating system/Architecture* là **Linux/X86_64**. Nhập *Task definition family* là `neonfoodmap-task-be`.  {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateNewTaskDefinition12.jpg" alt="picCreateNewTaskDefinition12" >}}
3. Chọn **Task CPU** `256` và **Task memory** `512 MiB`. Gán `NeonFoodmap-ECS-TaskExecution-Role` tại *Task execution role* để ECS pull image từ ECR và ghi log; gán `NeonFoodmap-ECS-Backend-Role` tại *Task role* cho quyền mà ứng dụng backend cần sử dụng.
4. Thêm container backend, chọn image từ ECR repository `neonfoodmap-backend` và khai báo *Container port* `8000` với protocol TCP. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateNewTaskDefinition4.jpg" alt="picCreateNewTaskDefinition4" >}} {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateNewTaskDefinition42.jpg" alt="picCreateNewTaskDefinition42" >}}
5. Khai báo các biến cần thiết cho task definition. Với thông tin password quan trọng chọn ValueFrom để đọc từ AWS Secrets Manager. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateNewTaskDefinition5.jpg" alt="picCreateNewTaskDefinition5" >}}
6. Trong phần logging, chọn CloudWatch Logs, tạo/chọn log group `/ecs/neonfoodmap-backend` và region `ap-southeast-1`. Thiết lập log retention theo chính sách dự án. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateNewTaskDefinition6.jpg" alt="picCreateNewTaskDefinition6" >}}
7. Nhấn **Create** để đăng ký revision.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/FinalBakend1.jpg" alt="FinalBakend1" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/FinalBakend2.jpg" alt="FinalBakend2" >}}

### Frontend Task Definition

Thực hiện tương tự backend, nhưng tạo family và container frontend độc lập. Việc tách task definition giúp frontend có thể được deploy mà không tạo revision backend và ngược lại.

| Mục cấu hình      | Backend                      | Frontend                      |
| -------------------- | ---------------------------- | ----------------------------- |
| Task family          | `neonfoodmap-task-be`      | `neonfoodmap-task-fe`       |
| ECR image            | `neonfoodmap-backend`      | `neonfoodmap-frontend`      |
| Container port       | `8000`                     | `80`                        |
| CPU / Memory         | `256` / `512 MiB`        | `256` / `512 MiB`         |
| CloudWatch log group | `/ecs/neonfoodmap-backend` | `/ecs/neonfoodmap-frontend` |

Với frontend, chọn image từ `neonfoodmap-frontend`, đặt container port `80` và log group `/ecs/neonfoodmap-frontend`.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/FinalFrontend1.jpg" alt="FinalFrontend1" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/FinalTaskDefinitions.jpg" alt="FinalTaskDefinitions" >}}

## Tạo Application Load Balancer

ALB được đặt trong hai public subnet, nhận lưu lượng Internet và chỉ chuyển tiếp request vào ECS task trong private subnet. Lớp phân tách này giúp frontend/backend không nhận truy cập trực tiếp từ Internet.

### Tạo Security Group cho ALB

1. Mở **EC2 Console → Security Groups → Create security group**.
2. Đặt tên `alb-sg`, mô tả *Security Group cho Public Application Load Balancer* và chọn VPC của dự án.
3. Thêm hai inbound rule: **HTTP / 80 / Anywhere-IPv4 (`0.0.0.0/0`)** và **HTTPS / 443 / Anywhere-IPv4 (`0.0.0.0/0`)**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBSecutiryGroup.jpg" alt="picALBSecutiryGroup" >}}
4. Giữ outbound rule mặc định để ALB có thể gửi request tới ECS task, sau đó chọn **Create security group**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBSGOutbound.jpg" alt="picALBSGOutbound" >}}

Security group của ECS task cấu hình theo chiều ngược lại: chỉ cho phép port `80` của frontend và port `8000` của backend có source là `alb-sg`; không mở các port này cho `0.0.0.0/0`. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picECS_SG.jpg" alt="picECS_SG" >}}

### Tạo Target Group và Health Check

ECS Fargate cấp private IP cho task, vì vậy cả hai target group phải dùng **Target type: IP addresses**. Không cần đăng ký IP thủ công vì ECS service sẽ tự đăng ký/deregister task khi deploy.

1. Vào **EC2 Console → Target Groups → Create target group** và tạo target group frontend với cấu hình sau. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picTG_HC1.jpg" alt="picTG_HC1" >}}

| Trường                      | Giá trị frontend     |
| ----------------------------- | ---------------------- |
| Target group name             | `TG-NeonFoodMap-FE`  |
| Target type                   | **IP addresses** |
| Protocol / Port               | HTTP /`80`           |
| Health check path             | `/`                  |
| Healthy / Unhealthy threshold | `2` / `2`          |
| Health check interval         | `30 seconds`         |

2. Chọn **Next**, bỏ qua bước đăng ký target IP và chọn **Create target group**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picTG_HC2.jpg" alt="picTG_HC2" >}}
3. Tạo target group backend với thông số dưới đây. Health check path cần trùng endpoint thực tế của Django;

| Trường                      | Giá trị backend      |
| ----------------------------- | ---------------------- |
| Target group name             | `TG-NeonFoodMap-BE`  |
| Target type                   | **IP addresses** |
| Protocol / Port               | HTTP /`8000`         |
| Health check path             | `/api/health/`       |
| Healthy / Unhealthy threshold | `2` / `2`          |
| Health check interval         | `30 seconds`         |

Thông tin của hai Target Group cho Backend và Frontend đã tạo.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picTG_HC_Final.jpg" alt="picTG_HC_Final" >}}

### Tạo ALB và Listener

1. Vào **EC2 Console → Load Balancers → Create load balancer**, chọn **Application Load Balancer**.
2. Nhập tên `ALB-NeonFoodMap`, chọn *Scheme* **Internet-facing** và *IP address type* **IPv4**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBCreate1.jpg" alt="picALBCreate1" >}}
3. Chọn VPC của dự án. Trong *Network mapping*, chọn đủ hai Availability Zone và hai **public subnet** có route đến Internet Gateway. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBCreate2.jpg" alt="picALBCreate2" >}}
4. Bỏ chọn default security group, sau đó chọn `alb-sg` đã tạo ở bước 4.
5. Tạo listener **HTTP : 80**. Ở *Default action*, chọn **Forward to** `TG-NeonFoodMap-FE`, rồi nhấn **Create load balancer**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBCreate3.jpg" alt="picALBCreate3" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBCreate4.jpg" alt="picALBCreate4" >}}

### Thêm rule định tuyến API

1. Chọn `ALB-NeonFoodMap`, mở tab **Listeners and rules** và chọn listener **HTTP:80**.
2. Chọn **Add rule**. Đặt tên `route-backend-api`, thêm condition *Path* với giá trị `/api/*`.
3. Ở action, chọn **Forward to** `TG-NeonFoodMap-BE`; đặt priority, ví dụ `10`, rồi lưu rule. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBFinal1.jpg" alt="picALBFinal1" >}}

Sau cấu hình này, mọi request thông thường được chuyển đến frontend target group; request có đường dẫn `/api/*` được chuyển đến backend target group. Khi triển khai HTTPS, thêm listener `443` gắn ACM certificate và chuyển hướng listener `80` sang HTTPS.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBFinal2.jpg" alt="picALBFinal2" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picALBFinal3.jpg" alt="picALBFinal3" >}}

## Tạo ECS Service và liên kết Target Group

Sau khi có task definition, target group và ALB, tạo từng ECS service để ECS tự quản lý vòng đời task và đăng ký private IP vào đúng target group.

### Tạo Backend Service

1. Mở **ECS → Clusters → NeonFoodmap-cluster → Create service**.
2. Chọn task definition family `neonfoodmap-task-be`, đặt service name `svc-neonfoodmap-be` và chọn **Task definition revision latest**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateECS12.jpg" alt="picCreateECS12" >}}
3. Trong phần networking, chọn VPC và các **private subnet** của ứng dụng, chọn ECS task security group. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateECS3.jpg" alt="picCreateECS3" >}}
4. **Load balancing**, chọn **Application Load Balancer** `ALB-NeonFoodMap`; chọn existing listener `80:HTTP`, container backend port `8000` và existing target group `TG-NeonFoodMap-BE`. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateECS4.jpg" alt="picCreateECS4" >}}
5. Kiểm tra cấu hình, chọn **Create**. ECS sẽ tự tạo task, đăng ký IP task vào frontend target group và Cloud Map. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateECS5.jpg" alt="picCreateECS5" >}}

### Tạo Frontend Service

Lặp lại quy trình tạo service cho frontend: chọn task definition `neonfoodmap-task-fe`, service name `svc-neonfoodmap-fe`, private subnet và ECS task security group. **Load balancing**, chọn `ALB-NeonFoodMap`, listener `80:HTTP`, container backend port `8000` và target group `TG-NeonFoodMap-BE`.

Sau khi tạo, ECS thực hiện rolling deployment. Trong thời gian này, giữ task đủ `Healthy` để ALB không chuyển request vào task chưa sẵn sàng.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3/picCreateECS6.jpg" alt="picCreateECS6" >}}
