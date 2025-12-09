---
title: "Các bài blogs đã dịch"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 3. </b> "
---



###  [Blog 1 - Monitoring server health with Amazon GameLift Servers](3.1-Blog1/)
Bài blog này hướng dẫn cách giám sát và chẩn đoán hiệu suất server game đa người chơi sử dụng Amazon GameLift. Nó nhấn mạnh việc sử dụng telemetry metrics (CPU, bộ nhớ, mạng, tick rate, và custom metrics) kết hợp với Amazon Managed Grafana để phát hiện crash, bottleneck và vấn đề tài nguyên. Bài viết cũng hướng dẫn xử lý các sự cố cụ thể như crash phiên game, CPU cao, và hỗ trợ container, đồng thời mở rộng giám sát với custom metrics và alert chủ động để đảm bảo trải nghiệm người chơi mượt mà.

### [Blog 2 - Advanced analytics using Amazon CloudWatch Logs Insights](3.2-Blog2/)
Bài blog này giới thiệu các tính năng phân tích nâng cao mới của Amazon CloudWatch Logs Insights, cho phép người dùng xử lý log hiệu quả hơn bằng cú pháp SQL và ngôn ngữ Piped Processing Language (PPL). Blog nhấn mạnh khả năng truy vấn, phân tích và trực quan hóa log mạnh mẽ hơn nhờ việc hỗ trợ JOIN giữa nhiều log group, subquery, hàm thống kê, cũng như khả năng phân tích JSON phức tạp.
Blog cũng giới thiệu các tính năng AI hỗ trợ phân tích log: tạo truy vấn tự động từ ngôn ngữ tự nhiên, tóm tắt kết quả truy vấn, và phát hiện bất thường (anomaly detection) theo yêu cầu. Những tính năng này giúp giảm thời gian điều tra sự cố, tăng tốc debug và nâng cao khả năng quan sát hệ thống.
Ngoài ra, bài viết mô tả các tình huống sử dụng thực tế như phân tích lỗi ứng dụng, tìm sự cố hiệu năng, tương quan log giữa nhiều microservice, cũng như triển khai quy trình phân tích dữ liệu log end-to-end trên CloudWatch.

### [Blog 3 - Handling sensitive log data using Amazon CloudWatch](3.3-Blog3/) 
Bài viết trình bày cách bảo vệ dữ liệu nhạy cảm khi ghi log bằng CloudWatch Logs: sử dụng chính sách bảo vệ để tự động phát hiện và **che (mask/redact)** các thông tin như PII, dữ liệu tài chính hay thông tin sức khỏe, ngay khi log được thu thập. CloudWatch hỗ trợ nhiều loại “managed data identifiers” và cho phép định nghĩa custom nếu cần. Chỉ những user/role với quyền `logs:Unmask` mới xem được dữ liệu gốc — giúp cân bằng giữa bảo mật dữ liệu và khả năng debug/giám sát.

