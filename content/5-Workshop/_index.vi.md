---
title: "Workshop"
date: "2025-09-08"
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

{{% notice warning %}}
⚠️ **Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn kể cả warning này.
{{% /notice %}}

# Xây dựng Hệ thống Serverless trên AWS - DaiVietBlood

#### Tổng quan

Workshop này hướng dẫn bạn xây dựng **Hệ thống Hiến máu & Cấp cứu Serverless (DaiVietBlood)** trên AWS. Bạn sẽ học cách thiết lập và cấu hình các dịch vụ AWS cốt lõi được sử dụng trong kiến trúc dự án.

![Ảnh kiến trúc AWS](/images/AWS_Architecture.png)

#### Các dịch vụ AWS sử dụng

| Dịch vụ | Mục đích |
|:--------|:---------|
| **Amazon VPC** | Tạo mạng riêng ảo với Public/Private Subnets |
| **NAT Gateway** | Cho phép tài nguyên trong Private Subnet truy cập Internet |
| **Amazon RDS** | Cơ sở dữ liệu MySQL cho ứng dụng |
| **AWS Lambda** | Xử lý logic nghiệp vụ serverless |
| **Amazon API Gateway** | Quản lý và expose REST APIs |
| **Amazon S3** | Lưu trữ tài nguyên tĩnh (images, files) |
| **Amazon CloudFront** | CDN phân phối nội dung toàn cầu |
| **AWS Amplify** | Host ứng dụng Frontend (React) |
| **AWS CodePipeline** | Tự động hóa CI/CD |
| **Amazon CloudWatch** | Giám sát và logging |

#### Bạn sẽ học được gì

- Thiết kế và triển khai kiến trúc **Serverless-First** trên AWS
- Cấu hình **VPC** với Public/Private Subnets đảm bảo bảo mật
- Tạo **RDS MySQL** trong Private Subnet
- Xây dựng **Lambda Functions** và kết nối với **API Gateway**
- Lưu trữ và phân phối nội dung với **S3** và **CloudFront**
- Deploy ứng dụng React với **AWS Amplify**
- Thiết lập **CI/CD Pipeline** tự động
- Giám sát ứng dụng với **CloudWatch**

#### Yêu cầu

- Tài khoản AWS với quyền Administrator
- Kiến thức cơ bản về các dịch vụ AWS
- Quen thuộc với Node.js và React
- AWS CLI đã cài đặt và cấu hình

#### Chi phí ước tính

Workshop này sử dụng các tài nguyên nằm trong **AWS Free Tier** khi có thể. Chi phí ước tính khoảng **~$15-20** nếu hoàn thành trong 1-2 ngày và dọn dẹp tài nguyên ngay sau đó.

#### Nội dung

1. [Tổng quan Workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequiste/)
3. [VPC & Amazon RDS](5.3-S3-vpc/)
4. [Lambda & API Gateway](5.4-S3-onprem/)
5. [S3, CloudFront & Amplify](5.5-Policy/)
6. [CI/CD, CloudWatch & Dọn dẹp](5.6-Cleanup/)
