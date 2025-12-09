---
title: "Tạo API Gateway"
date: "2025-09-08"
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Bước 1: Tạo REST API

1. Vào **API Gateway Console** → **Create API**

2. Chọn loại API:
   - **REST API** → **Build**

3. Tạo API mới:
   - **Protocol**: REST
   - **Create new API**: New API
   - **API name**: `daivietblood-api`
   - **Description**: REST API for DaiVietBlood system
   - **Endpoint Type**: Regional

4. Click **Create API**

---

#### Bước 2: Tạo Resources

**2.1. Tạo /users Resource**

1. Chọn root `/` → **Actions** → **Create Resource**

2. Cấu hình:
   - **Resource Name**: `users`
   - **Resource Path**: `users`
   - **Enable API Gateway CORS**: ✅ Check

3. Click **Create Resource**

**2.2. Tạo /emergency-requests Resource**

1. Chọn root `/` → **Actions** → **Create Resource**

2. Cấu hình:
   - **Resource Name**: `emergency-requests`
   - **Resource Path**: `emergency-requests`
   - **Enable API Gateway CORS**: ✅ Check

3. Click **Create Resource**

---

#### Bước 3: Tạo Methods cho /users

**3.1. GET /users**

1. Chọn `/users` → **Actions** → **Create Method** → **GET**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Region**: ap-southeast-1
   - **Lambda Function**: `daivietblood-get-users`

3. Click **Save** → **OK** (để thêm permission)

**3.2. POST /users**

1. Chọn `/users` → **Actions** → **Create Method** → **POST**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Region**: ap-southeast-1
   - **Lambda Function**: `daivietblood-create-user`

3. Click **Save** → **OK**

---

#### Bước 4: Tạo Methods cho /emergency-requests

**4.1. GET /emergency-requests**

1. Chọn `/emergency-requests` → **Actions** → **Create Method** → **GET**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Function**: `daivietblood-emergency-requests`

3. Click **Save** → **OK**

**4.2. POST /emergency-requests**

1. Chọn `/emergency-requests` → **Actions** → **Create Method** → **POST**

2. Integration setup:
   - **Integration type**: Lambda Function
   - **Use Lambda Proxy integration**: ✅ Check
   - **Lambda Function**: `daivietblood-emergency-requests`

3. Click **Save** → **OK**

---

#### Bước 5: Bật CORS

Cho mỗi resource (`/users`, `/emergency-requests`):

1. Chọn resource → **Actions** → **Enable CORS**

2. Cấu hình:
   - **Access-Control-Allow-Methods**: GET, POST, OPTIONS
   - **Access-Control-Allow-Headers**: Content-Type, X-Amz-Date, Authorization, X-Api-Key
   - **Access-Control-Allow-Origin**: `*`

3. Click **Enable CORS and replace existing CORS headers**

4. Click **Yes, replace existing values**

---

#### Bước 6: Deploy API

1. **Actions** → **Deploy API**

2. Deployment stage:
   - **Deployment stage**: [New Stage]
   - **Stage name**: `prod`
   - **Stage description**: Production stage

3. Click **Deploy**

4. Copy **Invoke URL**:
   ```
   https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod
   ```

{{% notice info %}}
ℹ️ Lưu URL này. Bạn sẽ cần nó để cấu hình frontend.
{{% /notice %}}

---

#### Bước 7: Tóm tắt cấu trúc API

Sau khi hoàn thành, cấu trúc API của bạn sẽ như sau:

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

#### Checklist xác minh

- [ ] REST API đã tạo
- [ ] /users resource đã tạo với GET và POST methods
- [ ] /emergency-requests resource đã tạo với GET và POST methods
- [ ] CORS đã bật cho tất cả resources
- [ ] API đã deploy lên `prod` stage
- [ ] Invoke URL đã lưu
