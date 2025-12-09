---
title: "Tạo VPC"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Bước 1: Tạo VPC

1. Vào **VPC Console** → **Your VPCs** → **Create VPC**

2. Cấu hình VPC:
   - **Resources to create**: VPC and more
   - **Name tag auto-generation**: `daivietblood`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - **IPv6 CIDR block**: No IPv6 CIDR block
   - **Tenancy**: Default

3. Cấu hình Subnets:
   - **Number of Availability Zones**: 2
   - **Number of public subnets**: 2
   - **Number of private subnets**: 2
   - **Customize subnets CIDR blocks**:
     - Public subnet CIDR block in ap-southeast-1a: `10.0.1.0/24`
     - Public subnet CIDR block in ap-southeast-1b: `10.0.2.0/24`
     - Private subnet CIDR block in ap-southeast-1a: `10.0.3.0/24`
     - Private subnet CIDR block in ap-southeast-1b: `10.0.4.0/24`

4. Cấu hình NAT Gateway:
   - **NAT gateways**: In 1 AZ
   
5. Cấu hình VPC Endpoints:
   - **VPC endpoints**: None (sẽ tạo sau nếu cần)

6. Click **Create VPC**

{{% notice info %}}
ℹ️ Việc tạo VPC mất 2-3 phút. Đợi đến khi status hiển thị "Available".
{{% /notice %}}

---

#### Bước 2: Xác minh tài nguyên VPC

Sau khi tạo, xác minh các tài nguyên sau đã được tạo:

| Tài nguyên | Tên | Chi tiết |
|:-----------|:----|:---------|
| VPC | daivietblood-vpc | 10.0.0.0/16 |
| Public Subnet 1 | daivietblood-subnet-public1-ap-southeast-1a | 10.0.1.0/24 |
| Public Subnet 2 | daivietblood-subnet-public2-ap-southeast-1b | 10.0.2.0/24 |
| Private Subnet 1 | daivietblood-subnet-private1-ap-southeast-1a | 10.0.3.0/24 |
| Private Subnet 2 | daivietblood-subnet-private2-ap-southeast-1b | 10.0.4.0/24 |
| Internet Gateway | daivietblood-igw | Attached to VPC |
| NAT Gateway | daivietblood-nat-public1-ap-southeast-1a | Trong Public Subnet 1 |
| Route Table (Public) | daivietblood-rtb-public | Routes đến IGW |
| Route Table (Private) | daivietblood-rtb-private1-ap-southeast-1a | Routes đến NAT |

---

#### Bước 3: Tạo Security Groups

**3.1. Security Group cho Lambda**

1. Vào **VPC Console** → **Security Groups** → **Create security group**

2. Cấu hình:
   - **Security group name**: `daivietblood-lambda-sg`
   - **Description**: Security group for Lambda functions
   - **VPC**: Chọn `daivietblood-vpc`

3. Inbound rules: (Để trống - Lambda khởi tạo kết nối)

4. Outbound rules:
   | Type | Protocol | Port | Destination | Description |
   |:-----|:---------|:-----|:------------|:------------|
   | All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

5. Click **Create security group**

---

**3.2. Security Group cho RDS**

1. Vào **VPC Console** → **Security Groups** → **Create security group**

2. Cấu hình:
   - **Security group name**: `daivietblood-rds-sg`
   - **Description**: Security group for RDS MySQL
   - **VPC**: Chọn `daivietblood-vpc`

3. Inbound rules:
   | Type | Protocol | Port | Source | Description |
   |:-----|:---------|:-----|:-------|:------------|
   | MySQL/Aurora | TCP | 3306 | daivietblood-lambda-sg | Allow Lambda access |

4. Outbound rules:
   | Type | Protocol | Port | Destination | Description |
   |:-----|:---------|:-----|:------------|:------------|
   | All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

5. Click **Create security group**

{{% notice warning %}}
⚠️ **Thực hành bảo mật tốt nhất:** Chỉ cho phép truy cập từ Lambda Security Group đến RDS. Không bao giờ mở port 3306 cho 0.0.0.0/0.
{{% /notice %}}

---

#### Bước 4: Tạo DB Subnet Group

1. Vào **RDS Console** → **Subnet groups** → **Create DB subnet group**

2. Cấu hình:
   - **Name**: `daivietblood-db-subnet-group`
   - **Description**: Subnet group for DaiVietBlood RDS
   - **VPC**: Chọn `daivietblood-vpc`

3. Thêm subnets:
   - **Availability Zones**: Chọn `ap-southeast-1a` và `ap-southeast-1b`
   - **Subnets**: Chọn cả hai **Private Subnets** (10.0.3.0/24 và 10.0.4.0/24)

4. Click **Create**

---

#### Checklist xác minh

- [ ] VPC đã tạo với CIDR 10.0.0.0/16
- [ ] 2 Public Subnets đã tạo
- [ ] 2 Private Subnets đã tạo
- [ ] Internet Gateway đã gắn vào VPC
- [ ] NAT Gateway đã tạo trong Public Subnet
- [ ] Route tables đã cấu hình đúng
- [ ] Lambda Security Group đã tạo
- [ ] RDS Security Group đã tạo với inbound rule từ Lambda SG
- [ ] DB Subnet Group đã tạo với Private Subnets
