---
title: "Blog 1"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# Giám sát sức khỏe server với Amazon GameLift Servers

**Tác giả:** Brian Schuster | **Ngày:** 20 NOV 2025 | **Chuyên mục:** Amazon GameLift, Amazon Managed Grafana, Phát triển game, Game, Công cụ quản lý

Chạy một trò chơi đa người chơi thành công đồng nghĩa với việc bạn phải liên tục cân bằng hiệu suất, khả năng mở rộng và trải nghiệm người chơi. Khi lượng người chơi tăng, những thách thức mới xuất hiện. Nguyên nhân có thể đến từ nhiều vấn đề khác nhau: có thể là trong mã server của bạn. Có thể là rò rỉ bộ nhớ, logic không hiệu quả, hoặc một lỗi chỉ xuất hiện trong điều kiện cụ thể.

Ngoài ra, việc tối ưu độ trễ bằng Amazon GameLift Servers cũng quan trọng, bằng cách phân bổ chiến lược năng lực qua các khu vực, đảm bảo người chơi kết nối với server gần họ nhất. Tuy nhiên, ngay cả khi đã tối ưu độ trễ, bạn vẫn có thể nhận được phản hồi về hiệu suất kém hoặc trải nghiệm chơi giảm sút từ một số người chơi.

Chúng tôi đã đề cập trước đó cách chẩn đoán và giải quyết các vấn đề mạng rộng, nhưng các chỉ số độ trễ đơn thuần sẽ không cho bạn toàn bộ bức tranh. Bạn cần có cái nhìn sâu hơn về những gì đang xảy ra trên server game của bạn.

### Quan sát phía server: vượt ra ngoài độ trễ

Khi sử dụng Amazon GameLift Servers với telemetry được bật, server game của bạn sẽ thu thập nhiều chỉ số chi tiết — bao gồm sử dụng tài nguyên (CPU, bộ nhớ, mạng), các chỉ số game-specific (như tick rate), và các chỉ số tùy chỉnh bạn định nghĩa. Các chỉ số này được ghi lại với các cấp độ khác nhau để bạn có thể phân tích hiệu suất ở mức process, container, host, hoặc toàn bộ fleet. Chúng có thể được đẩy vào hệ thống lưu trữ chỉ số của bạn và hiển thị bằng các công cụ bạn chọn.

Chúng tôi sẽ minh họa sử dụng **Amazon Managed Grafana** với các dashboard dựng sẵn. Việc thiết lập được tối ưu và có thể hoàn tất trong chưa đến một giờ.

Sức mạnh thực sự không chỉ nằm ở các chỉ số, mà còn ở cách các dashboard giúp bạn nhanh chóng xác định và chẩn đoán vấn đề.

---

### Khắc phục sự cố server game bị crash

Tình huống phổ biến: phiên game bị crash, người chơi bị ngắt kết nối giữa trận, bạn cần tìm nguyên nhân.

Nếu bạn chưa thiết lập dashboard giám sát, hãy làm theo hướng dẫn **Configure Amazon Grafana** — mất vài phút để provision dashboard dựng sẵn cho fleet. Lưu ý chúng tôi đang theo các bước SDK C++, điều chỉnh cho tùy chọn triển khai của bạn.

Sau khi cấu hình xong, mở Amazon Managed Grafana từ AWS Management Console và đi đến **EC2 Fleet Overview dashboard**. Hình 1 hiển thị sự kiện crash trong biểu đồ Game Server Crashes, trong khi phần **Crashed Game Sessions** hiển thị chi tiết phiên game bị crash. Cả instance và phiên game bị crash đều có liên kết, giúp điều tra sâu hơn.

![EC2 Fleet Overview dashboard showing a crashed Game Session.](/images/3-BlogsTranslated/1.png)
**Hình 1:** Dashboard EC2 Fleet Overview hiển thị phiên game bị crash.

Chọn instance bị ảnh hưởng. Biểu đồ bộ nhớ (Hình 2) cho thấy bộ nhớ tăng đột ngột, rồi giảm khi process crash — đặc trưng của rò rỉ bộ nhớ. Xem chi tiết theo phiên game, bạn sẽ thấy một phiên sử dụng bộ nhớ nhiều hơn đáng kể.

![Instance Performance dashboard showing memory leak.](/images/3-BlogsTranslated/2.png)
**Hình 2:** Dashboard Instance Performance hiển thị rò rỉ bộ nhớ.

Nhấn vào phiên game bị crash để xem **Server Performance dashboard** (Hình 3), hiển thị mức tiêu thụ tài nguyên của phiên đến lúc crash. Dashboard cho thấy phiên game này là nguyên nhân rò rỉ bộ nhớ.

![Server Performance dashboard showing memory leak.](/images/3-BlogsTranslated/3.png)
**Hình 3:** Dashboard Server Performance hiển thị rò rỉ bộ nhớ.

Mỗi biểu đồ đều có tooltip hướng dẫn cách đọc và giải thích dữ liệu. Bước tiếp theo rõ ràng: kiểm tra logs của phiên game bị crash để xác định nguyên nhân, có thể do chế độ chơi cụ thể hoặc hành động của người chơi. Các chỉ số giúp bạn xác định logs cần kiểm tra.

---

### Khắc phục tình trạng CPU cao

Tình huống khác: người chơi phản ánh game bị giật lag nhưng không crash. Nguyên nhân có thể liên quan CPU.

Chuyển đến **EC2 Instances Overview dashboard** trong Amazon Managed Grafana. Hình 4 hiển thị 20 instance EC2 tiêu thụ CPU cao nhất. Phần lớn giữ ở 2–3% CPU, nhưng vài instance lên 20–30% hoặc cao hơn.

![EC2 Instances Overview dashboard.](/images/3-BlogsTranslated/4.png)
**Hình 4:** Dashboard EC2 Instances Overview

Chọn một instance CPU cao. Dashboard phân tách CPU theo phiên game (Hình 5), chỉ ra ngay phiên nào tiêu thụ nhiều tài nguyên nhất. Bạn có thể xem logs phiên game đó, tập trung vào thời điểm CPU tăng cao.

![Instance Performance dashboard showing top CPU consuming game sessions.](/images/3-BlogsTranslated/5.png)
**Hình 5:** Dashboard Instance Performance hiển thị các phiên game tiêu thụ CPU cao.

Có thể bạn nhận thấy CPU cao liên quan đến kịch bản chiến đấu căng thẳng, hoặc lỗi pathfinding khiến tính toán quá mức. Chỉ số không nói chính xác lỗi, nhưng chỉ ra nơi cần kiểm tra.

---

### Hỗ trợ container

Nếu chạy fleet GameLift Servers bằng container thay vì EC2, cách khắc phục tương tự áp dụng. Hình 6 là **Container Fleet Overview dashboard**, hiển thị task tiêu thụ CPU hoặc memory cao nhất.

![Container Fleet Overview dashboard.](/images/3-BlogsTranslated/6.png)
**Hình 6:** Container Fleet Overview dashboard

Nhấn vào task cụ thể, dashboard **Container Performance** (Hình 7) phân tách theo container. Bạn có thể xem container server game có tiêu thụ đúng hay không, hoặc container phụ gây vấn đề. Chi tiết này giúp nhanh chóng cô lập vấn đề, dù chạy EC2 hay container.

![Container Performance dashboard.](/images/3-BlogsTranslated/7.png)
**Hình 7:** Container Performance dashboard

---

### Bước tiếp theo

Ngoài các chỉ số phần cứng và game cơ bản (tick rate, crashes), bạn có thể mở rộng giám sát bằng **custom metrics**.

Ví dụ các chỉ số game-specific:

- **Cân bằng chiến đấu:** Thời gian giết trung bình hoặc DPS theo loại vũ khí, phát hiện patch làm vũ khí quá mạnh.  
- **Khóa tiến trình:** Tỷ lệ thành công nhiệm vụ quan trọng hoặc boss để phát hiện lỗi ngăn người chơi tiến triển.  
- **Sức khỏe kinh tế:** Lạm phát tiền tệ hoặc pattern nhận vật phẩm để phát hiện exploit.  
- **Thời gian pathfinding AI:** Thời gian tính đường NPC để phát hiện kịch bản phức tạp gây lag.

Sau khi thiết lập custom metrics, tạo alert trong Amazon Managed Grafana cho cả hệ thống và game-specific. Ví dụ:

- Memory > 90%  
- CPU > 85% kéo dài  
- Tăng phiên crash  
- Spike trong custom metrics (như thất bại boss)

Khi alert kích hoạt, nhấp vào dashboard liên quan và bắt đầu điều tra — phát hiện lỗi trước khi người chơi phản ánh.

---

### Kết luận

Tối ưu độ trễ giúp kết nối người chơi đến vị trí đúng, nhưng **server-side observability** đảm bảo trải nghiệm mượt mà. Telemetry của GameLift Servers với dashboard dựng sẵn giúp chẩn đoán crash, bottleneck, và vấn đề tài nguyên nhanh chóng — mà không bị ngập trong raw metrics.

Lần tới khi người chơi phàn nàn lỗi game, bạn sẽ biết chính xác nơi cần kiểm tra, và với alert chủ động, bạn sẽ bắt lỗi trước khi họ nhận ra.

**Liên hệ AWS Representative** để biết cách tăng tốc doanh nghiệp của bạn.

---

**Đọc thêm:**

- [Getting started with Amazon GameLift Servers](https://aws.amazon.com/gamelift/getting-started/)  
- [Setting up alerts in Amazon Managed Grafana](https://docs.aws.amazon.com/grafana/latest/userguide/AMG.html)  
- [Available Amazon GameLift Servers dashboards and metrics](https://docs.aws.amazon.com/gamelift/latest/developerguide/metrics-dashboards.html)  

**Tác giả:** Brian Schuster, Principal Engineer tại AWS cho Amazon GameLift.