---
title: "Event 6"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.6. </b> "
---



# Báo cáo Tổng kết Sự kiện: AWS Cloud Mastery Series #3

**Chủ đề:** AWS Well-Architected – Security Pillar Workshop (Workshop chuyên sâu về Trụ cột Bảo mật)

## 1. Tổng quan & Diễn giả

Sự kiện tập trung vào trụ cột quan trọng nhất trong AWS Well-Architected Framework: **Bảo mật (Security)**. Nội dung được thiết kế để trang bị kiến thức từ cơ bản đến nâng cao về định danh, giám sát, bảo vệ hạ tầng, dữ liệu và quy trình ứng phó sự cố.

**Đội ngũ diễn giả & Chuyên gia:**
Sự kiện quy tụ các chuyên gia đến từ cộng đồng AWS (AWS Community Builders), các Captain của AWS Cloud Club từ nhiều trường đại học (HCMUTE, SGU, PTIT, HUFLIT), kỹ sư đám mây từ FCJ và đặc biệt là **Mendel Grabski** (Chuyên gia Bảo mật & DevOps).

**Giới thiệu về AWS Cloud Club:**
Đây là mạng lưới kết nối sinh viên và chuyên gia, giúp phát triển kỹ năng lãnh đạo kỹ thuật, cung cấp trải nghiệm thực tế (hands-on) và cơ hội mentoring lâu dài. Các Cloud Club tham gia trực thuộc FCJA bao gồm: HCMUTE, SGU, PTIT, và HUFLIT.

## 2. Các Nội dung Kỹ thuật Trọng tâm

### A. Quản lý Định danh & Truy cập (IAM)

IAM được xác định là "tuyến phòng thủ đầu tiên". Phiên thảo luận nhấn mạnh việc chuyển dịch từ quản lý thủ công sang tự động hóa và tuân thủ nghiêm ngặt các nguyên tắc:

- **Nguyên tắc đặc quyền tối thiểu (Least Privilege):** Chỉ cấp quyền vừa đủ.
- **Bảo vệ Root User:** Xóa access keys ngay sau khi tạo.
- **Service Control Policies (SCPs):** Sử dụng chính sách cấp tổ chức (Organization) để thiết lập "trần" quyền hạn cho các tài khoản thành viên (lưu ý: SCP chỉ giới hạn, không cấp quyền).
- **Permission Boundaries:** Giới hạn quyền tối đa cho từng User/Role cụ thể.
- **Xác thực đa yếu tố (MFA):** Khuyến khích sử dụng FIDO2 (bảo mật phần cứng/sinh trắc học) thay vì chỉ dựa vào TOTP truyền thống.
- **Xoay vòng thông tin xác thực (Credential Rotation):** Sử dụng **AWS Secrets Manager** để tự động hóa quy trình xoay vòng (create -> set -> test -> finish) và tích hợp EventBridge để quản lý lịch trình, loại bỏ rủi ro từ "hardcoded credentials".

### B. Giám sát Liên tục & Phát hiện Mối đe dọa (Detection)

Trọng tâm là xây dựng khả năng "nhìn thấy" (visibility) toàn diện và phản ứng tự động:

- **Đa tầng giám sát:** Kết hợp CloudTrail (ghi lại API call, truy cập S3/Lambda) và VPC Flow Logs (lưu lượng mạng).
- **Event-Driven Security:** Sử dụng **EventBridge** làm trung tâm xử lý sự kiện, cho phép định tuyến cảnh báo real-time tới Lambda/SNS/SQS hoặc điều phối giữa các tài khoản khác nhau (Cross-account routing).
- **Detection-as-Code:** Quản lý các quy tắc phát hiện và truy vấn (CloudTrail Lake queries) dưới dạng mã nguồn (version control), đảm bảo triển khai đồng bộ trên toàn tổ chức.

**Phân tích sâu về Amazon GuardDuty:**
Đây là giải pháp phát hiện mối đe dọa thông minh, hoạt động liên tục dựa trên 3 nguồn dữ liệu chính: CloudTrail, VPC Flow Logs, và DNS Logs.

- **Mở rộng phạm vi bảo vệ:** S3, EKS, RDS (phát hiện brute-force), Lambda (phát hiện network lạ), và Malware Protection (quét EBS).
- **Runtime Monitoring:** Sử dụng Agent để giám sát sâu bên trong OS (tiến trình, file access) trên EC2/EKS/Fargate.

### C. Tuân thủ & Cơ sở hạ tầng dưới dạng mã (IaC)

Việc tuân thủ các tiêu chuẩn bảo mật không còn là kiểm tra thủ công mà được tích hợp vào quy trình deployment:

- **Các tiêu chuẩn áp dụng:** AWS Foundational Security Best Practices, CIS Benchmark, PCI DSS, NIST.
- **Cơ chế thực thi:** Sử dụng **AWS CloudFormation** (IaC) để triển khai cấu hình chuẩn, kết hợp với **AWS Security Hub** để tự động kiểm tra (audit) tài nguyên so với các tiêu chuẩn đã đề ra.

### D. Bảo mật Hạ tầng Mạng & Bảo vệ Dữ liệu

- **Network Security:** Phân biệt rõ Security Groups (Stateful - tường lửa mức instance) và NACLs (Stateless - tường lửa mức subnet). Giới thiệu AWS Network Firewall cho các nhu cầu lọc Egress/IPS nâng cao và tích hợp Threat Intelligence để chặn traffic độc hại tự động.
- **Data Protection:**
  - **Mã hóa (Encryption):** Sử dụng KMS với Customer Master Keys (CMK) và Policy conditions để kiểm soát ngữ cảnh giải mã.
  - **Certificate:** Dùng AWS ACM để quản lý và tự động gia hạn SSL/TLS certificate.
  - **Dịch vụ:** Ép buộc sử dụng HTTPS/TLS 1.2+ cho S3 (qua Bucket Policy) và DB (PostgreSQL rds.force_ssl=1).

### E. Ứng phó Sự cố (Incident Response - IR)

Quy trình IR tiêu chuẩn gồm 5 bước: **Chuẩn bị -> Phát hiện & Phân tích -> Khoanh vùng (Containment) -> Diệt trừ & Khôi phục -> Bài học kinh nghiệm**.

- **Chiến lược phòng ngừa:** Không bao giờ public S3 bucket, cô lập các dịch vụ nhạy cảm trong private subnet, và mọi thay đổi hạ tầng phải thông qua IaC với quy trình review (double-gate).

## 3. Trải nghiệm Thực tế & Hỏi đáp (Q&A)

Sự kiện mang lại giá trị thực tiễn cao, đặc biệt liên quan trực tiếp đến dự án **"Automated Incident Response and Forensics"** mà team đang phát triển.

**Vấn đề thảo luận:**
Trong quá trình thử nghiệm dự án, team nhận thấy **Amazon GuardDuty** có độ trễ khoảng 5 phút để tạo ra một finding (phát hiện) sau khi sự cố xảy ra. Team đã đặt câu hỏi về giải pháp giảm thiểu độ trễ này.

**Giải đáp từ chuyên gia:**

- **Bản chất:** Độ trễ của GuardDuty là đặc tính kỹ thuật chấp nhận được do hệ thống cần thời gian phân tích khối lượng dữ liệu lớn để xác định chính xác mối đe dọa, tránh báo động giả (false positives).
- **Giải pháp thay thế:** Để đạt được khả năng phát hiện gần như thời gian thực (near real-time), chuyên gia gợi ý tích hợp các giải pháp bên thứ 3 như **OpenClarity** (mã nguồn mở) hoặc xây dựng logic phát hiện bất thường tùy chỉnh dựa trên CloudTrail events.

**Kết nối:**
Sau sự kiện, anh **Mendel Grabski** (Cựu Head of Security & DevOps) đã bày tỏ sự quan tâm và sẵn sàng hỗ trợ team về mặt chuyên môn cho dự án, mở ra cơ hội hợp tác và mentoring quý giá.

#### Một số hình ảnh khi tham gia sự kiện

- Thêm các hình ảnh của các bạn tại đây
  > Tổng thể, sự kiện không chỉ cung cấp kiến thức kỹ thuật mà còn giúp tôi thay đổi cách tư duy về thiết kế ứng dụng, hiện đại hóa hệ thống và phối hợp hiệu quả hơn giữa các team.
