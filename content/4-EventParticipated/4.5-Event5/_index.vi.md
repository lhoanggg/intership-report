---
title: "Event 5"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.5. </b> "
---



# Báo Cáo Tổng Kết: Tham dự AWS Cloud Mastery Series #2 – DevOps on AWS

### Mục tiêu tham dự

Tiếp nối chuỗi sự kiện, tôi đã tham dự buổi thứ hai với trọng tâm là **DevOps**. Mục tiêu của tôi là nắm vững các dịch vụ AWS hỗ trợ DevOps, hiểu sâu hơn về thiết kế quy trình CI/CD, các khái niệm Infrastructure as Code (IaC), cũng như cách triển khai và giám sát các ứng dụng container hóa trên nền tảng AWS.

### Diễn giả

Buổi chia sẻ quy tụ dàn diễn giả hùng hậu, bao gồm các AWS Community Builders và các kỹ sư giàu kinh nghiệm:

- Anh Trương Quang Tinh – Platform Engineer (TymeX)
- Anh Bảo Huỳnh, Nguyễn Khánh Phúc Thịnh, Trần Đại Vĩ, Huỳnh Hoàng Long, Phạm Hoàng Quý, Nghiêm Lê (Các AWS Community Builders)
- Bạn Đinh Lê Hoàng Anh – Cloud Engineer Trainee (First Cloud AI Journey)

### Những kiến thức đắt giá tôi đã thu hoạch được:

**1. Xây dựng nền tảng DevOps (DevOps Foundation)**
Các diễn giả đã giúp tôi định nghĩa lại rằng DevOps không phải là một chức danh công việc mà là một **tư duy và thói quen** làm việc. Cốt lõi nằm ở việc:

- Tự động hóa các tác vụ lặp đi lặp lại.
- Chia sẻ kiến thức giữa các team.
- Liên tục thử nghiệm và học hỏi.
- Đo lường hiệu quả bằng số liệu thực tế thay vì cảm tính.

Tôi cũng rút ra bài học về những sai lầm người mới hay gặp phải: tránh sa đà vào "tutorial hell" (chỉ xem hướng dẫn) mà không bắt tay vào làm dự án thật, và tập trung vào sự tiến bộ của bản thân thay vì so sánh với người khác.

**2. Thực chiến với Infrastructure as Code (IaC)**
Phần này giúp mở rộng tầm mắt của tôi về các công cụ IaC thay vì chỉ dính chặt lấy một công cụ duy nhất. Các diễn giả đã so sánh chi tiết:

- **CloudFormation:** Công cụ dạng template "chính chủ" của AWS.
- **AWS CDK:** Dành cho các lập trình viên thích viết hạ tầng bằng ngôn ngữ lập trình quen thuộc.
- **Terraform:** Lựa chọn tối ưu cho các team làm việc trên nền tảng đa đám mây (multi-cloud).

Thông điệp quan trọng nhất: Hạ tầng được xây dựng bằng Code (IaC) sẽ nhất quán, dễ bảo trì và an toàn hơn hẳn so với cấu hình thủ công (ClickOps).

**3. Container và các mô hình triển khai**
Nội dung đi từ cơ bản (Dockerfile, Image) đến các dịch vụ nâng cao trên AWS:

- **Amazon ECR:** Lưu trữ và quét bảo mật cho image.
- **Amazon ECS & EKS:** Hai lựa chọn điều phối container phổ biến. Việc so sánh giữa ECS (đơn giản, native) và EKS (mạnh mẽ với Kubernetes) giúp tôi biết khi nào nên dùng loại nào.
- **AWS App Runner:** Giải pháp triển khai nhanh gọn mà không cần lo lắng về quản lý cluster.

**4. Giám sát và Quan sát (Monitoring & Observability)**
Một hệ thống không thể thiếu khả năng giám sát. Tôi đã hiểu rõ hơn vai trò của:

- **Amazon CloudWatch:** Trung tâm của các chỉ số (metrics), nhật ký (logs) và cảnh báo (alarms).
- **AWS X-Ray:** Công cụ truy vết (tracing) giúp trực quan hóa luồng request và phát hiện điểm nghẽn (bottleneck).

Bài học cốt lõi: Các tính năng Observability phải được thiết kế **ngay từ đầu**, chứ không phải thêm vào sau khi hệ thống đã xây xong.

### Kế hoạch áp dụng vào công việc

Cụ thể, tôi dự định áp dụng kiến thức này vào **Dự án AI Chatbot** sắp tới của team:

- **Thiết lập CI/CD Pipeline:** Sử dụng **AWS CodePipeline** để tự động hóa toàn bộ quy trình từ Build, Test đến Deploy. Mục tiêu là đảm bảo mọi bản cập nhật code đều được kiểm thử và đẩy lên production một cách mượt mà.
- **Triển khai IaC:** Sử dụng **AWS CDK** để định nghĩa toàn bộ tài nguyên (Lambda, API Gateway, DynamoDB, S3, IAM...). Việc này giúp hệ thống dễ dàng tái sử dụng cho các môi trường khác nhau và mở rộng nhanh chóng khi cần.

Việc áp dụng quy trình này sẽ giúp dự án Chatbot phát triển nhanh hơn, giảm thiểu lỗi con người và giúp việc vận hành dễ dàng hơn.

**Trải nghiệm sự kiện**
Buổi chia sẻ đã mang lại cho tôi góc nhìn thực tế về cách các doanh nghiệp hiện đại triển khai DevOps trên AWS. Không chỉ dừng lại ở lý thuyết, những ví dụ thực tế từ diễn giả về CloudFormation, Terraform hay cách chọn EKS/ECS thực sự rất giá trị.

Ngoài kiến thức chuyên môn, đây cũng là dịp tốt để tôi kết nối với những người bạn cùng chí hướng và học hỏi những kinh nghiệm "xương máu" từ những người đi trước.
