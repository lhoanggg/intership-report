---
title: "Test API Endpoints"
date: "2025-09-08"
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Bước 1: Test từ API Gateway Console

**1.1. Test GET /users**

1. Vào **API Gateway Console** → Chọn `daivietblood-api`
2. Chọn `/users` → **GET**
3. Click **Test**
4. Click nút **Test**

Response mong đợi:
```json
{
  "statusCode": 200,
  "body": "[]"
}
```

---

#### Bước 2: Test với cURL

Thay `YOUR_API_URL` bằng Invoke URL thực tế của bạn.

**2.1. Tạo User (POST /users)**

```bash
curl -X POST https://YOUR_API_URL/prod/users \
  -H "Content-Type: application/json" \
  -d '{
    "email": "nguyen.van.a@example.com",
    "name": "Nguyen Van A",
    "blood_type": "O+",
    "phone": "0901234567"
  }'
```

Response mong đợi:
```json
{
  "id": 1,
  "email": "nguyen.van.a@example.com",
  "name": "Nguyen Van A",
  "blood_type": "O+",
  "phone": "0901234567"
}
```

**2.2. Lấy tất cả Users (GET /users)**

```bash
curl https://YOUR_API_URL/prod/users
```

Response mong đợi:
```json
[
  {
    "id": 1,
    "email": "nguyen.van.a@example.com",
    "name": "Nguyen Van A",
    "blood_type": "O+",
    "phone": "0901234567",
    "created_at": "2025-12-09T10:00:00.000Z"
  }
]
```

**2.3. Tạo yêu cầu cấp cứu (POST /emergency-requests)**

```bash
curl -X POST https://YOUR_API_URL/prod/emergency-requests \
  -H "Content-Type: application/json" \
  -d '{
    "requester_name": "Benh vien Cho Ray",
    "blood_type": "AB-",
    "units_needed": 5,
    "hospital": "Cho Ray Hospital",
    "urgency": "critical"
  }'
```

Response mong đợi:
```json
{
  "id": 1,
  "message": "Emergency request created"
}
```

**2.4. Lấy yêu cầu cấp cứu (GET /emergency-requests)**

```bash
curl https://YOUR_API_URL/prod/emergency-requests
```

---

#### Bước 3: Test với Postman

1. Mở Postman
2. Tạo Collection mới: `DaiVietBlood API`
3. Thêm các requests:

| Tên Request | Method | URL |
|:------------|:-------|:----|
| Get Users | GET | `{{baseUrl}}/users` |
| Create User | POST | `{{baseUrl}}/users` |
| Get Emergency Requests | GET | `{{baseUrl}}/emergency-requests` |
| Create Emergency Request | POST | `{{baseUrl}}/emergency-requests` |

4. Đặt Collection variable:
   - `baseUrl`: `https://YOUR_API_URL/prod`

---

#### Bước 4: Kiểm tra Lambda Logs

1. Vào **CloudWatch Console** → **Log groups**

2. Tìm log groups:
   - `/aws/lambda/daivietblood-get-users`
   - `/aws/lambda/daivietblood-create-user`
   - `/aws/lambda/daivietblood-emergency-requests`

3. Kiểm tra log streams gần đây để xem:
   - Các invocations thành công
   - Bất kỳ errors hoặc exceptions
   - Database connection logs

---

#### Các vấn đề thường gặp & Giải pháp

| Vấn đề | Nguyên nhân | Giải pháp |
|:-------|:------------|:----------|
| 502 Bad Gateway | Lambda error | Kiểm tra CloudWatch logs để xem chi tiết |
| Timeout | Lambda không thể kết nối RDS | Xác minh VPC, Subnets, Security Groups |
| CORS error | CORS chưa cấu hình | Bật CORS trên API Gateway |
| 500 Internal Server Error | Kết nối database thất bại | Kiểm tra DB credentials trong environment variables |

---

#### Bước 5: Kiểm tra Performance

1. Ghi nhận thời gian response cho mỗi API call
2. Lần gọi đầu tiên có thể chậm (Lambda cold start)
3. Các lần gọi tiếp theo sẽ nhanh hơn

Performance mong đợi:
| Endpoint | Cold Start | Warm |
|:---------|:-----------|:-----|
| GET /users | ~3-5s | ~200-500ms |
| POST /users | ~3-5s | ~200-500ms |
| GET /emergency-requests | ~3-5s | ~200-500ms |

{{% notice tip %}}
💡 **Mẹo:** Lambda cold start trong VPC có thể chậm. Cân nhắc sử dụng Provisioned Concurrency cho production workloads.
{{% /notice %}}

---

#### Checklist xác minh

- [ ] GET /users trả về mảng rỗng hoặc danh sách users
- [ ] POST /users tạo user mới thành công
- [ ] GET /emergency-requests trả về danh sách requests
- [ ] POST /emergency-requests tạo request mới
- [ ] Không có CORS errors trong browser console
- [ ] CloudWatch logs hiển thị invocations thành công
