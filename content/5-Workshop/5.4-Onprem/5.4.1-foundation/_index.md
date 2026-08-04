---
title: "Build the VPC and MySQL RDS"
date: 2026-08-03
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

## Create the VPC

### Create the VPC and subnets

Open **VPC Console** → **Your VPCs** → **Create VPC**. Select **VPC and more** and use the following settings:

| Setting | Value |
| --- | --- |
| Name tag auto-generation | `NeonFood` |
| IPv4 CIDR block | `10.0.0.0/16` |
| Availability Zones | `2` |
| Public subnets | `2` |
| Private subnets | `4` |

Select **Create VPC**. AWS creates the VPC, Internet Gateway, route tables, and these subnets:

| Subnet type | Name | CIDR |
| --- | --- | --- |
| Public | `NeonFood-subnet-public1-ap-southeast-1a` | `10.0.0.0/20` |
| Public | `NeonFood-subnet-public2-ap-southeast-1b` | `10.0.16.0/20` |
| Private | `NeonFood-subnet-private1-ap-southeast-1a` | `10.0.128.0/20` |
| Private | `NeonFood-subnet-private2-ap-southeast-1b` | `10.0.144.0/20` |
| Private | `NeonFood-subnet-private3-ap-southeast-1a` | `10.0.160.0/20` |
| Private | `NeonFood-subnet-private4-ap-southeast-1b` | `10.0.176.0/20` |

{{< event-image src="images/5-Workshop/5.3-S3-vpc/picCreateVPC1.jpg" alt="Create VPC" >}}

### Allocate an Elastic IP and create a NAT Gateway

In **Elastic IP addresses**, select **Allocate Elastic IP address**, keep the default settings, and add `Name=EIP-NAT-AZ1a`. Then open **NAT gateways** → **Create NAT gateway** and set the name to `NAT-Gateway-AZ1a`, the subnet to `NeonFood-subnet-public1-ap-southeast-1a`, connectivity type to **Public**, and select `EIP-NAT-AZ1a`. Wait until the gateway is **Available**.

{{< event-image src="images/5-Workshop/5.3-S3-vpc/picCreateNAT.jpg" alt="Create NAT Gateway" >}}

### Update private routes and public IPv4 settings

For the route tables attached to private subnets 1 and 3, add the following route:

| Destination | Target |
| --- | --- |
| `0.0.0.0/0` | `NAT-Gateway-AZ1a` |

For each public subnet, select **Actions → Edit subnet settings** and enable **auto-assign public IPv4 address**. Do not enable this option for private ECS and RDS subnets.

{{< event-image src="images/5-Workshop/5.3-S3-vpc/picVPC.jpg" alt="Completed VPC and NAT Gateway" >}}

## Create MySQL RDS

### Prepare the subnet group, parameter group, and security group

1. In **Amazon RDS → Subnet groups**, create `neonfoodmap-rds-subnet-group` in the NeonFoodMap VPC with private subnets in at least two AZs.
2. Create `neonfoodmap-mysql80-params` for the matching MySQL 8.0 family. Set `character_set_server=utf8mb4` and `collation_server=utf8mb4_unicode_ci` for Vietnamese text.
3. In **EC2 → Security Groups**, create `neonfoodmap-rds-sg`. Allow inbound `MYSQL/Aurora` on port `3306` only from the **ECS task security group**. Do not use `0.0.0.0/0` or a local-machine IP in production.

### Provision the database

1. Select **RDS → Databases → Create database → Standard/Full configuration**.
2. Choose MySQL, identifier `neonfoodmap-mysql-db`, class `db.t3.micro`, 20 GiB gp3 storage, and storage autoscaling up to 100 GiB.
3. Under **Connectivity**, choose the NeonFoodMap VPC, the DB subnet group, `Public access = No`, `neonfoodmap-rds-sg`, and port 3306.
4. Under **Additional configuration**, set the initial database to `buocchancoi_db`, select the parameter group, enable 7-day automated backups, `aws/rds` encryption, Error log, and Slow query log.
5. Select **Create database**. When the status is **Available**, copy the endpoint from **Connectivity & security** and save it as the `DB_HOST` secret.

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-foundation/picFinalRDS1.jpg" alt="Completed RDS database" >}}
{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-foundation/picDbSubnetGroup.jpg" alt="DB subnet group" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-foundation/picParameterGroup.jpg" alt="RDS parameter group" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-foundation/picSecurityGroup.jpg" alt="RDS security group" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-foundation/picCreateRDS1.jpg" alt="Create RDS database, connectivity" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-foundation/picCreateRDS2.jpg" alt="Create RDS database, additional configuration" >}}

{{< event-image src="images/5-Workshop/5.4-Onprem/5.4.1-foundation/picFinalRDS2.jpg" alt="Final RDS database settings" >}}
