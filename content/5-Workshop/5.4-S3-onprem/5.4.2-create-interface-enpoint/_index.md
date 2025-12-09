---
title: "Create API Gateway"
date: "2025-09-08"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Step 1: Create REST API

1. Go to **API Gateway Console** → **Create API**

2. Choose API type:
   - **REST API** → **Build**

3. Create new API:
   - **Protocol**: REST
   - **Create new API**: New API
   - **API name**: `daivietblood-api`
   - **Description**: REST API for DaiVietBlood system
   - **Endpoint Type**: Regional

4. Click **Create API**

---

#### Step 2: Create Resources

**2.1. Create /users Resource**

1. Select root `/` → **Actions** → **Create Resource**

2. Configure:
   - **Resource Name**: `users`
   - **Resource Path**: `users`
   - **Enable API Gateway CORS**: ✅ Check

3. Click **Create Resource**

**2.2. Create /emergency-requests Resource**

1. Select root `/` → **Actions** → **Create Resource**

2. Configure:
   - **Resource Name**: `emergency-requests`
   - **Resource Path**: `emergency-requests`
   - **Enable API Gateway CORS**: ✅ Check

3. Click **Create Resource**

---

#### Step 3: Create Methods for /users

**3.1. GET /users**

1. Select `/users` → **Actions** → **Create Method** → **GET**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Region**: ap-southeast-1
   - **Lambda Function**: `daivietblood-get-users`

3. Click **Save** → **OK** (to add permission)

**3.2. POST /users**

1. Select `/users` → **Actions** → **Create Method** → **POST**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Region**: ap-southeast-1
   - **Lambda Function**: `daivietblood-create-user`

3. Click **Save** → **OK**

---

#### Step 4: Create Methods for /emergency-requests

**4.1. GET /emergency-requests**

1. Select `/emergency-requests` → **Actions** → **Create Method** → **GET**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Function**: `daivietblood-emergency-requests`

3. Click **Save** → **OK**

**4.2. POST /emergency-requests**

1. Select `/emergency-requests` → **Actions** → **Create Method** → **POST**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Function**: `daivietblood-emergency-requests`

3. Click **Save** → **OK**

---

#### Step 5: Enable CORS

For each resource (`/users`, `/emergency-requests`):

1. Select resource → **Actions** → **Enable CORS**

2. Configure:
   - **Access-Control-Allow-Methods**: GET, POST, OPTIONS
   - **Access-Control-Allow-Headers**: Content-Type, X-Amz-Date, Authorization, X-Api-Key
   - **Access-Control-Allow-Origin**: `*`

3. Click **Enable CORS and replace existing CORS headers**

4. Click **Yes, replace existing values**

---

#### Step 6: Deploy API

1. **Actions** → **Deploy API**

2. Deployment stage:
   - **Deployment stage**: [New Stage]
   - **Stage name**: `prod`
   - **Stage description**: Production stage

3. Click **Deploy**

4. Copy the **Invoke URL**:
   ```
   https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod
   ```

{{% notice info %}}
ℹ️ Save this URL. You will need it for frontend configuration.
{{% /notice %}}

---

#### Step 7: API Structure Summary

After completing, your API structure should look like:

```
daivietblood-api
│
├── /users
│   ├── GET    → daivietblood-get-users
│   ├── POST   → daivietblood-create-user
│   └── OPTIONS (CORS)
│
└── /emergency-requests
    ├── GET    → daivietblood-emergency-requests
    ├── POST   → daivietblood-emergency-requests
    └── OPTIONS (CORS)
```

---

#### Verification Checklist

- [ ] REST API created
- [ ] /users resource created with GET and POST methods
- [ ] /emergency-requests resource created with GET and POST methods
- [ ] CORS enabled for all resources
- [ ] API deployed to `prod` stage
- [ ] Invoke URL saved
