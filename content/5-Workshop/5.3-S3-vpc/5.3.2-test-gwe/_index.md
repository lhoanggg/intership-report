---
title: "Create Amazon RDS"
date: "2025-09-08"
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Step 1: Create RDS MySQL Instance

1. Go to **RDS Console** → **Databases** → **Create database**

2. Choose database creation method:
   - **Standard create**

3. Engine options:
   - **Engine type**: MySQL
   - **Engine version**: MySQL 8.0.35 (or latest 8.0.x)

4. Templates:
   - **Free tier** (for workshop/testing)

5. Settings:
   - **DB instance identifier**: `daivietblood-db`
   - **Master username**: `admin`
   - **Credentials management**: Self managed
   - **Master password**: `YourSecurePassword123!`
   - **Confirm password**: `YourSecurePassword123!`

{{% notice warning %}}
⚠️ **Important:** Save your password securely. You will need it to connect from Lambda.
{{% /notice %}}

---

#### Step 2: Instance Configuration

1. Instance configuration:
   - **DB instance class**: db.t3.micro (Free tier eligible)
   - **Storage type**: General Purpose SSD (gp2)
   - **Allocated storage**: 20 GiB
   - **Storage autoscaling**: Disable (for cost control)

---

#### Step 3: Connectivity

1. Connectivity:
   - **Compute resource**: Don't connect to an EC2 compute resource
   - **Network type**: IPv4
   - **Virtual private cloud (VPC)**: `daivietblood-vpc`
   - **DB subnet group**: `daivietblood-db-subnet-group`
   - **Public access**: **No** ⚠️ Important!
   - **VPC security group**: Choose existing
   - **Existing VPC security groups**: `daivietblood-rds-sg`
   - **Availability Zone**: ap-southeast-1a

2. Database port:
   - **Database port**: 3306

---

#### Step 4: Database Authentication

1. Database authentication:
   - **Password authentication**

---

#### Step 5: Additional Configuration

1. Database options:
   - **Initial database name**: `daivietblood`
   - **DB parameter group**: default.mysql8.0
   - **Option group**: default:mysql-8-0

2. Backup:
   - **Enable automated backups**: Yes
   - **Backup retention period**: 7 days
   - **Backup window**: No preference

3. Encryption:
   - **Enable encryption**: Yes (default)

4. Monitoring:
   - **Enable Enhanced monitoring**: No (to reduce cost)

5. Maintenance:
   - **Enable auto minor version upgrade**: Yes
   - **Maintenance window**: No preference

6. Deletion protection:
   - **Enable deletion protection**: No (for workshop)

7. Click **Create database**

{{% notice info %}}
ℹ️ RDS creation takes 10-15 minutes. Wait until status shows "Available".
{{% /notice %}}

---

#### Step 6: Get RDS Endpoint

After RDS is available:

1. Go to **RDS Console** → **Databases** → Click `daivietblood-db`

2. In **Connectivity & security** tab, copy:
   - **Endpoint**: `daivietblood-db.xxxxxxxxxxxx.ap-southeast-1.rds.amazonaws.com`
   - **Port**: `3306`

3. Save these values for Lambda configuration:
```
DB_HOST=daivietblood-db.xxxxxxxxxxxx.ap-southeast-1.rds.amazonaws.com
DB_PORT=3306
DB_NAME=daivietblood
DB_USER=admin
DB_PASSWORD=YourSecurePassword123!
```

---

#### Step 7: Create Database Schema

Since RDS is in Private Subnet, you need to connect via a bastion host or use Lambda to initialize the schema.

**Option A: Using Lambda to Initialize (Recommended)**

Create a one-time Lambda function to initialize the database:

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

  // Create tables
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

#### Verification Checklist

- [ ] RDS instance created and status is "Available"
- [ ] RDS is in Private Subnet (Public access: No)
- [ ] RDS Security Group only allows access from Lambda SG
- [ ] Endpoint and credentials saved securely
- [ ] Initial database `daivietblood` created
- [ ] Database schema initialized (tables created)

---

#### Troubleshooting

| Issue | Solution |
|:------|:---------|
| Cannot connect to RDS | Verify Security Group allows inbound from Lambda SG |
| RDS creation failed | Check Service Quotas for RDS instances |
| Connection timeout | Ensure Lambda is in same VPC and has NAT Gateway access |
