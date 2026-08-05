---
title: "ECS Fargate và ALB"
date: 2026-08-03
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---
Sau khi pipeline GitHub Actions đã sẵn sàng, mục này triển khai **NeonFoodMap** trên Amazon ECS Fargate. Frontend và backend chạy trong private subnet; Application Load Balancer (ALB) nhận lưu lượng Internet, thực hiện health check và định tuyến request đến đúng service.

## Tạo ECS Cluster và Task Definition

NeonFoodMap sử dụng **Amazon ECS Fargate**, do đó đội dự án chỉ quản lý container và cấu hình task, không phải vận hành EC2 host. Backend và frontend được chạy bằng hai task definition riêng để có thể cập nhật, theo dõi log và phân quyền độc lập.

### Tạo Service Discovery Namespace

Trước khi tạo cluster, đăng ký namespace nội bộ trên **AWS Cloud Map**. Namespace cung cấp DNS private để các service trong VPC trao đổi và làm việc với nhau bằng tên ổn định thay vì phải dùng IP thay đổi theo từng Fargate task.

1. Mở **AWS Cloud Map Console** và chọn **Create namespace**.
2. Nhập *Namespace name* là `NeonFoodmap.internal`; mô tả là `Use for internal API Calls and DNS`.
3. Tại *Instance discovery*, chọn **API calls and DNS queries in VPCs**. Chọn VPC `NeonFoodmap` đã tạo cho dự án.
4. Giữ TTL mặc định `20 seconds`, sau đó nhấn **Create namespace**.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCloudMap.jpg" alt="picCloudMap" >}}

### Tạo Cloud Map Services cho Backend và Frontend

Trong namespace `NeonFoodmap.internal`, chọn **Create service**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCLoudMapService.jpg" alt="picCLoudMapService" >}}

| Cấu hình     | Backend                                         | Frontend                                          |
| -------------- | ----------------------------------------------- | ------------------------------------------------- |
| Service name   | `backend` → `backend.neonfoodmap.internal` | `frontend` → `frontend.neonfoodmap.internal` |
| Description    | Neon Foodmap Backend Service Discovery Name     | Neon Foodmap Frontend Service Discovery Name      |
| Routing policy | `WEIGHTED`                                    | `WEIGHTED`                                      |
| Record type    | `A`                                           | `A`                                             |
| DNS TTL        | `300` seconds                                 | `300` seconds                                   |
| Health check   | No health check                                 | No health check                                   |

Các service discovery record này sẽ được ECS service đăng ký/hủy đăng ký theo vòng đời task. Health check phục vụ traffic công khai vẫn do Target Group của ALB đảm nhiệm ở các bước sau.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picFinalSetupCloudMap.jpg" alt="picFinalSetupCloudMap" >}}

### Tạo ECS Cluster

1. Mở **Amazon ECS Console**, chọn **Clusters** ở thanh điều hướng bên trái và nhấn **Create cluster**.
2. Nhập tên cluster `NeonFoodmap-cluster`; chọn hạ tầng serverless **AWS Fargate**.
3. Chọn service connect là service discovery đã được chuẩn bị ở bước 1–2.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateCluster1.jpg" alt="picCreateCluster1" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateCluster2.jpg" alt="picCreateCluster2" >}}

### Tạo Backend Task Definition và Frontend Task Definition

1. Trong ECS, chọn **Task definitions → Create new task definition**.
2. Chọn **AWS Fargate**, *Operating system/Architecture* là **Linux/X86_64**. Nhập *Task definition family* là `neonfoodmap-task-be`.  {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateNewTaskDefinition12.jpg" alt="picCreateNewTaskDefinition12" >}}
3. Chọn **Task CPU** `256` và **Task memory** `512 MiB`. Gán `NeonFoodmap-ECS-TaskExecution-Role` tại *Task execution role* để ECS pull image từ ECR và ghi log; gán `NeonFoodmap-ECS-Backend-Role` tại *Task role* cho quyền mà ứng dụng backend cần sử dụng.
4. Thêm container backend, chọn image từ ECR repository `neonfoodmap-backend` và khai báo *Container port* `8000` với protocol TCP. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateNewTaskDefinition4.jpg" alt="picCreateNewTaskDefinition4" >}} {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateNewTaskDefinition42.jpg" alt="picCreateNewTaskDefinition42" >}}
5. Khai báo các biến cần thiết cho task definition. Với thông tin password quan trọng chọn ValueFrom để đọc từ AWS Secrets Manager. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateNewTaskDefinition5.jpg" alt="picCreateNewTaskDefinition5" >}}
6. Trong phần logging, chọn CloudWatch Logs, tạo/chọn log group `/ecs/neonfoodmap-backend` và region `ap-southeast-1`. Thiết lập log retention theo chính sách dự án. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateNewTaskDefinition6.jpg" alt="picCreateNewTaskDefinition6" >}}
7. Nhấn **Create** để đăng ký revision.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/FinalBakend1.jpg" alt="FinalBakend1" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/FinalBakend2.jpg" alt="FinalBakend2" >}}

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

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/FinalFrontend1.jpg" alt="FinalFrontend1" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/FinalTaskDefinitions.jpg" alt="FinalTaskDefinitions" >}}

## Tạo Application Load Balancer

ALB được đặt trong hai public subnet, nhận lưu lượng Internet và chỉ chuyển tiếp request vào ECS task trong private subnet. Lớp phân tách này giúp frontend/backend không nhận truy cập trực tiếp từ Internet.

### Tạo Security Group cho ALB

1. Mở **EC2 Console → Security Groups → Create security group**.
2. Đặt tên `alb-sg`, mô tả *Security Group cho Public Application Load Balancer* và chọn VPC của dự án.
3. Thêm hai inbound rule: **HTTP / 80 / Anywhere-IPv4 (`0.0.0.0/0`)** và **HTTPS / 443 / Anywhere-IPv4 (`0.0.0.0/0`)**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBSecutiryGroup.jpg" alt="picALBSecutiryGroup" >}}
4. Giữ outbound rule mặc định để ALB có thể gửi request tới ECS task, sau đó chọn **Create security group**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBSGOutbound.jpg" alt="picALBSGOutbound" >}}

Security group của ECS task cấu hình theo chiều ngược lại: chỉ cho phép port `80` của frontend và port `8000` của backend có source là `alb-sg`; không mở các port này cho `0.0.0.0/0`. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picECS_SG.jpg" alt="picECS_SG" >}}

### Tạo Target Group và Health Check

ECS Fargate cấp private IP cho task, vì vậy cả hai target group phải dùng **Target type: IP addresses**. Không cần đăng ký IP thủ công vì ECS service sẽ tự đăng ký/deregister task khi deploy.

1. Vào **EC2 Console → Target Groups → Create target group** và tạo target group frontend với cấu hình sau. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picTG_HC1.jpg" alt="picTG_HC1" >}}

| Trường                      | Giá trị frontend     |
| ----------------------------- | ---------------------- |
| Target group name             | `TG-NeonFoodMap-FE`  |
| Target type                   | **IP addresses** |
| Protocol / Port               | HTTP /`80`           |
| Health check path             | `/`                  |
| Healthy / Unhealthy threshold | `2` / `2`          |
| Health check interval         | `30 seconds`         |

2. Chọn **Next**, bỏ qua bước đăng ký target IP và chọn **Create target group**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picTG_HC2.jpg" alt="picTG_HC2" >}}
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

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picTG_HC_Final.jpg" alt="picTG_HC_Final" >}}

### Tạo ALB và Listener

1. Vào **EC2 Console → Load Balancers → Create load balancer**, chọn **Application Load Balancer**.
2. Nhập tên `ALB-NeonFoodMap`, chọn *Scheme* **Internet-facing** và *IP address type* **IPv4**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBCreate1.jpg" alt="picALBCreate1" >}}
3. Chọn VPC của dự án. Trong *Network mapping*, chọn đủ hai Availability Zone và hai **public subnet** có route đến Internet Gateway. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBCreate2.jpg" alt="picALBCreate2" >}}
4. Bỏ chọn default security group, sau đó chọn `alb-sg` đã tạo ở bước 4.
5. Tạo listener **HTTP : 80**. Ở *Default action*, chọn **Forward to** `TG-NeonFoodMap-FE`, rồi nhấn **Create load balancer**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBCreate3.jpg" alt="picALBCreate3" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBCreate4.jpg" alt="picALBCreate4" >}}

### Thêm rule định tuyến API

1. Chọn `ALB-NeonFoodMap`, mở tab **Listeners and rules** và chọn listener **HTTP:80**.
2. Chọn **Add rule**. Đặt tên `route-backend-api`, thêm condition *Path* với giá trị `/api/*`.
3. Ở action, chọn **Forward to** `TG-NeonFoodMap-BE`; đặt priority, ví dụ `10`, rồi lưu rule. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBFinal1.jpg" alt="picALBFinal1" >}}

Sau cấu hình này, mọi request thông thường được chuyển đến frontend target group; request có đường dẫn `/api/*` được chuyển đến backend target group. Khi triển khai HTTPS, thêm listener `443` gắn ACM certificate và chuyển hướng listener `80` sang HTTPS.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBFinal2.jpg" alt="picALBFinal2" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picALBFinal3.jpg" alt="picALBFinal3" >}}

## Tạo ECS Service và liên kết Target Group

Sau khi có task definition, target group và ALB, tạo từng ECS service để ECS tự quản lý vòng đời task và đăng ký private IP vào đúng target group.

### Tạo Backend Service

1. Mở **ECS → Clusters → NeonFoodmap-cluster → Create service**.
2. Chọn task definition family `neonfoodmap-task-be`, đặt service name `svc-neonfoodmap-be` và chọn **Task definition revision latest**. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateECS12.jpg" alt="picCreateECS12" >}}
3. Trong phần networking, chọn VPC và các **private subnet** của ứng dụng, chọn ECS task security group. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateECS3.jpg" alt="picCreateECS3" >}}
4. **Load balancing**, chọn **Application Load Balancer** `ALB-NeonFoodMap`; chọn existing listener `80:HTTP`, container backend port `8000` và existing target group `TG-NeonFoodMap-BE`. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateECS4.jpg" alt="picCreateECS4" >}}
5. Kiểm tra cấu hình, chọn **Create**. ECS sẽ tự tạo task, đăng ký IP task vào backend target group và Cloud Map. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateECS5.jpg" alt="picCreateECS5" >}}

### Tạo Frontend Service

Lặp lại quy trình tạo service cho frontend: chọn task definition `neonfoodmap-task-fe`, service name `svc-neonfoodmap-fe`, private subnet và ECS task security group. **Load balancing**, chọn `ALB-NeonFoodMap`, listener `80:HTTP`, container frontend port `80` và target group `TG-NeonFoodMap-FE`.

Sau khi tạo, ECS thực hiện rolling deployment. Trong thời gian này, giữ task đủ `Healthy` để ALB không chuyển request vào task chưa sẵn sàng.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.3-oidc-ecs-alb/picCreateECS6.jpg" alt="picCreateECS6" >}}
