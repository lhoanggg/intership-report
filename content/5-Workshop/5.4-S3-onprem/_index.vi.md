---
title: "Lambda & API Gateway"
date: "2025-09-08"
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Trong phần này, bạn sẽ tạo AWS Lambda functions và expose chúng qua Amazon API Gateway để xây dựng backend serverless cho DaiVietBlood.

#### Tổng quan Kiến trúc

![Ảnh đại diện của bạn](../../images/5-Workshop/lambda.jpg)


#### API Endpoints

| Method | Endpoint | Mô tả |
|:-------|:---------|:------|
| GET | /users | Lấy tất cả users |
| POST | /users | Tạo user mới |
| GET | /users/{id} | Lấy user theo ID |
| GET | /donations | Lấy tất cả donations |
| POST | /donations | Tạo lịch hẹn hiến máu |
| GET | /emergency-requests | Lấy yêu cầu cấp cứu |
| POST | /emergency-requests | Tạo yêu cầu cấp cứu |

#### Nội dung

1. [Tạo Lambda Functions](5.4.1-prepare/)
2. [Tạo API Gateway](5.4.2-create-interface-enpoint/)
3. [Test API Endpoints](5.4.3-test-endpoint/)
4. [Cấu hình CORS & Security](5.4.4-dns-simulation/)
