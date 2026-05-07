# Module 10: Implementing Monitoring, Elasticity, and High Availability

## 🧭 Overview

- Module này tập trung vào việc thiết kế các hệ thống có khả năng tự phục hồi và mở rộng trên AWS.
- Mục tiêu chính:
  - Tìm hiểu cách sử dụng **Amazon CloudWatch** và **Amazon EventBridge** để giám sát và phản ứng với sự kiện.
  - Triển khai **Amazon EC2 Auto Scaling** để đạt được tính đàn hồi (**Elasticity**).
  - Hiểu các chiến lược cân bằng tải (**Load Balancing**) và điều phối DNS (**Route 53**) để đảm bảo khả năng sẵn sàng cao (**High Availability**).
  - Áp dụng các nguyên lý của **AWS Well-Architected Framework**.

---

## 📘 Phần 1: Monitoring your resources (Giám sát tài nguyên)

### 🔑 Ý chính

- 🛠️ **Monitoring distributed components** (Giám sát các thành phần phân tán): Theo dõi sức khỏe vận hành (**Operational health**), sử dụng tài nguyên (**Resource utilization**) và hiệu suất ứng dụng (**Application performance**).
- 📈 **Amazon CloudWatch**: Dịch vụ giám sát và quản lý tập trung các chỉ số và nhật ký.
- ⚡ **Amazon EventBridge**: Dịch vụ điều phối sự kiện (Event bus) giúp xây dựng các kiến trúc phản hồi nhanh.
- 💰 **Monitoring costs** (Giám sát chi phí): Theo dõi và kiểm soát chi tiêu thông qua các công cụ quản lý tài chính của AWS.

### 🧠 Giải thích

- **Amazon CloudWatch**:
  - **Metrics** (Chỉ số): Thu thập dữ liệu về hiệu suất từ các dịch vụ AWS (mặc định) hoặc dữ liệu tùy chỉnh (**Custom metrics**).
  - **CloudWatch Logs** (Nhật ký): Tập hợp các file log từ tài nguyên và ứng dụng để dễ dàng truy vấn và lưu trữ.
  - **Alarms** (Cảnh báo): Theo dõi một chỉ số cụ thể và thực hiện hành động (như gửi thông báo qua **Amazon SNS** hoặc kích hoạt chính sách **Auto Scaling**) khi chỉ số đó vượt ngưỡng quy định.
  - **Dashboards** (Bảng điều khiển): Tạo các biểu đồ trực quan để xem nhanh trạng thái của toàn bộ hệ thống.

- **Amazon EventBridge**:
  - **Event bus** (Xe buýt sự kiện): Là một bộ định tuyến tiếp nhận sự kiện từ các nguồn (dịch vụ AWS, ứng dụng riêng hoặc SaaS) và phân phối chúng đến các đích (**Targets**).
  - **Rules** (Quy tắc): Xác định sự kiện nào sẽ được gửi đi dựa trên **Event pattern** (mẫu sự kiện) hoặc theo lịch trình (**Schedule**).
  - **Pipes** (Đường ống): Giúp kết nối điểm-đến-điểm giữa một nguồn và một đích với khả năng lọc và chuyển đổi dữ liệu đơn giản.

- **Cost Management Tools**:
  - **AWS Cost Explorer**: Phân tích biểu đồ chi phí và dự báo chi tiêu trong tương lai.
  - **AWS Budgets**: Thiết lập các giới hạn ngân sách và gửi cảnh báo khi chi phí thực tế hoặc dự báo vượt ngưỡng.
  - **AWS Cost and Usage Report**: Cung cấp dữ liệu chi tiết nhất về mức độ sử dụng tài nguyên và chi phí tương ứng.

### 📌 Ghi nhớ nhanh

| Khái niệm       | Chức năng chính                      | Thành phần quan trọng                 |
| :-------------- | :----------------------------------- | :------------------------------------ |
| **CloudWatch**  | Giám sát hiệu suất & sức khỏe        | Metrics, Logs, Alarms, Dashboards     |
| **EventBridge** | Điều phối hành động dựa trên sự kiện | Event Bus, Rules, Targets, Pipes      |
| **Cost Tools**  | Quản lý tài chính đám mây            | Cost Explorer, Budgets, Usage Reports |

### 💡 Ví dụ (nếu có)

```example
Quy trình phản hồi tự động:
1. CloudWatch Alarm theo dõi CPU Utilization của một EC2 instance.
2. Nếu CPU > 80% trong 5 phút, Alarm chuyển sang trạng thái "ALARM".
3. Alarm kích hoạt Amazon SNS gửi email cho kỹ trị viên.
4. Đồng thời, Alarm thông báo cho Auto Scaling Group để khởi tạo thêm một máy chủ mới nhằm giảm tải.
```

---

## 📘 Phần 2: Scaling your compute resources (Mở rộng tài nguyên tính toán)

### 🔑 Ý chính

- 🔄 **Elasticity** (Tính đàn hồi): Khả năng của hạ tầng có thể tự động **expand** (mở rộng) và **contract** (thu hẹp) dựa trên yêu cầu về dung lượng thực tế.
- 📏 **Scaling** (Mở rộng): Khả năng tăng hoặc giảm dung lượng tính toán của ứng dụng.
- 🤖 **Amazon EC2 Auto Scaling**: Dịch vụ tự động quản lý một tập hợp các thực thể EC2 để đảm bảo ứng dụng luôn có đủ tài nguyên.
- 📈 **Scaling Mechanisms** (Cơ chế mở rộng): Các phương thức khác nhau để kích hoạt việc thay đổi số lượng thực thể (theo lịch, theo chỉ số hoặc dự đoán).

### 🧠 Giải thích

- **Phân loại Scaling**:
  - **Vertical scaling** (Mở rộng theo chiều dọc): Thay thế một tài nguyên cũ bằng một tài nguyên mới có kích cỡ/cấu hình khác (ví dụ: nâng cấp từ t2.micro lên m5.large).
  - **Horizontal scaling** (Mở rộng theo chiều ngang): Thêm hoặc bớt số lượng tài nguyên hiện có (ví dụ: thêm 2 máy chủ EC2 vào hệ thống).

- **Thành phần của Amazon EC2 Auto Scaling group (ASG)**:
  - **Launch template** (Mẫu khởi chạy): Chứa các thông tin cấu hình để khởi tạo thực thể như **AMI ID**, **Instance type** (loại thực thể), **Security groups**.
  - **Capacity settings** (Thiết lập dung lượng):
    - **Minimum capacity** (Dung lượng tối thiểu): Số lượng thực thể ít nhất luôn phải duy trì.
    - **Maximum capacity** (Dung lượng tối đa): Giới hạn cao nhất mà nhóm có thể mở rộng.
    - **Desired capacity** (Dung lượng mong muốn): Số lượng thực thể lý tưởng mà hệ thống cố gắng duy trì trong điều kiện bình thường.

- **Các loại chính sách mở rộng**:
  - **Scheduled actions** (Hành động theo lịch): Mở rộng dựa trên ngày và giờ cụ thể, phù hợp cho **predictable workloads** (tải công việc có thể dự đoán trước).
  - **Dynamic policies** (Chính sách động): Mở rộng dựa trên các chỉ số thực tế như mức sử dụng CPU, phù hợp cho tải công việc có sự biến động (**spiky workloads**).
  - **Predictive scaling policy** (Chính sách mở rộng dự đoán): Sử dụng **Machine Learning** để phân tích dữ liệu lịch sử và tự động mở rộng trước khi lưu lượng truy cập tăng thực tế.

- **Tùy chọn mở rộng khác**:
  - **AWS Auto Scaling**: Sử dụng **Scaling plan** (kế hoạch mở rộng) để cấu hình cho nhiều tài nguyên cùng lúc (Aurora, EC2, ECS, DynamoDB).
  - **AWS Application Auto Scaling**: Dịch vụ mở rộng cho các tài nguyên không thuộc EC2 như **Lambda functions**, **Amazon SageMaker**, hoặc **Amazon ElastiCache**.

### 📌 Ghi nhớ nhanh

| Tiêu chí          | Vertical Scaling                         | Horizontal Scaling                                         |
| :---------------- | :--------------------------------------- | :--------------------------------------------------------- |
| **Hành động**     | Đổi cấu hình (Lớn hơn/Nhỏ hơn)           | Đổi số lượng (Nhiều hơn/Ít hơn)                            |
| **Đặc điểm**      | Thường yêu cầu downtime (thời gian chết) | Không gây gián đoạn ứng dụng                               |
| **Tính ứng dụng** | Phù hợp cho database đơn lẻ              | Phù hợp nhất cho tính sẵn sàng cao (**High Availability**) |

### 💡 Ví dụ (nếu có)

```example
Kịch bản Step scaling policy:
- Nếu CPU Utilization < 30%: Giảm 30% số lượng thực thể hiện có (Scale-in).
- Nếu CPU Utilization từ 40% - 60%: Giữ nguyên hệ thống (No change).
- Nếu CPU Utilization > 71%: Thêm 30% số lượng thực thể mới (Scale-out).
```

---

## 📘 Phần 3: Scaling your databases (Mở rộng cơ sở dữ liệu)

### 🔑 Ý chính

- 🏗️ **Vertical scaling** (Mở rộng theo chiều dọc): Thay đổi **Instance class** (lớp thực thể) hoặc kích thước thực thể để tăng sức mạnh tính toán và bộ nhớ cho database.
- 👯 **Horizontal scaling** (Mở rộng theo chiều ngang): Thêm các **Read replicas** (bản sao chỉ đọc) để chia tải truy vấn đọc dữ liệu.
- 📦 **Service managed storage scaling** (Quản lý mở rộng lưu trữ tự động): Dịch vụ tự động tăng dung lượng lưu trữ khi dữ liệu đầy mà không cần can thiệp thủ công.
- ⚡ **On-demand vs Provisioned** (Theo nhu cầu và Định mức sẵn): Các mô hình linh hoạt để quản lý thông lượng dữ liệu dựa trên tải thực tế.

### 🧠 Giải thích

- **Amazon Aurora**:
  - **Vertical scaling**: Thay đổi kích thước **DB instance class** cho thực thể chính.
  - **Horizontal scaling**: Sử dụng **Aurora Auto Scaling** để tự động thêm hoặc bớt số lượng **Aurora Replicas** dựa trên tải truy vấn đọc.
  - **Storage**: Tự động mở rộng lưu trữ theo cluster volume.

- **Amazon Aurora Serverless**:
  - Sử dụng **ACUs** (Aurora Capacity Units) để đo lường thông lượng.
  - Tự động điều chỉnh giữa mức **Minimum** và **Maximum** ACUs được cấu hình sẵn.
  - Phù hợp cho tải công việc không liên tục hoặc khó dự đoán.

- **Amazon RDS**:
  - **Vertical scaling**: Thay đổi thủ công **DB instance class** hoặc loại và kích thước ổ đĩa **EBS**.
  - **Horizontal scaling**: Thêm các **Read replica instances** để mở rộng khả năng đọc.
  - **Storage auto scaling**: Tự động tăng kích thước ổ đĩa khi dung lượng còn lại thấp.

- **Amazon DynamoDB**:
  - **On-demand mode**: Tự động mở rộng dựa trên số lượng request đọc/ghi thực tế mà không cần cấu hình dung lượng.
  - **Provisioned mode**: Sử dụng **RCUs** (Read Capacity Units) và **WCUs** (Write Capacity Units). Kết hợp với **Application Auto Scaling** để điều chỉnh dựa trên mẫu lưu lượng.
  - **Global secondary index** (Chỉ mục thứ cấp toàn cầu): Giúp giảm tải cho bảng chính bằng cách tạo các bảng mục lục phụ.

### 📌 Ghi nhớ nhanh

| Dịch vụ               | Phạm vi mở rộng | Cơ chế chính                          |
| :-------------------- | :-------------- | :------------------------------------ |
| **Aurora**            | Cluster (Cụm)   | Instance class & Replicas             |
| **Aurora Serverless** | Cluster (Cụm)   | ACUs (tự động 100%)                   |
| **Amazon RDS**        | Database (CSDL) | Instance class & Storage auto scaling |
| **DynamoDB**          | Table (Bảng)    | RCUs/WCUs hoặc On-demand              |

### 💡 Ví dụ (nếu có)

```example
Kịch bản mở rộng cho ứng dụng Cafe:
- Buổi sáng khách đông: DynamoDB được đặt ở chế độ On-demand để tự động xử lý hàng nghìn đơn hàng tăng đột biến.
- Phân tích báo cáo cuối ngày: Hệ thống sử dụng một Read replica của Amazon RDS để chạy các truy vấn thống kê nặng, tránh làm chậm database chính đang xử lý đơn hàng tại quầy.
```

---

## 📘 Phần 4: Using load balancers to create highly available environments (Sử dụng bộ cân bằng tải để tạo môi trường sẵn sàng cao)

### 🔑 Ý chính

- ⚖️ **Elastic Load Balancing (ELB)**: Tự động phân phối lưu lượng truy cập đến nhiều đích khác nhau (như EC2 instances, containers, IP addresses) để tăng độ tin cậy.
- 🏥 **Health checks**: Theo dõi tình trạng sức khỏe của các mục tiêu đã đăng ký và chỉ gửi lưu lượng đến những thực thể khỏe mạnh.
- 🏗️ **High Availability (HA)**: Đảm bảo hệ thống hoạt động liên tục với thời gian chết (**downtime**) tối thiểu bằng cách phân tán tài nguyên qua nhiều **Availability Zones (AZs)**.
- 🚦 **Scalability**: ELB có khả năng tự động mở rộng quy mô để đáp ứng sự thay đổi của lưu lượng truy cập đầu vào.

### 🧠 Giải thích

- **Cơ chế hoạt động**:
  - **Listeners** (Bộ lắng nghe): Kiểm tra các yêu cầu kết nối từ phía khách hàng dựa trên giao thức và cổng được cấu hình.
  - **Rules** (Quy tắc): Xác định cách bộ cân bằng tải định tuyến các yêu cầu đến các mục tiêu cụ thể.
  - **Target groups** (Nhóm mục tiêu): Tập hợp các thực thể (như EC2) nhận lưu lượng từ bộ cân bằng tải.

- **Phân loại bộ cân bằng tải (AWS Load Balancer Types)**:
  - **Application Load Balancer (ALB)**: Hoạt động ở tầng ứng dụng (**Layer 7**). Chuyên dùng cho lưu lượng **HTTP/HTTPS**, hỗ trợ định tuyến dựa trên nội dung (path-based hoặc host-based routing).
  - **Network Load Balancer (NLB)**: Hoạt động ở tầng giao vận (**Layer 4**). Xử lý hàng triệu yêu cầu mỗi giây với độ trễ cực thấp, hỗ trợ **TCP/UDP**, **TLS** và **Static IP**.
  - **Gateway Load Balancer (GWLB)**: Hoạt động ở tầng mạng (**Layer 3**). Dùng để triển khai và quản lý một dàn các thiết bị ảo bên thứ ba (như tường lửa, hệ thống phát hiện xâm nhập).
  - **Classic Load Balancer (CLB)**: Thế hệ cũ, hoạt động ở cả tầng 4 và tầng 7. Chỉ dùng cho các mạng EC2-Classic cũ.

### 📌 Ghi nhớ nhanh

| Loại Load Balancer | Tầng OSI    | Trường hợp sử dụng chính              |
| :----------------- | :---------- | :------------------------------------ |
| **ALB**            | **Layer 7** | Ứng dụng web hiện đại, Microservices  |
| **NLB**            | **Layer 4** | Hiệu suất cực cao, Static IP, Gaming  |
| **GWLB**           | **Layer 3** | Kiểm tra an ninh, Tường lửa bên thứ 3 |

**Các mức độ sẵn sàng cao (High Availability Metrics)**:

- **99.9%**: Downtime tối đa khoảng 8.76 giờ/năm.
- **99.99%**: Downtime tối đa khoảng 52.6 phút/năm.
- **99.999%**: Downtime tối đa chỉ còn 5.25 phút/năm (tiêu chuẩn vàng).

### 💡 Ví dụ (nếu có)

```example
Kiến trúc đa tầng (Multi-tier HA):
1. Client gửi yêu cầu đến Route 53.
2. Route 53 trỏ về Application Load Balancer (ALB).
3. ALB thực hiện Health check:
   - Nếu Instance A ở AZ1 chết -> ALB ngừng gửi khách đến đó.
   - Định tuyến toàn bộ khách sang Instance B ở AZ2.
4. Hệ thống vẫn chạy bình thường mà khách hàng không hề hay biết sự cố.
```

---

## 📘 Phần 5: Using Route 53 to create highly available environments (Sử dụng Route 53 để tạo môi trường sẵn sàng cao)

### 🔑 Ý chính

- 🌐 **Amazon Route 53**: Là một dịch vụ web hệ thống tên miền (**DNS**) có khả năng mở rộng cao và sẵn sàng cao.
- 🚦 **DNS Routing**: Chịu trách nhiệm điều hướng lưu lượng truy cập của người dùng đến các điểm cuối (**Endpoints**) khỏe mạnh.
- 🏥 **Health Checks**: Theo dõi tình trạng của các tài nguyên để quản lý việc chuyển vùng sự cố (**Failover**) giữa các **Availability Zones** hoặc các **Regions**.
- 🗺️ **Routing Policies**: Cung cấp nhiều tùy chọn định tuyến khác nhau để tối ưu hóa hiệu suất và độ tin cậy của ứng dụng.

### 🧠 Giải thích

- **Cơ chế hoạt động của Route 53**:
  - Route 53 đóng vai trò là **Authoritative Name Server** (Máy chủ tên miền có thẩm quyền), cung cấp câu trả lời cuối cùng cho các truy vấn DNS.
- **Hosted Zones (Vùng lưu trữ)**:
  - **Public hosted zone**: Sử dụng để định tuyến lưu lượng truy cập từ Internet đến các tài nguyên AWS.
  - **Private hosted zone**: Sử dụng để định tuyến lưu lượng trong nội bộ một hoặc nhiều **VPC** mà không lộ diện ra Internet.
- **Các loại Routing Policies (Chính sách định tuyến)**:
  - **Simple routing** (Định tuyến đơn giản): Cấu hình DNS cơ bản để trỏ đến một tài nguyên duy nhất.
  - **Weighted routing** (Định tuyến theo trọng số): Phân chia lưu lượng theo tỉ lệ phần trăm giữa nhiều tài nguyên (ví dụ: 90% cho bản cũ, 10% cho bản thử nghiệm).
  - **Latency routing** (Định tuyến theo độ trễ): Điều hướng người dùng đến vùng (**Region**) cung cấp thời gian phản hồi nhanh nhất.
  - **Failover routing** (Định tuyến dự phòng): Sử dụng trong cấu hình **Active-Passive**. Tự động chuyển hướng sang tài nguyên dự phòng khi tài nguyên chính gặp sự cố.
  - **Geolocation routing** (Định tuyến theo vị trí địa lý): Phân phối lưu lượng dựa trên vị trí thực tế của người dùng (quốc gia, châu lục).
  - **Geoproximity routing** (Định tuyến theo khoảng cách địa lý): Định tuyến dựa trên vị trí của tài nguyên và người dùng, có thể điều chỉnh mức độ ưu tiên bằng giá trị **bias**.
  - **Multivalue answer routing**: Trả về tối đa 8 bản ghi IP khỏe mạnh cho một truy vấn, giúp tăng tính sẵn sàng và cân bằng tải đơn giản.
  - **IP-based routing**: Định tuyến dựa trên dải địa chỉ IP của khách hàng.

- **Multi-Region Failover (Dự phòng đa vùng)**:
  - Route 53 kết hợp với **CloudWatch Alarms** để thực hiện chuyển hướng lưu lượng từ một Region bị lỗi sang một Region dự phòng đang hoạt động tốt.

### 📌 Ghi nhớ nhanh

| Loại định tuyến | Trường hợp sử dụng tiêu biểu                                   |
| :-------------- | :------------------------------------------------------------- |
| **Simple**      | Khi chỉ có một máy chủ hoặc một Load Balancer.                 |
| **Weighted**    | Thử nghiệm phần mềm mới (**Canary deployments**).              |
| **Latency**     | Ứng dụng toàn cầu cần tốc độ tối ưu.                           |
| **Failover**    | Thiết lập hệ thống khắc phục thảm họa (**Disaster Recovery**). |
| **Geolocation** | Hiển thị nội dung bằng ngôn ngữ địa phương của khách hàng.     |

### 💡 Ví dụ (nếu có)

```example
Thiết lập chuyển vùng sự cố (Failover):
- Bản ghi chính (Primary): Trỏ đến Application Load Balancer tại Region Singapore. Kèm theo Health check.
- Bản ghi dự phòng (Secondary): Trỏ đến một trang web tĩnh (Static site) trên Amazon S3 tại Region Tokyo.
- Hoạt động: Route 53 liên tục kiểm tra ALB ở Singapore. Nếu Load Balancer này không phản hồi, Route 53 sẽ tự động trả về địa chỉ của trang web tĩnh ở Tokyo để khách hàng vẫn thấy thông báo bảo trì thay vì lỗi trang.
```

---

## 📝 Knowledge Check: Bộ 1 - Bảo mật và Định danh

### ❓ Question 1

Which are characteristics of an AWS Identity and Access Management (IAM) group? (Select TWO.)

A. A user can belong to more than one group.

B. A group can have security credentials.

C. New users added to a group inherit the group’s permissions.

D. A group can belong to another group.

E. Permissions in a group policy always override permissions in a user policy.

✅ **Đáp án đúng:** A và C

### 🧠 Giải thích

- **A**: Một **IAM User** có thể là thành viên của tối đa 10 nhóm (mặc định), cho phép họ nhận quyền từ nhiều nguồn khác nhau.
- **C**: Đây là mục đích chính của nhóm: **Inheritance** (tính kế thừa). Thay vì gán quyền cho từng người, bạn gán quyền cho nhóm và bất kỳ ai vào nhóm đều tự động có quyền đó.

### ❌ Vì sao đáp án khác sai?

- **B**: Nhóm (Group) không có thông tin xác thực (**credentials**) như mật khẩu hay access key; chỉ có User mới có.
- **D**: IAM không hỗ trợ **Nested groups** (nhóm trong nhóm).
- **E**: AWS đánh giá chính sách dựa trên sự kết hợp; không có quy tắc mặc định nào nói policy của nhóm luôn ghi đè user.

💡 **Mẹo nhớ nhanh:** IAM Group không phải là một "thực thể định danh" có thể đăng nhập, nó chỉ là một cái "giỏ" để đựng User và chứa quyền hạn (**Permissions**).

---

### ❓ Question 3

A developer is a member of an IAM group that has access to Amazon S3 and Amazon EC2 and denies access to Amazon ECS. The developer also has a user policy attached which allows access to Amazon ECS and Amazon CloudFront. Which option describes the user’s access?

A. Access to Amazon S3, Amazon EC2, Amazon ECS, and Amazon CloudFront

B. Access to Amazon S3, Amazon EC2, and Amazon CloudFront, but no access to Amazon ECS

C. Access to Amazon ECS and Amazon CloudFront, but no access to Amazon S3 and Amazon EC2

D. Access to Amazon S3 and Amazon EC2, but no access to Amazon ECS and Amazon CloudFront

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- Trong AWS, một lệnh từ chối rõ ràng (**Explicit Deny**) luôn thắng một lệnh cho phép (**Allow**).
- Group policy từ chối ECS -> Dù User policy cho phép ECS thì kết quả cuối cùng vẫn là **Deny**.

### ❌ Vì sao đáp án khác sai?

- **A, C, D**: Đều sai vì không áp dụng đúng nguyên tắc "Deny wins over Allow" hoặc bỏ sót quyền CloudFront/S3/EC2 được cho phép.

💡 **Mẹo nhớ nhanh:** Hãy nhớ: **Allow + Deny = Deny**. Deny có quyền lực tuyệt đối trong AWS Policy.

---

## 📝 Knowledge Check: Bộ 2 - Vận hành và Điều phối (Module 10)

### ❓ Question 1

Which statement about Amazon EC2 Auto Scaling is accurate?

A. It requires the customer to use Reserved Instances only.

B. It can launch new Amazon EC2 instances based on a schedule.

C. It can launch Amazon EC2 instances in multiple Availability Zones.

D. It can launch Amazon EC2 instances, but customers must terminate instances after they are no longer needed.

✅ **Đáp án đúng:** C (Lưu ý: B cũng đúng về kỹ thuật nhưng C là đặc điểm cốt lõi của High Availability). Tuy nhiên, dựa trên tài liệu AWS Academy, đặc điểm quan trọng nhất để tạo hệ thống HA là khả năng chạy trên nhiều **Availability Zones**.

### 🧠 Giải thích

- **Amazon EC2 Auto Scaling** giúp duy trì tính sẵn sàng bằng cách phân tán các instance qua nhiều **AZs**. Nếu một AZ bị lỗi, ASG sẽ khởi tạo lại instance ở AZ khác khỏe mạnh.

### ❌ Vì sao đáp án khác sai?

- **A**: Có thể dùng On-demand, Spot hoặc Reserved instances.
- **D**: ASG tự động **terminate** (hủy) các instance không cần thiết khi thực hiện **scale-in**.

💡 **Mẹo nhớ nhanh:** Auto Scaling = Tự động hóa hoàn toàn việc tăng/giảm và duy trì số lượng máy chủ ổn định.

---

### ❓ Question 2

A devops engineer detected that the demand increases by a set amount on weekend days. Which type of scaling is the MOST appropriate in this case?

A. Dynamic

B. Predictive

C. Scheduled

D. Manual

✅ **Đáp án đúng:** C

### 🧠 Giải thích

- **Scheduled scaling**: Phù hợp cho tải công việc có tính chất chu kỳ và dự đoán được chính xác thời gian (ví dụ: thứ 7, chủ nhật hàng tuần).

### ❌ Vì sao đáp án khác sai?

- **A**: Dùng khi tải biến động bất ngờ (dựa trên CPU, RAM).
- **B**: Dùng máy học để dự đoán tải dài hạn, phức tạp hơn mức cần thiết cho lịch trình cố định hàng tuần.
- **D**: Cần sự can thiệp của con người, không tối ưu.

---

### ❓ Question 3

The EC2 instances must maintain 50 percent average CPU utilization. Which type of scaling is appropriate?

A. Target tracking scaling

B. Manual scaling

C. Simple scaling

D. Step scaling

✅ **Đáp án đúng:** A

### 🧠 Giải thích

- **Target tracking scaling**: Bạn chỉ cần chọn một chỉ số (như CPU 50%) và AWS sẽ tự động tính toán để giữ chỉ số đó ổn định. Nó giống như bộ ổn nhiệt (Thermostat) trong điều hòa.

---

### ❓ Question 7

The instances run an application that uses a custom protocol on TCP port 42000. Which load balancing solution is the best solution?

A. Application Load Balancer

B. Classic Load Balancer

C. Gateway Load Balancer

D. Network Load Balancer

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Network Load Balancer (NLB)** hoạt động ở tầng 4 (Transport layer), chuyên xử lý các giao thức **TCP/UDP** và các cổng tùy chỉnh với hiệu suất cực cao và độ trễ thấp.

### ❌ Vì sao đáp án khác sai?

- **A**: ALB hoạt động ở tầng 7 (Application), chỉ hỗ trợ HTTP/HTTPS/gRPC.

💡 **Mẹo nhớ nhanh:** Cứ thấy **TCP, UDP, Static IP** hoặc **Cổng lạ (non-HTTP)** -> Chọn ngay **NLB**.

---

### ❓ Question 10

A software engineer wants the account to stay within the AWS Free Tier and to not generate unexpected costs. Which approach require the LEAST effort?

A. Create an Amazon CloudWatch alarm to send an email message when the account billing exceeds $0.

B. Sign in to the AWS Management Console each month and check the billing dashboard.

C. Create an Amazon CloudWatch metric to monitor account billing and limit it to $0.

D. Create a service control policy (SCP) to restrict all services that are not included in the AWS Free Tier.

✅ **Đáp án đúng:** A

### 🧠 Giải thích

- Tạo một **CloudWatch billing alarm** là cách nhanh nhất và ít tốn sức nhất để nhận thông báo ngay khi phát sinh chi phí vượt ngưỡng 0$.

### ❌ Vì sao đáp án khác sai?

- **B**: Tốn công kiểm tra thủ công hàng tháng.
- **D**: Cực kỳ phức tạp và dễ gây lỗi hệ thống vì SCP không được thiết kế để quản lý Free Tier theo cách này.

💡 **Mẹo nhớ nhanh:** Billing + Alarm = Cách quản lý túi tiền thông minh nhất trên AWS.
