---
title: "Blog 2"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---


# Advanced analytics using Amazon CloudWatch Logs Insights

**Tác giả:** Joe Alioto & Dot Ho | **Ngày:** 27 AUG 2025 | **Chuyên mục:** Amazon CloudWatch, Observability, Logs, Analytics

Quản lý và phân tích log hiệu quả là rất quan trọng để duy trì hệ thống ổn định, bảo mật và hiệu suất cao. Amazon CloudWatch Logs Insights từ lâu đã là một công cụ mạnh mẽ để tìm kiếm, lọc và phân tích dữ liệu log từ nhiều log group khác nhau. Việc bổ sung hỗ trợ ngôn ngữ khai vấn OpenSearch Piped Processing Language (PPL) và OpenSearch SQL mang đến sự linh hoạt và quen thuộc hơn trong phân tích log.

Nếu bạn là developer đang debug lỗi ứng dụng, security analyst điều tra mối đe dọa, hoặc operations manager theo dõi hiệu suất — những cải tiến này giúp bạn:

- Sử dụng khả năng AI để sinh câu truy vấn từ ngôn ngữ tự nhiên (natural language query generation) và tóm tắt kết quả tự động — giảm thời gian từ log raw đến insight.
- Dễ dàng **correlate** logs nhờ JOIN giữa nhiều log group.
- Sử dụng thư viện hàm mạnh mẽ để xử lý & phân tích dữ liệu log (JSON parsing, toán học, xử lý chuỗi...) — không cần công cụ phụ.
- Correlate dữ liệu từ nhiều nguồn log khác nhau — phù hợp hệ thống phức tạp, micro-service, distributed system.

---

## Những khả năng nổi bật của PPL & SQL trong CloudWatch Logs Insights

### Ngôn ngữ truy vấn quen thuộc như SQL

Bạn có thể dùng SQL để phân tích log thay vì phải học cú pháp riêng. Ví dụ:

```sql
SELECT eventName, COUNT(*) as count
FROM loggroupname
GROUP BY eventName
ORDER BY count DESC
LIMIT 10
```

---

### Khả năng correlation — JOIN giữa nhiều log groups

Bạn có thể nối dữ liệu từ log nhóm khác nhau để có cái nhìn toàn diện. Ví dụ:

```sql
SELECT a.transaction_id,
       a.error_message AS application_error,
       a.timestamp AS application_timestamp,
       i.error_message AS infrastructure_error,
       i.timestamp AS infrastructure_timestamp
FROM application_logs a
LEFT JOIN infrastructure_logs i ON a.transaction_id = i.transaction_id
```

Tính năng này đặc biệt hữu ích trong hệ thống microservices, khi một transaction có thể trải qua nhiều dịch vụ khác nhau.

---

### Thư viện hàm để xử lý & phân tích dữ liệu log phức tạp

CloudWatch Logs Insights hỗ trợ các hàm xử lý JSON, toán học, xử lý chuỗi, và nhiều hàm hữu ích khác. Ví dụ bạn có thể:

```sql
SELECT
  JSON_EXTRACT_SCALAR(message, '$.user_id') as user_id,
  AVG(CAST(JSON_EXTRACT_SCALAR(message, '$.response_time') AS DOUBLE)) as avg_response_time
FROM loggroupname
GROUP BY JSON_EXTRACT_SCALAR(message, '$.user_id')
```

Điều này cho phép bạn phân tích sâu hơn mà không cần đưa log ra ngoài hệ thống.
###
 Truy vấn phức tạp: subqueries & aggregations nâng cao

Bạn có thể viết sub-query hoặc sử dụng các aggregation phức tạp để phân tích pattern hay hành vi không thường xuyên:

```sql
SELECT user_id, api_calls
FROM (
   SELECT user_id, COUNT(*) as api_calls
   FROM api_logs
   GROUP BY user_id
) subquery
WHERE api_calls > (SELECT AVG(api_calls) * 2 FROM subquery)
```

---

## Tạo truy vấn & phân tích nhanh bằng AI + on-demand anomaly detection

### Natural language query generation & summary

Bạn có thể gõ câu bằng tiếng Anh (hoặc ngôn ngữ hỗ trợ) đơn giản như:  
> "Get api count by eventSource and eventName and sort it by most to least"  

→ CloudWatch Logs Insights tự sinh query (PPL / SQL / Logs Insights QL) hợp lệ. Sau khi chạy, hệ thống có thể **tóm tắt kết quả** thành ngôn ngữ dễ hiểu — giúp bạn nhanh chóng hiểu insight mà không cần lướt hàng trăm hoặc hàng ngàn dòng log.

### On-demand anomaly detection

Một tính năng đáng chú ý: bạn có thể dùng lệnh để phát hiện pattern bất thường ngay lập tức — giúp bạn phát hiện vấn đề mà không cần cài đặt phức tạp trước:

- Dùng `pattern @message | anomaly` để tìm pattern bất thường.
- Kết quả trả về bao gồm các trường: `@description`, `@anomalyLogSamples`, `@priority`, `@priorityScore`, `@patternString`.
- Bạn cũng có thể dùng thêm `compare` để so sánh pattern giữa các khoảng thời gian — hữu ích khi muốn so sánh logs thời gian hiện tại vs quá khứ.

Use-cases điển hình:

- Giám sát bảo mật: phát hiện truy cập bất thường, login failure...
- Giám sát sức khỏe ứng dụng: tìm spikes lỗi, latency...
- Giám sát hạ tầng: phát hiện sử dụng tài nguyên bất thường...

---

## Correlation — subqueries & JOINs giữa nhiều log-group

Với khả năng JOIN và subqueries, bạn có thể correlates dữ liệu giữa nhiều log group — giúp theo dõi transaction end-to-end, debug cross-service, hoặc tra cứu các sự kiện liên quan giữa dịch vụ khác nhau.

**Ví dụ**: tìm tất cả orders có payment "completed"

**SQL:**

```sql
SELECT correlationId, timestamp
FROM `/orders/prod` o
WHERE EXISTS (
  SELECT 1
  FROM `/payments/prod` p
  WHERE p.correlationId = o.correlationId
    AND p.paymentStatus = 'completed'
)
ORDER BY timestamp DESC
LIMIT 10;
```

**PPL:**

```ppl
fields timestamp, correlationId
| where `correlationId` IN [
    search source=`/payments/prod`
    | where paymentStatus="completed"
    | fields `correlationId`
  ]
| head 10
```

Bạn cũng có thể dùng JOIN để kết hợp dữ liệu payment + order đầy đủ (orderId, paymentStatus, amount, phương thức thanh toán…).

Khi làm việc với correlation — bạn cần chú ý đến: thời gian phù hợp, indexing các field thường dùng, performance với dataset lớn, và cách xử lý các giá trị null hoặc thiếu correlationId.

---

## Kết luận

Những cải tiến trong CloudWatch Logs Insights thông qua hỗ trợ PPL & SQL đã mở rộng đáng kể khả năng quản lý, phân tích và khai thác log:

- Dễ tiếp cận hơn với ngôn ngữ quen thuộc (SQL).  
- Phân tích sâu hơn nhờ thư viện hàm & subqueries/aggregations.  
- Correlation giữa các nguồn log giúp debug & phân tích cross-service mạnh mẽ.  
- Generative AI giúp rút ngắn thời gian từ raw logs → insight.  
- On-demand anomaly detection giúp phát hiện vấn đề nhanh, sớm.  

Việc sử dụng Logs Insights giờ không đơn thuần là tìm kiếm log — mà là một **nền tảng phân tích log toàn diện**, phù hợp với hệ thống phân tán, micro-services, hoặc những môi trường cần observability cao.  

Nếu bạn muốn sử dụng Logs Insights cho production, nên cân nhắc việc định nghĩa index cho các trường hay dùng nhiều, để tăng hiệu năng, và thường xuyên rà soát lại saved queries khi hệ thống phát triển.
