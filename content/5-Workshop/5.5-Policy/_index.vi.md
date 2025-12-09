---
title: "S3, CloudFront & Amplify"
date: "2025-09-08"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

Trong phần này, bạn sẽ thiết lập Amazon S3 cho tài nguyên tĩnh, CloudFront để phân phối nội dung, và AWS Amplify để host ứng dụng React frontend.

#### Tổng quan Kiến trúc

![Ảnh đại diện của bạn](/images/5-Workshop/cloudfront.jpg)


---

#### Phần 1: Thiết lập Amazon S3

##### Bước 1: Tạo S3 Bucket cho Assets

1. Vào **S3 Console** → **Create bucket**

2. General configuration:
   - **Bucket name**: `daivietblood-assets-{your-account-id}`
   - **AWS Region**: Asia Pacific (Singapore) ap-southeast-1

3. Object Ownership:
   - **ACLs disabled** (khuyến nghị)

4. Block Public Access settings:
   - **Block all public access**: ✅ (Chúng ta sẽ dùng CloudFront)

5. Bucket Versioning:
   - **Enable** (khuyến nghị cho production)

6. Default encryption:
   - **Server-side encryption**: Enable
   - **Encryption type**: Amazon S3 managed keys (SSE-S3)

7. Click **Create bucket**

##### Bước 2: Tạo Bucket Policy cho CloudFront

Sau khi tạo CloudFront distribution (Phần 2), cập nhật bucket policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCloudFrontAccess",
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudfront.amazonaws.com"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::daivietblood-assets-{your-account-id}/*",
            "Condition": {
                "StringEquals": {
                    "AWS:SourceArn": "arn:aws:cloudfront::{account-id}:distribution/{distribution-id}"
                }
            }
        }
    ]
}
```

##### Bước 3: Upload Assets mẫu

1. Tạo cấu trúc folder:
   ```
   /images
     /blood-types
     /icons
     /banners
   /documents
   ```

2. Upload các hình ảnh mẫu cho ứng dụng

---

#### Phần 2: Thiết lập CloudFront

##### Bước 1: Tạo CloudFront Distribution

1. Vào **CloudFront Console** → **Create distribution**

2. Origin settings:
   - **Origin domain**: Chọn S3 bucket của bạn
   - **Origin path**: Để trống
   - **Name**: `daivietblood-s3-origin`
   - **Origin access**: Origin access control settings (recommended)
   - **Create new OAC**: Click **Create control setting**
     - Name: `daivietblood-oac`
     - Signing behavior: Sign requests

3. Default cache behavior:
   - **Viewer protocol policy**: Redirect HTTP to HTTPS
   - **Allowed HTTP methods**: GET, HEAD
   - **Cache policy**: CachingOptimized

4. Settings:
   - **Price class**: Use only North America and Europe (hoặc All edge locations)
   - **Default root object**: `index.html`

5. Click **Create distribution**

6. **Quan trọng**: Copy bucket policy được cung cấp và cập nhật S3 bucket policy

##### Bước 2: Lấy CloudFront Domain

Sau khi distribution được deploy (mất 5-10 phút):

1. Copy **Distribution domain name**:
   ```
   https://d1234567890.cloudfront.net
   ```

2. Test truy cập asset:
   ```
   https://d1234567890.cloudfront.net/images/logo.png
   ```

---

#### Phần 3: Thiết lập AWS Amplify

##### Bước 1: Chuẩn bị ứng dụng React

1. Tạo React app (nếu chưa có):
```bash
npx create-react-app daivietblood-frontend
cd daivietblood-frontend
```

2. Cài đặt dependencies:
```bash
npm install axios react-router-dom
```

3. Tạo file `.env`:
```
REACT_APP_API_URL=https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod
REACT_APP_ASSETS_URL=https://d1234567890.cloudfront.net
```

4. API service mẫu (`src/services/api.js`):
```javascript
import axios from 'axios';

const API_URL = process.env.REACT_APP_API_URL;

export const getUsers = async () => {
  const response = await axios.get(`${API_URL}/users`);
  return response.data;
};

export const createUser = async (userData) => {
  const response = await axios.post(`${API_URL}/users`, userData);
  return response.data;
};

export const getEmergencyRequests = async () => {
  const response = await axios.get(`${API_URL}/emergency-requests`);
  return response.data;
};

export const createEmergencyRequest = async (requestData) => {
  const response = await axios.post(`${API_URL}/emergency-requests`, requestData);
  return response.data;
};
```

5. Push lên GitHub repository

##### Bước 2: Deploy với Amplify

1. Vào **AWS Amplify Console** → **Create new app**

2. Chọn source:
   - **GitHub** → **Continue**
   - Authorize AWS Amplify truy cập GitHub của bạn

3. Thêm repository branch:
   - **Repository**: Chọn repository của bạn
   - **Branch**: main

4. Cấu hình build settings:
   - **App name**: `daivietblood-frontend`
   - **Build and test settings**: Auto-detected cho React

5. Build settings (amplify.yml):
```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - npm ci
    build:
      commands:
        - npm run build
  artifacts:
    baseDirectory: build
    files:
      - '**/*'
  cache:
    paths:
      - node_modules/**/*
```

6. Environment variables:
   - Thêm `REACT_APP_API_URL` và `REACT_APP_ASSETS_URL`

7. Click **Save and deploy**

##### Bước 3: Cấu hình Custom Domain (Tùy chọn)

1. Vào **App settings** → **Domain management**
2. Click **Add domain**
3. Nhập tên domain của bạn
4. Cấu hình DNS records theo hướng dẫn

---

#### Phần 4: Xác minh Deployment

1. Truy cập Amplify URL:
   ```
   https://main.d1234567890.amplifyapp.com
   ```

2. Test chức năng:
   - [ ] Homepage load đúng
   - [ ] API calls hoạt động (kiểm tra Network tab)
   - [ ] Images load từ CloudFront
   - [ ] Không có CORS errors

---

#### Checklist xác minh

- [ ] S3 bucket đã tạo với settings đúng
- [ ] CloudFront distribution đã deploy
- [ ] S3 bucket policy đã cập nhật cho CloudFront access
- [ ] Assets có thể truy cập qua CloudFront URL
- [ ] React app đã deploy lên Amplify
- [ ] Environment variables đã cấu hình
- [ ] Frontend có thể gọi API Gateway
- [ ] Images load từ CloudFront
