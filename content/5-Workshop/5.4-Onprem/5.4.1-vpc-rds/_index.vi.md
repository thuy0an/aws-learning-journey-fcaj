---
title: "VPC và RDS MySQL"
date: 2026-08-03
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---
## Các bước khởi tạo VPC

### Tạo VPC và subnet

Mở **VPC Console** → **Your VPCs** → **Create VPC**. Chọn **VPC and more** và cấu hình:

| Thông số                   | Giá trị       |
| ---------------------------- | --------------- |
| Name tag auto-generation     | `NeonFood`    |
| IPv4 CIDR block              | `10.0.0.0/16` |
| Number of Availability Zones | `2`           |
| Number of Public Subnets     | `2`           |
| Number of Private Subnets    | `4`           |

Chọn **Create VPC**. AWS tạo VPC, Internet Gateway, route table và các subnet sau:

| Loại subnet | Tên                                         | CIDR              |
| ------------ | -------------------------------------------- | ----------------- |
| Public       | `NeonFood-subnet-public1-ap-southeast-1a`  | `10.0.0.0/20`   |
| Public       | `NeonFood-subnet-public2-ap-southeast-1b`  | `10.0.16.0/20`  |
| Private      | `NeonFood-subnet-private1-ap-southeast-1a` | `10.0.128.0/20` |
| Private      | `NeonFood-subnet-private2-ap-southeast-1b` | `10.0.144.0/20` |
| Private      | `NeonFood-subnet-private3-ap-southeast-1a` | `10.0.160.0/20` |
| Private      | `NeonFood-subnet-private4-ap-southeast-1b` | `10.0.176.0/20` |

{{< event-image src="images/5-Workshop/5.3-Structure/picCreateVPC1.jpg" alt="Picture VPC Create 1" >}}

### Cấp Elastic IP

Tại **Elastic IP addresses**, chọn **Allocate Elastic IP address**, giữ cấu hình mặc định và thêm tag `Name=EIP-NAT-AZ1a`. Elastic IP này được gán cho NAT Gateway ở bước tiếp theo.

{{< event-image src="images/5-Workshop/5.3-Structure/picCreateVPC2.jpg" alt="Picture VPC Create 2" >}}

### Tạo NAT Gateway

Vào **NAT gateways** → **Create NAT gateway** và cấu hình: Name `NAT-Gateway-AZ1a`; subnet `NeonFood-subnet-public1-ap-southeast-1a`; connectivity type **Public**; Elastic IP allocation ID là `EIP-NAT-AZ1a`. Chọn **Create NAT gateway** và đợi trạng thái `Available`.

{{< event-image src="images/5-Workshop/5.3-Structure/picCreateNAT.jpg" alt="Create NAT Gateway" >}}

### Cập nhật route table private

Trong **Route tables**, chọn bảng đang gắn với `NeonFood-subnet-private1-ap-southeast-1a` và `NeonFood-subnet-private3-ap-southeast-1a`. Tại **Routes → Edit routes**, thêm:

| Destination   | Target               |
| ------------- | -------------------- |
| `0.0.0.0/0` | `NAT-Gateway-AZ1a` |

### Bật public IPv4 cho public subnet

Tại **Subnets**, lần lượt chọn hai public subnet → **Actions → Edit subnet settings** → bật **Enable auto-assign public IPv4 address** → **Save**. Không bật tùy chọn này cho private subnet chạy ECS/RDS.

### Kết quả sau khi tạo thành công và liên kết thành công VPC và NAT Gateway

{{< event-image src="images/5-Workshop/5.3-Structure/picVPC.jpg" alt="Succes Create VPC and NAT Gateway" >}}

## Tạo RDS MySQL

### Chuẩn bị subnet group và parameter group

1. Trong **Amazon RDS → Subnet groups**, chọn **Create DB Subnet Group**, đặt tên `neonfoodmap-rds-subnet-group`, chọn VPC NeonFoodMap và private subnet của ít nhất hai AZ. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-vpc-rds/picDbSubnetGroup.jpg" alt="picDbSubnetGroup" >}}
2. Trong **Parameter groups**, tạo `neonfoodmap-mysql80-params` cho MySQL 8.0/family tương ứng. Sửa `character_set_server=utf8mb4` và `collation_server=utf8mb4_unicode_ci` để xử lý dữ liệu tiếng Việt đúng. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-vpc-rds/picParameterGroup.jpg" alt="picParameterGroup" >}}
3. Trong **EC2 → Security Groups**, tạo `neonfoodmap-rds-sg`. Inbound chỉ cho phép `MYSQL/Aurora`, port `3306`, với source là **ECS task security group**. Không dùng `0.0.0.0/0` và không dùng IP máy local làm rule production. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-vpc-rds/picSecurityGroup.jpg" alt="picSecurityGroup" >}}

### Provision database

1. Chọn **RDS → Databases → Create database → Standard/Full configuration**.
2. Chọn MySQL, identifier `neonfoodmap-mysql-db`, class `db.t3.micro`, gp3 20 GiB và storage autoscaling tối đa 100 GiB theo Sprint.
3. Trong **Connectivity**, chọn VPC NeonFoodMap, DB subnet group đã tạo, `Public access = No`, `neonfoodmap-rds-sg` và port 3306. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-vpc-rds/picCreateRDS1.jpg" alt="picCreateRDS1" >}}
4. Trong **Additional configuration**, đặt initial database `buocchancoi_db`, chọn parameter group; bật automated backup 7 ngày, encryption bằng `aws/rds`, Error log và Slow query log. {{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-vpc-rds/picCreateRDS2.jpg" alt="picCreateRDS2" >}}
5. Chọn **Create database**, đợi trạng thái `Available`. Tại **Connectivity & security**, lấy endpoint và lưu vào secret `DB_HOST`.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-vpc-rds/picFinalRDS1.jpg" alt="picFinalRDS1" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-vpc-rds/picFinalRDS2.jpg" alt="picFinalRDS2" >}}
