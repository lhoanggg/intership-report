---
title: "Create VPC"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### Step 1: Create VPC

1. Go to **VPC Console** → **Your VPCs** → **Create VPC**

2. Configure VPC:
   - **Resources to create**: VPC and more
   - **Name tag auto-generation**: `daivietblood`
   - **IPv4 CIDR block**: `10.0.0.0/16`
   - **IPv6 CIDR block**: No IPv6 CIDR block
   - **Tenancy**: Default

3. Configure Subnets:
   - **Number of Availability Zones**: 2
   - **Number of public subnets**: 2
   - **Number of private subnets**: 2
   - **Customize subnets CIDR blocks**:
     - Public subnet CIDR block in ap-southeast-1a: `10.0.1.0/24`
     - Public subnet CIDR block in ap-southeast-1b: `10.0.2.0/24`
     - Private subnet CIDR block in ap-southeast-1a: `10.0.3.0/24`
     - Private subnet CIDR block in ap-southeast-1b: `10.0.4.0/24`

4. Configure NAT Gateway:
   - **NAT gateways**: In 1 AZ
   
5. Configure VPC Endpoints:
   - **VPC endpoints**: None (we'll create later if needed)

6. Click **Create VPC**

{{% notice info %}}
ℹ️ VPC creation takes 2-3 minutes. Wait until status shows "Available".
{{% /notice %}}

---

#### Step 2: Verify VPC Resources

After creation, verify the following resources were created:

| Resource | Name | Details |
|:---------|:-----|:--------|
| VPC | daivietblood-vpc | 10.0.0.0/16 |
| Public Subnet 1 | daivietblood-subnet-public1-ap-southeast-1a | 10.0.1.0/24 |
| Public Subnet 2 | daivietblood-subnet-public2-ap-southeast-1b | 10.0.2.0/24 |
| Private Subnet 1 | daivietblood-subnet-private1-ap-southeast-1a | 10.0.3.0/24 |
| Private Subnet 2 | daivietblood-subnet-private2-ap-southeast-1b | 10.0.4.0/24 |
| Internet Gateway | daivietblood-igw | Attached to VPC |
| NAT Gateway | daivietblood-nat-public1-ap-southeast-1a | In Public Subnet 1 |
| Route Table (Public) | daivietblood-rtb-public | Routes to IGW |
| Route Table (Private) | daivietblood-rtb-private1-ap-southeast-1a | Routes to NAT |

---

#### Step 3: Create Security Groups

**3.1. Security Group for Lambda**

1. Go to **VPC Console** → **Security Groups** → **Create security group**

2. Configure:
   - **Security group name**: `daivietblood-lambda-sg`
   - **Description**: Security group for Lambda functions
   - **VPC**: Select `daivietblood-vpc`

3. Inbound rules: (Leave empty - Lambda initiates connections)

4. Outbound rules:
   | Type | Protocol | Port | Destination | Description |
   |:-----|:---------|:-----|:------------|:------------|
   | All traffic | All | All | 0.0.0.0/0 | Allow all outbound |

5. Click **Create security group**

---

**3.2. Security Group for RDS**

1. Go to **VPC Console** → **Security Groups** → **Create security group**

2. Configure:
   - **Security group name**: `daivietblood-rds-sg`
   - **Description**: Security group for RDS MySQL
   - **VPC**: Select `daivietblood-vpc`

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
⚠️ **Security Best Practice:** Only allow access from Lambda Security Group to RDS. Never open port 3306 to 0.0.0.0/0.
{{% /notice %}}

---

#### Step 4: Create DB Subnet Group

1. Go to **RDS Console** → **Subnet groups** → **Create DB subnet group**

2. Configure:
   - **Name**: `daivietblood-db-subnet-group`
   - **Description**: Subnet group for DaiVietBlood RDS
   - **VPC**: Select `daivietblood-vpc`

3. Add subnets:
   - **Availability Zones**: Select `ap-southeast-1a` and `ap-southeast-1b`
   - **Subnets**: Select both **Private Subnets** (10.0.3.0/24 and 10.0.4.0/24)

4. Click **Create**

---

#### Verification Checklist

- [ ] VPC created with CIDR 10.0.0.0/16
- [ ] 2 Public Subnets created
- [ ] 2 Private Subnets created
- [ ] Internet Gateway attached to VPC
- [ ] NAT Gateway created in Public Subnet
- [ ] Route tables configured correctly
- [ ] Lambda Security Group created
- [ ] RDS Security Group created with inbound rule from Lambda SG
- [ ] DB Subnet Group created with Private Subnets
