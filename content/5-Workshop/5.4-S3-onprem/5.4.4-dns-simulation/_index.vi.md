---
title: "Cấu hình CORS & Security"
date: "2025-09-08"
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### Hiểu về CORS

**CORS (Cross-Origin Resource Sharing)** là tính năng bảo mật hạn chế các trang web gửi requests đến domain khác với domain đang phục vụ trang web.

Khi React frontend (hosted trên Amplify) gọi API Gateway, browser kiểm tra CORS headers để xác định request có được phép hay không.

---

#### Bước 1: Cấu hình CORS Headers trong Lambda

Đảm bảo tất cả Lambda functions trả về CORS headers đúng:

```javascript
const corsHeaders = {
  'Access-Control-Allow-Origin': '*',  // Hoặc domain cụ thể
  'Access-Control-Allow-Headers': 'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token',
  'Access-Control-Allow-Methods': 'GET,POST,PUT,DELETE,OPTIONS'
};

// Trong handler response:
return {
  statusCode: 200,
  headers: corsHeaders,
  body: JSON.stringify(data)
};
```

---

#### Bước 2: Cấu hình API Gateway CORS

**Cách 1: Sử dụng Console**

1. Vào **API Gateway Console** → Chọn API của bạn
2. Cho mỗi resource:
   - Chọn resource → **Actions** → **Enable CORS**
   - Cấu hình allowed origins, methods, headers
   - Click **Enable CORS and replace existing CORS headers**

**Cách 2: Sử dụng OPTIONS Method**

1. Tạo OPTIONS method cho mỗi resource
2. Integration type: **Mock**
3. Thêm Method Response với status 200
4. Thêm Integration Response với headers:

```
Access-Control-Allow-Headers: 'Content-Type,X-Amz-Date,Authorization,X-Api-Key'
Access-Control-Allow-Methods: 'GET,POST,OPTIONS'
Access-Control-Allow-Origin: '*'
```

---

#### Bước 3: API Gateway Security Best Practices

**3.1. Bật API Key (Tùy chọn)**

1. Vào **API Gateway** → **API Keys** → **Create API Key**
2. Name: `daivietblood-api-key`
3. Vào **Usage Plans** → **Create**
4. Cấu hình throttling và quota
5. Liên kết API Key với Usage Plan
6. Cho mỗi method, đặt **API Key Required**: true

**3.2. Bật Request Validation**

1. Vào **API Gateway** → **Models** → **Create**
2. Tạo model cho request body:

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

3. Áp dụng model cho POST method:
   - Chọn method → **Method Request**
   - **Request Validator**: Validate body
   - **Request Body**: Thêm model

**3.3. Bật Throttling**

1. Vào **Stages** → Chọn `prod`
2. **Stage Settings** → **Default Method Throttling**
3. Cấu hình:
   - **Rate**: 100 requests/second
   - **Burst**: 200 requests

---

#### Bước 4: Lambda Security Best Practices

**4.1. Sử dụng AWS Secrets Manager cho Credentials**

Thay vì lưu DB credentials trong environment variables:

1. Vào **Secrets Manager** → **Store a new secret**
2. Secret type: **Other type of secret**
3. Key/value pairs:
   ```
   DB_HOST: daivietblood-db.xxxx.rds.amazonaws.com
   DB_USER: admin
   DB_PASSWORD: YourSecurePassword123!
   DB_NAME: daivietblood
   ```
4. Secret name: `daivietblood/db-credentials`

5. Cập nhật Lambda để lấy secrets:

```javascript
const { SecretsManagerClient, GetSecretValueCommand } = require('@aws-sdk/client-secrets-manager');

const client = new SecretsManagerClient({ region: 'ap-southeast-1' });

const getDbCredentials = async () => {
  const command = new GetSecretValueCommand({ SecretId: 'daivietblood/db-credentials' });
  const response = await client.send(command);
  return JSON.parse(response.SecretString);
};
```

6. Thêm IAM permission cho Lambda role:
```json
{
  "Effect": "Allow",
  "Action": "secretsmanager:GetSecretValue",
  "Resource": "arn:aws:secretsmanager:ap-southeast-1:*:secret:daivietblood/*"
}
```

**4.2. Input Validation**

Luôn validate input trong Lambda:

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

#### Bước 5: Redeploy API

Sau khi thay đổi:

1. **Actions** → **Deploy API**
2. Chọn `prod` stage
3. Click **Deploy**

---

#### Security Checklist

- [ ] CORS đã cấu hình đúng
- [ ] Lambda trả về CORS headers đúng
- [ ] API Key đã bật (tùy chọn nhưng khuyến nghị)
- [ ] Request validation đã bật
- [ ] Throttling đã cấu hình
- [ ] DB credentials lưu trong Secrets Manager (khuyến nghị)
- [ ] Input validation trong Lambda functions
- [ ] API đã redeploy sau khi thay đổi
