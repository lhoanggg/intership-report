---
title: "Chuẩn bị"
date: "2025-09-08"
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Yêu cầu trước khi bắt đầu

Trước khi bắt đầu workshop này, hãy đảm bảo bạn có:

**1. Tài khoản AWS**
- Tài khoản AWS đang hoạt động với quyền Administrator
- Khuyến nghị: Sử dụng IAM User thay vì Root account
- Region: **Asia Pacific (Singapore) - ap-southeast-1**

**2. Công cụ phát triển Local**

| Công cụ | Phiên bản | Mục đích |
|:--------|:----------|:---------|
| Node.js | >= 18.x | Chạy Lambda functions locally |
| npm/yarn | Mới nhất | Quản lý packages |
| AWS CLI | >= 2.x | Tương tác với AWS services |
| Git | Mới nhất | Quản lý phiên bản |

**3. Yêu cầu kiến thức**
- Hiểu biết cơ bản về AWS services (VPC, EC2, S3)
- Quen thuộc với REST APIs
- Cơ bản Node.js/JavaScript hoặc Python
- Kiến thức React cơ bản

---

#### Bước 1: Cấu hình AWS CLI

1. Cài đặt AWS CLI từ [Hướng dẫn cài đặt AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

2. Cấu hình credentials:
```bash
aws configure
```

3. Nhập thông tin credentials:
```
AWS Access Key ID: [Access Key của bạn]
AWS Secret Access Key: [Secret Key của bạn]
Default region name: ap-southeast-1
Default output format: json
```

4. Xác minh cấu hình:
```bash
aws sts get-caller-identity
```

---

#### Bước 2: Tạo IAM User cho Workshop

1. Vào **IAM Console** → **Users** → **Create user**

2. Thông tin user:
   - User name: `workshop-admin`
   - Chọn: **Provide user access to the AWS Management Console**

3. Thiết lập quyền:
   - Chọn: **Attach policies directly**
   - Tìm và chọn: `AdministratorAccess`

4. Tạo user và lưu credentials an toàn

{{% notice warning %}}
⚠️ **Lưu ý bảo mật:** Sau khi hoàn thành workshop, hãy xóa IAM user này hoặc gỡ bỏ policy AdministratorAccess.
{{% /notice %}}

---

#### Bước 3: Kiểm tra Service Quotas

Đảm bảo tài khoản của bạn có đủ quotas cho:

| Dịch vụ | Tài nguyên | Tối thiểu cần |
|:--------|:-----------|:--------------|
| VPC | VPCs per Region | 1 |
| VPC | Subnets per VPC | 4 |
| VPC | NAT Gateways per AZ | 1 |
| RDS | DB Instances | 1 |
| Lambda | Concurrent Executions | 10 |
| API Gateway | REST APIs | 1 |
| S3 | Buckets | 2 |

Kiểm tra quotas tại: **Service Quotas Console** → Chọn service → View quotas

---

#### Bước 4: Chuẩn bị Source Code

1. Clone repository mẫu:
```bash
git clone https://github.com/your-repo/daivietblood-workshop.git
cd daivietblood-workshop
```

2. Cấu trúc project:
```
daivietblood-workshop/
├── frontend/           # Ứng dụng React
│   ├── src/
│   └── package.json
├── backend/            # Lambda functions
│   ├── functions/
│   └── package.json
├── infrastructure/     # CloudFormation templates
│   └── templates/
└── README.md
```

3. Cài đặt dependencies:
```bash
# Frontend
cd frontend && npm install

# Backend
cd ../backend && npm install
```

---

#### Bước 5: Ước tính Chi phí

| Dịch vụ | Cấu hình | Chi phí/Ngày |
|:--------|:---------|:-------------|
| NAT Gateway | 1 NAT Gateway | ~$1.08 |
| RDS | db.t3.micro | ~$0.52 |
| Lambda | Free Tier | $0.00 |
| API Gateway | Free Tier | $0.00 |
| S3 | < 5GB | ~$0.01 |
| CloudFront | < 1GB transfer | ~$0.01 |
| Amplify | Build & Host | ~$0.50 |

**Tổng ước tính: ~$2-3/ngày**

{{% notice tip %}}
💡 **Mẹo:** Hoàn thành workshop trong 1-2 ngày và dọn dẹp tài nguyên ngay lập tức để giảm thiểu chi phí.
{{% /notice %}}

---

#### Checklist trước khi bắt đầu

- [ ] Tài khoản AWS sẵn sàng với quyền Administrator
- [ ] AWS CLI đã cài đặt và cấu hình
- [ ] Node.js >= 18.x đã cài đặt
- [ ] Git đã cài đặt
- [ ] Source code đã clone
- [ ] Region đã đặt là ap-southeast-1
