# Module 14: Building Serverless Architectures and Microservices

## 🧭 Overview

- **Mục tiêu của module**: Giúp người học hiểu cách thiết kế hệ thống sử dụng kiến trúc **Serverless** (không máy chủ) và **Microservices** (vi dịch vụ) trên nền tảng AWS.
- **Những gì sẽ học**:
  - Khái niệm và lợi ích của **Thinking Serverless** (tư duy không máy chủ).
  - Đặc điểm của **Microservices** (kiến trúc vi dịch vụ).
  - Cách xây dựng giải pháp với **AWS Lambda**, **AWS Container services**, **AWS Step Functions** và **Amazon API Gateway**.
  - Áp dụng các nguyên tắc **AWS Well-Architected Framework** cho hệ thống Serverless.

---

## 📘 Phần 1: Thinking Serverless

### 🔑 Ý chính

**Thinking Serverless** là việc chuyển dịch tư duy từ quản lý cơ sở hạ tầng (máy chủ) sang tập trung hoàn toàn vào logic ứng dụng và giá trị kinh doanh. Thay vì lo lắng về việc cài đặt hay vá lỗi hệ điều hành, bạn sử dụng các dịch vụ mà AWS quản lý toàn bộ phần hạ tầng bên dưới.

### 🧠 Giải thích

Kiến trúc Serverless dựa trên 4 lợi ích cốt lõi:

- **No server management** (Không quản lý máy chủ): Bạn không cần cài đặt, bảo trì, hoặc cập nhật hệ điều hành (OS). AWS sẽ đảm nhận việc quản lý phần cứng và phần mềm nền tảng.
- **Continuous scaling** (Mở rộng liên tục): Hệ thống tự động co giãn theo lượng request (yêu cầu) thực tế, từ con số 0 đến hàng ngàn request mà không cần can thiệp thủ công.
- **Pay-for-value services** (Trả tiền theo giá trị sử dụng): Bạn chỉ trả tiền khi code thực sự chạy. Nếu không có request nào, chi phí sẽ bằng 0 (khác với việc thuê máy chủ chạy 24/7).
- **Built-in high availability** (Sẵn sàng cao tích hợp): Các dịch vụ Serverless mặc định có khả năng chống chịu lỗi và hoạt động trên nhiều **Availability Zones** (Vùng sẵn sàng).

### 📊 So sánh Operational Tasks (Nhiệm vụ vận hành)

| Nhiệm vụ                                                           | Server in a VPC (Máy chủ truyền thống) | Serverless (Không máy chủ) |
| :----------------------------------------------------------------- | :------------------------------------: | :------------------------: |
| **Configure an instance** (Cấu hình thực thể)                      |                   Có                   |         **Không**          |
| **Update OS** (Cập nhật hệ điều hành)                              |                   Có                   |         **Không**          |
| **Install app platform** (Cài đặt nền tảng ứng dụng)               |                   Có                   |         **Không**          |
| **Build and deploy apps** (Xây dựng và triển khai app)             |                   Có                   |             Có             |
| **Auto scaling & Load balancing** (Tự động co giãn & Cân bằng tải) |            Phải tự cấu hình            |    **Tự động tích hợp**    |
| **Monitor and maintain apps** (Giám sát và duy trì app)            |                   Có                   |             Có             |

### 📌 Ghi nhớ nhanh

- Serverless phù hợp nhất cho kiến trúc **Event-driven** (hướng sự kiện) và **Microservices**.
- Các dịch vụ tiêu biểu:
  - **Compute**: **AWS Lambda**, **AWS Fargate**.
  - **Storage**: **Amazon S3**, **Amazon EFS**.
  - **Data stores**: **Amazon DynamoDB**, **Amazon Aurora Serverless**.
  - **Application Integration**: **Amazon API Gateway**, **AWS Step Functions**, **Amazon SNS**, **Amazon SQS**.

### 💡 Ví dụ (nếu có)

```example
Một ứng dụng Web 3 lớp (Three-tier design) theo mô hình Serverless:
1. Web Tier: Amazon S3 (lưu trữ static website) + Amazon CloudFront (phân phối nội dung).
2. App Tier: Amazon API Gateway (nhận API request) + AWS Lambda (xử lý logic).
3. Data Tier: Amazon DynamoDB (lưu trữ dữ liệu NoSQL).
=> Toàn bộ hệ thống trên đều là Serverless, bạn không cần quản lý bất kỳ một máy chủ EC2 nào.
```

---

## 📘 Phần 2: Architecting serverless microservices (Thiết kế vi dịch vụ không máy chủ)

### 🔑 Ý chính

Kiến trúc **Microservices** (Vi dịch vụ) là cách tiếp cận xây dựng ứng dụng dưới dạng một tập hợp các dịch vụ nhỏ, độc lập. Khi kết hợp với **Serverless**, nhà phát triển có thể tập trung vào việc viết mã nguồn mà không cần lo lắng về việc quản lý cụ thể từng cụm máy chủ cho mỗi dịch vụ. 🏗️

### 🧠 Giải thích

Một **Microservice** đúng nghĩa cần có hai đặc tính cốt lõi:

- **Autonomous (Tự chủ)**:
  - Có thể phát triển và triển khai độc lập mà không ảnh hưởng đến các dịch vụ khác.
  - Khả năng **Scale** (Mở rộng) riêng biệt dựa trên nhu cầu của chính nó. 📈
  - Không chia sẻ mã nguồn (code) với các dịch vụ khác.
  - Giao tiếp qua các **Well-defined APIs** (Giao diện lập trình ứng dụng được định nghĩa rõ ràng).
- **Specialized (Chuyên biệt)**:
  - Tập trung giải quyết một bài toán kinh doanh duy nhất (Single business function). 🎯
  - Thuộc quyền quản lý của một nhóm phát triển nhỏ (đội ngũ có quyền tự do chọn công cụ công nghệ).
  - **Stateless** (Không lưu trạng thái): Giúp dịch vụ dễ dàng mở rộng và phục hồi.
  - Có **Own data store** (Kho lưu trữ dữ liệu riêng): Mỗi dịch vụ sở hữu cơ sở dữ liệu riêng, tránh phụ thuộc chéo.

**Lợi ích của Microservices**:

- **Team agility (Sự linh hoạt của đội ngũ)**: Các nhóm nhỏ làm việc nhanh hơn trên các phần việc riêng biệt.
- **Resilience (Khả năng phục hồi)**: Nếu một dịch vụ bị lỗi, các phần còn lại của ứng dụng vẫn có thể hoạt động. 🛡️
- **Technological freedom (Tự do công nghệ)**: Có thể chọn các ngôn ngữ lập trình hoặc Database khác nhau cho từng dịch vụ.
- **Reusable code (Mã nguồn có thể tái sử dụng)**: Một chức năng có thể được gọi bởi nhiều dịch vụ khác nhau.

### 📌 Ghi nhớ nhanh

Các mô hình **Serverless Patterns** phổ biến trên AWS cho Microservices:

1. **RESTful APIs**: Sử dụng **Amazon API Gateway** + **AWS Lambda** + **Serverless data store** (như DynamoDB). 🌐
2. **Containers**: Sử dụng **Amazon API Gateway** / **ALB** + **AWS Fargate** + **Serverless data store**. 📦
3. **Streaming**: Sử dụng **Amazon Kinesis** + **AWS Lambda** + **Serverless data store**. 🌊

### 💡 Ví dụ (nếu có)

```example
Chuyển đổi ứng dụng Monolithic (Nguyên khối) sang Microservices:

- Monolithic: Một ứng dụng duy nhất chứa cả quản lý Users, Topics và Messages. Lỗi ở Messages có thể làm sập toàn bộ ứng dụng.
- Microservices:
  + User service (Quản lý người dùng)
  + Topic service (Quản lý chủ đề)
  + Message service (Quản lý tin nhắn)
Mỗi dịch vụ này chạy trên Lambda hoặc Fargate riêng biệt và giao tiếp với nhau qua API.
```

---

## 📘 Phần 3: Building serverless architectures with AWS Lambda (Xây dựng kiến trúc không máy chủ với AWS Lambda)

### 🔑 Ý chính

**AWS Lambda** là dịch vụ tính toán cốt lõi của mô hình Serverless, cho phép bạn chạy mã nguồn (code) dưới dạng các hàm (**Functions**) mà không cần chuẩn bị hay quản lý máy chủ. Bạn chỉ cần tải mã nguồn lên, và Lambda sẽ đảm nhận toàn bộ việc thực thi, mở rộng và bảo trì. ⚡

### 🧠 Giải thích

Để sử dụng **AWS Lambda** hiệu quả, bạn cần nắm vững các thành phần và cách thức hoạt động sau:

- **Function Configuration (Cấu hình hàm)**:
  - **Runtime**: Chọn ngôn ngữ lập trình (Python, Node.js, Java...). 💻
  - **Memory**: Chỉ định lượng bộ nhớ RAM (từ 128 MB đến 10 GB). Khi tăng RAM, CPU cũng tăng theo tỷ lệ tương ứng.
  - **Timeout**: Thời gian chạy tối đa của một hàm là **15 phút**. ⏳
- **Lambda Invocation (Các kiểu gọi hàm)**:
  - **Synchronous (Đồng bộ)**: Hàm được gọi và Client chờ phản hồi ngay lập tức. Thường dùng với **Amazon API Gateway** hoặc **ALB**. 🔄
  - **Asynchronous (Bất đồng bộ)**: Sự kiện được đẩy vào hàng đợi internal của Lambda, Client không cần chờ kết quả. Thường dùng với **Amazon S3** (khi có file mới) hoặc **Amazon SNS**. 📨
  - **Event Source Mapping**: Lambda tự động lấy dữ liệu (polling) từ các dịch vụ như **Amazon SQS** hoặc **Amazon DynamoDB Streams** để xử lý. 📜
- **Lambda Layers (Lớp Lambda)**:
  - Cho phép đóng gói các thư viện, phụ thuộc (**Dependencies**) hoặc **Custom runtimes** để tái sử dụng cho nhiều hàm khác nhau trong cùng một Region. Điều này giúp giảm dung lượng gói triển khai chính. 📦
- **Networking (Mạng)**:
  - Mặc định, hàm Lambda chạy trong một VPC bảo mật do AWS quản lý.
  - Bạn có thể cấu hình hàm kết nối với **Customer VPC** của mình thông qua **Elastic Network Interface (ENI)** để truy cập các tài nguyên nội bộ như Database RDS. 🔒

### 📊 So sánh Operational Tasks (Nhiệm vụ vận hành)

| Nhiệm vụ vận hành                     | Server truyền thống          | AWS Lambda (Serverless)                  |
| :------------------------------------ | :--------------------------- | :--------------------------------------- |
| **Patching OS (Vá lỗi hệ điều hành)** | Có                           | **Không**                                |
| **Scale (Co giãn)**                   | Auto Scaling (Cần thiết lập) | **Tự động liên tục**                     |
| **Availability (Độ sẵn sàng)**        | Phải tự thiết kế đa vùng     | **Tích hợp sẵn (Fault tolerant)**        |
| **Cost (Chi phí)**                    | Trả theo thực thể (Instance) | **Trả theo số lần gọi & thời gian chạy** |

### 📌 Ghi nhớ nhanh

- **Event object**: Chứa dữ liệu đầu vào của sự kiện (ví dụ: thông tin về file vừa upload).
- **Context object**: Cung cấp thông tin về môi trường thực thi và thời gian còn lại trước khi timeout.
- **Firecracker**: Công nghệ ảo hóa siêu nhẹ (MicroVM) giúp Lambda khởi động cực nhanh và bảo mật cao. 🚀

### 💡 Ví dụ (nếu có)

```example
Quy trình xử lý ảnh tự động:
1. Người dùng tải ảnh lên Amazon S3.
2. S3 kích hoạt (Trigger) một hàm Lambda theo kiểu Asynchronous.
3. Hàm Lambda đọc ảnh, tạo Thumbnail (ảnh thu nhỏ) và lưu lại vào S3 hoặc DynamoDB.
=> Bạn không cần máy chủ chạy 24/7 chỉ để đợi ảnh, Lambda chỉ chạy và tính tiền khi có ảnh được upload.
```

---

## 📘 Phần 4: Building microservice applications with AWS container services (Xây dựng ứng dụng vi dịch vụ với các dịch vụ container của AWS)

### 🔑 Ý chính

Trong khi Lambda phù hợp cho các đoạn mã ngắn, **Containers** (Thùng chứa) cung cấp một môi trường chạy ứng dụng linh hoạt và mạnh mẽ hơn. AWS cung cấp các dịch vụ để lưu trữ, điều phối và chạy các container này một cách hiệu quả, giúp đơn giản hóa việc triển khai các kiến trúc **Microservices** phức tạp. 📦

### 🧠 Giải thích

Để xây dựng ứng dụng với container trên AWS, bạn cần hiểu rõ các lựa chọn về dịch vụ và hạ tầng:

- **Khi nào chọn Containers thay vì Lambda?**:
  - **Runtime duration**: Khi ứng dụng cần chạy lâu hơn **15 phút**. ⏳
  - **Memory intensive**: Khi ứng dụng cần nhiều hơn **10 GB RAM**. 🧠
  - **Legacy migration**: Khi cần di chuyển các ứng dụng cũ (on-premises) lên mây mà không muốn viết lại toàn bộ mã nguồn (**Refactoring**).
  - **Cost**: Khi ứng dụng chạy liên tục với cường độ cao, chạy container trên EC2 có thể có chi phí cố định tối ưu hơn Lambda. 💰
- **Hệ sinh thái dịch vụ Container của AWS**:
  - **Registry (Kho lưu trữ)**: **Amazon Elastic Container Registry (Amazon ECR)** - Nơi lưu trữ và quản lý các **Container images**.
  - **Orchestration (Điều phối)**: 🏗️
    - **Amazon Elastic Container Service (Amazon ECS)**: Dịch vụ điều phối mạnh mẽ, đơn giản, tích hợp sâu với các công cụ của AWS.
    - **Amazon Elastic Kubernetes Service (Amazon EKS)**: Dịch vụ điều phối sử dụng chuẩn **Kubernetes**, phù hợp cho các đội ngũ đã quen với hệ sinh thái K8s.
- **Compute Options (Lựa chọn tài nguyên tính toán)**:
  - **Amazon EC2**: Bạn quản lý các cụm máy chủ ảo để chạy container. Tối ưu về quyền kiểm soát.
  - **AWS Fargate**: Mô hình **Serverless for containers**. Bạn không cần quản lý máy chủ hay cụm máy chủ (No cluster management), chỉ cần định nghĩa tài nguyên và chạy. 🚀

### 📊 So sánh Lựa chọn Điều phối (Orchestration)

| Đặc điểm                     | Amazon ECS                   | Amazon EKS                         |
| :--------------------------- | :--------------------------- | :--------------------------------- |
| **Complexity (Độ phức tạp)** | Đơn giản, dễ tiếp cận        | Phức tạp, cần kiến thức chuyên sâu |
| **Toolset (Bộ công cụ)**     | Sử dụng AWS toolset          | Sử dụng Kubernetes toolset         |
| **Team experience**          | Phù hợp đội ngũ mới làm quen | Phù hợp đội ngũ đã biết Kubernetes |
| **Scaling (Co giãn)**        | Tự động dựa trên nhu cầu     | Cần cấu hình thủ công hơn          |

### 📌 Ghi nhớ nhanh

- **Dockerfile**: Tệp văn bản chứa các lệnh để xây dựng một **Container image**.
- **Task Definition (Định nghĩa nhiệm vụ)**: Bản thiết kế (blueprint) trong ECS mô tả container nào sẽ chạy, lượng CPU/RAM và các cổng kết nối. 📜
- **Pod**: Đơn vị nhỏ nhất trong EKS (Kubernetes) chứa một hoặc nhiều container.
- **Per-second billing**: Cách tính phí của **AWS Fargate**, chỉ trả tiền cho dung lượng thực tế sử dụng mỗi giây.

### 💡 Ví dụ (nếu có)

```example
Di chuyển một ứng dụng Node.js cũ (Monolithic):
1. Đóng gói ứng dụng vào một Docker image thông qua Dockerfile.
2. Đẩy image lên Amazon ECR.
3. Tạo một ECS Cluster sử dụng AWS Fargate để chạy image đó.
4. Sau này, tách dần các chức năng (như Payments, Inventory) thành các container riêng lẻ để tạo thành kiến trúc Microservices.
```

---

## 📘 Phần 5: Orchestrating microservices with AWS Step Functions (Điều phối vi dịch vụ với AWS Step Functions)

### 🔑 Ý chính

**AWS Step Functions** là một dịch vụ **Serverless orchestration** (điều phối không máy chủ) giúp quản lý các quy trình công việc (**Workflows**) giữa nhiều dịch vụ AWS khác nhau[cite: 688, 831]. Nó giải quyết bài toán phối hợp các **Microservices** vốn hoạt động độc lập và không lưu trạng thái thành một quy trình nghiệp vụ hoàn chỉnh[cite: 104, 678]. 🔄

### 🧠 Giải thích

Kiến trúc vi dịch vụ thường gặp khó khăn trong việc quản lý các phụ thuộc chéo hoặc xử lý lỗi khi các dịch vụ gọi nhau trực tiếp. **Step Functions** cung cấp giải pháp thông qua:

- **State Machines (Máy trạng thái)**: Một workflow bao gồm một chuỗi các bước được gọi là **States** (trạng thái) dựa trên sự kiện[cite: 689, 832]. 📋
- **Amazon States Language (Ngôn ngữ trạng thái Amazon)**: Sử dụng cấu trúc dựa trên JSON để định nghĩa các bước, logic rẽ nhánh và các tích hợp dịch vụ[cite: 765, 833]. ⚙️
- **Error Handling (Xử lý lỗi)**: Tự động quản lý việc **Retries** (thử lại) sau khi bị timeout hoặc gặp lỗi, cũng như hỗ trợ logic try-catch-finally để đảm bảo hệ thống bền bỉ[cite: 676, 691, 726]. 🛡️
- **State Management (Quản lý trạng thái)**: Duy trì trạng thái, trạm kiểm soát (**Checkpoints**) và khả năng khởi động lại các workflow bị gián đoạn[cite: 690].

### 📊 So sánh Workflow Types (Các loại quy trình công việc)

| Đặc điểm                     | Standard Workflows (Quy trình tiêu chuẩn)            | Express Workflows (Quy trình nhanh)                 |
| :--------------------------- | :--------------------------------------------------- | :-------------------------------------------------- |
| **Duration (Thời lượng)**    | Lên đến 1 năm (Long-running)[cite: 698].             | Tối đa 5 phút (Short-running)[cite: 698].           |
| **Processing (Xử lý)**       | **Asynchronous** (Bất đồng bộ)[cite: 698].           | Cả **Synchronous** và **Asynchronous**[cite: 698].  |
| **Run model (Mô hình chạy)** | **Exactly-once** (Chỉ chạy đúng một lần)[cite: 698]. | **At-least-once** hoặc **At-most-once**[cite: 698]. |
| **Pricing (Giá cả)**         | Tính theo số lần chuyển đổi trạng thái[cite: 698].   | Tính theo số yêu cầu và thời lượng chạy[cite: 698]. |

### 📌 Ghi nhớ nhanh

Các loại **States** (bước) phổ biến trong một workflow [cite: 743-761]:

- **Task state**: Thực hiện một công việc (thường là gọi một Lambda function hoặc dịch vụ AWS khác)[cite: 745, 835].
- **Choice state**: Kiểm tra điều kiện dữ liệu để quyết định bước tiếp theo (giống lệnh if-else)[cite: 753].
- **Parallel state**: Chạy nhiều nhánh workflow cùng một lúc[cite: 754].
- **Map state**: Chạy một tập hợp các bước cho mỗi mục trong một danh sách dữ liệu[cite: 756].
- **Wait state**: Tạm dừng workflow trong một khoảng thời gian nhất định[cite: 749].

### 💡 Ví dụ (nếu có)

```example
Quy trình xử lý đơn hàng (Order Processing):
1. Task: Gọi Lambda để kiểm tra tồn kho (Check Inventory).
2. Choice: Nếu còn hàng -> Bước 3; Nếu hết hàng -> Bước Fail.
3. Parallel:
   - Nhánh A: Gọi Lambda xử lý thanh toán (Payment).
   - Nhánh B: Gọi Lambda gửi email xác nhận cho khách (Notification).
4. Task: Gọi Lambda yêu cầu vận chuyển (Delivery).
5. Success: Hoàn tất đơn hàng.
=> Step Functions đảm bảo nếu thanh toán lỗi, nó sẽ tự động Retry hoặc chạy bước hoàn tiền (Rollback).
```

---

## 📘 Phần 6: Extending serverless architectures with Amazon API Gateway (Mở rộng kiến trúc không máy chủ với Amazon API Gateway)

### 🔑 Ý chính

**Amazon API Gateway** đóng vai trò là "cửa ngõ" bảo mật và quản lý cho các ứng dụng Serverless. Nó cho phép các nhà phát triển tạo, xuất bản, duy trì và bảo mật các **API** ở bất kỳ quy mô nào, kết nối người dùng với các dịch vụ backend như **AWS Lambda**, **Amazon DynamoDB**, hoặc bất kỳ ứng dụng web nào. 🚪

### 🧠 Giải thích

**Lợi ích cốt lõi của API Gateway**:

- **Standardize communication (Tiêu chuẩn hóa giao tiếp)**: Kết nối các ứng dụng viết bằng nhiều ngôn ngữ khác nhau một cách thống nhất, che giấu sự phức tạp của hệ thống backend. 🌐
- **Protect microservices (Bảo vệ vi dịch vụ)**:
  - **Authorization (Ủy quyền)**: Kiểm soát quyền truy cập tài nguyên.
  - **Throttling (Điều tiết lưu lượng)**: Giới hạn số lượng yêu cầu để ngăn chặn tấn công hoặc quá tải hệ thống.
  - **Request validation (Xác thực yêu cầu)**: Kiểm tra định dạng dữ liệu trước khi gửi đi.
- **Performance & Cost (Hiệu suất và Chi phí)**: Hỗ trợ **Caching** (Bộ nhớ đệm) các phản hồi để giảm độ trễ và giảm tải cho backend. ⚡
- **Monetization (Kiếm tiền)**: Theo dõi số lượng sử dụng của từng khách hàng để phục vụ mục đích tính phí.

### 📊 So sánh các loại API (API Types)

| Loại API          | Phù hợp nhất cho...            | Đặc điểm chính                                                                    |
| :---------------- | :----------------------------- | :-------------------------------------------------------------------------------- |
| **HTTP API**      | Các vi dịch vụ (Microservices) | Tối ưu về **Latency** (Độ trễ thấp) và **Cost** (Chi phí thấp).                   |
| **REST API**      | Quản lý API nâng cao           | Đầy đủ tính năng quản lý, chính sách tài nguyên, xác thực phức tạp.               |
| **WebSocket API** | Ứng dụng thời gian thực        | Thiết lập kết nối hai chiều, giữ trạng thái (**Stateful**) giữa client và server. |

### 📌 Ghi nhớ nhanh

- **Backend Integrations (Tích hợp phía sau)**: Có thể kết nối trực tiếp với các dịch vụ AWS (First-class integrations) như **Amazon SQS**, **Kinesis**, hoặc **Step Functions** mà không cần qua Lambda. 🔗
- **Stages (Giai đoạn)**: Dễ dàng triển khai và quản lý nhiều phiên bản API cho các môi trường khác nhau (Dev, Test, Prod).
- **VPC Link**: Cho phép API Gateway kết nối an toàn với các tài nguyên nằm trong **Customer VPC** (Mạng riêng ảo của khách hàng). 🔒

### 💡 Ví dụ (nếu có)

```example
Hệ thống đặt món ăn trực tuyến:
1. Amazon API Gateway (HTTP API): Nhận yêu cầu "Đặt món" từ điện thoại khách hàng.
2. Nó kiểm tra tính hợp lệ của gói tin và giới hạn tốc độ (Throttling) để tránh spam.
3. API Gateway gọi trực tiếp AWS Step Functions để bắt đầu quy trình chế biến và giao hàng.
4. Thông tin đơn hàng được tự động đẩy vào Amazon DynamoDB để lưu trữ mà không cần viết code trung gian.
```
