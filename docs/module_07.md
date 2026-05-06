# Module 07: Creating a Networking Environment

## 🧭 Overview

- Hiểu và giải thích được vai trò của **virtual private cloud (VPC)** trong kiến trúc mạng của đám mây AWS.
- Nhận diện các thành phần bên trong **Amazon VPC** dùng để kết nối môi trường mạng với internet.
- Cách ly và bảo mật các tài nguyên bên trong môi trường mạng AWS.
- Thiết lập và giám sát một **Amazon VPC** hoàn chỉnh cùng với **subnets**, **internet gateway**, **route tables**, và **security group**.
- Áp dụng các nguyên tắc của **AWS Well-Architected Framework** khi lên kế hoạch và tạo lập môi trường mạng.

---

## 📘 Phần 1: Introducing Amazon VPC

### 🔑 Ý chính

- **Amazon VPC** là nền tảng cốt lõi để xây dựng không gian mạng độc lập trên AWS.
- Mạng ảo này được cấu trúc thông qua các **CIDR block** và chia nhỏ thành các **subnets**.
- Truy cập internet được quản lý thông qua **internet gateway** (dành cho mạng công khai) và **NAT gateway** (dành cho mạng riêng tư).
- Điều hướng giao thông mạng (traffic) bằng các **route tables**.
- Quản lý địa chỉ IP tĩnh bằng **Elastic IP addresses**.

### 🧠 Giải thích

- **Amazon VPC** (Mạng ảo riêng tư được xác định qua lập trình, cách ly về mặt logic giống hệt một mạng trung tâm dữ liệu truyền thống. Nó thuộc về một **Region** duy nhất và được định cỡ bằng dải địa chỉ IP nội bộ gọi là **CIDR block**).
- **CIDR block** (Khối định tuyến liên mạng, dùng để khai báo dải địa chỉ IP khả dụng cho toàn bộ **Amazon VPC**).
- **Route table** (Bảng định tuyến dùng để xác định nơi các luồng dữ liệu mạng sẽ được gửi đi).
- **Public subnet** (Mạng con công khai, có chứa đường dẫn kết nối với **internet gateway** để truy cập trực tiếp ra internet. Các instance ở đây yêu cầu phải có cả **Private IP address** và **Public IP address**).
- **Private subnet** (Mạng con riêng tư, tuyệt đối không có đường truy cập trực tiếp ra ngoài internet).
- **Internet gateway** (Cổng kết nối internet, đóng vai trò là cầu nối cho phép tài nguyên trong **Amazon VPC** giao tiếp với mạng internet bên ngoài).
- **NAT gateway** (Cổng dịch địa chỉ mạng, giúp các tài nguyên đang nằm trong **private subnet** có thể gửi yêu cầu ra internet - ví dụ để cập nhật phần mềm - nhưng chặn các kết nối khởi tạo từ internet đi vào).
- **Elastic IP address** (Địa chỉ IP tĩnh, công khai, được liên kết trực tiếp với một instance và có thể linh hoạt chuyển sang instance mới nếu instance cũ bị hỏng).

### 📌 Ghi nhớ nhanh

- 🔄 **So sánh cách chọn Subnet cho từng loại tài nguyên:**
  - **Database instances** (Máy chủ cơ sở dữ liệu) ➔ Bắt buộc đặt ở **Private subnet** để bảo mật.
  - **Batch-processing instances** (Máy chủ xử lý tác vụ ngầm) ➔ Đặt ở **Private subnet**.
  - **Web application instances** (Máy chủ ứng dụng Web) ➔ Có thể đặt ở **Public subnet** hoặc **Private subnet** (nếu dùng kèm Load Balancer).
  - **NAT gateway** (hoặc NAT instance) ➔ Bắt buộc đặt ở **Public subnet** để làm cầu nối ra ngoài.
- Một **Elastic IP address** là tài sản của tài khoản AWS, bạn có thể tháo nó ra khỏi máy chủ này và gắn vào máy chủ khác rất dễ dàng.

### 💡 Ví dụ (nếu có)

```example
Giả sử bạn đang xây dựng một ứng dụng bán hàng:
- Máy chủ chạy website của bạn cần giao tiếp với khách hàng -> Đặt vào Public subnet và cấp Public IP.
- Máy chủ chứa dữ liệu đơn hàng và khách hàng -> Đặt vào Private subnet, chỉ cho phép máy chủ website kết nối vào, hoàn toàn vô hình với internet.
- Khi máy chủ dữ liệu cần tải bản cập nhật bảo mật -> Nó sẽ đi xuyên qua một NAT Gateway (đặt tại Public subnet) để ra internet tải về một cách an toàn.
```

---

## 📘 Phần 2: Securing network resources

### 🔑 Ý chính

- Bảo mật mạng trong AWS được xây dựng theo mô hình **Defense in depth** (phòng thủ đa lớp).
- Kiểm soát lưu lượng thông qua **Security groups** (nhóm bảo mật) và **Network ACLs** (danh sách kiểm soát truy cập mạng).
- Sử dụng **AWS Network Firewall** để tăng cường khả năng phát hiện và ngăn chặn xâm nhập.
- Quản trị các tài nguyên trong vùng mạng riêng tư an toàn bằng **Bastion host**.

### 🧠 Giải thích

- **Security group** (Nhóm bảo mật: Hoạt động như một tường lửa ảo cho các **instance**. Nó kiểm soát lưu lượng ở cấp độ **giao diện mạng (NIC)** của tài nguyên).
- **Network ACL - Access Control List** (Danh sách kiểm soát truy cập mạng: Hoạt động như một tường lửa cho **subnet**. Đây là lớp bảo mật thứ hai kiểm soát lưu lượng ra vào ở cấp độ mạng con).
- **Stateful** (Tính chất ghi nhớ trạng thái: Nếu bạn gửi một yêu cầu đi, lưu lượng phản hồi cho yêu cầu đó sẽ tự động được phép quay lại mà không cần cấu hình thêm luật).
- **Stateless** (Tính chất không ghi nhớ trạng thái: Bạn phải cấu hình quy tắc cho cả chiều đi và chiều về một cách thủ công).
- **AWS Network Firewall** (Dịch vụ tường lửa mạng có khả năng kiểm soát lưu lượng chi tiết hơn, hỗ trợ lọc tên miền và ngăn chặn các mối đe dọa dựa trên mẫu chữ ký).
- **Bastion host** (Máy chủ trung chuyển: Là một EC2 instance nằm trong **Public subnet**, đóng vai trò là điểm truy cập duy nhất để quản trị viên kết nối SSH hoặc RDP vào các máy chủ nằm trong **Private subnet**).

### 📌 Ghi nhớ nhanh

🔄 **So sánh Security Groups và Network ACLs:**

| Đặc điểm               | Security Groups 🛡️                         | Network ACLs 🧱                          |
| :--------------------- | :----------------------------------------- | :--------------------------------------- |
| **Phạm vi bảo vệ**     | Cấp độ tài nguyên (Instance/Interface)     | Cấp độ mạng con (Subnet)                 |
| **Quy tắc (Rules)**    | Chỉ có quy tắc **Allow** (Cho phép)        | Có cả quy tắc **Allow** và **Deny**      |
| **Trạng thái (State)** | **Stateful** (Ghi nhớ trạng thái)          | **Stateless** (Không nhớ trạng thái)     |
| **Thứ tự áp dụng**     | Kiểm tra tất cả quy tắc trước khi cho phép | Kiểm tra theo thứ tự số (nhỏ nhất trước) |
| **Mặc định (Mới)**     | Chặn mọi traffic chiều vào                 | Cho phép mọi traffic ra/vào              |

### 💡 Ví dụ (nếu có)

```example
Mô hình truy cập an toàn với Bastion Host:
1. Quản trị viên SSH từ máy cá nhân vào Bastion Host (nằm trong Public Subnet) qua cổng 22.
2. Security Group của Bastion Host chỉ cho phép IP của Quản trị viên.
3. Từ Bastion Host, Quản trị viên SSH tiếp vào Application Server (nằm trong Private Subnet).
4. Security Group của Application Server chỉ cho phép truy cập từ Security Group của Bastion Host.
```

---

## 📘 Phần 3: Connecting to managed AWS services

### 🔑 Ý chính

- Các tài nguyên trong VPC có thể truy cập các dịch vụ AWS được quản lý (managed services) mà không cần đi qua internet công cộng.
- **VPC endpoints** (Điểm cuối VPC) cung cấp kết nối riêng tư, bảo mật và hiệu suất cao.
- Có hai loại endpoint chính: **Interface VPC endpoint** và **Gateway VPC endpoint**.
- **AWS PrivateLink** là công nghệ cốt lõi đứng sau các interface endpoint để kết nối các dịch vụ một cách an toàn.

### 🧠 Giải thích

- **VPC endpoint** (Điểm cuối VPC: Một thiết bị ảo cho phép bạn kết nối riêng tư giữa VPC của mình với các dịch vụ AWS được hỗ trợ mà không cần **internet gateway**, **NAT device**, hay kết nối **VPN**).
- **Interface VPC endpoint** (Điểm cuối VPC giao diện: Sử dụng **AWS PrivateLink**. Nó tạo ra một **Elastic network interface (ENI)** với địa chỉ IP riêng tư từ dải IP của subnet để làm điểm tiếp nhận lưu lượng cho dịch vụ mục tiêu).
- **Gateway VPC endpoint** (Điểm cuối VPC cổng: Một mục tiêu (target) được chỉ định trong **route table** của bạn để điều hướng lưu lượng đến **Amazon S3** hoặc **Amazon DynamoDB**).
- **AWS PrivateLink** (Công nghệ cung cấp kết nối riêng tư giữa các VPC, các dịch vụ AWS và mạng on-premises của bạn mà không để lưu lượng tiếp xúc với internet).
- **Gateway Load Balancer endpoint** (Điểm cuối Gateway Load Balancer: Được sử dụng để chặn và kiểm tra lưu lượng mạng thông qua các thiết bị bảo mật ảo trước khi dữ liệu đi tiếp).

### 📌 Ghi nhớ nhanh

🔄 **So sánh Interface Endpoint và Gateway Endpoint (đối với Amazon S3):**

| Đặc điểm             | Interface VPC endpoints 🧩         | Gateway VPC endpoints 🚪                   |
| :------------------- | :--------------------------------- | :----------------------------------------- |
| **Cách truy cập**    | Sử dụng IP riêng tư trong subnet   | Sử dụng IP công khai của S3                |
| **Cơ chế hoạt động** | Dựa trên **PrivateLink** (ENI)     | Dựa trên **Route Table** (Bảng định tuyến) |
| **Dịch vụ hỗ trợ**   | Rất nhiều dịch vụ AWS khác nhau    | Chỉ dành cho **S3** và **DynamoDB**        |
| **Chi phí**          | Có tính phí theo giờ và dung lượng | **Hoàn toàn miễn phí**                     |
| **Băng thông**       | Tối đa 100 Gbps mỗi AZ             | Không giới hạn                             |
| **On-premises**      | Cho phép truy cập từ mạng nội bộ   | Không cho phép truy cập trực tiếp          |

### 💡 Ví dụ (nếu có)

```example
Trường hợp sử dụng:
Bạn có một máy chủ EC2 nằm trong Private Subnet (không có internet).
- Để lưu trữ file ảnh vào Amazon S3 mà không tốn chi phí và không cần cấu hình phức tạp: Bạn nên tạo một Gateway VPC Endpoint.
- Để máy chủ đó gửi log về Amazon CloudWatch hoặc gọi API từ AWS Lambda một cách an toàn: Bạn cần tạo một Interface VPC Endpoint cho từng dịch vụ đó.
```

---

## 📘 Phần 4: Monitoring your network

### 🔑 Ý chính

- Giám sát lưu lượng mạng là bước quan trọng để phát hiện sự cố và đảm bảo an ninh.
- **VPC Flow Logs** là công cụ chủ đạo để ghi lại dữ liệu về các luồng lưu lượng IP.
- Sử dụng các công cụ phân tích nâng cao như **Reachability Analyzer** và **Network Access Analyzer** để kiểm tra cấu hình mạng.
- Tận dụng **Traffic Mirroring** để sao chép dữ liệu mạng phục vụ cho việc kiểm tra bảo mật chuyên sâu.

### 🧠 Giải thích

- **VPC Flow Logs** (Nhật ký luồng VPC): Một tính năng cho phép bạn ghi lại thông tin về lưu lượng IP đi đến và đi ra từ các giao diện mạng (**Network Interfaces**) trong VPC. Bạn có thể tạo nhật ký ở cấp độ **VPC**, **Subnet**, hoặc từng **Elastic Network Interface (ENI)**.
- **Reachability Analyzer** (Trình phân tích khả năng tiếp cận): Một công cụ kiểm tra kết nối tĩnh. Nó giúp bạn biết liệu một nguồn (source) và đích (destination) có thể giao tiếp với nhau hay không dựa trên cấu hình hiện tại mà không cần gửi gói tin thực tế.
- **Network Access Analyzer** (Trình phân tích truy cập mạng): Công cụ giúp xác định các đường dẫn mạng có thể dẫn đến việc truy cập không mong muốn hoặc không theo đúng quy định bảo mật vào tài nguyên của bạn.
- **Traffic Mirroring** (Phản chiếu lưu lượng): Tính năng cho phép bạn sao chép toàn bộ lưu lượng mạng từ một nguồn cụ thể và gửi đến các thiết bị giám sát hoặc bảo mật để phân tích nội dung gói tin (Packet inspection).

### 📌 Ghi nhớ nhanh

🔄 **So sánh các công cụ giám sát và khắc phục sự cố:**

| Công cụ                     | Mục đích chính 🎯       | Cách hoạt động ⚙️                                                           |
| :-------------------------- | :---------------------- | :-------------------------------------------------------------------------- |
| **VPC Flow Logs**           | Theo dõi lưu lượng IP   | Ghi lại metadata (IP nguồn/đích, cổng, giao thức, hành động ACCEPT/REJECT). |
| **Reachability Analyzer**   | Khắc phục sự cố kết nối | Phân tích cấu hình đường dẫn (Route table, SG, NACL) để tìm điểm chặn.      |
| **Network Access Analyzer** | Kiểm toán bảo mật       | Tìm các lỗ hổng truy cập tiềm ẩn hoặc cấu hình sai quy định.                |
| **Traffic Mirroring**       | Kiểm tra chuyên sâu     | Sao chép nội dung gói tin để phân tích mã độc hoặc xâm nhập.                |

### 💡 Ví dụ (nếu có)

```example
Phân tích một bản ghi (record) trong VPC Flow Logs:
- srcaddr: 172.31.16.139 (IP nguồn)
- dstport: 22 (Cổng đích - SSH)
- protocol: 6 (Giao thức TCP)
- action: REJECT (Hành động: Bị từ chối)

=> Ý nghĩa: Có một nỗ lực kết nối SSH từ địa chỉ IP trên nhưng đã bị Security Group hoặc Network ACL chặn lại.
```

---

## 📝 Knowledge Check

### ❓ Question 1

Amazon Elastic Compute Cloud (**Amazon EC2**) instances running in a **private subnet** (mạng con riêng tư) need to download software patches from the internet. However, the **EC2 instances** cannot be directly accessible from the internet. Which actions should be taken? (Select TWO)[cite: 1]

A. Configure a **NAT gateway** (cổng dịch địa chỉ mạng) in a **public subnet** (mạng con công khai).

B. Configure a **NAT instance** (phiên bản NAT) in a **private subnet**.

C. Define a custom **route table** (bảng định tuyến) with a route to the **internet gateway** (cổng internet) for internet traffic and associate it with the **private subnets**.

D. Define a custom **route table** with a route to the **NAT gateway** for internet traffic and associate it with the **private subnets**.

E. Assign **Elastic IP addresses** (địa chỉ IP tĩnh công khai) to the EC2 instances.

✅ **Đáp án đúng:** A, D[cite: 1]

### 🧠 Giải thích

- **NAT gateway** (cổng dịch địa chỉ mạng) phải được đặt trong một **public subnet** để có thể kết nối ra internet thông qua **internet gateway**[cite: 1].
- Cần một **route table** (bảng định tuyến) tùy chỉnh cho **private subnet** trỏ lưu lượng đi ra internet ($0.0.0.0/0$) đến **NAT gateway** để các instance bên trong tải được patch[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: **NAT instance** hoặc **NAT gateway** đặt trong **private subnet** sẽ không thể kết nối ra internet vì chính subnet đó không có đường ra[cite: 1].
- C: Trỏ trực tiếp từ **private subnet** ra **internet gateway** sẽ khiến nó trở thành **public subnet**, không đáp ứng yêu cầu "không được truy cập trực tiếp từ internet"[cite: 1].
- E: **Elastic IP addresses** cho phép truy cập hai chiều từ internet, vi phạm tính bảo mật của **private subnet**[cite: 1].

💡 **Mẹo nhớ nhanh:** Muốn **Private Subnet** ra internet an toàn: Cần **NAT Gateway** (ở Public) + cập nhật **Route Table**[cite: 1].

---

### ❓ Question 2

What is the primary role of an **Amazon VPC** (mạng ảo riêng tư) in **AWS Cloud networking**?[cite: 1]

A. To provide a physical data center for **EC2 instances**.

B. To create a programmatically defined, **logically isolated** (cách ly về mặt logic) virtual network.

C. To manage global **DNS records** for all AWS services.

D. To store large amounts of **unstructured data**.

✅ **Đáp án đúng:** B[cite: 1]

### 🧠 Giải thích

- **Amazon VPC** là một mạng ảo được xác định bằng phần mềm, cung cấp không gian **logically isolated** (cách ly logic) hoàn toàn trên đám mây AWS để người dùng kiểm soát tài nguyên của mình[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: **Amazon VPC** là mạng ảo, không phải cơ sở hạ tầng vật lý (data center)[cite: 1].
- C: Đây là nhiệm vụ của **Amazon Route 53**, không phải VPC[cite: 1].
- D: Đây là nhiệm vụ của các dịch vụ lưu trữ như **Amazon S3**[cite: 1].

---

### ❓ Question 3

Which component is required for a **subnet** (mạng con) to be considered a **public subnet** (mạng con công khai)?[cite: 1]

A. A connection to an **AWS Direct Connect** link.

B. An associated **route table** (bảng định tuyến) with a route to an **internet gateway** (cổng internet).

C. A **NAT gateway** (cổng dịch địa chỉ mạng) configured within that subnet.

D. Only **Private IP addresses** (địa chỉ IP nội bộ) assigned to all instances.

✅ **Đáp án đúng:** B[cite: 1]

### 🧠 Giải thích

- Một **public subnet** được xác định bởi việc có một bản ghi trong **route table** của nó trỏ lưu lượng internet ($0.0.0.0/0$) đến một **internet gateway**[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: **Direct Connect** dùng cho kết nối vật lý riêng giữa on-premises và AWS, không phải điều kiện cần cho public subnet[cite: 1].
- C: **NAT gateway** nằm trong public subnet để giúp private subnet ra ngoài, nhưng bản thân nó không làm subnet đó trở thành "public"[cite: 1].
- D: Instance trong **public subnet** bắt buộc phải có **Public IP** để giao tiếp với internet[cite: 1].

---

### ❓ Question 4

Which statement correctly describes a **Security Group** (nhóm bảo mật) in an **Amazon VPC**?[cite: 1]

A. It is **stateless** (không ghi nhớ trạng thái).

B. It operates at the **subnet level** (cấp độ mạng con).

C. It is **stateful** (có ghi nhớ trạng thái).

D. It allows both **allow** (cho phép) and **deny** (từ chối) rules.

✅ **Đáp án đúng:** C[cite: 1]

### 🧠 Giải thích

- **Security Groups** là **stateful** (ghi nhớ trạng thái), nghĩa là nếu một yêu cầu đi vào được cho phép, lưu lượng phản hồi đi ra sẽ tự động được phép mà không cần kiểm tra lại quy tắc[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: **Network ACL** mới là dịch vụ **stateless**[cite: 1].
- B: **Security Group** hoạt động ở cấp độ **resource/interface** (giao diện mạng), không phải subnet[cite: 1].
- D: **Security Group** chỉ hỗ trợ quy tắc **Allow**; mặc định nó sẽ chặn (deny) mọi thứ không được liệt kê[cite: 1].

---

### ❓ Question 5

A **Network ACL** (danh sách kiểm soát truy cập mạng) operates at which level of the **AWS networking environment**?[cite: 1]

A. **Instance level** (cấp độ phiên bản).

B. **Subnet level** (cấp độ mạng con).

C. **Region level** (cấp độ khu vực).

D. **Account level** (cấp độ tài khoản).

✅ **Đáp án đúng:** B[cite: 1]

### 🧠 Giải thích

- **Network ACLs** hoạt động như một tường lửa cho toàn bộ **subnet**, kiểm soát mọi lưu lượng ra vào ranh giới của mạng con đó[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: **Security Group** mới là thành phần hoạt động ở cấp độ **instance**[cite: 1].
- C & D: VPC và Subnet nằm trong một **Region**, nhưng các công cụ lọc gói tin (filtering) chỉ hoạt động ở cấp mạng cục bộ (Subnet hoặc Interface)[cite: 1].

---

### ❓ Question 6

What is the primary benefit of using a **Gateway VPC Endpoint** (điểm cuối VPC cổng) for **Amazon S3**?[cite: 1]

A. It allows access to S3 from **on-premises** (mạng nội bộ) environments.

B. It provides a private connection to S3 that is **not billed** (không tính phí) and has **no throughput limits** (không giới hạn băng thông).

C. It uses **AWS PrivateLink** (kết nối mạng riêng tư) to provide private IPs.

D. It supports any AWS service across any **AWS Region**.

✅ **Đáp án đúng:** B[cite: 1]

### 🧠 Giải thích

- **Gateway VPC endpoints** cho **Amazon S3** và **DynamoDB** hoàn toàn miễn phí và không có giới hạn băng thông, đồng thời tích hợp trực tiếp vào **route table** của VPC[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: **Gateway Endpoints** không hỗ trợ truy cập từ on-premises; bạn cần **Interface Endpoints** cho việc này[cite: 1].
- C: **Interface Endpoints** mới sử dụng công nghệ **AWS PrivateLink**[cite: 1].
- D: Gateway Endpoints chỉ hỗ trợ **S3** và **DynamoDB** trong cùng một Region[cite: 1].

💡 **Mẹo nhớ nhanh:** **Gateway Endpoint** = Miễn phí + S3/DynamoDB[cite: 1].

---

### ❓ Question 7

What is the purpose of a **Bastion Host** (máy chủ trung chuyển) in an AWS network?[cite: 1]

A. To serve as a high-speed **database server**.

B. To provide an additional layer of **encryption** (mã hóa) for S3 buckets.

C. To allow administrators to **securely manage** (quản lý an toàn) instances in a **private subnet** from an external network.

D. To act as a **load balancer** (bộ cân bằng tải) for web traffic.

✅ **Đáp án đúng:** C[cite: 1]

### 🧠 Giải thích

- **Bastion host** là một instance được đặt trong **public subnet**, cho phép người quản trị kết nối vào (thường qua SSH hoặc RDP) để từ đó kết nối tiếp vào các máy chủ nằm sâu trong **private subnet**[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: Máy chủ DB thường đặt ở **private subnet**, không làm nhiệm vụ trung chuyển[cite: 1].
- B: Bảo mật S3 được quản lý qua **IAM** và **Bucket Policies**, không qua Bastion host[cite: 1].
- D: Đây là nhiệm vụ của **Elastic Load Balancing (ELB)**[cite: 1].

---

### ❓ Question 8

Which tool can capture information about **IP traffic** (lưu lượng IP) going to and from network interfaces in your VPC?[cite: 1]

A. **AWS CloudTrail**.

B. **VPC Flow Logs** (nhật ký luồng VPC).

C. **Amazon GuardDuty**.

D. **AWS Trusted Advisor**.

✅ **Đáp án đúng:** B[cite: 1]

### 🧠 Giải thích

- **VPC Flow Logs** ghi lại toàn bộ thông tin (metadata) về các gói tin IP ra vào giao diện mạng, giúp ích cho việc giám sát và khắc phục sự cố mạng[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: **CloudTrail** ghi lại các hoạt động **API** (ai đã làm gì), không ghi lại lưu lượng mạng thực tế[cite: 1].
- C: Đây là dịch vụ phát hiện đe dọa thông minh, có thể sử dụng dữ liệu từ Flow Logs nhưng bản thân nó không phải là công cụ ghi log[cite: 1].
- D: Đây là dịch vụ tư vấn tối ưu chi phí và bảo mật, không dùng để capture traffic[cite: 1].

---

### ❓ Question 9

How can you test whether a **source and destination** (nguồn và đích) resource in a VPC have connectivity?[cite: 1]

A. **Network Access Analyzer**.

B. **Traffic Mirroring**.

C. **Reachability Analyzer** (trình phân tích khả năng tiếp cận).

D. **VPC Flow Logs**.

✅ **Đáp án đúng:** C[cite: 1]

### 🧠 Giải thích

- **Reachability Analyzer** cho phép bạn kiểm tra kết nối giữa hai tài nguyên bất kỳ trong VPC bằng cách phân tích cấu hình đường đi mạng mà không cần gửi dữ liệu thực tế[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: Dùng để xác định các lỗ hổng truy cập mạng không mong muốn, không phải công cụ test kết nối A-B[cite: 1].
- B: Dùng để sao chép lưu lượng mạng để phân tích chuyên sâu (packet inspection)[cite: 1].
- D: Chỉ ghi lại lịch sử traffic đã xảy ra, không dùng để chủ động test khả năng kết nối[cite: 1].

---

### ❓ Question 10

According to the **AWS Well-Architected Framework**, what is a best practice for **IP subnet allocation** (phân bổ mạng con IP)?[cite: 1]

A. Use the smallest possible CIDR block to save costs.

B. Place all resources in a single large subnet for simplicity.

C. Ensure allocation accounts for **expansion** (mở rộng) and **availability** (tính sẵn sàng).

D. Assign only **Public IP addresses** to improve performance.

✅ **Đáp án đúng:** C[cite: 1]

### 🧠 Giải thích

- Trụ cột **Reliability** (độ tin cậy) khuyến nghị khi thiết kế mạng cần tính toán dải IP đủ rộng để có thể mở rộng quy mô trong tương lai và phân bổ qua nhiều **Availability Zones**[cite: 1].

### ❌ Vì sao đáp án khác sai?

- A: CIDR quá nhỏ sẽ gây khó khăn khi lượng traffic hoặc số lượng instance tăng trưởng[cite: 1].
- B: Việc gộp chung tất cả vào một subnet gây rủi ro bảo mật và không có tính sẵn sàng cao[cite: 1].
- D: Việc lạm dụng Public IP làm tăng nguy cơ bị tấn công từ internet[cite: 1].
