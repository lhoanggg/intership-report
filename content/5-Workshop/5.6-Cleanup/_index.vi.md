---
title: "CI/CD, CloudWatch & Dọn dẹp"
date: "2025-09-08"
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Trong phần cuối này, bạn sẽ thiết lập CI/CD Pipeline, cấu hình CloudWatch monitoring, và dọn dẹp tất cả tài nguyên sau khi hoàn thành workshop.

---

## Phần 1: CI/CD Pipeline với CodePipeline

#### Bước 1: Tạo CodeBuild Project

1. Vào **CodeBuild Console** → **Create build project**

2. Project configuration:
   - **Project name**: `daivietblood-backend-build`
   - **Description**: Build project for Lambda functions

3. Source:
   - **Source provider**: GitHub
   - **Repository**: Chọn repository của bạn
   - **Branch**: main

4. Environment:
   - **Environment image**: Managed image
   - **Operating system**: Amazon Linux 2
   - **Runtime**: Standard
   - **Image**: aws/codebuild/amazonlinux2-x86_64-standard:4.0
   - **Service role**: New service role

5. Buildspec:
   - **Build specifications**: Use a buildspec file
   - Tạo file `buildspec.yml` trong repository:

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      nodejs: 18
    commands:
      - echo Installing dependencies...
      - cd backend && npm ci
  
  pre_build:
    commands:
      - echo Running tests...
      - npm test || true
  
  build:
    commands:
      - echo Building Lambda packages...
      - mkdir -p dist
      - zip -r dist/get-users.zip functions/get-users/
      - zip -r dist/create-user.zip functions/create-user/
      - zip -r dist/emergency-requests.zip functions/emergency-requests/
  
  post_build:
    commands:
      - echo Updating Lambda functions...
      - aws lambda update-function-code --function-name daivietblood-get-users --zip-file fileb://dist/get-users.zip
      - aws lambda update-function-code --function-name daivietblood-create-user --zip-file fileb://dist/create-user.zip
      - aws lambda update-function-code --function-name daivietblood-emergency-requests --zip-file fileb://dist/emergency-requests.zip

artifacts:
  files:
    - dist/**/*
```

6. Click **Create build project**

---

#### Bước 2: Tạo CodePipeline

1. Vào **CodePipeline Console** → **Create pipeline**

2. Pipeline settings:
   - **Pipeline name**: `daivietblood-pipeline`
   - **Service role**: New service role

3. Source stage:
   - **Source provider**: GitHub (Version 2)
   - **Connection**: Tạo connection mới hoặc chọn existing
   - **Repository name**: Chọn repository của bạn
   - **Branch name**: main
   - **Output artifact format**: CodePipeline default

4. Build stage:
   - **Build provider**: AWS CodeBuild
   - **Project name**: `daivietblood-backend-build`

5. Deploy stage:
   - **Skip deploy stage** (Lambda đã được update trong build stage)

6. Click **Create pipeline**

---

#### Bước 3: Thêm IAM Permissions cho CodeBuild

1. Vào **IAM Console** → **Roles**
2. Tìm role `codebuild-daivietblood-backend-build-service-role`
3. Thêm inline policy:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "lambda:UpdateFunctionCode",
                "lambda:GetFunction"
            ],
            "Resource": "arn:aws:lambda:ap-southeast-1:*:function:daivietblood-*"
        }
    ]
}
```

---

## Phần 2: CloudWatch Monitoring

#### Bước 1: Tạo CloudWatch Dashboard

1. Vào **CloudWatch Console** → **Dashboards** → **Create dashboard**

2. Dashboard name: `DaiVietBlood-Monitoring`

3. Thêm widgets:

**Widget 1: Lambda Invocations**
- Widget type: Line
- Metrics: Lambda → By Function Name → Invocations
- Chọn tất cả daivietblood functions

**Widget 2: Lambda Errors**
- Widget type: Number
- Metrics: Lambda → By Function Name → Errors
- Statistic: Sum

**Widget 3: Lambda Duration**
- Widget type: Line
- Metrics: Lambda → By Function Name → Duration
- Statistic: Average

**Widget 4: API Gateway Requests**
- Widget type: Line
- Metrics: ApiGateway → By Api Name → Count

**Widget 5: RDS Connections**
- Widget type: Line
- Metrics: RDS → Per-Database Metrics → DatabaseConnections

---

#### Bước 2: Tạo CloudWatch Alarms

**Alarm 1: Lambda Errors**

1. Vào **CloudWatch** → **Alarms** → **Create alarm**
2. Select metric: Lambda → By Function Name → Errors
3. Conditions:
   - Threshold type: Static
   - Whenever Errors is: Greater than 5
   - Period: 5 minutes
4. Notification:
   - Create new SNS topic: `daivietblood-alerts`
   - Email: your-email@example.com
5. Alarm name: `DaiVietBlood-Lambda-Errors`

**Alarm 2: RDS CPU High**

1. Create alarm
2. Select metric: RDS → Per-Database Metrics → CPUUtilization
3. Conditions:
   - Threshold: Greater than 80%
   - Period: 5 minutes
4. Notification: Use existing SNS topic
5. Alarm name: `DaiVietBlood-RDS-CPU-High`

**Alarm 3: API Gateway 5XX Errors**

1. Create alarm
2. Select metric: ApiGateway → By Api Name → 5XXError
3. Conditions:
   - Threshold: Greater than 10
   - Period: 5 minutes
4. Alarm name: `DaiVietBlood-API-5XX-Errors`

---

#### Bước 3: Cấu hình Log Insights

1. Vào **CloudWatch** → **Logs** → **Logs Insights**

2. Chọn log groups:
   - `/aws/lambda/daivietblood-get-users`
   - `/aws/lambda/daivietblood-create-user`
   - `/aws/lambda/daivietblood-emergency-requests`

3. Query mẫu - Tìm errors:
```
fields @timestamp, @message
| filter @message like /ERROR/
| sort @timestamp desc
| limit 50
```

4. Query mẫu - Thống kê duration:
```
fields @timestamp, @duration
| stats avg(@duration), max(@duration), min(@duration) by bin(1h)
```

---

## Phần 3: Dọn dẹp Tài nguyên

{{% notice warning %}}
⚠️ **Quan trọng:** Thực hiện các bước sau để tránh phát sinh chi phí không mong muốn.
{{% /notice %}}

#### Thứ tự dọn dẹp (Quan trọng!)

Dọn dẹp theo thứ tự sau để tránh dependency errors:

#### Bước 1: Xóa Amplify App

1. Vào **Amplify Console**
2. Chọn `daivietblood-frontend`
3. **Actions** → **Delete app**
4. Xác nhận xóa

#### Bước 2: Xóa CloudFront Distribution

1. Vào **CloudFront Console**
2. Chọn distribution → **Disable**
3. Đợi status chuyển sang "Deployed"
4. Chọn distribution → **Delete**

#### Bước 3: Xóa S3 Buckets

1. Vào **S3 Console**
2. Chọn bucket `daivietblood-assets-*`
3. **Empty** bucket trước
4. Sau đó **Delete** bucket

#### Bước 4: Xóa API Gateway

1. Vào **API Gateway Console**
2. Chọn `daivietblood-api`
3. **Actions** → **Delete**

#### Bước 5: Xóa Lambda Functions

1. Vào **Lambda Console**
2. Xóa từng function:
   - `daivietblood-get-users`
   - `daivietblood-create-user`
   - `daivietblood-emergency-requests`
3. Xóa Lambda Layer: `mysql2-layer`

#### Bước 6: Xóa RDS Instance

1. Vào **RDS Console** → **Databases**
2. Chọn `daivietblood-db`
3. **Actions** → **Delete**
4. Bỏ chọn "Create final snapshot"
5. Chọn "I acknowledge..."
6. Nhập `delete me` để xác nhận

#### Bước 7: Xóa VPC Resources

1. Vào **VPC Console**

2. Xóa NAT Gateway:
   - **NAT Gateways** → Chọn NAT Gateway → **Delete**
   - Đợi status "Deleted"

3. Release Elastic IP:
   - **Elastic IPs** → Chọn EIP → **Release**

4. Xóa VPC Endpoints (nếu có):
   - **Endpoints** → Chọn endpoints → **Delete**

5. Xóa Security Groups (trừ default):
   - **Security Groups** → Xóa `daivietblood-lambda-sg`, `daivietblood-rds-sg`

6. Xóa DB Subnet Group:
   - **RDS Console** → **Subnet groups** → Delete `daivietblood-db-subnet-group`

7. Xóa VPC:
   - **Your VPCs** → Chọn `daivietblood-vpc` → **Delete VPC**
   - Điều này sẽ xóa subnets, route tables, internet gateway

#### Bước 8: Xóa CI/CD Resources

1. **CodePipeline Console** → Delete `daivietblood-pipeline`
2. **CodeBuild Console** → Delete `daivietblood-backend-build`

#### Bước 9: Xóa CloudWatch Resources

1. **CloudWatch** → **Dashboards** → Delete `DaiVietBlood-Monitoring`
2. **CloudWatch** → **Alarms** → Delete tất cả alarms liên quan
3. **CloudWatch** → **Log groups** → Delete các log groups `/aws/lambda/daivietblood-*`

#### Bước 10: Xóa IAM Resources

1. **IAM Console** → **Roles**
2. Xóa các roles:
   - `daivietblood-lambda-role`
   - `codebuild-daivietblood-*`
   - `codepipeline-daivietblood-*`

---

#### Checklist Dọn dẹp

- [ ] Amplify app đã xóa
- [ ] CloudFront distribution đã xóa
- [ ] S3 buckets đã empty và xóa
- [ ] API Gateway đã xóa
- [ ] Lambda functions và layers đã xóa
- [ ] RDS instance đã xóa
- [ ] NAT Gateway đã xóa
- [ ] Elastic IP đã release
- [ ] VPC và tất cả components đã xóa
- [ ] CodePipeline và CodeBuild đã xóa
- [ ] CloudWatch dashboards, alarms, log groups đã xóa
- [ ] IAM roles đã xóa

---

#### Xác minh không còn chi phí

1. Vào **AWS Cost Explorer**
2. Kiểm tra không có tài nguyên nào đang chạy
3. Vào **Billing Console** → **Bills** để xác nhận

{{% notice tip %}}
💡 **Mẹo:** Đặt Budget Alert trong AWS Budgets để nhận thông báo khi chi phí vượt ngưỡng.
{{% /notice %}}

---

## Kết luận Workshop

Chúc mừng! 🎉 Bạn đã hoàn thành workshop xây dựng hệ thống Serverless trên AWS.

#### Những gì bạn đã học:

1. ✅ Thiết kế và triển khai VPC với Public/Private Subnets
2. ✅ Tạo RDS MySQL trong môi trường bảo mật
3. ✅ Xây dựng Lambda functions và expose qua API Gateway
4. ✅ Cấu hình S3 và CloudFront cho static assets
5. ✅ Deploy React app với AWS Amplify
6. ✅ Thiết lập CI/CD Pipeline tự động
7. ✅ Giám sát ứng dụng với CloudWatch

#### Bước tiếp theo:

- Tìm hiểu thêm về AWS Well-Architected Framework
- Khám phá các tính năng nâng cao như X-Ray tracing
- Thử nghiệm với Aurora Serverless cho database
- Implement authentication với Amazon Cognito
