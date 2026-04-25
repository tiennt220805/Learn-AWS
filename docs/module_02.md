# Module 02: Introducing Cloud Architecting

## 🧭 Overview

Module này cung cấp cái nhìn tổng quan về việc thiết kế hệ thống trên nền tảng đám mây, bao gồm:

- Định nghĩa **Cloud Architecture** (Kiến trúc đám mây).
- Giới thiệu **AWS Well-Architected Framework** (Khung thiết kế tối ưu của AWS).
- Các **Best practices** (Thực hành tốt nhất) để xây dựng giải pháp.
- Tìm hiểu về **AWS Global Infrastructure** (Cơ sở hạ tầng toàn cầu của AWS).

---

## 📘 Phần 1: Cloud Architecting là gì?

### 🔑 Ý chính

- **Cloud Architecture** (Kiến trúc đám mây) là việc áp dụng các đặc tính của đám mây vào một giải pháp cụ thể.
- Giải pháp này sử dụng các dịch vụ và tính năng của đám mây để đáp ứng nhu cầu kỹ thuật và các **Business use cases** (Tình huống sử dụng thực tế của doanh nghiệp).
- Mục tiêu là xây dựng hệ thống: **Highly available** (Tính sẵn sàng cao), **Scalable** (Có khả năng mở rộng) và **Reliable** (Đáng tin cậy).

### 🧠 Giải thích

Quá trình thiết kế kiến trúc đám mây liên quan đến việc kết nối các thành phần rời rạc thành một hệ thống thống nhất. Vai trò của **Cloud Architect** (Kiến trúc sư đám mây) được chia thành 3 giai đoạn chính:

1.  **Plan** (Lập kế hoạch):
    - Thiết lập chiến lược kỹ thuật đám mây cùng với các nhà lãnh đạo doanh nghiệp.
    - Phân tích giải pháp dựa trên nhu cầu và yêu cầu thực tế của tổ chức.
2.  **Research** (Nghiên cứu):
    - Tìm hiểu các thông số kỹ thuật của dịch vụ đám mây và yêu cầu của **Workload** (Khối lượng công việc).
    - Xem xét các kiến trúc hiện có và thiết kế các giải pháp **Prototype** (Nguyên mẫu).
3.  **Build** (Xây dựng):
    - Thiết kế **Transformation roadmap** (Lộ trình chuyển đổi) với các cột mốc và người phụ trách cụ thể.
    - Quản lý quá trình **Adoption** (Áp dụng) và **Migration** (Di chuyển dữ liệu/hệ thống lên mây).

### 📌 Ghi nhớ nhanh

| Vai trò           | Trách nhiệm chính                                            |
| :---------------- | :----------------------------------------------------------- |
| **Customer**      | Người đưa ra quyết định dựa trên nhu cầu kinh doanh.         |
| **Architect**     | Người thiết kế sơ đồ hệ thống và đưa ra chiến lược kỹ thuật. |
| **Building crew** | Đội ngũ triển khai và thực hiện việc xây dựng thực tế.       |

### 💡 Ví dụ (nếu có)

```example
Một doanh nghiệp thương mại điện tử cần hệ thống không bị sập khi có đợt giảm giá lớn.
Cloud Architect sẽ thiết kế một giải pháp sử dụng:
- Amazon EC2: Để chạy máy chủ web.
- Auto Scaling: Để tự động thêm máy chủ khi lượng người truy cập tăng đột biến.
- Amazon S3: Để lưu trữ hình ảnh sản phẩm một cách an toàn.
```

---

## 📘 Phần 2: AWS Well-Architected Framework

### 🔑 Ý chính

- **AWS Well-Architected Framework** (Khung thiết kế tối ưu của AWS) cung cấp một phương pháp nhất quán để đánh giá các kiến trúc hệ thống và đưa ra các hướng dẫn để triển khai thiết kế.
- Framework được tổ chức thành **6 Pillars** (6 trụ cột) chính.
- Mỗi trụ cột chứa một bộ các câu hỏi nền tảng giúp kiểm tra xem một kiến trúc có tuân thủ các **Cloud best practices** (Thực hành tốt nhất về đám mây) hay không.
- **AWS WA Tool** (Công cụ đánh giá kiến trúc) giúp xem xét trạng thái các **Workloads** (Khối lượng công việc) và cung cấp kế hoạch hành động cụ thể.

### 🧠 Giải thích

Hệ thống được xây dựng dựa trên 6 trụ cột cốt lõi để đảm bảo sự ổn định và hiệu quả:

1.  **Operational Excellence** (Vận hành xuất sắc):
    - Chạy và giám sát các hệ thống để cung cấp giá trị kinh doanh.
    - Liên tục cải tiến các quy trình và thủ tục hỗ trợ.
    - Coi toàn bộ khối lượng công việc là **Code** (Mã nguồn).
2.  **Security** (Bảo mật):
    - Bảo vệ thông tin, hệ thống và tài sản.
    - Xây dựng nền tảng danh tính mạnh mẽ và duy trì khả năng truy vết (**Traceability**).
    - Áp dụng bảo mật ở tất cả các tầng (**Security at all layers**).
3.  **Reliability** (Độ tin cậy):
    - Khả năng phục hồi nhanh chóng sau các sự cố gián đoạn.
    - Tự động đáp ứng nhu cầu tính toán (**Compute demand**) và giảm thiểu các tác động tiêu cực.
4.  **Performance Efficiency** (Hiệu quả hiệu năng):
    - Sử dụng tài nguyên máy tính hiệu quả để đáp ứng yêu cầu.
    - Lựa chọn và duy trì các loại tài nguyên tối ưu cho khối lượng công việc.
5.  **Cost Optimization** (Tối ưu hóa chi phí):
    - Tránh các chi phí không cần thiết.
    - Sử dụng các **Managed services** (Dịch vụ được quản lý) để giảm thiểu chi phí vận hành máy chủ.
6.  **Sustainability** (Bền vững):
    - Giảm thiểu tác động đến môi trường (ví dụ: tiêu thụ điện năng).
    - Tối đa hóa việc sử dụng tài nguyên (**Maximizing utilization**) để tránh lãng phí.

### 📌 Ghi nhớ nhanh

| Trụ cột                       | Trọng tâm chính                           |
| :---------------------------- | :---------------------------------------- |
| **Operational Excellence** ⚙️ | Quy trình, giám sát và cải tiến liên tục. |
| **Security** 🛡️               | Bảo vệ dữ liệu, quản lý quyền truy cập.   |
| **Reliability** 🔄            | Khả năng phục hồi, duy trì sự ổn định.    |
| **Performance Efficiency** ⚡ | Tốc độ, lựa chọn tài nguyên phù hợp.      |
| **Cost Optimization** 💰      | Tiết kiệm tiền, loại bỏ lãng phí.         |
| **Sustainability** 🌱         | Bảo vệ môi trường, tối ưu tài nguyên.     |

### 💡 Ví dụ (nếu có)

```example
Sử dụng AWS Well-Architected Tool để kiểm tra một ứng dụng Web:
- Công cụ phát hiện bạn chỉ dùng 1 Availability Zone (Vi phạm Reliability).
- Nó sẽ đưa ra Action Plan: "Hãy triển khai thêm máy chủ ở Zone thứ 2 để đảm bảo hệ thống vẫn chạy nếu 1 Zone gặp sự cố".
```

---

## 📘 Phần 3: Best practices for building solutions on AWS

### 🔑 Ý chính

- **Design trade-offs** (Đánh đổi trong thiết kế): Cân nhắc giữa các yếu tố (ví dụ: đánh đổi độ nhất quán để lấy hiệu suất/độ trễ thấp) và đưa ra quyết định dựa trên dữ liệu thực nghiệm.
- **Scalability** (Khả năng mở rộng): Đảm bảo kiến trúc có thể tự động thay đổi để đáp ứng sự biến động của nhu cầu người dùng.
- **Automation** (Tự động hóa): Sử dụng mã nguồn để thiết lập hạ tầng nhằm giảm thiểu sai sót từ con người.
- **Loose coupling** (Liên kết lỏng lẻo): Xây dựng các thành phần độc lập để lỗi ở một bộ phận không làm sụp đổ toàn bộ hệ thống.
- **Design services, not servers** (Thiết kế dịch vụ, không phải máy chủ): Ưu tiên sử dụng các dịch vụ được quản lý (**Managed services**) và không máy chủ (**Serverless**).

### 🧠 Giải thích

Việc xây dựng giải pháp tối ưu trên AWS cần tuân thủ các nguyên tắc cốt lõi sau:

1.  **Infrastructure as Code - IaC** (Hạ tầng dưới dạng mã):
    - Sử dụng các mẫu (**Templates**) để triển khai tài nguyên tự động.
    - Giúp sao chép các môi trường giống hệt nhau một cách nhanh chóng.
2.  **Disposable resources** (Tài nguyên có thể thay thế):
    - Coi tài nguyên là tạm thời. Thay vì sửa lỗi trên máy chủ cũ, hãy triển khai máy chủ mới với cấu hình đúng.
    - **Stop** các tài nguyên không sử dụng để tiết kiệm chi phí.
3.  **Removing Single points of failure** (Loại bỏ điểm yếu duy nhất):
    - Sử dụng cơ chế **Replication** (Sao chép dữ liệu) sang máy chủ dự phòng (**Standby**).
    - Khi máy chủ chính lỗi, hệ thống tự động chuyển sang máy chủ phụ để duy trì dịch vụ.
4.  **Caching** (Bộ nhớ đệm):
    - Lưu trữ dữ liệu truy cập thường xuyên tại các **Edge node** (Nốt mạng biên) để giảm độ trễ cho người dùng.
5.  **Security at all layers** (Bảo mật ở mọi lớp):
    - Áp dụng nguyên tắc **Least privilege** (Quyền hạn tối thiểu).
    - Mã hóa dữ liệu cả khi đang truyền và khi đang lưu trữ (**Encrypt data in transit and at rest**).

### 📌 Ghi nhớ nhanh

| Mô hình thiết kế      | Đặc điểm                                                              | Trạng thái                      |
| :-------------------- | :-------------------------------------------------------------------- | :------------------------------ |
| **Tight Coupling** 🔗 | Các bộ phận phụ thuộc nhau chặt chẽ; khó mở rộng.                     | **Anti-pattern** (Nên tránh)    |
| **Loose Coupling** 🔓 | Sử dụng **Load Balancer** hoặc **Queue** để tách biệt; linh hoạt cao. | **Best practice** (Khuyên dùng) |

### 💡 Ví dụ (nếu có)

```example
Sử dụng Caching với Amazon CloudFront:
- Lần đầu (First request): Người dùng yêu cầu file "dog.jpg", hệ thống lấy từ S3 bucket gốc.
- Lần sau (Subsequent requests): File đã có sẵn tại Edge node gần người dùng nhất.
-> Kết quả: Tải ảnh nhanh hơn, giảm tải cho server gốc và tiết kiệm chi phí băng thông.
```

---

## 📘 Phần 4: AWS Global Infrastructure

### 🔑 Ý chính

- **AWS Global Infrastructure** (Cơ sở hạ tầng toàn cầu của AWS) được thiết kế để mang lại hiệu suất cao, độ trễ thấp và tính sẵn sàng cao nhất.
- Các thành phần cốt lõi bao gồm: **AWS Regions** (Vùng AWS), **Availability Zones - AZs** (Vùng sẵn sàng), và **Points of Presence - PoPs** (Điểm hiện diện).
- Việc lựa chọn vị trí đặt tài nguyên dựa trên: **Compliance** (Tính tuân thủ pháp lý), **Latency** (Độ trễ), **Cost** (Chi phí) và **Service availability** (Sự sẵn có của dịch vụ).

### 🧠 Giải thích

Hạ tầng toàn cầu của AWS được phân cấp từ quy mô lớn đến nhỏ để tối ưu hóa việc phân phối ứng dụng:

1.  **AWS Regions** (Vùng AWS):
    - Là một khu vực địa lý cụ thể trên thế giới (ví dụ: Tokyo, Singapore).
    - Mỗi Region hoàn toàn độc lập và cách biệt với các Region khác.
    - Dữ liệu không bao giờ tự động rời khỏi một Region trừ khi khách hàng thiết lập lệnh sao chép.
2.  **Availability Zones - AZs** (Vùng sẵn sàng):
    - Mỗi Region bao gồm ít nhất **hai hoặc nhiều hơn** các AZ.
    - Một AZ bao gồm một hoặc nhiều **Data centers** (Trung tâm dữ liệu) vật lý.
    - Các AZ được kết nối với nhau bằng mạng cáp quang tốc độ cao, độ trễ cực thấp nhưng tách biệt về nguồn điện và hạ tầng để tránh lỗi dây chuyền.
3.  **AWS Local Zones** (Vùng cục bộ):
    - Là phần mở rộng của một Region, đặt các dịch vụ tính toán và lưu trữ gần các trung tâm dân cư hoặc công nghiệp lớn để hỗ trợ các ứng dụng yêu cầu độ trễ dưới 10 mili giây.
4.  **Points of Presence - PoPs** (Điểm hiện diện):
    - Bao gồm **Edge Locations** (Điểm biên) và **Regional Edge Caches** (Bộ nhớ đệm biên vùng).
    - Được sử dụng bởi dịch vụ **Amazon CloudFront** (CDN) để phân phối nội dung đến người dùng cuối với tốc độ nhanh nhất bằng cách lưu trữ bản sao dữ liệu (Caching) gần họ.

### 📌 Ghi nhớ nhanh

| Khái niệm                | Quy mô           | Mục đích chính                                                  |
| :----------------------- | :--------------- | :-------------------------------------------------------------- |
| **Region** 🌎            | Toàn cầu         | Tuân thủ pháp lý dữ liệu và giảm độ trễ khu vực.                |
| **Availability Zone** 🏢 | Trong một Region | Đảm bảo **Fault isolation** (Cách ly lỗi) và tính sẵn sàng cao. |
| **Edge Location** 📍     | Khắp thế giới    | Giảm độ trễ bằng cách **Caching** nội dung gần người dùng.      |

### 💡 Ví dụ (nếu có)

```example
Nếu bạn xây dựng một ứng dụng cho người dùng tại Việt Nam:
- Chọn Region: Singapore (ap-southeast-1) vì gần nhất về địa lý để có độ trễ thấp.
- Thiết lập High Availability: Chạy máy chủ trên ít nhất 2 AZs khác nhau (ví dụ: 1a và 1b) để nếu 1 AZ gặp sự cố thiên tai, ứng dụng vẫn hoạt động.
- Tối ưu tốc độ: Sử dụng Edge Location tại TP.HCM hoặc Hà Nội để cache các hình ảnh, video của ứng dụng.
```

---

## 📝 Knowledge Check

### ❓ Question 1

Which is the best definition of cloud architecture?

A. Combining frontend and backend software and components to create highly available and scalable web services that meet the needs of an organization

B. Relocating traditional on-premises data centers to internet-accessible data centers that a vendor manages

C. Designing applications in cloud-based, shared IT infrastructure by using virtual machines and fault-tolerant data stores in the cloud

D. Applying cloud characteristics to a solution that uses cloud services and features to meet technical and business requirements

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Cloud architecture** (Kiến trúc đám mây) là thực hành áp dụng các đặc tính của đám mây vào một giải pháp.
- Giải pháp này sử dụng các dịch vụ và tính năng của đám mây để đáp ứng các nhu cầu kỹ thuật và các **Business use cases** (tình huống sử dụng của doanh nghiệp).

### ❌ Vì sao đáp án khác sai?

- A: Chỉ tập trung vào việc kết hợp phần mềm, chưa bao hàm việc áp dụng các đặc tính cốt lõi của đám mây vào toàn bộ giải pháp.
- B: Đây mô tả quá trình **Migration** (di chuyển) hạ tầng cũ lên đám mây, không phải định nghĩa về kiến trúc.
- C: Đây là một ví dụ về việc sử dụng hạ tầng chia sẻ, nhưng thiếu tính chiến lược trong việc đáp ứng yêu cầu kinh doanh cụ thể.

---

### ❓ Question 2

The AWS Well-Architected Framework has six pillars. Three of the pillars are security, operational excellence, and sustainability. What are two of the other pillars of the Well-Architected Framework? (Select TWO.)

A. Risk management

B. Cost optimization

C. Governance

D. Reliability

E. Privacy

✅ **Đáp án đúng:** B và D

### 🧠 Giải thích

- **Cost optimization** (Tối ưu hóa chi phí) là một trong 6 trụ cột, tập trung vào việc tránh các chi phí không cần thiết.
- **Reliability** (Độ tin cậy) đảm bảo hệ thống có khả năng phục hồi sau các gián đoạn và đáp ứng nhu cầu tính toán.

### ❌ Vì sao đáp án khác sai?

- A, C, E: Đây không phải là các trụ cột chính thức trong danh sách 6 trụ cột của **AWS Well-Architected Framework**.

💡 **Mẹo nhớ nhanh:** 6 trụ cột gồm: **Operational Excellence**, **Security**, **Reliability**, **Performance Efficiency**, **Cost Optimization**, và **Sustainability**.

---

### ❓ Question 3

Which actions are consistent with the operational excellence pillar of the AWS Well-Architected Framework? (Select TWO.)

A. Apply software engineering principles and methodology to infrastructure as code.

B. Plan and manage the full lifecycle of hardware assets.

C. Ensure operations personnel document changes to the infrastructure.

D. Evaluate organizational structures and roles to identify skill gaps.

E. Review and improve processes and procedures on a continuous cycle.

✅ **Đáp án đúng:** A và E

### 🧠 Giải thích

- Coi toàn bộ khối lượng công việc là **Code** (mã nguồn) cho phép áp dụng các nguyên lý kỹ thuật phần mềm vào hạ tầng.
- Liên tục cải tiến các quy trình và thủ tục hỗ trợ để tối ưu hóa vận hành.

### ❌ Vì sao đáp án khác sai?

- B: Việc quản lý phần cứng vật lý thuộc trách nhiệm của AWS trong mô hình đám mây .
- C: Trụ cột này ưu tiên việc tự động hóa các thay đổi thay vì tài liệu hóa thủ công bởi nhân sự vận hành.
- D: Việc đánh giá cấu trúc tổ chức không phải là hành động kỹ thuật cốt lõi được mô tả cho trụ cột này .

---

### ❓ Question 4

A specific application requires a frontend web tier of multiple servers that communicate with a backend application tier of multiple servers. Which design most closely follows AWS best practices?

A. Create a full mesh network between the web and application tiers, so that each web server can communicate directly with every application server.

B. Design the web tier to communicate with the application tier through the Elastic Load Balancing (ELB) service.

C. Assign a dedicated application server and a dedicated connection to each web server.

D. Create multiple instances that each combine a web frontend and application backend in the same instance.

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- Sử dụng **Elastic Load Balancing (ELB)** giúp tách biệt các tầng (decoupling), đảm bảo tính linh hoạt.
- Đây là ví dụ về **Loosely coupled components** (các thành phần liên kết lỏng lẻo), giúp hệ thống bền bỉ hơn.

### ❌ Vì sao đáp án khác sai?

- A: Đây là **Tightly coupled** (liên kết chặt chẽ), khiến việc quản lý và mở rộng trở nên cực kỳ phức tạp.
- C: Tạo ra sự phụ thuộc cứng nhắc giữa các server, vi phạm nguyên tắc thiết kế linh hoạt.
- D: Kết hợp các tầng vào một instance tạo ra **Single point of failure** (điểm yếu duy nhất) và khó mở rộng độc lập.

---

### ❓ Question 5

A solutions architect is developing a process for handling server failures. Which process most closely follows AWS best practices?

A. The operations staff detects a system failure. They initiate automation to provision a new server.

B. The operations staff detects a system failure. They notify the systems administrator, who provisions a new server by using the AWS Management Console.

C. Amazon CloudWatch detects a system failure. It notifies the systems administrator, who provisions a new server by using the AWS Management Console.

D. Amazon CloudWatch detects a system failure. It initiates automation to provision a new server.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- Tự động hóa việc phát hiện và xử lý lỗi là thực hành tốt nhất trên AWS.
- **Amazon CloudWatch** tự động phát hiện instance không khỏe mạnh và **Amazon EC2 Auto Scaling** sẽ tự động khởi chạy server mới.

### ❌ Vì sao đáp án khác sai?

- A, B, C: Các quy trình này vẫn dựa vào việc phát hiện hoặc thao tác thủ công từ nhân sự, làm chậm tốc độ phục hồi hệ thống.

💡 **Mẹo nhớ nhanh:** Với **EC2**, hãy kết hợp **CloudWatch** để giám sát và **Auto Scaling** để tự động phục hồi.

---

### ❓ Question 6

A company is considering moving their on-premises data center to the cloud. Their primary motivation is to increase their cost efficiency. Which approach most closely follows AWS best practices?

A. Provision the servers that are needed and stop services when they are not being used.

B. Maintain the on-premises data center as long as possible.

C. Replicate their on-premises data center in the cloud.

D. Provision some of the servers in the cloud and ensure the servers run 24/7.

✅ **Đáp án đúng:** A

### 🧠 Giải thích

- Tối ưu chi phí bằng cách dừng các tài nguyên khi không sử dụng.
- Chỉ cung cấp những gì cần thiết và tận dụng tính linh hoạt của đám mây để tăng hiệu quả chi phí.

### ❌ Vì sao đáp án khác sai?

- B: Việc duy trì hạ tầng cũ không giúp đạt được mục tiêu tăng hiệu quả chi phí thông qua đám mây.
- C: Chỉ sao chép nguyên mẫu mà không thay đổi cách vận hành (như tắt server khi rảnh) sẽ không tối ưu được chi phí.
- D: Chạy server 24/7 cho các tác vụ không cần thiết là hành vi lãng phí tài nguyên.

---

### ❓ Question 7

A company stores read-only data in Amazon S3. Most users are in the same country as the company headquarters. Some users are located around the world. Which design decision most closely follows AWS best practices?

A. Replicate objects across buckets in AWS Regions around the world. Users access the bucket in the Region closest that is to them.

B. Use a bucket in the Region closest to the company headquarters.

C. Use a bucket in the Region that has the lowest average latency for all users.

D. Use a bucket in the AWS Region that is closest to the company headquarters. All users access the data through Amazon CloudFront.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Amazon CloudFront** sử dụng các **Edge nodes** để lưu trữ nội dung gần người dùng, giúp giảm độ trễ.
- Đây là phương pháp tối ưu hơn so với việc sao chép dữ liệu thủ công ra nhiều Region.

### ❌ Vì sao đáp án khác sai?

- A: Việc sao chép dữ liệu ra quá nhiều Region gây tốn kém chi phí lưu trữ và phức tạp trong quản lý.
- B, C: Chỉ dùng một bucket ở một Region sẽ không giải quyết được vấn đề độ trễ cho người dùng toàn cầu.

---

### ❓ Question 8

A consultant must access a large object in an S3 bucket. They need one day to access the file. Which method for granting access most closely follows AWS best practices?

A. Create a user account for the consultant. Grant the user account permissions to access the S3 bucket through the AWS Management Console.

B. Enable public access on the S3 bucket. Give the object URL to the consultant.

C. Create a presigned URL to the object that expires in 24 hours, and give it to the consultant.

D. Copy the object to a new S3 bucket. Enable public access on the new bucket. From the new bucket, get the object URL, and give it to the consultant.

✅ **Đáp án đúng:** C

### 🧠 Giải thích

- Áp dụng nguyên tắc **Least privilege** (quyền hạn tối thiểu) bằng cách chỉ cấp quyền truy cập tạm thời.
- **Presigned URL** là phương pháp an toàn để cấp quyền truy cập có thời hạn mà không cần tạo user mới.

### ❌ Vì sao đáp án khác sai?

- A: Tạo tài khoản người dùng mới cho một tác vụ tạm thời là không cần thiết và khó quản lý.
- B, D: Cho phép **Public access** (truy cập công khai) là một rủi ro bảo mật nghiêm trọng đối với dữ liệu của tổ chức.

💡 **Mẹo nhớ nhanh:** Với **S3**, bảo mật là trên hết. Luôn tránh **Public access** trừ khi thực sự cần thiết.

---

### ❓ Question 9

Which are main considerations that influence which AWS Regions to use? (Select TWO.)

A. Application resiliency during system failures

B. Compliance with laws and regulations

C. Security and access control

D. Latency reduction for end users

E. Protection against localized natural disasters

✅ **Đáp án đúng:** B và D

### 🧠 Giải thích

- Lựa chọn **Region** thường dựa trên yêu cầu về tuân thủ pháp luật (ví dụ: dữ liệu phải ở trong nước).
- Giảm độ trễ cho người dùng cuối bằng cách chọn Region gần họ nhất.

### ❌ Vì sao đáp án khác sai?

- A, E: Khả năng chống chịu thất bại hệ thống và thiên tai cục bộ thường được giải quyết bằng cách sử dụng nhiều **Availability Zones** thay vì Region.
- C: Bảo mật và kiểm soát truy cập là các tính năng sẵn có đồng nhất ở tất cả các Region.

---

### ❓ Question 10

Which are main considerations that influence which Availability Zones to use? (Select TWO.)

A. Protection against localized natural disasters

B. Application resiliency during system failures

C. Compliance with laws and regulations

D. Latency reduction for end users

E. Security and access control

✅ **Đáp án đúng:** A và B

### 🧠 Giải thích

- Mỗi **Availability Zone** được thiết kế để cách ly lỗi, bảo vệ hệ thống trước các thảm họa tự nhiên cục bộ.
- Việc sao chép ứng dụng qua nhiều AZ giúp tăng cường khả năng phục hồi (**Resiliency**) khi một khu vực gặp sự cố.

### ❌ Vì sao đáp án khác sai?

- C, D: Đây là các yếu tố then chốt khi lựa chọn **Region**, không phải AZ.
- E: Các tiêu chuẩn bảo mật được áp dụng nhất quán trên toàn bộ hạ tầng AWS.

💡 **Mẹo nhớ nhanh:** Chọn **Region** vì Luật và Tốc độ. Chọn **AZ** vì Độ bền bỉ và Chống thảm họa.

---

# Practice Test - Module 02: Introducing Cloud Architecting

## Question 1

Which of the following is the best definition of cloud architecture?

A. Managing on-premises hardware more effectively by using internet-connected tools
B. Relocating traditional data centers to shared physical facilities managed by a third party
C. Applying cloud characteristics to a solution to meet technical and business requirements
D. Designing web services using only frontend software components

✅ Correct Answer: C

### 🧠 Explanation

Kiến trúc đám mây là việc áp dụng các đặc tính của điện toán đám mây vào một giải pháp cụ thể. Nó sử dụng các dịch vụ và tính năng của AWS để đáp ứng cả yêu cầu về mặt kỹ thuật lẫn nhu cầu kinh doanh của tổ chức.

### ❌ Why Other Choices Are Wrong

- A: Chỉ đề cập đến việc quản lý phần cứng tại chỗ, không phải kiến trúc đám mây.
- B: Đây mô tả quá trình di chuyển hoặc thuê chỗ đặt máy chủ (colocation), không phải thiết kế kiến trúc.
- D: Quá hạn hẹp vì chỉ tập trung vào phần mềm frontend.

---

## Question 2

How many pillars are currently defined in the AWS Well-Architected Framework?

A. Four
B. Five
C. Six
D. Seven

✅ Correct Answer: C

### 🧠 Explanation

Khung thiết kế tối ưu của AWS (AWS Well-Architected Framework) hiện tại được xây dựng dựa trên **6 trụ cột** cốt lõi để đảm bảo hệ thống vận hành hiệu quả và ổn định.

### ❌ Why Other Choices Are Wrong

- A, B, D: Đây là các con số không chính xác. Framework ban đầu có ít trụ cột hơn, nhưng hiện tại đã được mở rộng thành 6.

---

## Question 3

Which pillar of the AWS Well-Architected Framework focuses on the ability of a system to recover from service or infrastructure disruptions?

A. Security
B. Reliability
C. Operational Excellence
D. Performance Efficiency

✅ Correct Answer: B

### 🧠 Explanation

Trụ cột **Reliability** (Độ tin cậy) tập trung vào khả năng tự phục hồi của hệ thống khi gặp các sự cố gián đoạn hạ tầng hoặc dịch vụ, đảm bảo hệ thống luôn sẵn sàng phục vụ.

### ❌ Why Other Choices Are Wrong

- A: Tập trung vào bảo vệ thông tin và tài sản.
- C: Tập trung vào việc vận hành và giám sát hệ thống để mang lại giá trị kinh doanh.
- D: Tập trung vào việc sử dụng tài nguyên máy tính hiệu quả để đáp ứng các yêu cầu.

---

## Question 4

Which component of the AWS Global Infrastructure consists of one or more discrete data centers with redundant power and networking?

A. AWS Region
B. Edge Location
C. Availability Zone
D. Local Zone

✅ Correct Answer: C

### 🧠 Explanation

**Availability Zone (AZ)** (Vùng sẵn sàng) là một hoặc nhiều trung tâm dữ liệu riêng biệt trong một Region, có nguồn điện, làm mát và mạng lưới kết nối dự phòng để đảm bảo tính sẵn sàng cao.

### ❌ Why Other Choices Are Wrong

- A: Là một khu vực địa lý lớn chứa nhiều AZ.
- B: Là các điểm hiện diện dùng để cache nội dung qua CloudFront.
- D: Là phần mở rộng của Region được đặt gần các khu vực dân cư lớn để giảm độ trễ.

---

## Question 5

What is a key benefit of using Infrastructure as Code (IaC)?

A. It increases the manual effort required to provision resources
B. It reduces configuration errors caused by manual processes
C. It allows users to manage the physical security of data centers
D. It eliminates the need for monitoring services like CloudWatch

✅ Correct Answer: B

### 🧠 Explanation

Hạ tầng dưới dạng mã (IaC) cho phép triển khai tài nguyên bằng các mẫu script, giúp giảm thiểu tối đa các sai sót cấu hình do con người thực hiện thủ công.

### ❌ Why Other Choices Are Wrong

- A: IaC thực tế làm giảm nỗ lực thủ công thông qua tự động hóa.
- C: Bảo mật vật lý là trách nhiệm của AWS, người dùng không thể can thiệp.
- D: IaC không thay thế việc giám sát; hệ thống vẫn cần CloudWatch để theo dõi hiệu năng.

---

## Question 6

Which design principle helps to ensure that a failure in one component does not cause the entire system to fail?

A. Tight coupling
B. Loose coupling
C. Manual scaling
D. Vertical scaling

✅ Correct Answer: B

### 🧠 Explanation

**Loose coupling** (Liên kết lỏng lẻo) giúp tách biệt các thành phần của hệ thống. Khi một thành phần gặp sự cố, các thành phần khác vẫn có thể tiếp tục hoạt động, ngăn chặn lỗi dây chuyền.

### ❌ Why Other Choices Are Wrong

- A: Liên kết chặt chẽ khiến lỗi của một phần ảnh hưởng ngay lập tức đến toàn bộ hệ thống.
- C, D: Scaling (mở rộng) liên quan đến việc đáp ứng nhu cầu tải, không trực tiếp giải quyết việc cách ly lỗi.

---

## Question 7

Which AWS service provides an action plan with step-by-step guidance on how to build better workloads based on best practices?

A. AWS Management Console
B. Amazon CloudWatch
C. AWS Well-Architected Tool
D. AWS Trusted Advisor

✅ Correct Answer: C

### 🧠 Explanation

**AWS Well-Architected Tool** cung cấp các câu hỏi kiểm tra và đưa ra kế hoạch hành động cụ thể để cải thiện kiến trúc hệ thống dựa trên các thực hành tốt nhất của AWS.

### ❌ Why Other Choices Are Wrong

- A: Là giao diện để quản lý tài nguyên, không cung cấp tư vấn kiến trúc.
- B: Là dịch vụ giám sát và cảnh báo.
- D: Kiểm tra hạ tầng để tìm cách tối ưu chi phí và bảo mật nhưng không chuyên sâu về quy trình kiến trúc như WA Tool.

---

## Question 8

A company needs to run latency-sensitive portions of their application closer to end users in a specific geography where no AWS Region exists. Which service should they use?

A. AWS Local Zones
B. Edge Locations
C. Availability Zones
D. Regional Edge Caches

✅ Correct Answer: A

### 🧠 Explanation

**AWS Local Zones** đặt các dịch vụ tính toán, lưu trữ và cơ sở dữ liệu gần các trung tâm dân cư/công nghiệp lớn, giúp giảm độ trễ cho các ứng dụng yêu cầu tốc độ phản hồi cực nhanh.

### ❌ Why Other Choices Are Wrong

- B: Chủ yếu dùng để phân phối nội dung tĩnh/động qua CloudFront, không chạy toàn bộ ứng dụng.
- C: Nằm trong các Region chính thức.
- D: Là một lớp đệm giữa CloudFront và server gốc.

---

## Question 9

Which best practice involves using Amazon CloudFront to minimize redundant data retrieval operations?

A. Data replication
B. Caching
C. Automation
D. Scalability

✅ Correct Answer: B

### 🧠 Explanation

**Caching** (Bộ nhớ đệm) thông qua Amazon CloudFront cho phép lưu trữ dữ liệu tại các điểm gần người dùng nhất, giúp giảm thiểu việc phải quay lại server gốc để lấy dữ liệu lặp đi lặp lại.

### ❌ Why Other Choices Are Wrong

- A: Là sao chép dữ liệu để đảm bảo độ bền.
- C: Là tự động hóa quy trình.
- D: Là khả năng tăng/giảm tài nguyên theo nhu cầu.

---

## Question 10

What does the "Principle of Least Privilege" refer to in the Security pillar?

A. Granting all users administrative access to simplify management
B. Providing only the necessary access required to perform a specific task
C. Reducing the cost of security services by limiting their usage
D. Allowing public access to all S3 buckets for ease of sharing

✅ Correct Answer: B

### 🧠 Explanation

Nguyên tắc quyền hạn tối thiểu yêu cầu chỉ cấp cho người dùng hoặc dịch vụ các quyền truy cập vừa đủ để họ hoàn thành công việc, nhằm hạn chế rủi ro nếu tài khoản bị xâm nhập.

### ❌ Why Other Choices Are Wrong

- A, D: Đây là những hành vi vi phạm nghiêm trọng các tiêu chuẩn bảo mật của AWS.
- C: Bảo mật không nên bị giới hạn chỉ để giảm chi phí nếu nó ảnh hưởng đến an toàn hệ thống.

---

## Question 11

When designing for "Disposable Resources", what should a cloud architect automate?

A. The manual backup of every database hourly
B. The provisioning, termination, and configuration of resources
C. The physical maintenance of server racks
D. The documentation of every technical change in a paper log

✅ Correct Answer: B

### 🧠 Explanation

Tài nguyên đám mây nên được coi là tạm thời (có thể thay thế). Do đó, việc tự động hóa khởi tạo, cấu hình và chấm dứt tài nguyên là rất quan trọng để hệ thống linh hoạt và nhất quán.

### ❌ Why Other Choices Are Wrong

- A, D: Việc thực hiện thủ công đi ngược lại với lợi ích của đám mây.
- C: Đây là trách nhiệm của AWS trong mô hình trách nhiệm chia sẻ.

---

## Question 12

In the context of the Sustainability pillar, which action helps minimize environmental impact?

A. Running all servers 24/7 at 10% utilization
B. Maximizing resource utilization to avoid waste
C. Using only on-premises data centers
D. Avoiding the use of managed services

✅ Correct Answer: B

### 🧠 Explanation

Trụ cột Bền vững khuyến khích tối đa hóa việc sử dụng tài nguyên để giảm lượng năng lượng tiêu thụ lãng phí, từ đó giảm thiểu tác động đến môi trường.

### ❌ Why Other Choices Are Wrong

- A: Gây lãng phí điện năng cực lớn cho hiệu suất thấp.
- C: Các trung tâm dữ liệu đám mây thường hiệu quả hơn về năng lượng so với trung tâm dữ liệu tại chỗ.
- D: Managed services giúp AWS tối ưu hóa tài nguyên cho nhiều khách hàng cùng lúc, thân thiện với môi trường hơn.

---

## Question 13

Which of the following is a design trade-off example mentioned in AWS best practices?

A. Trading security for lower cost
B. Trading consistency and durability for lower latency and higher performance
C. Trading automation for more manual control
D. Trading scalability for fixed infrastructure

✅ Correct Answer: B

### 🧠 Explanation

Khi thiết kế, đôi khi bạn cần đánh đổi sự nhất quán dữ liệu tức thời (consistency) lấy tốc độ phản hồi nhanh (latency) và hiệu suất cao để đáp ứng trải nghiệm người dùng.

### ❌ Why Other Choices Are Wrong

- A: AWS không bao giờ khuyến khích đánh đổi bảo mật lấy chi phí.
- C, D: Đám mây hướng tới tự động hóa và mở rộng, việc đánh đổi ngược lại là bước lùi.

---

## Question 14

What is the primary purpose of an AWS Region?

A. To provide a single data center for all global customers
B. To serve as a geographical area that contains multiple Availability Zones
C. To cache content closer to end users for faster delivery
D. To provide a physical location for customers to store their own hardware

✅ Correct Answer: B

### 🧠 Explanation

AWS Region là một khu vực địa lý riêng biệt trên thế giới, bên trong chứa nhiều Availability Zones được kết nối với nhau bằng mạng lưới cáp quang độ trễ thấp.

### ❌ Why Other Choices Are Wrong

- A: Region chứa nhiều trung tâm dữ liệu, không phải chỉ một.
- C: Đây là vai trò của Edge Locations.
- D: Khách hàng không thể trực tiếp đặt phần cứng của mình vào Region của AWS.

---

## Question 15

Which pillar focuses on avoiding unneeded expenses and using managed services to reduce the cost of ownership?

A. Performance Efficiency
B. Operational Excellence
C. Cost Optimization
D. Reliability

✅ Correct Answer: C

### 🧠 Explanation

Trụ cột **Cost Optimization** (Tối ưu hóa chi phí) tập trung vào việc loại bỏ các lãng phí, đo lường hiệu quả và sử dụng các dịch vụ được quản lý để giảm bớt chi phí vận hành.

### ❌ Why Other Choices Are Wrong

- A: Tập trung vào tốc độ và hiệu suất.
- B: Tập trung vào quy trình vận hành.
- D: Tập trung vào khả năng chịu lỗi và phục hồi.

---

## Question 16

What is the most effective way to handle a server failure according to AWS best practices?

A. Have an administrator manually restart the server via the console
B. Use Amazon CloudWatch to detect the failure and initiate automation to provision a new server
C. Wait for users to report the error before taking any action
D. Replicate the entire data center to another Region immediately

✅ Correct Answer: B

### 🧠 Explanation

Cách tối ưu nhất là dùng hệ thống giám sát (CloudWatch) tự động phát hiện lỗi và kích hoạt quy trình tự động khởi tạo server mới (Auto Scaling) để thay thế server hỏng mà không cần con người can thiệp.

### ❌ Why Other Choices Are Wrong

- A: Quá chậm và dễ sai sót.
- C: Làm gián đoạn dịch vụ và gây trải nghiệm tệ cho khách hàng.
- D: Quá tốn kém và không cần thiết cho lỗi của một máy chủ đơn lẻ.

---

## Question 17

Which architecture pattern ensures that if a primary database server fails, a secondary server can pick up the load?

A. Vertical scaling
B. Standby replication
C. Full mesh networking
D. Static caching

✅ Correct Answer: B

### 🧠 Explanation

Việc tạo một server dự phòng (Standby) và sao chép dữ liệu liên tục từ server chính đảm bảo rằng hệ thống có thể chuyển đổi nhanh chóng nếu server chính gặp sự cố.

### ❌ Why Other Choices Are Wrong

- A: Chỉ làm tăng sức mạnh của một máy chủ duy nhất, nếu nó hỏng hệ thống vẫn sập.
- C: Là cấu trúc kết nối mạng, không liên quan đến dự phòng dữ liệu.
- D: Chỉ lưu trữ dữ liệu tĩnh, không xử lý được các giao dịch cơ sở dữ liệu động.

---

## Question 18

What is a "Point of Presence" (PoP) in the context of AWS Global Infrastructure?

A. A specific AWS Region with the highest number of customers
B. A location used by CloudFront to deliver content with lower latency
C. The physical office of an AWS Solutions Architect
D. A backup data center used only for disaster recovery

✅ Correct Answer: B

### 🧠 Explanation

PoP bao gồm các Edge Locations và Regional Edge Caches, giúp Amazon CloudFront lưu trữ và phân phối nội dung gần người dùng hơn để giảm độ trễ.

### ❌ Why Other Choices Are Wrong

- A, C, D: Đây đều là những định nghĩa không liên quan đến hạ tầng mạng phân phối nội dung của AWS.

---

## Question 19

Which design principle encourages "Designing services, not servers"?

A. Using EC2 instances for every application component
B. Utilizing containers or serverless solutions when appropriate
C. Building custom hardware protocols for internal networking
D. Maintaining long-running servers that are never rebooted

✅ Correct Answer: B

### 🧠 Explanation

Thay vì tự quản lý và bảo trì các server (EC2), AWS khuyến khích dùng các dịch vụ Managed hoặc Serverless (như Lambda, Fargate) để tập trung vào ứng dụng và giảm bớt gánh nặng quản lý hạ tầng.

### ❌ Why Other Choices Are Wrong

- A, D: Đây là tư duy quản lý server truyền thống, tốn nhiều công sức vận hành.
- C: AWS đã quản lý sẵn các giao thức mạng tối ưu, người dùng không nên tự xây dựng phức tạp hóa.

---

## Question 20

Which pillar of the Well-Architected Framework emphasizes viewing the entire workload as code and continually improving processes?

A. Reliability
B. Security
C. Operational Excellence
D. Sustainability

✅ Correct Answer: C

### 🧠 Explanation

Trụ cột **Operational Excellence** tập trung vào việc quản lý vận hành dưới dạng mã nguồn, thực hiện các thay đổi nhỏ có thể đảo ngược và liên tục học hỏi từ các sai sót để cải tiến quy trình.

### ❌ Why Other Choices Are Wrong

- A, B, D: Mặc dù các trụ cột này cũng hưởng lợi từ code và cải tiến, nhưng "Operational Excellence" là trụ cột định nghĩa chính cho các quy trình này.

---

## 📊 Final Score

- **Score:** 20/20 (100%)
- **Status:** Passed

## 🎯 Weak Topics

- **No Weak Topics Identified:** Bạn đã nắm vững toàn bộ kiến thức về Module 2, bao gồm các nguyên tắc thiết kế, hạ tầng toàn cầu và 6 trụ cột của Well-Architected Framework.
