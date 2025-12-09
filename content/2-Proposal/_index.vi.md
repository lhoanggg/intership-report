---
title: "Bản đề xuất"
date: "2025-09-08"
weight: 2
chapter: false
pre: " <b> 2. </b> "
---



# Hệ thống Hiến máu & Cấp cứu Đại Việt (DaiVietBlood)

**Thực hiện bởi:** Skyline Team – Đại học FPT TP.HCM

**Ngày lập:** 07/12/2025

---

## MỤC LỤC

1. **BỐI CẢNH VÀ ĐỘNG LỰC**
   - 1.1 Tóm tắt điều hành
   - 1.2 Tiêu chí thành công của dự án
   - 1.3 Các giả định
2. **KIẾN TRÚC GIẢI PHÁP / SƠ ĐỒ KIẾN TRÚC**
   - 2.1 Sơ đồ kiến trúc kỹ thuật
   - 2.2 Kế hoạch kỹ thuật
   - 2.3 Kế hoạch dự án
   - 2.4 Các cân nhắc về bảo mật
3. **HOẠT ĐỘNG VÀ SẢN PHẨM BÀN GIAO**
   - 3.1 Hoạt động và Sản phẩm bàn giao
   - 3.2 Ngoài phạm vi dự án (Out of Scope)
   - 3.3 Lộ trình tiến lên Production
4. **BẢNG PHÂN TÍCH CHI PHÍ AWS DỰ KIẾN**
5. **ĐỘI NGŨ THỰC HIỆN**
6. **NGUỒN LỰC & ƯỚC TÍNH CHI PHÍ NHÂN SỰ**
7. **NGHIỆM THU**

---

## 1. BỐI CẢNH VÀ ĐỘNG LỰC

### 1.1 TÓM TẮT ĐIỀU HÀNH (EXECUTIVE SUMMARY)

**Bối cảnh Khách hàng:**
Hệ thống DaiVietBlood được thiết kế để phục vụ cộng đồng, bao gồm người hiến máu tình nguyện, bệnh nhân cần máu khẩn cấp và các chuyên gia y tế tại Việt Nam. Khách hàng chính của hệ thống là người hiến máu, gia đình bệnh nhân và nhân viên y tế chịu trách nhiệm quản lý kho máu và lịch hiến. Họ cần một nền tảng tập trung, tin cậy để tối ưu hóa quy trình khớp nối giữa người hiến và người nhận, cải thiện khả năng liên lạc trong các trường hợp khẩn cấp. Trong bối cảnh chuyển đổi số y tế, DaiVietBlood cung cấp giải pháp an toàn, dễ tiếp cận nhằm giải quyết tình trạng khan hiếm máu cục bộ.

**Mục tiêu Kinh doanh và Kỹ thuật:**
Việc di chuyển hệ thống DaiVietBlood từ môi trường cục bộ (Local/On-premise) lên AWS mang lại lợi thế vượt trội:

- **Về kinh doanh:** AWS cho phép ứng dụng mở rộng linh hoạt theo số lượng người dùng, giảm chi phí vận hành hạ tầng cứng và đảm bảo hiệu suất ổn định trên toàn quốc.
- **Về kỹ thuật:** AWS cung cấp độ sẵn sàng cao (High Availability) và bảo mật dữ liệu y tế. Việc áp dụng kiến trúc **Serverless** (AWS Lambda, API Gateway, Cognito, RDS) giúp đơn giản hóa quản lý backend, tăng tốc độ phát triển và giảm chi phí bảo trì. Hệ thống được tích hợp giám sát toàn diện (CloudWatch) và tuân thủ các tiêu chuẩn bảo mật nghiêm ngặt.

**Tóm tắt các Use Cases chính:**

| Vai trò                   | Chức năng chính                  | Mô tả ngắn                                                                                   |
| :------------------------ | :------------------------------- | :------------------------------------------------------------------------------------------- |
| **Khách (Guest)**         | Truy cập thông tin công khai     | Xem hướng dẫn hiến máu, bảng tương thích nhóm máu, bài viết giáo dục mà không cần đăng nhập. |
| **Thành viên (Member)**   | Đăng ký/Đăng nhập, Quản lý hồ sơ | Tạo tài khoản, cập nhật thông tin cá nhân và nhóm máu.                                       |
|                           | Đặt lịch hiến máu                | Chọn thời gian và địa điểm để hiến máu.                                                      |
|                           | Gửi yêu cầu cấp cứu              | Gửi yêu cầu máu khẩn cấp, hệ thống tự động tìm người hiến phù hợp.                           |
| **Nhân viên (Staff)**     | Quản lý yêu cầu & Kho máu        | Duyệt yêu cầu cấp cứu, xác nhận lịch hiến, cập nhật tồn kho máu.                             |
| **Quản trị viên (Admin)** | Quản trị hệ thống                | Quản lý tài khoản người dùng, cấu hình khung giờ hiến, xem báo cáo tổng quan.                |

**Tóm tắt Dịch vụ Chuyên nghiệp của Đối tác:**
Skyline Team sẽ cung cấp dịch vụ chuyển đổi số toàn diện, bao gồm đánh giá ứng dụng hiện tại, thiết kế kiến trúc Cloud-native, và thực hiện di chuyển (migration) hệ thống sang môi trường AWS Serverless. Chúng tôi cam kết bàn giao một hệ thống an toàn, có khả năng mở rộng, kèm theo quy trình CI/CD tự động hóa và tài liệu vận hành chi tiết.

### 1.2 TIÊU CHÍ THÀNH CÔNG CỦA DỰ ÁN

1.  **Chức năng:** 100% chức năng cốt lõi (đăng ký, đặt lịch, yêu cầu khẩn cấp, quản trị) hoạt động ổn định trên AWS, không phát sinh lỗi hồi quy.
2.  **Độ sẵn sàng:** Hệ thống đạt Uptime ≥ 99.9%, đảm bảo truy cập liên tục 24/7.
3.  **Hiệu năng:** Thời gian phản hồi ứng dụng cải thiện ít nhất 30% so với phiên bản chạy cục bộ. Thời gian xử lý yêu cầu cấp cứu giảm 40%.
4.  **Chi phí:** Tối ưu hóa chi phí hạ tầng ít nhất 20% nhờ mô hình Serverless và Auto-scaling.
5.  **Trải nghiệm người dùng:** Tỷ lệ chấp nhận UAT đạt tối thiểu 95% cho mọi vai trò người dùng.
6.  **Bảo mật:** Tuân thủ đầy đủ các yêu cầu về mã hóa dữ liệu, quản lý truy cập (IAM) và bảo mật API.
7.  **Vận hành:** Quy trình CI/CD tự động hóa hoàn toàn với thời gian deploy < 10 phút. Hệ thống giám sát (Monitoring) bao phủ 100% dịch vụ quan trọng.

### 1.3 CÁC GIẢ ĐỊNH

Dự án được triển khai dựa trên các giả định sau. Nếu có thay đổi, phạm vi và tiến độ có thể cần điều chỉnh:

**Giả định về Kỹ thuật & Kiến trúc:**

- **Mã nguồn:** Ứng dụng chạy Local hiện tại (Frontend & Backend) đã hoàn thiện về mặt logic chức năng. Dự án tập trung vào việc Refactoring (tái cấu trúc) để đưa lên Cloud (Serverless), không bao gồm phát triển tính năng mới.
- **AWS Region:** Toàn bộ hạ tầng được triển khai tại **Singapore (ap-southeast-1)** để tối ưu độ trễ cho người dùng Việt Nam. _Lưu ý: Trong giai đoạn thử nghiệm, do sử dụng cấu hình VPC và tài nguyên hạn chế của gói Free Tier/Student, độ trễ có thể dao động (ước tính ~3.5s/request)._
- **Hạn mức Dịch vụ (Service Limits):** Tài khoản AWS sử dụng hạn mức mặc định (Soft limits). Việc tăng hạn mức để giảm độ trễ sẽ do Khách hàng phê duyệt khi cần thiết.
- **Tích hợp bên thứ ba:** Hệ thống sử dụng API Gemini cho các tính năng AI hỗ trợ.
- **Quyền truy cập:** Nhóm Skyline được cấp quyền Admin (IAM Role) để khởi tạo tài nguyên.

**Giả định về Vận hành & Tài chính:**

- **Tên miền:** Khách hàng sở hữu tên miền (ví dụ: daivietblood.com) và quyền cấu hình DNS.
- **Chi phí:** Bảng ước tính chi phí dựa trên giả định lưu lượng khoảng 50.000 yêu cầu API/tháng. Chi phí thực tế phụ thuộc vào mức sử dụng (Pay-as-you-go).

---

## 2. KIẾN TRÚC GIẢI PHÁP / SƠ ĐỒ KIẾN TRÚC

### 2.1 SƠ ĐỒ KIẾN TRÚC KỸ THUẬT

Hệ thống DaiVietBlood sử dụng kiến trúc **Serverless-First** trên AWS Cloud, ưu tiên khả năng mở rộng, bảo mật và tối ưu vận hành.

**(Tham khảo hình ảnh sơ đồ kiến trúc đã cung cấp trong tài liệu gốc)**

**Các thành phần chính:**

1.  **Hạ tầng Mạng (VPC):**
    - _Public Subnet:_ Chứa Internet Gateway và NAT Gateway.
    - _Private Subnet:_ Chứa AWS Lambda và Amazon RDS để cô lập và bảo mật dữ liệu, ngăn chặn truy cập trực tiếp từ Internet.
2.  **Ứng dụng & Dữ liệu:**
    - _Frontend:_ Host trên **AWS Amplify**, phân phối qua **Amazon CloudFront** (CDN) và lưu trữ assets trên **S3**.
    - _Authentication:_ **Amazon Cognito** quản lý định danh và cấp phát JWT token.
    - _API & Compute:_ **Amazon API Gateway** tiếp nhận request, chuyển đến **AWS Lambda** để xử lý logic nghiệp vụ.
    - _Database:_ **Amazon RDS** lưu trữ dữ liệu có cấu trúc, đặt trong Private Subnet.
3.  **DevOps & Giám sát:**
    - _CI/CD:_ Sử dụng **AWS CodePipeline, CodeBuild, CodeDeploy** để tự động hóa quy trình triển khai.
    - _Monitoring:_ **Amazon CloudWatch** thu thập logs và metrics tập trung.

### 2.2 KẾ HOẠCH KỸ THUẬT

Quy trình triển khai kỹ thuật tuân theo phương pháp Infrastructure-as-Code (IaC):

- **Tự động hóa Hạ tầng:** Sử dụng **AWS CloudFormation** để khởi tạo VPC, Lambda, RDS, API Gateway, đảm bảo tính nhất quán giữa các môi trường (Dev/Staging/Prod).
- **Phát triển Ứng dụng:** Refactor backend thành các hàm Lambda module hóa (NodeJS/Python). Các biến môi trường và thông tin nhạy cảm (DB credentials) được mã hóa an toàn.
- **Quy trình CI/CD:**
  - Source (GitHub) -> Build (CodeBuild) -> Deploy (CloudFormation/CodeDeploy).
  - Bao gồm bước phê duyệt thủ công (Manual Approval) trước khi deploy ra môi trường Production.
- **Chiến lược Kiểm thử:** Unit Test cho Lambda, Integration Test cho API và Load Test để đảm bảo khả năng chịu tải.

### 2.3 KẾ HOẠCH DỰ ÁN

Dự án áp dụng mô hình Agile Scrum trong 8 tuần (4 Sprints):

- **Sprint 1 (Nền tảng):** Thiết lập AWS Account, VPC, RDS.
- **Sprint 2 (Backend Core):** Phát triển Lambda, API Gateway, Cognito.
- **Sprint 3 (Tích hợp):** Deploy Frontend (Amplify), hoàn thiện CI/CD Pipeline.
- **Sprint 4 (Ổn định hóa):** UAT, Tối ưu hiệu năng, Bàn giao.

### 2.4 CÁC CÂN NHẮC VỀ BẢO MẬT

1.  **Quản lý Truy cập:** Sử dụng Cognito cho xác thực người dùng và IAM Role cho phân quyền dịch vụ (Least Privilege).
2.  **Cô lập Mạng:** Database và Lambda nằm trong Private Subnet, chỉ truy cập Internet qua NAT Gateway.
3.  **Bảo vệ Dữ liệu:** Mã hóa dữ liệu khi lưu trữ (At-rest) trên RDS/S3 và khi truyền tải (In-transit) qua HTTPS.
4.  **Giám sát An ninh:** CloudWatch Logs ghi lại toàn bộ hoạt động để phục vụ kiểm tra (audit) và phát hiện xâm nhập.

---

## 3. HOẠT ĐỘNG VÀ SẢN PHẨM BÀN GIAO

### 3.1 HOẠT ĐỘNG VÀ SẢN PHẨM BÀN GIAO

| Giai đoạn                     | Thời gian | Hoạt động chính                                                              | Sản phẩm bàn giao                          | Ước tính (Man-day) |
| :---------------------------- | :-------- | :--------------------------------------------------------------------------- | :----------------------------------------- | :----------------- |
| **Phân tích & Thiết kế**      | Tuần 1    | Đánh giá hiện trạng Local, thiết kế kiến trúc Cloud, lên kế hoạch migration. | Tài liệu SRS, Sơ đồ kiến trúc, Đặc tả API. | 5                  |
| **Phát triển Local**          | Tuần 2-3  | Xây dựng logic backend, database schema, unit test cục bộ.                   | Prototype Backend, Database Schema.        | 10                 |
| **Frontend & Tích hợp**       | Tuần 4-5  | Phát triển Frontend, tích hợp API local, chuẩn bị code để refactor.          | Ứng dụng hoàn chỉnh chạy Local.            | 10                 |
| **Thiết lập Hạ tầng AWS**     | Tuần 6    | Viết script CloudFormation, khởi tạo VPC, RDS, IAM.                          | Template IaC, Môi trường VPC an toàn.      | 5                  |
| **Refactor & Deploy Backend** | Tuần 7-8  | Chuyển đổi sang Lambda, cấu hình API Gateway, Cognito.                       | Backend Serverless hoạt động trên AWS.     | 10                 |
| **Deploy Frontend & CI/CD**   | Tuần 9-10 | Host Frontend trên Amplify, thiết lập Pipeline tự động.                      | URL Production, CI/CD Pipeline.            | 10                 |
| **Testing & Go-live**         | Tuần 11   | UAT, Kiểm thử bảo mật, Tối ưu hiệu năng.                                     | Báo cáo UAT, Báo cáo bảo mật.              | 5                  |
| **Bàn giao & Đào tạo**        | Tuần 12   | Chuyển giao tài khoản, đào tạo vận hành, bàn giao tài liệu.                  | Tài liệu hướng dẫn vận hành, Nghiệm thu.   | 5                  |

### 3.2 NGOÀI PHẠM VI DỰ ÁN (OUT OF SCOPE) - GIAI ĐOẠN MVP

Do giới hạn về thời gian và tài nguyên của giai đoạn MVP, các hạng mục sau chưa được bao gồm:

- Thuật toán tìm kiếm người dùng tối ưu theo vị trí thực (Geo-location) (Hiện tại sử dụng logic đơn giản hóa).
- Khả năng tự động mở rộng (Auto-scaling) phức tạp cho tầng Database (Hiện tại dùng RDS cơ bản).
- Tối ưu hóa độ trễ chuyên sâu (Latency Optimization) cho các khu vực ngoài Singapore.
- Các tiêu chuẩn bảo mật nâng cao (Advanced Security Compliance) như HIPAA/PCI-DSS.

### 3.3 LỘ TRÌNH TIẾN LÊN PRODUCTION (PATH TO PRODUCTION)

Để nâng cấp từ bản MVP hiện tại lên hệ thống Production quy mô lớn, cần thực hiện:

1.  **Chiến lược Môi trường:** Tách biệt hoàn toàn môi trường Dev/Staging/Prod trên các tài khoản AWS khác nhau (Multi-account strategy).
2.  **Mở rộng Database:** Chuyển sang Amazon Aurora Serverless hoặc sử dụng Read Replicas để tăng khả năng chịu tải đọc/ghi.
3.  **Nâng cao Giám sát:** Tích hợp AWS X-Ray để truy vết (trace) request và phát hiện điểm nghẽn hiệu năng.
4.  **Tăng cường Bảo mật:** Triển khai AWS WAF với các tập luật chặn DDoS và bot tự động; sử dụng Amazon Inspector để quét lỗ hổng định kỳ.

---

## 4. BẢNG PHÂN TÍCH CHI PHÍ AWS DỰ KIẾN

_Khu vực: Asia Pacific (Singapore)_

| Hạng mục      | Dịch vụ      | Cấu hình ước tính                                             | Chi phí tháng (USD)  |
| :------------ | :----------- | :------------------------------------------------------------ | :------------------- |
| **Network**   | NAT Gateway  | 1 NAT Gateway (Bắt buộc cho Private Subnet) + Data Processing | ~$43.13              |
|               | VPC          | Subnets, Security Groups                                      | ~$13.14              |
|               | CloudFront   | 5GB Data Transfer (Tận dụng Free Tier)                        | ~$3.00               |
| **Compute**   | Lambda       | 1,000 requests, 512MB RAM (Free Tier)                         | ~$0.00               |
|               | API Gateway  | 1,000 requests                                                | ~$0.00               |
| **Database**  | RDS          | db.t3.micro, 20GB Storage                                     | ~$21.74              |
| **Storage**   | S3           | 5GB Storage, 200 requests                                     | ~$0.14               |
| **Hosting**   | Amplify      | Build & Hosting, WAF enabled                                  | ~$16.77              |
| **Ops**       | CloudWatch   | Logs, Metrics, Alarms                                         | ~$9.41               |
| **CI/CD**     | CodePipeline | 1 Active Pipeline                                             | ~$1.05               |
| **Tổng cộng** |              |                                                               | **~$108.38 / Tháng** |

_Lưu ý: Chi phí thực tế có thể thấp hơn nhờ gói Free Tier (12 tháng đầu) và AWS Credit cho sinh viên._

---

## 5. ĐỘI NGŨ THỰC HIỆN

- **Mentor (AWS FCJ):** Nguyễn Gia Hưng - Head of Solutions Architect.
- **Quản lý Dự án (PM):** Nguyễn Đức Lân - Điều phối, quản lý tiến độ, tối ưu chi phí và chiến lược UAT.
- **Technical Lead:** Nguyễn Công Minh - Phụ trách CI/CD, Infrastructure (CDK), Bảo mật và Lambda.
- **Solution Architect:** Đỗ Khang - Thiết kế kiến trúc Serverless, tích hợp AI Chatbot, Chính sách dịch vụ.
- **Fullstack Developer:** Lê Hoàng Anh - Phát triển API, UI/UX Frontend và bảo mật ứng dụng.
- **Data Engineer:** Nguyễn Quách Lam Giang - Quản trị RDS, thiết kế VPC/Subnet và giám sát CloudWatch.

---

## 6. NGUỒN LỰC & ƯỚC TÍNH CHI PHÍ NHÂN SỰ

**Phân bổ thời gian (Giờ công):**
Dự án huy động tổng cộng **750 giờ công** chia đều cho 5 thành viên qua các giai đoạn: Nền tảng, Phát triển Core, Phân tích dữ liệu, Kiểm thử và Bàn giao.

**Phân bổ chi phí:**

- **Nhân sự:** $0 (Do sinh viên thực hiện trong khuôn khổ thực tập/đồ án, tính vào tín chỉ học thuật).
- **Hạ tầng AWS:** ~$15 (Chi phí thực tế phát sinh trong quá trình dev/test sau khi trừ Credit).
- **Tổng chi phí dự án:** Rất tối ưu, chủ yếu dựa trên nguồn lực nội bộ và hỗ trợ từ chương trình AWS FCJ.

---

## 7. NGHIỆM THU

### 7.1 Bàn giao Sản phẩm:

Sau khi hoàn thành giai đoạn "Bàn giao", Skyline Team sẽ gửi toàn bộ Mã nguồn, Tài liệu Kiến trúc, Tài khoản Admin và Hướng dẫn vận hành cho Khách hàng/Mentor.

### 7.2 Thời gian & Quy trình Nghiệm thu:

Khách hàng có 05 ngày làm việc để kiểm tra và thực hiện UAT. Nếu sản phẩm đáp ứng Tiêu chí thành công (Mục 1.2), Khách hàng sẽ ký xác nhận nghiệm thu.

### 7.3 Xử lý Lỗi:

Nếu phát sinh lỗi nghiêm trọng hoặc thiếu tính năng so với phạm vi đã cam kết, Skyline Team có trách nhiệm khắc phục và gửi lại để nghiệm thu trong thời gian sớm nhất.
