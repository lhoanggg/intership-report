---
title: "S3, CloudFront & Amplify"
date: "2025-09-08"
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

In this section, you will set up Amazon S3 for static assets, CloudFront for content distribution, and AWS Amplify to host the React frontend application.

#### Architecture Overview

![Ảnh đại diện của bạn](/images/5-Workshop/cloudfront.jpg)

---

#### Part 1: Amazon S3 Setup

##### Step 1: Create S3 Bucket for Assets

1. Go to **S3 Console** → **Create bucket**

2. General configuration:
   - **Bucket name**: `daivietblood-assets-{your-account-id}`
   - **AWS Region**: Asia Pacific (Singapore) ap-southeast-1

3. Object Ownership:
   - **ACLs disabled** (recommended)

4. Block Public Access settings:
   - **Block all public access**: ✅ (We'll use CloudFront)

5. Bucket Versioning:
   - **Enable** (recommended for production)

6. Default encryption:
   - **Server-side encryption**: Enable
   - **Encryption type**: Amazon S3 managed keys (SSE-S3)

7. Click **Create bucket**

##### Step 2: Create Bucket Policy for CloudFront

After creating CloudFront distribution (Part 2), update bucket policy:

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

##### Step 3: Upload Sample Assets

1. Create folder structure:
   ```
   /images
     /blood-types
     /icons
     /banners
   /documents
   ```

2. Upload sample images for the application

---

#### Part 2: CloudFront Setup

##### Step 1: Create CloudFront Distribution

1. Go to **CloudFront Console** → **Create distribution**

2. Origin settings:
   - **Origin domain**: Select your S3 bucket
   - **Origin path**: Leave empty
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
   - **Price class**: Use only North America and Europe (or All edge locations)
   - **Default root object**: `index.html`

5. Click **Create distribution**

6. **Important**: Copy the bucket policy provided and update your S3 bucket policy

##### Step 2: Get CloudFront Domain

After distribution is deployed (takes 5-10 minutes):

1. Copy the **Distribution domain name**:
   ```
   https://d1234567890.cloudfront.net
   ```

2. Test accessing an asset:
   ```
   https://d1234567890.cloudfront.net/images/logo.png
   ```

---

#### Part 3: AWS Amplify Setup

##### Step 1: Prepare React Application

1. Create React app (if not exists):
```bash
npx create-react-app daivietblood-frontend
cd daivietblood-frontend
```

2. Install dependencies:
```bash
npm install axios react-router-dom
```

3. Create `.env` file:
```
REACT_APP_API_URL=https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/prod
REACT_APP_ASSETS_URL=https://d1234567890.cloudfront.net
```

4. Sample API service (`src/services/api.js`):
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

5. Push to GitHub repository

##### Step 2: Deploy with Amplify

1. Go to **AWS Amplify Console** → **Create new app**

2. Choose source:
   - **GitHub** → **Continue**
   - Authorize AWS Amplify to access your GitHub

3. Add repository branch:
   - **Repository**: Select your repository
   - **Branch**: main

4. Configure build settings:
   - **App name**: `daivietblood-frontend`
   - **Build and test settings**: Auto-detected for React

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
   - Add `REACT_APP_API_URL` and `REACT_APP_ASSETS_URL`

7. Click **Save and deploy**

##### Step 3: Configure Custom Domain (Optional)

1. Go to **App settings** → **Domain management**
2. Click **Add domain**
3. Enter your domain name
4. Configure DNS records as instructed

---

#### Part 4: Verify Deployment

1. Access Amplify URL:
   ```
   https://main.d1234567890.amplifyapp.com
   ```

2. Test functionality:
   - [ ] Homepage loads correctly
   - [ ] API calls work (check Network tab)
   - [ ] Images load from CloudFront
   - [ ] No CORS errors

---

#### Verification Checklist

- [ ] S3 bucket created with proper settings
- [ ] CloudFront distribution deployed
- [ ] S3 bucket policy updated for CloudFront access
- [ ] Assets accessible via CloudFront URL
- [ ] React app deployed to Amplify
- [ ] Environment variables configured
- [ ] Frontend can call API Gateway
- [ ] Images load from CloudFront
