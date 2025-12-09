---
title: "Tạo Amazon RDS"
date: "2025-09-08"
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Bước 1: Tạo RDS MySQL Instance

1. Vào **RDS Console** → **Databases** → **Create database**

2. Chọn phương thức tạo database:
   - **Standard create**

3. Engine options:
   - **Engine type**: MySQL
   - **Engine version**: MySQL 8.0.35 (hoặc phiên bản 8.0.x mới nhất)

4. Templates:
   - **Free tier** (cho workshop/testing)

5. Settings:
   - **DB instance identifier**: `daivietblood-db`
   - **Master username**: `admin`
   - **Credentials management**: Self managed
   - **Master password**: `YourSecurePassword123!`
   - **Confirm password**: `YourSecurePassword123!`

{{% notice warning %}}
⚠️ **Quan trọng:** Lưu password an toàn. Bạn sẽ cần nó để kết nối từ Lambda.
{{% /notice %}}

---

#### Bước 2: Cấu hình Instance

1. Instance configuration:
   - **DB instance class**: db.t3.micro (Free tier eligible)
   - **Storage type**: General Purpose SSD (gp2)
   - **Allocated storage**: 20 GiB
   - **Storage autoscaling**: Disable (để kiểm soát chi phí)

---

#### Bước 3: Kết nối

1. Connectivity:
   - **Compute resource**: Don't connect to an EC2 compute resource
   - **Network type**: IPv4
   - **Virtual private cloud (VPC)**: `daivietblood-vpc`
   - **DB subnet group**: `daivietblood-db-subnet-group`
   - **Public access**: **No** ⚠️ Quan trọng!
   - **VPC security group**: Choose existing
   - **Existing VPC security groups**: `daivietblood-rds-sg`
   - **Availability Zone**: ap-southeast-1a

2. Database port:
   - **Database port**: 3306

---

#### Bước 4: Xác thực Database

1. Database authentication:
   - **Password authentication**

---

#### Bước 5: Cấu hình bổ sung

1. Database options:
   - **Initial database name**: `daivietblood`
   - **DB parameter group**: default.mysql8.0
   - **Option group**: default:mysql-8-0

2. Backup:
   - **Enable automated backups**: Yes
   - **Backup retention period**: 7 days
   - **Backup window**: No preference

3. Encryption:
   - **Enable encryption**: Yes (mặc định)

4. Monitoring:
   - **Enable Enhanced monitoring**: No (để giảm chi phí)

5. Maintenance:
   - **Enable auto minor version upgrade**: Yes
   - **Maintenance window**: No preference

6. Deletion protection:
   - **Enable deletion protection**: No (cho workshop)

7. Click **Create database**

{{% notice info %}}
ℹ️ Việc tạo RDS mất 10-15 phút. Đợi đến khi status hiển thị "Available".
{{% /notice %}}

---

#### Bước 6: Lấy RDS Endpoint

Sau khi RDS available:

1. Vào **RDS Console** → **Databases** → Click `daivietblood-db`

2. Trong tab **Connectivity & security**, copy:
   - **Endpoint**: `daivietblood-db.xxxxxxxxxxxx.ap-southeast-1.rds.amazonaws.com`
   - **Port**: `3306`

3. Lưu các giá trị này cho cấu hình Lambda:
```
DB_HOST=daivietblood-db.xxxxxxxxxxxx.ap-southeast-1.rds.amazonaws.com
DB_PORT=3306
DB_NAME=daivietblood
DB_USER=admin
DB_PASSWORD=YourSecurePassword123!
```

---

#### Bước 7: Tạo Database Schema

Vì RDS nằm trong Private Subnet, bạn cần kết nối qua bastion host hoặc sử dụng Lambda để khởi tạo schema.

**Cách A: Sử dụng Lambda để khởi tạo (Khuyến nghị)**

Tạo Lambda function một lần để khởi tạo database:

```javascript
// init-db.js
const mysql = require('mysql2/promise');

exports.handler = async (event) => {
  const connection = await mysql.createConnection({
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASSWORD,
    database: process.env.DB_NAME
  });

  // Tạo các bảng
  const createUsersTable = `
    CREATE TABLE IF NOT EXISTS users (
      id INT AUTO_INCREMENT PRIMARY KEY,
      email VARCHAR(255) UNIQUE NOT NULL,
      name VARCHAR(255) NOT NULL,
      blood_type ENUM('A+', 'A-', 'B+', 'B-', 'AB+', 'AB-', 'O+', 'O-') NOT NULL,
      phone VARCHAR(20),
      created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    )
  `;

  const createDonationsTable = `
    CREATE TABLE IF NOT EXISTS donations (
      id INT AUTO_INCREMENT PRIMARY KEY,
      user_id INT NOT NULL,
      donation_date DATE NOT NULL,
      location VARCHAR(255),
      status ENUM('scheduled', 'completed', 'cancelled') DEFAULT 'scheduled',
      created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
      FOREIGN KEY (user_id) REFERENCES users(id)
    )
  `;

  const createEmergencyRequestsTable = `
    CREATE TABLE IF NOT EXISTS emergency_requests (
      id INT AUTO_INCREMENT PRIMARY KEY,
      requester_name VARCHAR(255) NOT NULL,
      blood_type ENUM('A+', 'A-', 'B+', 'B-', 'AB+', 'AB-', 'O+', 'O-') NOT NULL,
      units_needed INT NOT NULL,
      hospital VARCHAR(255) NOT NULL,
      urgency ENUM('critical', 'urgent', 'normal') DEFAULT 'normal',
      status ENUM('open', 'fulfilled', 'cancelled') DEFAULT 'open',
      created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    )
  `;

  await connection.execute(createUsersTable);
  await connection.execute(createDonationsTable);
  await connection.execute(createEmergencyRequestsTable);

  await connection.end();

  return {
    statusCode: 200,
    body: JSON.stringify({ message: 'Database initialized successfully' })
  };
};
```

---

#### Checklist xác minh

- [ ] RDS instance đã tạo và status là "Available"
- [ ] RDS nằm trong Private Subnet (Public access: No)
- [ ] RDS Security Group chỉ cho phép truy cập từ Lambda SG
- [ ] Endpoint và credentials đã lưu an toàn
- [ ] Database ban đầu `daivietblood` đã tạo
- [ ] Database schema đã khởi tạo (các bảng đã tạo)

---

#### Xử lý sự cố

| Vấn đề | Giải pháp |
|:-------|:----------|
| Không thể kết nối đến RDS | Xác minh Security Group cho phép inbound từ Lambda SG |
| Tạo RDS thất bại | Kiểm tra Service Quotas cho RDS instances |
| Connection timeout | Đảm bảo Lambda cùng VPC và có NAT Gateway access |
