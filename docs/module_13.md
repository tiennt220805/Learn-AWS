# Module 13: Building Decoupled Architectures (Xây dựng kiến trúc phi tập trung)

## 🧭 Overview

- **Mục tiêu:** Giúp kiến trúc hệ thống trở nên linh hoạt, dễ dàng mở rộng và tăng khả năng chịu lỗi. 🛡️
- **Những gì sẽ học:**
  - Phân biệt **Tightly coupled** (Liên kết chặt chẽ) và **Loosely coupled** (Liên kết lỏng lẻo).
  - Cách hoạt động và khi nào nên dùng **Amazon SQS**, **Amazon SNS** và **Amazon MQ**.
  - Phương pháp sử dụng các dịch vụ trung gian để phân tách các thành phần trong hệ thống.

---

## 📘 Phần 1: Decoupling your architecture (Phân tách kiến trúc của bạn)

### 🔑 Ý chính

- **Tightly coupled** (Liên kết chặt chẽ): Các thành phần phụ thuộc trực tiếp vào nhau. 🔗
- **Loosely coupled** (Liên kết lỏng lẻo): Loại bỏ sự phụ thuộc trực tiếp bằng cách thêm các thành phần trung gian. ⛓️‍💥
- **Synchronous** (Đồng bộ): Giao tiếp gửi và đợi phản hồi ngay lập tức.
- **Asynchronous** (Bất đồng bộ): Giao tiếp không cần phản hồi ngay, cho phép các thành phần hoạt động độc lập.

### 🧠 Giải thích

- **Tightly coupled architecture** (Kiến trúc liên kết chặt chẽ):
  - Giao tiếp thường là **Synchronous** (Đồng bộ).
  - **Thách thức:** Khó mở rộng vì việc thêm server mới có thể yêu cầu cập nhật mã nguồn (**Code updates**).
  - **Rủi ro:** Tạo ra **Single point of failure** (Điểm lỗi duy nhất) – nếu một server ứng dụng bị sập, toàn bộ hệ thống sẽ ngừng hoạt động. ⚠️

- **Loosely coupled architecture** (Kiến trúc liên kết lỏng lẻo):
  - Sử dụng các giải pháp như **Load Balancer** hoặc **Message Queue** để làm trung gian.
  - **Lợi ích:**
    - **Scalability** (Khả năng mở rộng): Dễ dàng thêm bớt tài nguyên mà không ảnh hưởng đến các phần khác. 📈
    - **Resiliency** (Khả năng phục hồi): Một thành phần lỗi không làm sập toàn bộ ứng dụng. 🏥
    - **Maintenance** (Bảo trì): Có thể thay đổi một phần của ứng dụng mà không gây gián đoạn (**Downtime**).

- **So sánh giải pháp:**

| Đặc điểm           | Synchronous (Đồng bộ)                  | Asynchronous (Bất đồng bộ)       |
| :----------------- | :------------------------------------- | :------------------------------- |
| **Cơ chế**         | Gửi và đợi phản hồi.                   | Gửi và tiếp tục xử lý việc khác. |
| **Ví dụ hạ tầng**  | **ELB** (Elastic Load Balancing).      | **Amazon SQS**, **Amazon MQ**.   |
| **Ví dụ ứng dụng** | **Microservices** giao tiếp trực tiếp. | **Amazon SNS** (Topic based).    |

### 📌 Ghi nhớ nhanh

- **Tightly coupled** = Phụ thuộc nhiều + Khó Scale + Dễ sập toàn bộ. ❌
- **Loosely coupled** = Độc lập + Dễ Scale + Chịu lỗi tốt. ✅
- Muốn hệ thống bền bỉ, hãy luôn tìm cách **Decouple** (Phân tách) các thành phần.

### 💡 Ví dụ (nếu có)

```example
Một ứng dụng Web ba lớp (Three-tier architecture):
- Tightly coupled: Web server kết nối trực tiếp bằng địa chỉ IP của App server. Nếu App server thay đổi, Web server phải sửa code.
- Loosely coupled: Web server kết nối tới một Load Balancer (ALB). ALB sẽ tự điều phối traffic đến các App server phía sau. Web server không cần biết có bao nhiêu App server đang chạy.
```

---

## 📘 Phần 2: Decoupling applications with Amazon SQS (Phân tách ứng dụng với Amazon SQS)

### 🔑 Ý chính

- **Amazon SQS (Simple Queue Service)**: Dịch vụ hàng đợi tin nhắn được quản lý hoàn toàn (**Fully managed**), giúp phân tách và tích hợp các hệ thống phân tán. 📩
- **Point-to-point messaging** (Nhắn tin điểm-đến-điểm): Mô hình gửi tin nhắn từ một ứng dụng gửi (**Producer**) đến duy nhất một ứng dụng nhận (**Consumer**) thông qua hàng đợi.
- **Message queue** (Hàng đợi tin nhắn): Thành phần trung gian lưu trữ tin nhắn cho đến khi chúng được xử lý và xóa. 📥

### 🧠 Giải thích

- **Thành phần cơ bản**:
  - **Message** (Tin nhắn): Kích thước tối đa **256 KB**. Tin nhắn ở lại trong hàng đợi cho đến khi được xóa hoặc hết hạn.
  - **Dead-letter queue - DLQ** (Hàng đợi tin nhắn chết): Nơi lưu trữ các tin nhắn không thể xử lý thành công sau nhiều lần thử. 🚩
  - **Visibility timeout** (Thời gian hiển thị tạm ẩn): Khoảng thời gian tin nhắn bị "ẩn" khỏi các Consumer khác sau khi một Consumer đã lấy nó để xử lý.

- **So sánh các loại Queue**:

| Đặc điểm      | Standard Queue (Hàng đợi tiêu chuẩn)                           | FIFO Queue (First-In-First-Out)                                                      |
| :------------ | :------------------------------------------------------------- | :----------------------------------------------------------------------------------- |
| **Thứ tự**    | **Best-effort ordering** (Nỗ lực tối đa, không đảm bảo 100%).  | **First-In-First-Out** (Đảm bảo đúng thứ tự gửi vào).                                |
| **Phân phối** | **At-least-once delivery** (Ít nhất một lần, có thể bị trùng). | **Exactly-once processing** (Xử lý chính xác một lần, không trùng).                  |
| **Hiệu năng** | Gần như không giới hạn (**Nearly unlimited throughput**).      | Giới hạn số lượng giao dịch trên giây (**High throughput** nhưng thấp hơn Standard). |

- **Cơ chế Polling (Truy vấn tin nhắn)**:
  - **Short polling** (Truy vấn ngắn): Trả về phản hồi ngay lập tức ngay cả khi hàng đợi trống (dễ dẫn đến phản hồi trống, tốn phí). ⏱️
  - **Long polling** (Truy vấn dài): Đợi tin nhắn xuất hiện trong một khoảng thời gian nhất định (**Wait time > 0**) trước khi phản hồi (giúp giảm chi phí và số lượng phản hồi trống). ⏳

### 📌 Ghi nhớ nhanh

- **Producer** -> **SQS Queue** -> **Consumer**.
- Dùng **Standard Queue** khi cần tốc độ cực cao và không quan trọng thứ tự. 🚀
- Dùng **FIFO Queue** khi thứ tự tin nhắn là yếu tố sống còn (Ví dụ: giao dịch ngân hàng). 🔢
- **Long polling** là cách tốt nhất để tiết kiệm chi phí. 💰

### 💡 Ví dụ (nếu có)

```example
Hệ thống xử lý đơn hàng (Order Processing):
1. Producer (Ứng dụng Capture Order): Nhận đơn hàng từ khách và đẩy thông tin vào SQS Queue.
2. SQS Queue: Giữ các đơn hàng an toàn, ngay cả khi hệ thống phía sau bị quá tải.
3. Consumer (Ứng dụng Fulfillment): Lấy từng đơn hàng từ Queue để đóng gói và vận chuyển.
=> Nếu ứng dụng vận chuyển bị chậm, đơn hàng vẫn nằm an toàn trong SQS mà không bị mất.
```

---

## 📘 Phần 3: Decoupling applications with Amazon SNS (Phân tách ứng dụng với Amazon SNS)

### 🔑 Ý chính

- **Amazon SNS (Simple Notification Service)**: Dịch vụ tin nhắn theo mô hình **Publish/Subscribe (Pub/Sub)** được quản lý hoàn toàn. 📢
- **Publisher** (Người xuất bản): Ứng dụng gửi tin nhắn đến một **Topic**.
- **Subscriber** (Người đăng ký): Các ứng dụng hoặc đầu cuối nhận tin nhắn từ **Topic**.
- **Topic** (Chủ đề): Thành phần trung gian dùng để phân tách nhà cung cấp tin nhắn và người nhận. 📍
- **Fanout** (Xòe quạt): Cơ chế một tin nhắn gửi đến Topic được đẩy đi đồng thời cho nhiều Subscriber khác nhau. 🪭

### 🧠 Giải thích

- **Cơ chế hoạt động**:
  - Sử dụng cơ chế **Push mechanism** (Cơ chế đẩy): SNS chủ động đẩy tin nhắn đến các Subscriber ngay khi có tin mới (giao tiếp thụ động đối với người nhận).
  - **Standard Topic**: Sử dụng khi thứ tự tin nhắn không quá quan trọng, hỗ trợ số lượng tin nhắn cực lớn.
  - **FIFO Topic**: Sử dụng khi yêu cầu thứ tự tin nhắn chính xác và xử lý duy nhất một lần (**Exactly-once processing**).
- **Các loại Subscriber phổ biến**:
  - **SQS queue**: Cho phép lưu trữ tin nhắn để xử lý sau (mô hình phổ biến nhất).
  - **AWS Lambda**: Kích hoạt các đoạn mã xử lý sự kiện tự động.
  - **HTTP/HTTPS endpoint**: Gửi dữ liệu đến các ứng dụng web bên ngoài.
  - **Email/SMS/Mobile Push**: Thông báo trực tiếp đến thiết bị người dùng. 📱
- **So sánh SNS và SQS**:

| Đặc điểm                  | Amazon SNS                                                   | Amazon SQS                                              |
| :------------------------ | :----------------------------------------------------------- | :------------------------------------------------------ |
| **Mô hình**               | **Publisher-Subscriber** (Một-nhiều).                        | **Producer-Consumer** (Một-một).                        |
| **Cơ chế nhận**           | **Push (Passive)**: Hệ thống đẩy tin đi.                     | **Pull (Active)**: Ứng dụng tự lấy tin.                 |
| **Lưu trữ (Persistence)** | **No**: Tin nhắn không được lưu lại nếu không có người nhận. | **Yes**: Tin nhắn được lưu trữ bền vững trong hàng đợi. |
| **Khả năng Fanout**       | Có (Gửi một tin cho nhiều người nhận).                       | Không (Một tin thường chỉ cho một người nhận).          |

### 📌 Ghi nhớ nhanh

- **SNS** = Giao hàng tận nhà (**Push**). 🚚
- **SQS** = Đến kho lấy hàng (**Pull**). 🏭
- Muốn gửi 1 tin nhắn cho nhiều hệ thống khác nhau cùng lúc -> Dùng **SNS Topic**.
- SNS không lưu trữ tin nhắn; nếu không có Subscriber nào tại thời điểm gửi, tin nhắn sẽ bị hủy. 💨

### 💡 Ví dụ (nếu có)

```example
Hệ thống xử lý đơn hàng thương mại điện tử:
1. Khách hàng thanh toán thành công, ứng dụng gửi tin nhắn vào SNS Topic "OrderSuccess".
2. SNS thực hiện Fanout đến các Subscriber:
   - SQS Queue 1: Đẩy dữ liệu cho bộ phận đóng gói hàng.
   - SQS Queue 2: Đẩy dữ liệu cho bộ phận cập nhật điểm thưởng thành viên.
   - Email Service: Gửi hóa đơn điện tử cho khách hàng ngay lập tức.
```

---

## 📘 Phần 4: Decoupling a hybrid application with Amazon MQ (Phân tách ứng dụng lai với Amazon MQ)

### 🔑 Ý chính

- **Amazon MQ**: Dịch vụ môi giới tin nhắn (**Message broker**) được quản lý hoàn toàn dành cho **Apache ActiveMQ** và **RabbitMQ**. 🔄
- **Open standards** (Tiêu chuẩn mở): Hỗ trợ các API và giao thức nhắn tin tiêu chuẩn ngành như **JMS**, **AMQP**, **STOMP**, **MQTT**, và **WebSocket**.
- **Hybrid Cloud** (Đám mây lai): Là lựa chọn hàng đầu để kết nối các hệ thống tại chỗ (**On-premises**) với đám mây AWS. 🏢☁️

### 🧠 Giải thích

- **Đặc điểm nổi bật**:
  - Cung cấp cả hai mô hình: **Queue-based** (Hàng đợi) và **Topic-based** (Chủ đề).
  - Giúp giảm bớt gánh nặng quản lý, vận hành và thiết lập các bộ môi giới tin nhắn phức tạp.
- **Khi nào nên sử dụng**:
  - **Migration** (Di cư): Khi bạn muốn chuyển một ứng dụng đang sử dụng các message broker có sẵn từ trung tâm dữ liệu lên Cloud mà không cần viết lại mã nguồn. 🚚
  - **Interoperability** (Khả năng tương tác): Cần giao tiếp giữa các thành phần sử dụng các ngôn ngữ lập trình và hệ điều hành khác nhau qua giao thức chính thống.
- **So sánh lựa chọn giải pháp**:

| Đặc điểm             | Amazon SQS / SNS                             | Amazon MQ                                         |
| :------------------- | :------------------------------------------- | :------------------------------------------------ |
| **Ưu tiên**          | **Cloud-native** (Xây dựng mới trên Cloud).  | **Migration** (Di chuyển hệ thống cũ).            |
| **Giao thức**        | AWS API riêng.                               | Tiêu chuẩn mở (JMS, AMQP, v.v.).                  |
| **Khả năng mở rộng** | Gần như vô hạn và tự động.                   | Theo kích thước instance được chọn.               |
| **Mô hình giá**      | Trả theo lượt yêu cầu (**Pay per request**). | Trả theo giờ và dung lượng (**Pay per hour/GB**). |

### 📌 Ghi nhớ nhanh

- **Amazon MQ** = Giải pháp lý tưởng cho ứng dụng **Hybrid** (Lai) và **Migration** (Di chuyển hệ thống).
- Hỗ trợ đầy đủ **ActiveMQ** và **RabbitMQ**. 🐰
- Sử dụng khi ứng dụng yêu cầu các giao thức tin nhắn truyền thống mà SQS/SNS không hỗ trợ.

### 💡 Ví dụ (nếu có)

```example
Một doanh nghiệp sản xuất có máy chủ tại nhà máy (Corporate data center) đang chạy ứng dụng Producer để gửi thông tin hình ảnh.
- Họ thiết lập một Amazon MQ broker trên AWS Cloud.
- Ứng dụng tại nhà máy gửi tin nhắn qua giao thức tiêu chuẩn đến Amazon MQ.
- Ứng dụng xử lý trên AWS (Consumer) sẽ lấy tin nhắn từ broker này để thực hiện xử lý hoặc lưu trữ vào Amazon EFS.
=> Giúp kết nối hai môi trường khác nhau một cách mượt mà.
```
