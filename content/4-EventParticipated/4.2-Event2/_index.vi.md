---
title: "Event 2"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---



# Data Resiliency in a Cloud-first World

### Mục Đích Của Sự Kiện

- Chia sẻ best practices trong thiết kế ứng dụng hiện đại
- Giới thiệu phương pháp DDD và event-driven architecture
- Hướng dẫn lựa chọn compute services phù hợp
- Giới thiệu công cụ AI hỗ trợ development lifecycle

### Danh Sách Diễn Giả

- **Paul Haverfield** -
  Principal Storage Specialist BDM, APJ
- **Tamelly Lim** - Specialist Solutions Architect
- **Ameen Khan S** - GTM specialist for Storage - Data & AI pillar covering ASEAN markets
- **Paul Hidalgo** -

### Nội Dung Nổi Bật

Hôm nay tôi đã có cơ hội tham dự chương trình của AWS với một chủ đề cực kỳ cấp thiết trong bối cảnh hiện nay: **Data Resiliency (Khả năng phục hồi dữ liệu)**. Không chỉ đơn thuần là sao lưu (backup), sự kiện đã mở ra nhiều góc nhìn mới về cách bảo vệ tài sản số trước các mối đe dọa ngày càng tinh vi.

Dưới đây là những điểm cốt lõi (Key Takeaways) mà tôi đã đúc kết được:

### 1. Định nghĩa lại: Data Resiliency khác gì với High Availability (HA) và Disaster Recovery (DR)?

Trước đây chúng ta thường tập trung vào HA (đảm bảo hệ thống luôn online) hay DR (khôi phục sau thảm họa vật lý). Tuy nhiên, **Data Resiliency** là một khái niệm rộng hơn và mang tính "chủ động" hơn:

- **Bối cảnh:** "Everything fails, all the time" (Werner Vogels) – Mọi thứ đều có thể hỏng hóc, kể cả các khóa vật lý hay phần cứng.
- **Sự khác biệt:** Nếu HA xử lý sự cố hạ tầng, Data Resiliency tập trung vào **sự toàn vẹn của dữ liệu**. Nó là khả năng tổ chức duy trì hoạt động, chống chịu và phục hồi ngay cả khi chịu sự tấn công mạng (như Ransomware) hoặc lỗi con người.
- **Mục tiêu:** Phát hiện bất thường (Anomalies) và tự động hóa quy trình phản ứng mà không cần can thiệp của con người.

### 2. Tại sao Data Resiliency trở thành "Nhu cầu tất yếu"?

Sự bùng nổ của việc tạo dữ liệu đi kèm với các lỗ hổng công nghệ mới. Ba xu hướng chính thúc đẩy sự chuyển dịch từ _Protection_ sang _Resiliency_:

1.  **Yêu cầu pháp lý (Regulatory):** Tuân thủ luật bảo vệ dữ liệu khắt khe.
2.  **Công nghệ (Technology):** Sự phức tạp của Multi-cloud và Hybrid-cloud.
3.  **Bối cảnh mối đe dọa (Threat Landscape):** Ransomware không chỉ mã hóa dữ liệu chính mà còn tấn công cả các bản backup.

### 3. Data Immutability: "Tấm khiên" bất khả xâm phạm

Một từ khóa được nhắc đến liên tục là **Data Immutability (Tính bất biến của dữ liệu)**.

- Đây là khả năng tạo ra các bản sao dữ liệu **không thể thay đổi, không thể xóa sửa** trong một khoảng thời gian định sẵn.
- Trong trường hợp bị tấn công Ransomware, ngay cả khi hacker có quyền admin cao nhất, chúng cũng không thể tác động đến bản backup này.
- Nó đóng vai trò là "Last line of defense" (tuyến phòng thủ cuối cùng) để đảm bảo luôn có một phiên bản sạch để khôi phục.

### 4. Chiến lược bảo vệ: Mô hình AWS 3-2-1-1-0

Quy tắc backup 3-2-1 truyền thống đã được nâng cấp để phù hợp với kỷ nguyên đám mây:

- **3** bản sao dữ liệu.
- **2** phương tiện lưu trữ khác nhau.
- **1** bản sao lưu trữ off-site (khác region).
- **1** bản sao offline hoặc **Immutable** (Air-gapped).
- **0** lỗi xảy ra trong quá trình khôi phục (được kiểm chứng bằng test tự động).

Các khái niệm quan trọng cần nhớ:

- **RPO (Recovery Point Objective):** Chấp nhận mất bao nhiêu dữ liệu?
- **RTO (Recovery Time Objective):** Mất bao lâu để hệ thống chạy lại?
- **Backup Vault:** Container lưu trữ backup, được mã hóa bởi AWS KMS để tăng cường bảo mật.

### 5. Hệ sinh thái công cụ (Tools & Solutions)

Sự kiện đã giới thiệu các giải pháp tích hợp mạnh mẽ trên AWS:

- **Commvault Cloud on AWS:**
  - Cung cấp _Air Gap Protect_ (cách ly dữ liệu an toàn).
  - _Cloud Rewind:_ Khả năng "tua ngược" thời gian để khôi phục toàn bộ instances, VPC như chưa từng có sự cố.
- **Clumio:**
  - Giải pháp All-in-one backup đơn giản hóa.
  - Sử dụng kiến trúc _Serverless Workflow_ (đội quân Lambda functions) giúp tối ưu chi phí và vận hành.
- **Elastio:**
  - Tập trung vào: Detect (Phát hiện), Respond (Phản hồi), Recover (Khôi phục).
  - Quét Malware/Ransomware ngay trong các bản Snapshot để đảm bảo bản backup không chứa mã độc tiềm ẩn.

### 6. Workshop Architecture: Kiến trúc thực tế

Trong phần thực hành, chúng tôi đã triển khai một mô hình bảo vệ toàn diện:

**Các thành phần chính:**

- **Nguồn:** EC2 Instances (EBS) và S3 Bucket chứa dữ liệu quan trọng.
- **Cơ chế:** Sử dụng **AWS Backup Plan** với lịch trình hàng giờ (hourly).
- **Lớp bảo vệ:**
  - _Primary:_ Lưu tại Vault thường (`workshop-sources-regular-vault`).
  - _Secondary (Air-gapped):_ Copy sang Region khác (`us-east-1-LAG-Vault`) với chế độ **Immutability**.
- **Kiểm thử (Validation):** Tích hợp **Elastio** với AWS Backup.
  - Tự động quét Malware trên bản backup.
  - Thực hiện _Restore Testing_ hàng giờ để chắc chắn rằng dữ liệu backup có thể dùng được ("0 error" strategy).

---

### Tổng kết

Sự kiện đã thay đổi tư duy của tôi từ việc chỉ "sao lưu dữ liệu" sang "xây dựng khả năng phục hồi". Trong kỷ nguyên mà tấn công mạng là điều không thể tránh khỏi, việc sở hữu một chiến lược Data Resiliency với tính năng **Immutability** và **Automation** (như Elastio/Commvault) chính là chìa khóa sống còn của doanh nghiệp.

#### Một số hình ảnh khi tham gia sự kiện

- Thêm các hình ảnh của các bạn tại đây
  > Tổng thể, sự kiện không chỉ cung cấp kiến thức kỹ thuật mà còn giúp tôi thay đổi cách tư duy về thiết kế ứng dụng, hiện đại hóa hệ thống và phối hợp hiệu quả hơn giữa các team.
