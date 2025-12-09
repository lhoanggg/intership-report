---
title: "Tổng quan Workshop"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc Hệ thống

Hệ thống DaiVietBlood sử dụng kiến trúc **Serverless-First** trên AWS Cloud, ưu tiên khả năng mở rộng, bảo mật và tối ưu vận hành.

![Ảnh kiến trúc AWS](/images/5-Workshop/AWS_Architecture.jpg)

#### Các thành phần Kiến trúc

**1. Hạ tầng Mạng (VPC)**

| Thành phần | Mô tả |
|:-----------|:------|
| **VPC** | Virtual Private Cloud với CIDR 10.0.0.0/16 |
| **Public Subnet** | Chứa NAT Gateway, cho phép truy cập Internet |
| **Private Subnet** | Chứa Lambda, RDS - cô lập khỏi Internet |
| **NAT Gateway** | Cho phép tài nguyên Private Subnet truy cập Internet |
| **Internet Gateway** | Cho phép Public Subnet giao tiếp với Internet |

**2. Tầng Ứng dụng & Dữ liệu**

| Dịch vụ | Vai trò |
|:--------|:--------|
| **AWS Lambda** | Xử lý logic nghiệp vụ (CRUD operations, yêu cầu cấp cứu) |
| **API Gateway** | Tiếp nhận HTTP requests, điều hướng đến Lambda |
| **Amazon RDS** | Cơ sở dữ liệu MySQL lưu trữ thông tin người dùng, kho máu |
| **Amazon S3** | Lưu trữ file tĩnh (hình ảnh, tài liệu) |

**3. Frontend & Phân phối**

| Dịch vụ | Vai trò |
|:--------|:--------|
| **AWS Amplify** | Host ứng dụng React |
| **CloudFront** | CDN phân phối nội dung toàn cầu với độ trễ thấp |

**4. DevOps & Giám sát**

| Dịch vụ | Vai trò |
|:--------|:--------|
| **CodePipeline** | Tự động hóa quy trình CI/CD |
| **CodeBuild** | Build và test mã nguồn |
| **CloudWatch** | Thu thập logs, metrics, thiết lập alarms |

#### Luồng Dữ liệu

```
User Request → CloudFront → Amplify (Frontend)
                              ↓
                         API Gateway
                              ↓
                         AWS Lambda (Private Subnet)
                              ↓
                         Amazon RDS (Private Subnet)
```

#### Mô hình Bảo mật

1. **Cô lập Mạng**: RDS và Lambda nằm trong Private Subnet, không truy cập trực tiếp Internet
2. **IAM Roles**: Mỗi service có quyền tối thiểu cần thiết (Least Privilege)
3. **Mã hóa Dữ liệu**: At-rest (RDS, S3) và In-transit (HTTPS)
4. **Security Groups**: Kiểm soát traffic inbound/outbound cho từng tài nguyên

#### Mục tiêu Workshop

Sau khi hoàn thành workshop này, bạn sẽ có thể:

- ✅ Tạo VPC với phân đoạn mạng hợp lý
- ✅ Triển khai RDS MySQL trong Private Subnet
- ✅ Xây dựng Lambda functions và expose qua API Gateway
- ✅ Cấu hình S3 và CloudFront cho nội dung tĩnh
- ✅ Deploy ứng dụng React với Amplify
- ✅ Thiết lập CI/CD pipeline
- ✅ Giám sát với CloudWatch
