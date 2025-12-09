---
title: "Event 4"
date: "2025-09-08"
weight: 1
chapter: false
pre: " <b> 4.4. </b> "
---



**Báo Cáo Tổng Kết: Tham dự AWS Cloud Mastery Series #1 - AI/ML/GenAI on AWS**

**Mục tiêu tham dự**
Vừa qua, tôi đã có cơ hội tham gia sự kiện mở màn cho chuỗi "AWS Cloud Mastery" với chủ đề tập trung vào AI, Machine Learning và Generative AI. Mục đích chính của tôi là cập nhật bức tranh toàn cảnh về các công nghệ này trên nền tảng AWS và tìm hiểu cách áp dụng chúng vào các bài toán thực tế của doanh nghiệp.

**Diễn giả**
Buổi chia sẻ có sự góp mặt của các chuyên gia giàu kinh nghiệm trong ngành, bao gồm anh Lâm Tuấn Kiệt (Sr DevOps Engineer - FPT Software), anh Danh Hoàng Hiếu Nghi (AI Engineer - Renova Cloud), bạn Đinh Lê Hoàng Anh (Cloud Engineer Trainee) và anh Văn Hoàng Kha (Community Builder).

**Những kiến thức đắt giá tôi đã thu hoạch được:**

**1. Sức mạnh của Generative AI trên Amazon Bedrock**
Đây là phần tôi thấy ấn tượng nhất. Amazon Bedrock đóng vai trò như một nền tảng trung tâm, cung cấp quyền truy cập vào các Foundation Models (FMs) hàng đầu từ Anthropic, OpenAI, Meta, v.v. Điều này giúp chúng ta không cần tốn công xây dựng model từ con số 0 mà có thể tinh chỉnh các model có sẵn này.

Tôi cũng đã củng cố thêm kỹ năng về **Prompt Engineering** (Kỹ thuật đặt lệnh), hiểu rõ hơn về cách điều hướng mô hình qua các chiến lược:

- _Zero-shot:_ Đưa yêu cầu trực tiếp mà không cần ví dụ.
- _Few-shot:_ Cung cấp một vài ví dụ mẫu để model học theo.
- _Chain-of-Thought:_ Yêu cầu model diễn giải từng bước tư duy để có kết quả logic hơn.

Đặc biệt, kỹ thuật **RAG (Retrieval Augmented Generation)** được nhấn mạnh như một giải pháp tối ưu để nâng cao độ chính xác:

- _Retrieval (Truy xuất):_ Lấy dữ liệu thực từ kho kiến thức doanh nghiệp.
- _Augmentation (Tăng cường):_ Đưa dữ liệu đó vào làm ngữ cảnh cho prompt.
- _Generation (Tạo sinh):_ Model trả lời dựa trên thông tin thực tế, giảm thiểu ảo giác (hallucination).

Bên cạnh đó là **Amazon Titan Embeddings**, công cụ giúp chuyển đổi văn bản thành các vector phục vụ cho việc tìm kiếm ngữ nghĩa và quy trình RAG đa ngôn ngữ.

**2. Hệ sinh thái AWS AI Services**
Ngoài GenAI, tôi cũng được ôn lại các dịch vụ AI "mì ăn liền" (API có sẵn) của AWS giúp giải quyết nhanh các bài toán cụ thể mà không cần training model phức tạp:

- Phân tích hình ảnh/video (Rekognition).
- Dịch thuật (Translate) và chuyển đổi giọng nói/văn bản (Transcribe, Polly).
- Bóc tách dữ liệu văn bản (Textract) và phân tích ngôn ngữ tự nhiên (Comprehend).
- Tìm kiếm thông minh (Kendra) hay phát hiện bất thường (Lookout).

Phần demo ứng dụng nhận diện khuôn mặt _AMZPhoto_ đã minh họa rất trực quan cách tích hợp các dịch vụ này vào sản phẩm thực tế.

**3. Amazon Bedrock AgentCore – Đưa AI Agent vào thực tiễn**
Đây là một framework mới giúp giải quyết bài toán vận hành AI Agent ở quy mô lớn (production-ready). Nó hỗ trợ quản lý bộ nhớ dài hạn, bảo mật danh tính, tích hợp công cụ (như trình duyệt, code interpreter) và quan trọng nhất là khả năng giám sát (observability). Điều này giúp việc triển khai các framework như CrewAI hay LangGraph trở nên an toàn và hiệu quả hơn trên nền tảng AWS.

**Kế hoạch áp dụng vào công việc**
Từ những gì đã học, tôi định hướng sẽ áp dụng ngay các kiến thức sau:

- **Triển khai RAG & AgentCore:** Đề xuất và áp dụng vào các dự án nội bộ sắp tới cần tính năng GenAI để tăng độ chính xác và khả năng tự động hóa.
- **Tối ưu quy trình phát triển:** Sử dụng các AWS AI Services có sẵn thay vì tự xây dựng để rút ngắn thời gian đưa sản phẩm ra thị trường (Time-to-market).
- **Cải thiện hiệu suất Model:** Áp dụng các kỹ thuật Prompt Engineering nâng cao để tối ưu hóa kết quả đầu ra cho các tác vụ AI hiện tại.

**Trải nghiệm bên lề**
Không chỉ được tiếp thu kiến thức mà không khí sự kiện cũng vô cùng sôi động. Tôi đã may mắn lọt vào **Top 6** cuộc thi Kahoot, tuy nhiên lại rớt khỏi bảng xếp hạng ở những câu cuối. Dù vậy, đây vẫn là một trò chơi thú vị giúp tôi củng cố lại kiến thức trong sự kiện lần này.
