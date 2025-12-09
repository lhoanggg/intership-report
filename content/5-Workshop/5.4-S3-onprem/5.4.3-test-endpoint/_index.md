---
title: "Test API Endpoints"
date: "2025-09-08"
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### Step 1: Test from API Gateway Console

**1.1. Test GET /users**

1. Go to **API Gateway Console** → Select `daivietblood-api`
2. Select `/users` → **GET**
3. Click **Test**
4. Click **Test** button

Expected response:
```json
{
  "statusCode": 200,
  "body": "[]"
}
```

---

#### Step 2: Test with cURL

Replace `YOUR_API_URL` with your actual Invoke URL.

**2.1. Create a User (POST /users)**

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

Expected response:
```json
{
  "id": 1,
  "email": "nguyen.van.a@example.com",
  "name": "Nguyen Van A",
  "blood_type": "O+",
  "phone": "0901234567"
}
```

**2.2. Get All Users (GET /users)**

```bash
curl https://YOUR_API_URL/prod/users
```

Expected response:
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

**2.3. Create Emergency Request (POST /emergency-requests)**

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

Expected response:
```json
{
  "id": 1,
  "message": "Emergency request created"
}
```

**2.4. Get Emergency Requests (GET /emergency-requests)**

```bash
curl https://YOUR_API_URL/prod/emergency-requests
```

---

#### Step 3: Test with Postman

1. Open Postman
2. Create new Collection: `DaiVietBlood API`
3. Add requests:

| Request Name | Method | URL |
|:-------------|:-------|:----|
| Get Users | GET | `{{baseUrl}}/users` |
| Create User | POST | `{{baseUrl}}/users` |
| Get Emergency Requests | GET | `{{baseUrl}}/emergency-requests` |
| Create Emergency Request | POST | `{{baseUrl}}/emergency-requests` |

4. Set Collection variable:
   - `baseUrl`: `https://YOUR_API_URL/prod`

---

#### Step 4: Verify Lambda Logs

1. Go to **CloudWatch Console** → **Log groups**

2. Find log groups:
   - `/aws/lambda/daivietblood-get-users`
   - `/aws/lambda/daivietblood-create-user`
   - `/aws/lambda/daivietblood-emergency-requests`

3. Check recent log streams for:
   - Successful invocations
   - Any errors or exceptions
   - Database connection logs

---

#### Common Issues & Solutions

| Issue | Cause | Solution |
|:------|:------|:---------|
| 502 Bad Gateway | Lambda error | Check CloudWatch logs for details |
| Timeout | Lambda cannot reach RDS | Verify VPC, Subnets, Security Groups |
| CORS error | CORS not configured | Enable CORS on API Gateway |
| 500 Internal Server Error | Database connection failed | Check DB credentials in environment variables |

---

#### Step 5: Performance Check

1. Note the response time for each API call
2. First call may be slow (Lambda cold start)
3. Subsequent calls should be faster

Expected performance:
| Endpoint | Cold Start | Warm |
|:---------|:-----------|:-----|
| GET /users | ~3-5s | ~200-500ms |
| POST /users | ~3-5s | ~200-500ms |
| GET /emergency-requests | ~3-5s | ~200-500ms |

{{% notice tip %}}
💡 **Tip:** Lambda cold start in VPC can be slow. Consider using Provisioned Concurrency for production workloads.
{{% /notice %}}

---

#### Verification Checklist

- [ ] GET /users returns empty array or user list
- [ ] POST /users creates new user successfully
- [ ] GET /emergency-requests returns requests list
- [ ] POST /emergency-requests creates new request
- [ ] No CORS errors in browser console
- [ ] CloudWatch logs show successful invocations
