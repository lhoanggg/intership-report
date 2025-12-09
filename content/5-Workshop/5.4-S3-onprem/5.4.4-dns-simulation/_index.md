---
title: "Configure CORS & Security"
date: "2025-09-08"
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Understanding CORS

**CORS (Cross-Origin Resource Sharing)** is a security feature that restricts web pages from making requests to a different domain than the one serving the web page.

When your React frontend (hosted on Amplify) calls your API Gateway, the browser checks CORS headers to determine if the request is allowed.

---

#### Step 1: Configure CORS Headers in Lambda

Ensure all Lambda functions return proper CORS headers:

```javascript
const corsHeaders = {
  'Access-Control-Allow-Origin': '*',  // Or specific domain
  'Access-Control-Allow-Headers': 'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token',
  'Access-Control-Allow-Methods': 'GET,POST,PUT,DELETE,OPTIONS'
};

// In your handler response:
return {
  statusCode: 200,
  headers: corsHeaders,
  body: JSON.stringify(data)
};
```

---

#### Step 2: Configure API Gateway CORS

**Method 1: Using Console**

1. Go to **API Gateway Console** → Select your API
2. For each resource:
   - Select resource → **Actions** → **Enable CORS**
   - Configure allowed origins, methods, headers
   - Click **Enable CORS and replace existing CORS headers**

**Method 2: Using OPTIONS Method**

1. Create OPTIONS method for each resource
2. Integration type: **Mock**
3. Add Method Response with status 200
4. Add Integration Response with headers:

```
Access-Control-Allow-Headers: 'Content-Type,X-Amz-Date,Authorization,X-Api-Key'
Access-Control-Allow-Methods: 'GET,POST,OPTIONS'
Access-Control-Allow-Origin: '*'
```

---

#### Step 3: API Gateway Security Best Practices

**3.1. Enable API Key (Optional)**

1. Go to **API Gateway** → **API Keys** → **Create API Key**
2. Name: `daivietblood-api-key`
3. Go to **Usage Plans** → **Create**
4. Configure throttling and quota
5. Associate API Key with Usage Plan
6. For each method, set **API Key Required**: true

**3.2. Enable Request Validation**

1. Go to **API Gateway** → **Models** → **Create**
2. Create model for request body:

```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "title": "CreateUserModel",
  "type": "object",
  "required": ["email", "name", "blood_type"],
  "properties": {
    "email": { "type": "string", "format": "email" },
    "name": { "type": "string", "minLength": 1 },
    "blood_type": { 
      "type": "string",
      "enum": ["A+", "A-", "B+", "B-", "AB+", "AB-", "O+", "O-"]
    },
    "phone": { "type": "string" }
  }
}
```

3. Apply model to POST method:
   - Select method → **Method Request**
   - **Request Validator**: Validate body
   - **Request Body**: Add model

**3.3. Enable Throttling**

1. Go to **Stages** → Select `prod`
2. **Stage Settings** → **Default Method Throttling**
3. Configure:
   - **Rate**: 100 requests/second
   - **Burst**: 200 requests

---

#### Step 4: Lambda Security Best Practices

**4.1. Use AWS Secrets Manager for Credentials**

Instead of storing DB credentials in environment variables:

1. Go to **Secrets Manager** → **Store a new secret**
2. Secret type: **Other type of secret**
3. Key/value pairs:
   ```
   DB_HOST: daivietblood-db.xxxx.rds.amazonaws.com
   DB_USER: admin
   DB_PASSWORD: YourSecurePassword123!
   DB_NAME: daivietblood
   ```
4. Secret name: `daivietblood/db-credentials`

5. Update Lambda to retrieve secrets:

```javascript
const { SecretsManagerClient, GetSecretValueCommand } = require('@aws-sdk/client-secrets-manager');

const client = new SecretsManagerClient({ region: 'ap-southeast-1' });

const getDbCredentials = async () => {
  const command = new GetSecretValueCommand({ SecretId: 'daivietblood/db-credentials' });
  const response = await client.send(command);
  return JSON.parse(response.SecretString);
};
```

6. Add IAM permission to Lambda role:
```json
{
  "Effect": "Allow",
  "Action": "secretsmanager:GetSecretValue",
  "Resource": "arn:aws:secretsmanager:ap-southeast-1:*:secret:daivietblood/*"
}
```

**4.2. Input Validation**

Always validate input in Lambda:

```javascript
const validateUser = (body) => {
  const errors = [];
  
  if (!body.email || !isValidEmail(body.email)) {
    errors.push('Invalid email');
  }
  
  if (!body.name || body.name.length < 1) {
    errors.push('Name is required');
  }
  
  const validBloodTypes = ['A+', 'A-', 'B+', 'B-', 'AB+', 'AB-', 'O+', 'O-'];
  if (!validBloodTypes.includes(body.blood_type)) {
    errors.push('Invalid blood type');
  }
  
  return errors;
};
```

---

#### Step 5: Redeploy API

After making changes:

1. **Actions** → **Deploy API**
2. Select `prod` stage
3. Click **Deploy**

---

#### Security Checklist

- [ ] CORS configured correctly
- [ ] Lambda returns proper CORS headers
- [ ] API Key enabled (optional but recommended)
- [ ] Request validation enabled
- [ ] Throttling configured
- [ ] DB credentials stored in Secrets Manager (recommended)
- [ ] Input validation in Lambda functions
- [ ] API redeployed after changes
