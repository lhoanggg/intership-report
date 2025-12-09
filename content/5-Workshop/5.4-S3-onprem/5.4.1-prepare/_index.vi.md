---
title: "Tạo Lambda Functions"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Bước 1: Tạo IAM Role cho Lambda

1. Vào **IAM Console** → **Roles** → **Create role**

2. Trusted entity type:
   - **AWS service**
   - **Use case**: Lambda

3. Thêm permissions:
   - `AWSLambdaVPCAccessExecutionRole`
   - `AWSLambdaBasicExecutionRole`

4. Role details:
   - **Role name**: `daivietblood-lambda-role`
   - **Description**: IAM role for DaiVietBlood Lambda functions

5. Click **Create role**

---

#### Bước 2: Tạo Lambda Layer cho Dependencies

1. Tạo folder cho dependencies:
```bash
mkdir -p nodejs
cd nodejs
npm init -y
npm install mysql2
```

2. Tạo file zip:
```bash
cd ..
zip -r mysql2-layer.zip nodejs
```

3. Vào **Lambda Console** → **Layers** → **Create layer**

4. Cấu hình:
   - **Name**: `mysql2-layer`
   - **Upload**: Chọn `mysql2-layer.zip`
   - **Compatible runtimes**: Node.js 18.x, Node.js 20.x

5. Click **Create**

---

#### Bước 3: Tạo Lambda Function - Get Users

1. Vào **Lambda Console** → **Functions** → **Create function**

2. Basic information:
   - **Function name**: `daivietblood-get-users`
   - **Runtime**: Node.js 20.x
   - **Architecture**: x86_64
   - **Execution role**: Use existing role → `daivietblood-lambda-role`

3. Click **Create function**

4. Thêm Layer:
   - Cuộn xuống **Layers** → **Add a layer**
   - **Custom layers** → Chọn `mysql2-layer`
   - Click **Add**

5. Cấu hình VPC:
   - Vào **Configuration** → **VPC** → **Edit**
   - **VPC**: `daivietblood-vpc`
   - **Subnets**: Chọn cả hai Private Subnets
   - **Security groups**: `daivietblood-lambda-sg`
   - Click **Save**

6. Thêm Environment Variables:
   - Vào **Configuration** → **Environment variables** → **Edit**
   - Thêm:
     ```
     DB_HOST = daivietblood-db.xxxx.ap-southeast-1.rds.amazonaws.com
     DB_PORT = 3306
     DB_NAME = daivietblood
     DB_USER = admin
     DB_PASSWORD = YourSecurePassword123!
     ```
   - Click **Save**

7. Thêm code trong tab **Code**:

```javascript
const mysql = require('mysql2/promise');

let connection;

const getConnection = async () => {
  if (!connection) {
    connection = await mysql.createConnection({
      host: process.env.DB_HOST,
      port: process.env.DB_PORT,
      user: process.env.DB_USER,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_NAME
    });
  }
  return connection;
};

exports.handler = async (event) => {
  try {
    const conn = await getConnection();
    const [rows] = await conn.execute('SELECT * FROM users');
    
    return {
      statusCode: 200,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      },
      body: JSON.stringify(rows)
    };
  } catch (error) {
    console.error('Error:', error);
    return {
      statusCode: 500,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      },
      body: JSON.stringify({ error: 'Internal server error' })
    };
  }
};
```

8. Click **Deploy**

---

#### Bước 4: Tạo Lambda Function - Create User

1. Tạo function mới: `daivietblood-create-user`
2. Cấu hình giống như trên (VPC, Layer, Environment Variables)
3. Thêm code:

```javascript
const mysql = require('mysql2/promise');

let connection;

const getConnection = async () => {
  if (!connection) {
    connection = await mysql.createConnection({
      host: process.env.DB_HOST,
      port: process.env.DB_PORT,
      user: process.env.DB_USER,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_NAME
    });
  }
  return connection;
};

exports.handler = async (event) => {
  try {
    const body = JSON.parse(event.body);
    const { email, name, blood_type, phone } = body;

    if (!email || !name || !blood_type) {
      return {
        statusCode: 400,
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*'
        },
        body: JSON.stringify({ error: 'Missing required fields' })
      };
    }

    const conn = await getConnection();
    const [result] = await conn.execute(
      'INSERT INTO users (email, name, blood_type, phone) VALUES (?, ?, ?, ?)',
      [email, name, blood_type, phone || null]
    );

    return {
      statusCode: 201,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      },
      body: JSON.stringify({
        id: result.insertId,
        email,
        name,
        blood_type,
        phone
      })
    };
  } catch (error) {
    console.error('Error:', error);
    
    if (error.code === 'ER_DUP_ENTRY') {
      return {
        statusCode: 409,
        headers: {
          'Content-Type': 'application/json',
          'Access-Control-Allow-Origin': '*'
        },
        body: JSON.stringify({ error: 'Email already exists' })
      };
    }

    return {
      statusCode: 500,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*'
      },
      body: JSON.stringify({ error: 'Internal server error' })
    };
  }
};
```

---

#### Bước 5: Tạo Lambda Function - Emergency Requests

1. Tạo function: `daivietblood-emergency-requests`
2. Cấu hình giống như trên
3. Thêm code:

```javascript
const mysql = require('mysql2/promise');

let connection;

const getConnection = async () => {
  if (!connection) {
    connection = await mysql.createConnection({
      host: process.env.DB_HOST,
      port: process.env.DB_PORT,
      user: process.env.DB_USER,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_NAME
    });
  }
  return connection;
};

exports.handler = async (event) => {
  const conn = await getConnection();
  const method = event.httpMethod;

  try {
    if (method === 'GET') {
      const [rows] = await conn.execute(
        'SELECT * FROM emergency_requests WHERE status = "open" ORDER BY urgency DESC, created_at DESC'
      );
      return {
        statusCode: 200,
        headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
        body: JSON.stringify(rows)
      };
    }

    if (method === 'POST') {
      const body = JSON.parse(event.body);
      const { requester_name, blood_type, units_needed, hospital, urgency } = body;

      const [result] = await conn.execute(
        'INSERT INTO emergency_requests (requester_name, blood_type, units_needed, hospital, urgency) VALUES (?, ?, ?, ?, ?)',
        [requester_name, blood_type, units_needed, hospital, urgency || 'normal']
      );

      return {
        statusCode: 201,
        headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
        body: JSON.stringify({ id: result.insertId, message: 'Emergency request created' })
      };
    }

    return {
      statusCode: 405,
      headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
      body: JSON.stringify({ error: 'Method not allowed' })
    };
  } catch (error) {
    console.error('Error:', error);
    return {
      statusCode: 500,
      headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' },
      body: JSON.stringify({ error: 'Internal server error' })
    };
  }
};
```

---

#### Checklist xác minh

- [ ] IAM Role đã tạo với VPC và Basic execution permissions
- [ ] Lambda Layer đã tạo với mysql2 package
- [ ] Lambda functions đã tạo và deploy:
  - [ ] daivietblood-get-users
  - [ ] daivietblood-create-user
  - [ ] daivietblood-emergency-requests
- [ ] Tất cả functions đã cấu hình VPC (Private Subnets)
- [ ] Environment variables đã thiết lập đúng
- [ ] Functions đã deploy thành công
