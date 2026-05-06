# Module 08: Connecting Networks

## 🧭 Overview

- **Mục tiêu của module:** Khám phá các phương pháp và kiến trúc mạng để kết nối các tài nguyên an toàn và hiệu quả.
- **Những gì sẽ học:**
  - Cách kết nối mạng **on-premises** (mạng nội bộ doanh nghiệp) với **AWS Cloud** (Đám mây AWS).
  - Cách kết nối nhiều **VPCs** (Mạng đám mây riêng ảo) trong AWS Cloud.
  - Sử dụng **VPC peering** (Kết nối ngang hàng VPC) để liên kết các mạng.
  - Cách mở rộng quy mô (scale) mạng VPC.
  - Áp dụng nguyên tắc **AWS Well-Architected Framework** (Khung kiến trúc tối ưu AWS) khi thiết kế kết nối mạng.

---

## 📘 Phần 1: Scaling your VPC network with AWS Transit Gateway

### 🔑 Ý chính

- 🌐 Giải quyết bài toán mở rộng mạng lưới với hàng trăm/hàng ngàn VPCs.
- 🔄 Sử dụng kiến trúc **Hub-and-spoke** (Trung tâm và vệ tinh).
- 🌍 Khả năng kết nối chéo giữa các **AWS Regions** (Khu vực) và **AWS accounts** (Tài khoản AWS).
- 🚦 Quản lý **Route table** (Bảng định tuyến) tập trung.

### 🧠 Giải thích

- **AWS Transit Gateway** (Cổng trung chuyển AWS): Là một **managed AWS service** (dịch vụ được quản lý bởi AWS) hoạt động như một **Regional router** (bộ định tuyến trung tâm cấp khu vực). Dịch vụ này cho phép kết nối các **VPCs** và mạng **on-premises** thông qua một điểm tập trung duy nhất dựa trên kiến trúc **hub-and-spoke**.
- **Auto-scaling** (Tự động mở rộng quy mô): Tự động điều chỉnh năng lực xử lý dựa trên khối lượng lưu lượng mạng (network traffic).
- **Transit gateway peering** (Kết nối ngang hàng cổng trung chuyển): Các Transit Gateway có thể được kết nối (peer) với nhau ngay cả khi chúng nằm ở các **AWS Regions** khác nhau hoặc thuộc các **AWS accounts** khác nhau.
- **Transit Gateway Flow Logs** (Nhật ký lưu lượng mạng): Tính năng cho phép xuất bản nhật ký lưu lượng đi qua gateway để phục vụ việc giám sát và bảo mật.
- **Pricing** (Chi phí): Chi phí được tính dựa trên số lượng kết nối mỗi giờ và lượng dữ liệu (throughput) truyền qua cổng.

⚖️ **So sánh kiến trúc kết nối nhiều VPCs:**

- **Full mesh architecture** (Kiến trúc dạng lưới toàn phần): Mỗi VPC phải kết nối trực tiếp với tất cả các VPC khác. Khi số lượng VPC tăng, số lượng kết nối tăng theo cấp số nhân gây ra sự phức tạp khổng lồ trong việc quản lý **Route table**.
- **Hub-and-spoke architecture** (Kiến trúc trung tâm và vệ tinh): Sử dụng **Transit Gateway** làm **Hub** (Trung tâm). Các VPC đóng vai trò là vệ tinh kết nối trực tiếp vào Hub. Dễ dàng mở rộng, quản lý cấu hình định tuyến đơn giản và gọn gàng.

### 📌 Ghi nhớ nhanh

- 🎯 Nhắc đến **AWS Transit Gateway**, hãy nghĩ ngay đến từ khóa **Hub-and-spoke** và **Centralized Router** (Bộ định tuyến tập trung).
- ⚙️ Hỗ trợ gắn kết (attach) hàng ngàn VPCs một cách dễ dàng.
- 📍 Giúp đơn giản hóa việc quản lý **Route table** ở quy mô lớn (các VPC chỉ cần trỏ traffic về Transit Gateway thay vì trỏ đến từng VPC riêng lẻ).
- 🔗 Cung cấp khả năng định tuyến liền mạch xuyên **Region** và xuyên **Account**.

### 💡 Ví dụ (nếu có)

```example
Tình huống: Một tập đoàn có 5 phòng ban (A, B, C, D, E), mỗi phòng ban có một VPC riêng và nằm rải rác ở nhiều AWS Account khác nhau. Tập đoàn muốn tất cả các phòng ban đều có thể giao tiếp mạng với nhau.

- Thay vì tạo 10 kết nối VPC Peering chéo nhau phức tạp:
  Tập đoàn triển khai 1 AWS Transit Gateway làm trung tâm (Hub).

- Cấu hình Route Table (Bảng định tuyến):
  Trong VPC của mỗi phòng ban, quản trị viên chỉ cần thêm 1 dòng định tuyến:
  Destination (Đích đến): 10.0.0.0/8 (Dải mạng chung) -> Target (Mục tiêu): tgw-id (ID của Transit Gateway).

Nhờ vậy, mọi dữ liệu nội bộ sẽ được gửi về Transit Gateway và Transit Gateway sẽ tự động biết cách phân phối đến đúng VPC đích một cách tập trung và an toàn.
```

---

## 📘 Phần 2: Connecting VPCs in AWS with VPC peering

### 🔑 Ý chính

- 🤝 Thiết lập kết nối **one-to-one** (một-đối-một) giữa hai VPC.
- 🔒 Dữ liệu di chuyển trong mạng nội bộ của AWS, không đi qua **public internet**.
- 🚫 Không hỗ trợ **Transitive peering** (kết nối bắc cầu).
- ↔️ Có thể kết nối xuyên **AWS accounts** (tài khoản) và **AWS Regions** (vùng).

### 🧠 Giải thích

- **VPC Peering Connection** (Kết nối ngang hàng VPC): Là một kết nối mạng cho phép bạn định tuyến lưu lượng (traffic) giữa hai VPC bằng địa chỉ **private IP**. Các instance trong hai VPC có thể giao tiếp với nhau như thể chúng nằm trong cùng một mạng.
- **Mesh architecture** (Kiến trúc dạng lưới): Vì mỗi kết nối là 1-1, nên khi có nhiều VPC cần thông tin với nhau, bạn phải tạo nhiều kết nối riêng biệt, tạo thành một mạng lưới phức tạp.
- **No transitive peering** (Không có tính chất bắc cầu): Đây là đặc điểm quan trọng nhất. Nếu VPC A kết nối với VPC B, và VPC B kết nối với VPC C, thì VPC A **không** thể gửi dữ liệu trực tiếp cho VPC C thông qua VPC B. Bạn phải tạo một kết nối peering trực tiếp giữa A và C.
- **Route tables** (Bảng định tuyến): Sau khi tạo kết nối, bạn phải cập nhật thủ công bảng định tuyến của cả hai VPC. Đích đến (**Destination**) là dải IP của VPC đối phương và mục tiêu (**Target**) là ID của kết nối peering.
- **Overlapping CIDR** (Dải IP trùng lặp): VPC Peering **không hoạt động** nếu hai VPC có dải địa chỉ IP trùng nhau. Trong trường hợp này, bạn phải dùng **AWS PrivateLink**.

### 📌 Ghi nhớ nhanh

- 💸 **Cost** (Chi phí): Việc thiết lập kết nối là **miễn phí**, nhưng bạn sẽ bị tính phí truyền dữ liệu (**Data transfer**) khi dữ liệu đi xuyên qua các **Availability Zones** hoặc **Regions**.
- 🛡️ **Security** (Bảo mật): Bạn có thể tham chiếu các **Security Groups** của VPC đối phương để quản lý quyền truy cập một cách linh hoạt.
- 📍 **Use cases** (Trường hợp sử dụng): Thích hợp để chia sẻ tài nguyên dùng chung như **File sharing VPC**, **Active Directory**, hoặc các dịch vụ khách hàng tập trung.

### 💡 Ví dụ (nếu có)

```example
Giả sử bạn có 2 VPC:
- VPC A: 10.1.0.0/16 (Chứa ứng dụng)
- VPC B: 10.2.0.0/16 (Chứa Cơ sở dữ liệu)

Để kết nối chúng bằng VPC Peering:
1. Gửi yêu cầu kết nối từ VPC A đến VPC B.
2. VPC B chấp nhận yêu cầu.
3. Cập nhật Route Table:
   - Tại VPC A: Thêm dòng 10.2.0.0/16 -> pcx-9999 (ID của kết nối).
   - Tại VPC B: Thêm dòng 10.1.0.0/16 -> pcx-9999.
Dữ liệu giờ đây có thể chạy trực tiếp giữa hai VPC an toàn.
```

---

## 📘 Phần 3: Connecting to your remote network with AWS Site-to-Site VPN

### 🔑 Ý chính

- 🔐 Thiết lập kết nối bảo mật giữa mạng **on-premises** (mạng nội bộ) và **AWS VPC**.
- 🌐 Sử dụng giao thức **IPsec** (Bộ giao thức bảo mật Internet) để mã hóa dữ liệu truyền qua **public internet** (mạng Internet công cộng).
- 🏗️ Bao gồm hai thành phần chính: **Customer gateway** và **Virtual private gateway** (hoặc **Transit gateway**).
- ⚖️ Chi phí thấp và thời gian triển khai nhanh hơn so với kết nối vật lý riêng biệt.

### 🧠 Giải thích

- **Customer gateway** (Cổng khách hàng): Là một thiết bị phần cứng hoặc ứng dụng phần mềm nằm ở phía mạng **on-premises** của bạn.
- **Virtual private gateway** (Cổng riêng ảo - VGW): Là thành phần neo đậu phía AWS, được gắn vào một **VPC** duy nhất để nhận kết nối VPN.
- **IPsec VPN tunnels** (Đường hầm VPN IPsec): Mỗi kết nối VPN tạo ra **2 đường hầm** chạy song song qua các **Availability Zones** khác nhau. Điều này đảm bảo tính **High availability** (sẵn sàng cao); nếu một đường hầm gặp sự cố, lưu lượng sẽ tự động chuyển sang đường hầm còn lại.
- **AWS VPN CloudHub**: Một mô hình cho phép nhiều trang web từ xa (nhiều mạng on-premises khác nhau) kết nối với nhau và kết nối với VPC thông qua một **Virtual private gateway** duy nhất.
- **AWS Global Accelerator** (Bộ tăng tốc toàn cầu): Có thể được sử dụng để tối ưu hóa hiệu suất bằng cách định tuyến lưu lượng VPN qua mạng lưới hạ tầng toàn cầu của AWS thay vì đi hoàn toàn qua Internet công cộng.

📊 **So sánh điểm gắn kết VPN:**

| Đặc điểm        | Virtual private gateway (VGW)   | Transit gateway (TGW)                           |
| :-------------- | :------------------------------ | :---------------------------------------------- |
| **Phạm vi**     | Kết nối tới **1 VPC** duy nhất. | Kết nối tới **nhiều VPC** cùng lúc.             |
| **Độ phức tạp** | Đơn giản cho kết nối nhỏ.       | Phù hợp cho mạng lưới quy mô lớn (**Scaling**). |
| **Tính năng**   | Cơ bản.                         | Hỗ trợ phân đoạn mạng và định tuyến linh hoạt.  |

### 📌 Ghi nhớ nhanh

- ⚡ **High availability**: Luôn có **2 tunnels** cho mỗi kết nối để dự phòng.
- 💰 **Pricing**: Tính phí theo mỗi giờ kết nối (**VPN connection-hour**) và phí truyền dữ liệu.
- 🚀 **Speed**: Tốc độ phụ thuộc vào băng thông Internet của bạn và giới hạn của thiết bị VPN.
- 🛠️ **Setup**: Thường là lựa chọn đầu tiên khi cần kết nối mạng hybrid nhanh chóng.

### 💡 Ví dụ (nếu có)

```example
Một công ty khởi nghiệp muốn kết nối máy chủ dữ liệu tại văn phòng với ứng dụng web chạy trên AWS:
1. Họ cấu hình bộ định tuyến tại văn phòng làm Customer gateway.
2. Họ tạo một Virtual private gateway trên AWS và gắn vào VPC.
3. AWS cung cấp cấu hình cho 2 IPsec tunnels.
4. Dữ liệu nhạy cảm được truyền đi từ văn phòng lên đám mây thông qua Internet nhưng đã được mã hóa hoàn toàn, đảm bảo an toàn thông tin.
```

---

## 📘 Phần 4: Connecting to your remote network with AWS Direct Connect

### 🔑 Ý chính

- 🔌 Cung cấp kết nối mạng **dedicated** (chuyên dụng) và **private** (riêng tư) giữa văn phòng và AWS.
- 🏢 Sử dụng kết nối cáp quang vật lý thông qua chuẩn **VLAN** (Mạng cục bộ ảo).
- 📈 Đảm bảo **consistent network experience** (trải nghiệm mạng ổn định) với băng thông và hiệu suất có thể dự đoán được.
- 🛡️ Tăng cường bảo mật và đáp ứng các yêu cầu tuân thủ (**compliance**) do không đi qua Internet công cộng.

### 🧠 Giải thích

- **AWS Direct Connect** (Kết nối trực tiếp AWS): Thay vì sử dụng Internet, Direct Connect thiết lập một đường truyền vật lý riêng biệt. Điều này giúp giảm độ trễ (**latency**) và tăng tốc độ truyền tải dữ liệu lớn.
- **Virtual Interface - VIF** (Giao diện ảo): Để sử dụng kết nối này, bạn cần cấu hình các giao diện ảo tùy theo mục đích:
  - **Private virtual interface** (Giao diện ảo riêng tư): Dùng để kết nối đến **Virtual private gateway** của một VPC cụ thể.
  - **Public virtual interface** (Giao diện ảo công cộng): Dùng để truy cập các dịch vụ AWS có endpoint công khai (ví dụ: **Amazon S3**, **DynamoDB**) mà không cần qua Internet.
  - **Transit virtual interface** (Giao diện ảo trung chuyển): Dùng để kết nối đến **Transit Gateway** thông qua một **Direct Connect gateway**, giúp một đường truyền DX có thể tiếp cận hàng ngàn VPC.
- **High Availability & Resiliency** (Tính sẵn sàng và bền bỉ cao):
  - **Primary + Backup**: Sử dụng Direct Connect làm đường truyền chính và **AWS Site-to-Site VPN** làm đường truyền dự phòng (tiết kiệm chi phí).
  - **Maximum Resiliency**: Sử dụng nhiều kết nối Direct Connect tại các địa điểm (**DX locations**) khác nhau để loại bỏ điểm yếu duy nhất.

### 📌 Ghi nhớ nhanh

- 🚀 **Performance**: Tốt hơn VPN vì không bị ảnh hưởng bởi sự tắc nghẽn của Internet.
- 📉 **Cost**: Phí dữ liệu truyền ra (**Data Transfer Out**) thường rẻ hơn so với truyền qua Internet, phù hợp cho **Large datasets** (tập dữ liệu lớn).
- 🔗 **Direct Connect Gateway**: Là thành phần bắt buộc nếu muốn dùng 1 kết nối DX để liên kết với các VPC ở các **Regions** khác nhau.

### 💡 Ví dụ (nếu có)

```example
Một ngân hàng cần chuyển 100TB dữ liệu sao lưu mỗi ngày lên Amazon S3:
- Nếu dùng Internet: Tốc độ không ổn định, tốn phí truyền tải cao và rủi ro bảo mật.
- Giải pháp: Thiết lập 1 kết nối AWS Direct Connect 10Gbps.
- Cấu hình: Tạo một Public VIF để đẩy dữ liệu trực tiếp vào S3.
Kết quả: Tốc độ luôn duy trì ở mức tối đa, dữ liệu đi trong mạng riêng của AWS, chi phí trên mỗi GB dữ liệu giảm đáng kể.
```

---

## 📝 Knowledge Check

### ❓ Question 1

What is the simplest way to connect 100 virtual private clouds (VPCs) together?

A. Chain VPCs together by using VPC peering.

B. Connect the VPCs to AWS Transit Gateway.

C. Connect each VPC to all the other VPCs by using VPC peering.

D. Create a hub-and-spoke network by using AWS VPN CloudHub.

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **AWS Transit Gateway** (**Cổng trung chuyển AWS**) hoạt động như một **Regional router** (**bộ định tuyến khu vực**) trung tâm.
- Nó được thiết kế đặc biệt để đơn giản hóa việc kết nối hàng ngàn **VPCs** thông qua kiến trúc **hub-and-spoke** (**trung tâm và vệ tinh**), thay vì phải quản lý hàng trăm kết nối riêng lẻ.

### ❌ Vì sao đáp án khác sai?

- **A & C: VPC peering**: Việc tạo hàng trăm kết nối đối đầu (**mesh**) sẽ cực kỳ phức tạp để quản lý bảng định tuyến và không hỗ trợ kết nối bắc cầu (**transitive**).
- **D: AWS VPN CloudHub**: Dùng để kết nối nhiều mạng **on-premises** (**nội bộ**) với nhau qua một **Virtual Private Gateway**, không phải là cách đơn giản nhất để kết nối các VPC.

💡 **Mẹo nhớ nhanh:** Cứ thấy kết nối **nhiều VPC** (số lượng lớn) -> Nghĩ ngay đến **Transit Gateway**.

---

### ❓ Question 2

A company needs network traffic to flow between an AWS account in one Region to another account in a different Region. What should they set up between the transit gateways in each region?

A. AWS Direct Connect

B. Transit gateway peering attachment

C. AWS Site-to-Site VPN

D. AWS PrivateLink

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Transit gateway peering attachment** (**Kết nối ngang hàng cổng trung chuyển**) cho phép kết nối hai **Transit Gateways** ở các **Regions** (**Khu vực**) khác nhau hoặc các **Accounts** (**Tài khoản**) khác nhau.
- Điều này giúp lưu lượng mạng đi qua hạ tầng riêng của AWS, đảm bảo bảo mật và hiệu suất cao.

### ❌ Vì sao đáp án khác sai?

- **A: Direct Connect**: Dùng kết nối mạng nội bộ với AWS, không phải kết nối giữa hai TGW.
- **C: Site-to-Site VPN**: Có thể kết nối nhưng không hiệu quả và ổn định bằng peering nội bộ của TGW.
- **D: PrivateLink**: Dùng để truy cập dịch vụ, không phải để kết nối toàn bộ hạ tầng mạng của hai TGW.

---

### ❓ Question 3

A company has two virtual private clouds (VPCs). VPC A has a Classless Inter-Domain Routing (CIDR) block of 10.1.0.0/16. VPC B has CIDR block of 10.2.0.0/16. Both VPCs belong to the same AWS account. What is the simplest way to connect the two VPCs so that they can route all traffic between them?

A. AWS Direct Connect

B. VPC endpoints

C. AWS Site-to-Site VPN

D. VPC peering

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **VPC peering** (**Kết nối ngang hàng VPC**) là cách đơn giản và trực tiếp nhất để kết nối **02 VPC** không trùng lặp dải IP (**non-overlapping CIDR**).
- Nó tạo ra một kết nối **one-to-one** (**một-đối-một**) để các instance giao tiếp bằng **private IP**.

### ❌ Vì sao đáp án khác sai?

- **A: Direct Connect**: Quá phức tạp và đắt đỏ chỉ để kết nối 2 VPC trong cùng account.
- **B: VPC endpoints**: Chỉ dùng để truy cập dịch vụ cụ thể (như S3), không dùng để định tuyến toàn bộ lưu lượng giữa 2 VPC.
- **C: Site-to-Site VPN**: Yêu cầu thiết lập cổng và đường hầm, phức tạp hơn peering.

---

### ❓ Question 4

Systems in a secure subnet in a virtual private cloud (VPC) must access a bucket in Amazon S3. Which solutions stop traffic from crossing the internet? (Select TWO.)

A. Create a VPC peering connection to Amazon S3.

B. Use the private IP address of Amazon S3.

C. Use VPC interface endpoints.

D. Use a private IP address for the system.

E. Create a VPC gateway endpoint for Amazon S3.

✅ **Đáp án đúng:** C và E

### 🧠 Giải thích

- **VPC gateway endpoint** (**Điểm cuối cổng VPC**): Là giải pháp truyền thống và miễn phí để kết nối VPC tới **Amazon S3** qua mạng nội bộ AWS.
- **VPC interface endpoints** (**Điểm cuối giao diện VPC**): Một tùy chọn khác sử dụng **PrivateLink**, giúp truy cập S3 qua **private IP** trong VPC. Cả hai đều giúp lưu lượng không bao giờ đi qua **internet**.

### ❌ Vì sao đáp án khác sai?

- **A**: Không thể tạo Peering trực tiếp với dịch vụ S3.
- **B**: S3 không cung cấp một IP tĩnh duy nhất để bạn tự cấu hình.
- **D**: Chỉ có IP riêng là chưa đủ, cần có cơ chế định tuyến nội bộ tới S3.

💡 **Mẹo nhớ nhanh:** Truy cập **S3/DynamoDB** nội bộ -> Tìm từ khóa **Gateway Endpoint** hoặc **Interface Endpoint**.

---

### ❓ Question 5

A company has three virtual private clouds (VPCs). VPCs A, B, and C have CIDR blocks that do not overlap. Both A and C have separate VPC peering connections with B. However, A cannot communicate with C. What is the simplest and most cost-effective way to enable full communication between A and C?

A. Link all three VPCs through a transit VPC, and route all traffic through the transit VPC.

B. Add a peering connection between A and C, and route traffic between A and C through the peering connection.

C. Add routes to B to enable traffic between A and C through B.

D. Create VPC endpoints in A and C for the individual hosts that need to communicate with each other.

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- Do **VPC peering** không hỗ trợ **transitive peering** (**kết nối bắc cầu**), nên A không thể mượn B để nói chuyện với C.
- Giải pháp đơn giản nhất cho 3 VPC là tạo thêm một kết nối peering trực tiếp giữa A và C.

### ❌ Vì sao đáp án khác sai?

- **A: Transit VPC**: Tốn kém và phức tạp hơn so với chỉ thêm 1 kết nối peering.
- **C: Route qua B**: Sẽ thất bại vì AWS cấm hành vi bắc cầu này trong VPC peering.
- **D**: VPC endpoints không dùng cho mục đích kết nối liên thông mạng giữa các host trong 2 VPC khác nhau.

---

### ❓ Question 6

Because of a natural disaster, a company moved a secondary data center to a temporary facility with internet connectivity. It needs a secure connection to the company's virtual private cloud (VPC) that must be operational as soon as possible. The data center will move again in 2 weeks. Which option meets the requirements?

A. AWS Direct Connect

B. VPC endpoints

C. AWS Site-to-Site VPN

D. VPC peering

✅ **Đáp án đúng:** C

### 🧠 Giải thích

- **AWS Site-to-Site VPN** có ưu điểm là **operational as soon as possible** (**triển khai cực nhanh**) vì nó chạy trên nền internet sẵn có.
- Do đây là nhu cầu tạm thời (2 tuần), VPN là lựa chọn tối ưu về thời gian và chi phí.

### ❌ Vì sao đáp án khác sai?

- **A: Direct Connect**: Mất nhiều tuần/tháng để kéo cáp vật lý, không kịp cho yêu cầu "nhanh nhất có thể".
- **B & D**: Không dùng để kết nối mạng **on-premises** (**tại chỗ**) với VPC.

---

### ❓ Question 7

A company is concerned about internet disruptions. It wants to efficiently route traffic from their on-premises network to an AWS edge location close to their customer gateway device. What should they use?

A. AWS Global Accelerator

B. AWS Transit Gateway

C. AWS VPN CloudHub

D. AWS Direct Connect

✅ **Đáp án đúng:** A

### 🧠 Giải thích

- **AWS Global Accelerator** sử dụng mạng lưới **Edge locations** (**Điểm biên**) toàn cầu của AWS để tối ưu hóa đường truyền.
- Nó giúp lưu lượng từ **Customer gateway** đi vào mạng AWS sớm nhất có thể, tránh các rắc rối và gián đoạn của internet công cộng.

### ❌ Vì sao đáp án khác sai?

- **B**: Dùng để kết nối các VPC.
- **C**: Dùng kết nối nhiều chi nhánh về một trung tâm.
- **D**: Direct Connect là đường truyền riêng, không liên quan đến việc tối ưu hóa qua Edge locations như Global Accelerator.

---

### ❓ Question 8

A company is implementing a system to back up on-premises systems to AWS. Which network connectivity method provides a solution with the most consistent performance?

A. Virtual private cloud (VPC) endpoints

B. AWS Direct Connect

C. AWS Site-to-Site VPN

D. Virtual private cloud (VPC) peering

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **AWS Direct Connect** cung cấp một kết nối vật lý riêng biệt, không đi chung đường internet.
- Điều này mang lại **most consistent performance** (**hiệu suất ổn định nhất**) và băng thông dự đoán được, rất quan trọng cho việc sao lưu dữ liệu lớn.

### ❌ Vì sao đáp án khác sai?

- **A & D**: Không dùng để kết nối từ on-premises lên AWS.
- **C: VPN**: Chạy qua internet nên hiệu suất bị phụ thuộc vào nhà mạng, không ổn định bằng Direct Connect.

---

### ❓ Question 9

A company uses a single AWS Direct Connect connection between their on-premises network and their virtual private cloud (VPC). They want to ensure that the network connectivity is highly available by adding a backup connection. Which network connectivity method provides the most cost-effective solution for the backup connection?

A. An on-demand AWS Site-to-Site VPN connection across the internet

B. Another AWS Direct Connect connection through the same Direct Connect location

C. An on-demand AWS Client VPN connection across the internet

D. Another AWS Direct Connect connection through a different Direct Connect location

✅ **Đáp án đúng:** A

### 🧠 Giải thích

- Sử dụng **Site-to-Site VPN** làm dự phòng cho **Direct Connect** là kiến trúc phổ biến và **cost-effective** (**tối ưu chi phí**) nhất.
- Bạn chỉ trả tiền VPN theo giờ và nó tận dụng đường internet có sẵn làm phương án dự phòng khi Direct Connect gặp sự cố.

### ❌ Vì sao đáp án khác sai?

- **B & D**: Thêm một đường Direct Connect nữa sẽ rất đắt đỏ.
- **C: Client VPN**: Dùng cho nhân viên kết nối từ laptop, không dùng cho kết nối hạ tầng data center.

---

### ❓ Question 10

A company is connecting a virtual private cloud (VPC) to multiple on-premises data centers using a virtual private network (VPN). Which implementation ensures resiliency and predictable bandwidth requirements?

A. Implement AWS Transit Gateway to connect to each on-premises data center.

B. Establish multiple Border Gateway Protocol (BGP) sessions for each VPC to create connectivity to multiple VPCs across multiple AWS Regions.

C. Use a many-to-many mesh topology, such as Amazon VPC peering.

D. Implement Direct Connect as the primary connection and use the VPN as a secondary failover connection from each data center.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Direct Connect** đáp ứng yêu cầu **predictable bandwidth** (**băng thông dự đoán được**) do là đường truyền riêng.
- **VPN** đóng vai trò là **secondary failover** (**dự phòng thất bại**) đảm bảo tính **resiliency** (**khả năng phục hồi/bền bỉ**). Đây là mô hình chuẩn cho các hệ thống quan trọng.

### ❌ Vì sao đáp án khác sai?

- **A**: TGW giúp quản lý nhưng không tự đảm bảo "băng thông dự đoán được" nếu chỉ dùng VPN đơn thuần.
- **B**: BGP là giao thức định tuyến, không phải giải pháp kết nối vật lý đảm bảo băng thông.
- **C**: VPC peering không dùng để kết nối với data center.

💡 **Mẹo nhớ nhanh:** Keyword **"Predictable performance/bandwidth"** -> Chọn **Direct Connect**. Keyword **"Resiliency/Backup"** -> Chọn thêm **VPN**.

---
