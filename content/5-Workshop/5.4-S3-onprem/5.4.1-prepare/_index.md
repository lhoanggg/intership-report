---
title: "Create Lambda Functions"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### Step 1: Create IAM Role for Lambda

1. Go to **IAM Console** → **Roles** → **Create role**

2. Trusted entity type:
   - **AWS service**
   - **Use case**: Lambda

3. Add permissions:
   - `AWSLambdaVPCAccessExecutionRole`
   - `AWSLambdaBasicExecutionRole`

4. Role details:
   - **Role name**: `daivietblood-lambda-role`
   - **Description**: IAM role for DaiVietBlood Lambda functions

5. Click **Create role**

---

#### Step 2: Create Lambda Layer for Dependencies

1. Create a folder for dependencies:
```bash
mkdir -p nodejs
cd nodejs
npm init -y
npm install mysql2
```

2. Create zip file:
```bash
cd ..
zip -r mysql2-layer.zip nodejs
```

3. Go to **Lambda Console** → **Layers** → **Create layer**

4. Configure:
   - **Name**: `mysql2-layer`
   - **Upload**: Select `mysql2-layer.zip`
   - **Compatible runtimes**: Node.js 18.x, Node.js 20.x

5. Click **Create**

---

#### Step 3: Create Lambda Function - Get Users

1. Go to **Lambda Console** → **Functions** → **Create function**

2. Basic information:
   - **Function name**: `daivietblood-get-users`
   - **Runtime**: Node.js 20.x
   - **Architecture**: x86_64
   - **Execution role**: Use existing role → `daivietblood-lambda-role`

3. Click **Create function**

4. Add Layer:
   - Scroll to **Layers** → **Add a layer**
   - **Custom layers** → Select `mysql2-layer`
   - Click **Add**

5. Configure VPC:
   - Go to **Configuration** → **VPC** → **Edit**
   - **VPC**: `daivietblood-vpc`
   - **Subnets**: Select both Private Subnets
   - **Security groups**: `daivietblood-lambda-sg`
   - Click **Save**

6. Add Environment Variables:
   - Go to **Configuration** → **Environment variables** → **Edit**
   - Add:
     ```
     DB_HOST = daivietblood-db.xxxx.ap-southeast-1.rds.amazonaws.com
     DB_PORT = 3306
     DB_NAME = daivietblood
     DB_USER = admin
     DB_PASSWORD = YourSecurePassword123!
     ```
   - Click **Save**

7. Add code in **Code** tab:

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

#### Step 4: Create Lambda Function - Create User

1. Create new function: `daivietblood-create-user`
2. Same configuration as above (VPC, Layer, Environment Variables)
3. Add code:

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

#### Step 5: Create Lambda Function - Emergency Requests

1. Create function: `daivietblood-emergency-requests`
2. Same configuration
3. Add code:

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

#### Verification Checklist

- [ ] IAM Role created with VPC and Basic execution permissions
- [ ] Lambda Layer created with mysql2 package
- [ ] Lambda functions created and deployed:
  - [ ] daivietblood-get-users
  - [ ] daivietblood-create-user
  - [ ] daivietblood-emergency-requests
- [ ] All functions configured with VPC (Private Subnets)
- [ ] Environment variables set correctly
- [ ] Functions deployed successfully
