# Module 06: Adding a Database Layer (Thêm lớp cơ sở dữ liệu)

## 🧭 Overview

- Module này giúp bạn hiểu cách lựa chọn và triển khai lớp cơ sở dữ liệu phù hợp trên AWS nhằm tối ưu hóa hiệu suất và bảo mật.
- **Những gì sẽ học:**
  - So sánh các loại Database (Cơ sở dữ liệu) và các dịch vụ AWS cung cấp.
  - Cách sử dụng **Amazon RDS** (Relational Database Service) và các tính năng nâng cao.
  - Khi nào nên dùng **Amazon DynamoDB** (Cơ sở dữ liệu NoSQL).
  - Nhận diện các **Purpose-built database** (Cơ sở dữ liệu chuyên dụng).
  - Phương pháp di chuyển dữ liệu (**Migration**) vào AWS.
  - Áp dụng các nguyên tắc của **AWS Well-Architected Framework** cho lớp Database.

---

## 📘 Phần 1: Database layer considerations (Các cân nhắc cho lớp cơ sở dữ liệu)

### 🔑 Ý chính

Để thiết kế một lớp Database hiệu quả, Cloud Architect cần cân nhắc 4 yếu tố then chốt:

1. **Scalability (Khả năng mở rộng):** Khả năng đáp ứng lưu lượng truy cập (throughput) tăng cao.
2. **Storage requirements (Yêu cầu lưu trữ):** Dung lượng dữ liệu cần chứa (Gigabytes, Terabytes hay Petabytes).
3. **Data characteristics (Đặc điểm dữ liệu):** Mô hình dữ liệu (Data model), cách truy cập dữ liệu và yêu cầu về **Latency** (độ trễ).
4. **Durability (Tính bền vững):** Mức độ chịu lỗi, khả năng phục hồi dữ liệu và các quy định tuân thủ pháp lý.

### 🧠 Giải thích

#### ⚖️ So sánh Relational và Non-Relational Databases

| Đặc điểm            | **Relational Databases** (CSDL quan hệ)                                      | **Non-Relational Databases** (CSDL phi quan hệ)                                          |
| :------------------ | :--------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------- |
| **Cấu trúc**        | Dạng bảng (**Tabular**) gồm hàng và cột.                                     | Đa dạng: **Key-value**, **Document**, hoặc **Graph**.                                    |
| **Schema**          | Chặt chẽ (**Strict schema**).                                                | Linh hoạt (**Flexible schema**).                                                         |
| **Cách mở rộng**    | Thường là **Vertical scaling** (Mở rộng dọc - tăng cấu hình máy chủ).        | Thường là **Horizontal scaling** (Mở rộng ngang - thêm nhiều máy chủ).                   |
| **Ưu điểm**         | Tính toàn vẹn dữ liệu cao, dùng ngôn ngữ **SQL** phổ biến.                   | Hiệu năng cực cao, độ trễ thấp, khả năng mở rộng lớn.                                    |
| **Trường hợp dùng** | Phù hợp cho **Online Transactional Processing (OLTP)**, dữ liệu có cấu trúc. | Phù hợp cho **Caching**, lưu trữ JSON, ứng dụng cần tốc độ phản hồi tính bằng mili giây. |

#### 🛠 Trách nhiệm quản trị (Managed Services)

Khi sử dụng các dịch vụ **Managed Database Service** (Dịch vụ cơ sở dữ liệu được quản lý) của AWS, người dùng sẽ giảm bớt được rất nhiều công việc:

- **AWS quản lý:** Điện năng, mạng, phần cứng, lắp đặt OS, vá lỗi OS (OS Patches), vá lỗi Database, sao lưu (Backups), và đảm bảo tính sẵn sàng cao (High Availability).
- **Bạn chỉ quản lý:** **Application Optimization** (Tối ưu hóa ứng dụng).

#### 📈 Các kiểu Scaling (Mở rộng)

- **Vertical scaling (Mở rộng dọc):** Tăng sức mạnh cho máy chủ hiện có (thêm RAM, CPU).
- **Horizontal scaling (Mở rộng ngang):** Thêm nhiều máy chủ chạy song song để chia tải.

### 📌 Ghi nhớ nhanh

- 🏢 **Relational** = Bảng + Schema cố định + SQL + Scale dọc.
- 🚀 **Non-Relational** = Linh hoạt + Tốc độ cao + Scale ngang.
- ☁️ **Managed Services** giúp bạn tập trung vào code, AWS lo phần hạ tầng và vận hành Database.
- 📏 **Capacity planning** (Lập kế hoạch dung lượng) cần dựa trên việc phân tích lưu trữ hiện tại và dự báo tương lai.

### 💡 Ví dụ (nếu có)

```example
- Relational Database: Hệ thống quản lý giao dịch ngân hàng (cần tính toàn vẹn dữ liệu cực cao).
- Non-Relational Database: Hệ thống lưu trữ hồ sơ người dùng game hoặc giỏ hàng trực tuyến (cần tốc độ truy cập cực nhanh cho hàng triệu người dùng).
```

---

## 📘 Phần 2: Amazon RDS (Dịch vụ cơ sở dữ liệu quan hệ của Amazon)

### 🔑 Ý chính

- **Amazon RDS** là một **Managed Service** (Dịch vụ được quản lý) giúp đơn giản hóa việc thiết lập, vận hành và mở rộng cơ sở dữ liệu quan hệ trên AWS.
- Hỗ trợ 07 loại **Database Engines** (Công cụ cơ sở dữ liệu) phổ biến: **Aurora** (tương thích MySQL & PostgreSQL), **MySQL**, **PostgreSQL**, **MariaDB**, **Oracle**, và **SQL Server**.
- Cơ chế lưu trữ: Sử dụng các ổ đĩa **Amazon EBS** (Elastic Block Store) để chứa dữ liệu và nhật ký (logs).

### 🧠 Giải thích

#### 🌟 Lợi ích của Amazon RDS

- **Lower administrative burden** (Giảm bớt gánh nặng quản trị): AWS lo việc cài đặt hạ tầng, vá lỗi phần mềm (OS Patches) và bảo trì phần cứng.
- **Highly scalable** (Khả năng mở rộng cao): Bạn có thể thay đổi cấu hình CPU và RAM của máy chủ database chỉ với vài cú click.
- **Available and durable** (Tính sẵn sàng và bền bỉ): Tự động sao lưu (**Automated backups**), chụp ảnh hệ thống (**Snapshots**) và tự động thay thế máy chủ khi có sự cố.
- **Secure** (Bảo mật): Cách ly database trong mạng ảo riêng (**VPC**), mã hóa dữ liệu tĩnh bằng **AWS KMS** và dữ liệu đang truyền qua **SSL/TLS**.

#### 🚀 Amazon Aurora - Cơ sở dữ liệu chuyên dụng cho đám mây

- Là hệ quản trị CSDL quan hệ (**RDBMS**) được tối ưu riêng cho nền tảng đám mây của AWS.
- **Performance** (Hiệu suất): Cung cấp hiệu năng cực cao với chi phí chỉ bằng **1/10** so với các giải pháp thương mại truyền thống.
- **Aurora Serverless**: Tự động mở rộng/thu hẹp tài nguyên (Auto scaling) theo nhu cầu thực tế, lý tưởng cho các ứng dụng có lưu lượng truy cập thất thường.
- **Aurora Cluster**: Một Cluster volume có thể trải dài qua nhiều **Availability Zones** (Vùng sẵn sàng), giúp bảo vệ dữ liệu tối đa.

#### 📊 Các phương thức mở rộng (Scaling)

- **Vertical Scaling** (Mở rộng dọc): Thay đổi kích cỡ Instance (ví dụ từ `db.m6g.large` lên `db.m6g.xlarge`) để tăng tài nguyên CPU/RAM.
- **Horizontal Scaling** (Mở rộng ngang): Tạo các **Read Replicas** (Bản sao chỉ đọc) để chia sẻ tải cho các truy vấn đọc dữ liệu.

### 📌 Ghi nhớ nhanh

- 🏢 **Multi-AZ Deployment**: Sử dụng để đảm bảo **High Availability** (Tính sẵn sàng cao) thông qua việc đồng bộ dữ liệu sang một bản dự phòng ở Zone khác.
- 📑 **Read Replicas**: Sử dụng để tăng hiệu suất đọc (**Scalability**) thông qua cơ chế sao chép không đồng bộ.
- 🛡 **Security Best Practice**: Luôn đặt database instance trong **Private Subnet** (Phân vùng mạng riêng) và dùng **Security Groups** để kiểm soát truy cập IP.

### 💡 Ví dụ (nếu có)

```example
Tình huống nâng cấp hệ thống:
- Nếu Database bị treo do quá tải CPU: Thực hiện Vertical Scaling (nâng cấp loại Instance).
- Nếu ứng dụng có quá nhiều người xem báo cáo nhưng ít người ghi dữ liệu: Tạo thêm Read Replicas để giảm tải cho Database chính.
```

---

## 📘 Phần 3: Amazon RDS proxy connection management (Quản lý kết nối qua Amazon RDS Proxy)

### 🔑 Ý chính

- **Amazon RDS Proxy** là một trình trung gian (proxy) cơ sở dữ liệu có tính sẵn sàng cao, được quản lý hoàn toàn cho Amazon RDS.
- Công cụ này giúp ứng dụng trở nên **Scalable** (có khả năng mở rộng) hơn, **Resilient** (khả năng phục hồi) tốt hơn và **Secure** (bảo mật) hơn.
- RDS Proxy nằm giữa ứng dụng và cơ sở dữ liệu để quản lý các luồng kết nối một cách hiệu quả.

### 🧠 Giải thích

#### 🔋 Connection pooling (Gom cụm kết nối) - Tăng khả năng mở rộng

- **Vấn đề:** Khi có quá nhiều kết nối mở đồng thời từ ứng dụng, cơ sở dữ liệu sẽ bị cạn kiệt tài nguyên CPU và bộ nhớ.
- **Giải pháp:** RDS Proxy thực hiện **Pools and shares** (gom và chia sẻ) các kết nối cơ sở dữ liệu. Nhờ đó, database nhận ít kết nối trực tiếp hơn và có thể hoạt động hiệu quả hơn.

#### 🔄 Seamless and fast failover (Chuyển đổi dự phòng nhanh và mượt mà) - Tăng tính sẵn sàng

- RDS Proxy tự động phát hiện lỗi và kết nối tới **Standby database** (cơ sở dữ liệu dự phòng) mà không làm ngắt kết nối từ phía ứng dụng.
- Giảm thời gian **Failover** lên tới **66%** đối với các cơ sở dữ liệu Multi-AZ.
- Các giao dịch (**Transactions**) sẽ được đưa vào hàng đợi (**Queued**) trong quá trình chuyển đổi dự phòng, giúp ứng dụng không bị lỗi kết nối đột ngột.

#### 🛡 Streamlined authentication (Xác thực tinh gọn) - Tăng tính bảo mật

- Hỗ trợ **IAM authentication** (xác thực thông qua Identity and Access Management), loại bỏ việc lưu mật khẩu trong code.
- Lưu trữ thông tin đăng nhập (credentials) an toàn trong **AWS Secrets Manager**.

### 📌 Ghi nhớ nhanh

- 🧱 **RDS Proxy** = Lớp đệm trung gian bảo vệ và tối ưu kết nối cho DB.
- 📉 Giảm tải cho DB nhờ cơ chế **Connection pooling**.
- ⚡ **Failover** cực nhanh, giữ cho ứng dụng luôn hoạt động ổn định khi DB chính gặp sự cố.
- 🔑 Tăng bảo mật nhờ tích hợp **Secrets Manager** và **IAM**.

### 💡 Ví dụ (nếu có)

```example
Một ứng dụng sử dụng kiến trúc Serverless với AWS Lambda thường tạo ra hàng nghìn kết nối ngắn hạn cùng lúc. Nếu các kết nối này đi thẳng vào RDS, cơ sở dữ liệu sẽ bị treo ngay lập tức.
=> Giải pháp: Cho Lambda kết nối qua RDS Proxy. Proxy sẽ giữ một lượng kết nối ổn định tới DB và phân phối lại cho các hàm Lambda, giúp hệ thống không bị quá tải.
```

---

## 📘 Phần 4: Amazon DynamoDB (Cơ sở dữ liệu NoSQL của Amazon)

### 🔑 Ý chính

- **Amazon DynamoDB** là dịch vụ cơ sở dữ liệu **NoSQL** được quản lý hoàn toàn (**Fully managed**), hoạt động theo mô hình **Serverless** (không máy chủ).
- Hỗ trợ cả hai mô hình dữ liệu là **Key-value** (Khóa-giá trị) và **Document** (Tài liệu).
- Cung cấp hiệu suất ổn định tính bằng mili giây (**Millisecond performance**) ở bất kỳ quy mô nào.
- Phù hợp cho các ứng dụng quy mô lớn, yêu cầu tốc độ cao và khả năng mở rộng không giới hạn.

### 🧠 Giải thích

#### 🏗 Cấu trúc dữ liệu trong DynamoDB

- **Table** (Bảng): Là một tập hợp các dữ liệu.
- **Item** (Mục): Một nhóm các thuộc tính được định danh duy nhất (tương đương với một "dòng" trong CSDL quan hệ).
- **Attribute** (Thuộc tính): Thành phần dữ liệu cơ bản (tương đương với "cột").

#### 🔑 Khóa chính (Primary Keys)

- **Simple Primary Key**: Chỉ bao gồm một thuộc tính duy nhất gọi là **Partition Key** (Khóa phân vùng).
- **Composite Primary Key**: Kết hợp giữa **Partition Key** và **Sort Key** (Khóa sắp xếp).

#### 🔍 So sánh các loại Chỉ mục phụ (Secondary Indexes)

| Đặc điểm           | **Global Secondary Index (GSI)**                      | **Local Secondary Index (LSI)**                            |
| :----------------- | :---------------------------------------------------- | :--------------------------------------------------------- |
| **Khóa chính**     | Có thể thay đổi cả **Partition Key** và **Sort Key**. | Giữ nguyên **Partition Key**, chỉ thay đổi **Sort Key**.   |
| **Phạm vi**        | Toàn bộ bảng.                                         | Trong phạm vi một **Partition Key**.                       |
| **Tính nhất quán** | **Eventual consistency** (Nhất quán cuối cùng).       | **Eventual** hoặc **Strong consistency** (Nhất quán mạnh). |

#### 🌍 Tính năng nâng cao

- **Global Tables**: Tự động sao chép dữ liệu đa vùng (**Multi-Region**) và đa hoạt động (**Multi-active**) để phục vụ người dùng toàn cầu với độ trễ cực thấp.
- **DynamoDB Streams**: Ghi lại các thay đổi trong bảng (thêm, sửa, xóa) theo thời gian thực, lý tưởng cho kiến trúc hướng sự kiện (**Event-driven architecture**).
- **Security** (Bảo mật): Mã hóa dữ liệu tĩnh (**Encryption at rest**) theo mặc định và kiểm soát truy cập chi tiết qua **IAM**.

### 📌 Ghi nhớ nhanh

- ⚡ **Performance**: Tốc độ cực nhanh, không thay đổi ngay cả khi dữ liệu tăng từ Gigabytes lên Petabytes.
- 🧱 **Scalability**: Tự động mở rộng hoặc thu hẹp tài nguyên để đáp ứng lưu lượng truy cập.
- 🛡 **Reliability**: Tự động sao lưu và có tính năng **Point-in-time recovery** (Phục hồi dữ liệu tại một thời điểm) để bảo vệ dữ liệu khỏi các thao tác sai sót.
- 💸 **Cost**: Chỉ trả tiền cho những gì bạn sử dụng (theo dung lượng lưu trữ và thông lượng truy cập).

### 💡 Ví dụ (nếu có)

```example
- Gaming: Lưu trữ hồ sơ người chơi (Player profiles), lịch sử phiên đấu và bảng xếp hạng (Leaderboards) cho hàng triệu người dùng đồng thời.
- E-commerce: Lưu trữ giỏ hàng trực tuyến (Shopping carts) hoặc danh mục sản phẩm (Product catalogs) với sơ đồ dữ liệu linh hoạt.
- IoT: Lưu trữ dữ liệu từ hàng tỷ cảm biến gửi về liên tục với độ trễ thấp nhất.
```

---

## 📘 Phần 5: Purpose-built databases (Cơ sở dữ liệu chuyên dụng)

### 🔑 Ý chính

- AWS cung cấp một hệ sinh thái các cơ sở dữ liệu được thiết kế chuyên biệt nhằm giải quyết các bài toán cụ thể mà một cơ sở dữ liệu quan hệ truyền thống khó có thể đáp ứng hiệu quả.
- Việc lựa chọn Database dựa trên nguyên tắc **Matching a database to your business need** (Khớp cơ sở dữ liệu với nhu cầu kinh doanh): xem xét **Suitable workloads** (Khối lượng công việc phù hợp), **Data model** (Mô hình dữ liệu) và **Access patterns** (Cách thức truy cập dữ liệu).

### 🧠 Giải thích

#### 📑 Danh sách các Database chuyên dụng từ AWS

| Dịch vụ AWS                   | Loại Database                         | Đặc điểm nổi bật                                                                                               |
| :---------------------------- | :------------------------------------ | :------------------------------------------------------------------------------------------------------------- |
| **Amazon Redshift**           | **Data warehouse** (Kho dữ liệu)      | Dùng cho **OLAP** (Xử lý phân tích trực tuyến), lưu trữ dạng cột (**Columnar storage**), quy mô **Petabytes**. |
| **Amazon DocumentDB**         | **Document database** (CSDL tài liệu) | Tương thích **MongoDB**, dùng định dạng **JSON**, schema linh hoạt cho phát triển ứng dụng nhanh.              |
| **Amazon Keyspaces**          | **Wide-column** (CSDL cột rộng)       | Tương thích **Apache Cassandra**, tối ưu cho việc ghi dữ liệu nặng và truy vấn tốc độ cao.                     |
| **Amazon MemoryDB for Redis** | **In-memory** (CSDL trong bộ nhớ)     | Tốc độ cực nhanh (micro giây), đảm bảo tính bền vững dữ liệu (**Durability**) và phục hồi cao.                 |
| **Amazon Neptune**            | **Graph database** (CSDL đồ thị)      | Xử lý các dữ liệu có mối quan hệ phức tạp, liên kết chặt chẽ (đồ thị).                                         |
| **Amazon Timestream**         | **Timeseries** (CSDL chuỗi thời gian) | Lưu trữ và xử lý dữ liệu thay đổi theo thời gian (ví dụ: dữ liệu từ cảm biến IoT).                             |
| **Amazon QLDB**               | **Ledger database** (CSDL sổ cái)     | Cung cấp nhật ký giao dịch bất biến (**Immutable**) và có thể xác minh bằng mã hóa.                            |

#### 🎯 Cách chọn Database phù hợp

- **Analyze requirements** (Phân tích yêu cầu): Xem xét các đặc tính về độ trễ, thông lượng và dung lượng.
- **Understand Data model** (Hiểu mô hình dữ liệu): Dữ liệu là dạng bảng, dạng cây, hay là một chuỗi các sự kiện thời gian?
- **Review use cases** (Xem xét các trường hợp sử dụng): Tham khảo kiến trúc mẫu để tối ưu hóa hiệu suất và chi phí.

### 📌 Ghi nhớ nhanh

- 📊 **Redshift** = Phân tích dữ liệu lớn + Báo cáo (BI).
- 📂 **DocumentDB** = Dữ liệu JSON + Schema linh hoạt.
- ⚡ **MemoryDB** = Tốc độ siêu nhanh + Không mất dữ liệu.
- 🕸 **Neptune** = Mối quan hệ phức tạp + Đồ thị.
- 🕒 **Timestream** = Dữ liệu IoT + Theo dòng thời gian.
- 📒 **QLDB** = Sổ cái + Không thể thay đổi lịch sử.

### 💡 Ví dụ (nếu có)

```example
- Fraud detection (Phát hiện gian lận): Sử dụng Amazon Neptune để tìm ra các mối liên kết bất thường giữa hàng triệu tài khoản.
- Fleet management (Quản lý đội xe): Sử dụng Amazon Timestream để ghi lại tọa độ GPS và trạng thái động cơ của hàng nghìn xe mỗi giây.
- Financial records (Hồ sơ tài chính): Sử dụng Amazon QLDB để tạo ra một hệ thống sổ cái lưu trữ lịch sử tín dụng không thể bị xóa sửa.
```

---

## 📘 Phần 6: Migrating data into AWS databases (Di chuyển dữ liệu vào cơ sở dữ liệu AWS)

### 🔑 Ý chính

- **AWS DMS (Database Migration Service)** là dịch vụ cốt lõi giúp di chuyển các kho dữ liệu và cơ sở dữ liệu vào AWS một cách an toàn và nhanh chóng.
- Hỗ trợ di chuyển dữ liệu trong khi cơ sở dữ liệu nguồn vẫn đang hoạt động (**Minimal downtime**).
- Có hai loại di chuyển chính: **Homogeneous migration** (Cùng loại) và **Heterogeneous migration** (Khác loại).

### 🧠 Giải thích

#### 🔄 Các loại Migration (Di chuyển)

| Đặc điểm           | **Homogeneous migration** (Di chuyển cùng loại)          | **Heterogeneous migration** (Di chuyển khác loại)             |
| :----------------- | :------------------------------------------------------- | :------------------------------------------------------------ |
| **Định nghĩa**     | Di chuyển giữa các Database có cùng **Database Engine**. | Di chuyển giữa các Database có **Database Engine** khác nhau. |
| **Ví dụ**          | MySQL sang **Amazon RDS MySQL**.                         | Oracle hoặc SQL Server sang **Amazon Aurora**.                |
| **Công cụ hỗ trợ** | Chủ yếu dùng **AWS DMS**.                                | Cần **AWS DMS** kết hợp với **AWS SCT**.                      |

#### 🛠 Các công cụ hỗ trợ quan trọng

- **AWS DMS Fleet Advisor**: Tự động kiểm kê và đánh giá toàn bộ các máy chủ cơ sở dữ liệu hiện có tại hệ thống tại chỗ (**On-premises**) để lập kế hoạch di chuyển.
- **AWS SCT (Schema Conversion Tool)**: Công cụ giúp chuyển đổi lược đồ (**Schema**) và mã SQL từ cơ sở dữ liệu nguồn sang định dạng tương đương của cơ sở dữ liệu đích khác loại.
- **AWS DMS Schema Conversion**: Một tính năng tích hợp sẵn trong luồng công việc của DMS để đánh giá và chuyển đổi lược đồ một cách tập trung.
- **Replication (Sao chép)**: DMS có thể sao chép dữ liệu liên tục từ nguồn đến đích theo lịch trình hoặc theo yêu cầu, đảm bảo dữ liệu luôn được cập nhật.

### 📌 Ghi nhớ nhanh

- 🚛 **AWS DMS**: Dùng để di chuyển dữ liệu thực tế (dòng, bảng).
- 🧬 **AWS SCT**: Dùng để "dịch" cấu trúc (Schema) khi đổi loại Database.
- 📋 **Fleet Advisor**: Dùng để khám phá và đánh giá hệ thống cũ trước khi bắt đầu.
- 💧 **Data Lake**: DMS không chỉ chuyển vào Database mà còn có thể đẩy dữ liệu vào **Amazon S3** để tạo kho dữ liệu thô.

### 💡 Ví dụ (nếu có)

```example
- Trường hợp 1: Bạn đang chạy PostgreSQL trên server riêng và muốn chuyển lên Amazon RDS PostgreSQL. Đây là Homogeneous migration, bạn chỉ cần dùng AWS DMS để đẩy dữ liệu qua.
- Trường hợp 2: Bạn muốn bỏ hệ thống Oracle cũ kỹ để chuyển sang Amazon Aurora PostgreSQL nhằm tiết kiệm chi phí. Đây là Heterogeneous migration, bạn phải dùng AWS SCT để chuyển đổi cấu trúc Oracle sang PostgreSQL trước, sau đó mới dùng AWS DMS để chuyển dữ liệu.
```

---

## 📝 Knowledge Check

### ❓ Question 1

Which statement that compares a **database service** (**dịch vụ cơ sở dữ liệu**) that AWS manages with a database on an **Amazon EC2 instance** is true?

A. AWS manages operating system (OS) patches for a database on an EC2 instance.

B. Configuring backups for a database on a managed database service isn't needed.

C. Configuring backups for a database on an EC2 instance isn't needed.

D. AWS manages database patches for a database on a managed database service.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- Khi sử dụng **Managed Database Service** (**dịch vụ cơ sở dữ liệu được quản lý**) như **Amazon RDS**, AWS sẽ chịu trách nhiệm thực hiện các tác vụ quản trị như **Database Patches** (**vá lỗi cơ sở dữ liệu**) và **Database Backups** (**sao lưu dữ liệu**).
- Điều này giúp giảm bớt **Administrative burden** (**gánh nặng quản trị**) cho người dùng.

### ❌ Vì sao đáp án khác sai?

- **A:** Trên **Amazon EC2**, người dùng có toàn quyền kiểm soát nên AWS không tự ý vá lỗi phần mềm cơ sở dữ liệu cho bạn.
- **B:** Mặc dù AWS hỗ trợ công cụ, nhưng bạn vẫn cần cấu hình các tham số như **Retention Period** (**thời gian lưu trữ bản sao lưu**).
- **C:** Trên EC2, người dùng phải tự chịu trách nhiệm hoàn toàn cho việc sao lưu dữ liệu.

💡 **Mẹo nhớ nhanh:** Với **Managed Service**, AWS làm hộ bạn mọi thứ từ lớp DB trở xuống. Với **EC2**, bạn phải tự làm từ lớp DB trở lên.

---

### ❓ Question 2

A small startup company is deciding which database service to use for an enrollment system for their online training website. Which requirements might lead them to select **Amazon RDS** rather than **Amazon DynamoDB**? (Select TWO.)

A. Student, course, and registration data are stored in many different tables.

B. The data is highly structured.

C. Data and transactions must be encrypted to protect personal information.

D. Data must be backed up in case of disasters.

E. The enrollment system must be highly available.

✅ **Đáp án đúng:** A và B

### 🧠 Giải thích

- **Dữ liệu quan hệ (Relational):** RDS được tối ưu hóa cho dữ liệu có cấu trúc chặt chẽ (**Highly structured**) và lưu trữ trong các bảng có liên kết với nhau.
- **Truy vấn phức tạp:** RDS hỗ trợ tốt các phép toán **Joins** giữa nhiều bảng khác nhau (như bảng sinh viên, khóa học và đăng ký).

### ❌ Vì sao đáp án khác sai?

- **C, D, E:** Cả **Amazon RDS** và **Amazon DynamoDB** đều hỗ trợ mã hóa (**Encryption**), sao lưu (**Backup**) và tính sẵn sàng cao (**High Availability**). Đây không phải là các yếu tố riêng biệt để chọn cái này thay vì cái kia.

---

### ❓ Question 3

A startup company is building an order inventory system with a web frontend and is looking for a **real-time transactional database** (**cơ sở dữ liệu giao dịch thời gian thực**). Which service would best meet their needs?

A. Amazon DocumentDB (with MongoDB compatibility)

B. Amazon DynamoDB

C. Amazon Neptune

D. Amazon Redshift

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Amazon DynamoDB** cung cấp hiệu suất ổn định tính bằng mili giây (**Millisecond performance**) và có khả năng mở rộng cực lớn, rất phù hợp cho các ứng dụng web có lưu lượng giao dịch thời gian thực cao.
- Đây là dịch vụ **Serverless** (**không máy chủ**) giúp ứng dụng phản hồi nhanh chóng mà không cần quản lý hạ tầng phức tạp.

### ❌ Vì sao đáp án khác sai?

- **A:** Dùng cho dữ liệu dạng tài liệu JSON linh hoạt, không tối ưu bằng DynamoDB cho các giao dịch đơn giản nhưng tốc độ cực cao.
- **C:** Là **Graph database** (**CSDL đồ thị**) dùng cho các dữ liệu có mối quan hệ phức tạp.
- **D:** Là **Data warehouse** dùng cho phân tích báo cáo (**OLAP**), không dùng cho giao dịch thời gian thực (**OLTP**).

---

### ❓ Question 4

A small game company is designing an online game. Which proposed online game object features could make **Amazon DynamoDB** a better solution? (Select TWO.)

A. A high amount of read activity on player-created objects and a low amount of writes

B. Game data items that include binary data and might exceed 700 MB

C. A set of common object attributes for player-created objects

D. Game items that can be modified using data contained in other tables

E. Unpredictable object attributes for player-created objects

✅ **Đáp án đúng:** A, E (và thường đi kèm với yêu cầu về khả năng mở rộng cho hàng triệu người dùng)

### 🧠 Giải thích

- **Flexible Schema (Lược đồ linh hoạt):** DynamoDB cho phép lưu trữ các mục dữ liệu có các thuộc tính không đoán trước được (**Unpredictable attributes**), điều này rất phổ biến trong các trò chơi nơi người chơi có thể tạo vật phẩm tùy ý.
- **Khả năng mở rộng:** DynamoDB cực kỳ phù hợp để lưu trữ dữ liệu người chơi và bảng xếp hạng cho hàng triệu người dùng đồng thời và cho phép truy vấn dữ liệu tốc độ cao.

### ❌ Vì sao đáp án khác sai?

- **C:** RDS có thể xử lý tốt các thuộc tính chung và hỗ trợ **Read Replicas** cho lượng truy cập đọc cao.
- **B:** DynamoDB có giới hạn kích thước mục (400 KB). Dữ liệu 700 MB nên được lưu trữ trên **Amazon S3**.
- **D:** Việc sửa đổi dựa trên bảng khác yêu cầu phép **Join**, vốn là thế mạnh của **Relational Database** như RDS.

---

### ❓ Question 5

An organization is concerned about an increase in fraud. Which service could help with building **real-time graph database queries** (**truy vấn CSDL đồ thị thời gian thực**) for **fraud detection** (**phát hiện gian lận**)?

A. Amazon DynamoDB

B. Amazon Redshift

C. Amazon Neptune

D. Amazon RDS

✅ **Đáp án đúng:** C

### 🧠 Giải thích

- **Amazon Neptune** là dịch vụ **Graph database** chuyên dụng, cho phép truy vấn các mối quan hệ phức tạp giữa các tập dữ liệu khác nhau.
- **Fraud detection** là một trong những trường hợp sử dụng phổ biến nhất của Neptune nhờ khả năng phát hiện các mẫu liên kết bất thường nhanh chóng.

---

### ❓ Question 7

Which techniques should be used to secure an **Amazon RDS database**? (Select THREE.)

A. An Amazon Virtual Private Cloud (Amazon VPC) gateway endpoint

B. AWS Identity and Access Management (IAM) policies to define access at the table, row, and column levels

C. A virtual private cloud (VPC) to provide instance isolation

D. Security groups to control network access to individual RDS instances

E. A virtual private gateway (VGW) to filter traffic

F. Encryption both at rest and in transit to protect sensitive data

✅ **Đáp án đúng:** C, D, F

### 🧠 Giải thích

- **VPC Isolation:** Đặt database trong **Private Subnet** của VPC để cách ly khỏi internet.
- **Security Groups:** Sử dụng như một tường lửa ảo để kiểm soát các dải IP được phép kết nối đến DB instance.
- **Encryption:** Mã hóa dữ liệu tĩnh qua **AWS KMS** và dữ liệu đang truyền qua **SSL/TLS**.

### ❌ Vì sao đáp án khác sai?

- **A:** Gateway endpoint dùng cho S3 và DynamoDB, không dùng cho RDS.
- **B:** IAM trong RDS dùng để quản lý tài nguyên (tạo/xóa DB), còn quyền truy cập bảng/hàng/cột phải dùng tính năng bảo mật của **Database Engine**.

---

### ❓ Question 8

Which techniques should be used to secure **Amazon DynamoDB**? (Select THREE.)

A. Security groups to control network access to individual instances

B. A virtual private cloud (VPC) to provide instance isolation

C. AWS Identity and Access Management (IAM) policies to define access at the table, item, or attribute level

D. A virtual private gateway (VGW) to filter traffic

E. Encryption to protect sensitive data

F. An Amazon Virtual Private Cloud (Amazon VPC) gateway endpoint

✅ **Đáp án đúng:** C, E, F

### 🧠 Giải thích

- **IAM Policies:** DynamoDB hỗ trợ kiểm soát truy cập chi tiết (**Fine-grained access control**) đến tận cấp độ **Item** (**mục**) hoặc **Attribute** (**thuộc tính**).
- **Encryption:** DynamoDB mặc định mã hóa toàn bộ dữ liệu người dùng khi ở trạng thái nghỉ.
- **VPC Gateway Endpoint:** Giúp các tài nguyên trong VPC kết nối đến DynamoDB mà không cần đi qua internet công cộng.

---

### ❓ Question 9

A company wants to migrate their on-premises **Oracle database** to **Amazon Aurora MySQL**. Which process describes the high-level steps most accurately?

A. Use AWS DMS to migrate the data, and then use AWS SCT to convert the schema.

B. Use AWS DMS to directly migrate from the Oracle database to Amazon Aurora MySQL.

C. Use AWS Schema Conversion Tool (AWS SCT) to synchronously convert the schema and migrate the data.

D. Use AWS Schema Conversion Tool (AWS SCT) to convert the schema, and then use AWS Database Migration Service (AWS DMS) to migrate the data.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- Đây là quy trình **Heterogeneous migration** (**di chuyển khác loại**).
- **Bước 1:** Dùng **AWS SCT** để chuyển đổi cấu trúc (**Schema**) từ Oracle sang định dạng của MySQL.
- **Bước 2:** Sau khi có cấu trúc ở đích, dùng **AWS DMS** để thực hiện việc di chuyển dữ liệu thực tế.

---

### ❓ Question 10

A cloud architect is setting up an application to use an **Amazon RDS MySQL** DB instance. The database must be architected for **high availability** across **Availability Zones** and **AWS Regions** with minimal downtime. How should they meet this requirement?

A. Set up an RDS MySQL Single-AZ DB instance. Configure a read replica in a different Region.

B. Set up an RDS MySQL Single-AZ DB instance. Copy automated snapshots to at least one other Region.

C. Set up an RDS MySQL Multi-AZ DB instance. Configure an appropriate backup window.

D. Set up an RDS MySQL Multi-AZ DB instance. Configure a read replica in a different Region.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Multi-AZ Deployment:** Đảm bảo tính sẵn sàng cao trong một Region bằng cách tự động sao chép đồng bộ sang một **Standby Instance** ở AZ khác.
- **Read Replica (Cross-Region):** Giúp mở rộng tính sẵn sàng sang một **AWS Region** khác, đồng thời hỗ trợ phục hồi sau thảm họa và tăng hiệu suất đọc.

💡 **Mẹo nhớ nhanh:** **Multi-AZ** = Dự phòng tại chỗ (Failover trong 1 Region). **Read Replica** = Mở rộng quy mô và dự phòng thảm họa (khác Region).

---

# Practice Test - Module 06: Adding a Database Layer (Giai đoạn 3)

## Question 1

In the AWS Shared Responsibility Model, which of the following is a responsibility of the customer when running a database on an Amazon EC2 instance?

A. Patching the underlying host operating system

B. Managing the database software and patches

C. Replacing failed hardware components in the data center

D. Configuring the physical network infrastructure

✅ Correct Answer: B

### 🧠 Explanation

Khi chạy Database trên Amazon EC2, bạn đang sử dụng mô hình "Unmanaged". Khách hàng chịu trách nhiệm quản lý mọi thứ từ lớp Hệ điều hành khách (Guest OS) trở lên, bao gồm cài đặt, cấu hình và vá lỗi phần mềm Database.

### ❌ Why Other Choices Are Wrong

- A: AWS chịu trách nhiệm vá lỗi Host OS và hạ tầng vật lý.
- C, D: AWS quản lý toàn bộ hạ tầng vật lý, trung tâm dữ liệu và mạng lưới cơ sở.

---

## Question 2

A company needs a relational database that can automatically scale its compute capacity based on application demand without manual intervention. Which AWS service is the best fit?

A. Amazon RDS for MySQL

B. Amazon Aurora Serverless

C. Amazon Redshift

D. Amazon DynamoDB

✅ Correct Answer: B

### 🧠 Explanation

Amazon Aurora Serverless là cấu hình tự động mở rộng (on-demand auto scaling) cho Aurora. Nó tự động quản lý dung lượng và điều chỉnh dựa trên nhu cầu thực tế của ứng dụng, phù hợp cho các workload không ổn định.

### ❌ Why Other Choices Are Wrong

- A: RDS MySQL yêu cầu bạn chọn kích cỡ Instance cụ thể và việc thay đổi thường cần thao tác thủ công.
- C: Redshift là kho dữ liệu dành cho phân tích (OLAP), không tối ưu cho giao dịch ứng dụng thông thường.
- D: DynamoDB là cơ sở dữ liệu NoSQL, không phải Relational.

---

## Question 3

A startup is developing a mobile game and needs to store player profiles. The schema is expected to change frequently as new features are added. Which database type is most suitable?

A. Relational database with a strict schema

B. Non-relational database with a flexible schema

C. Data warehouse for complex join queries

D. Ledger database for immutable history

✅ Correct Answer: B

### 🧠 Explanation

Cơ sở dữ liệu Non-relational (NoSQL) như DynamoDB hỗ trợ Flexible schemas, cho phép thay đổi cấu trúc dữ liệu dễ dàng mà không cần định nghĩa trước một cách chặt chẽ, rất phù hợp cho các ứng dụng có tính năng thay đổi nhanh.

### ❌ Why Other Choices Are Wrong

- A: Relational database có Schema chặt chẽ, việc thay đổi cấu trúc bảng thường phức tạp và tốn thời gian.
- C: Data warehouse dành cho phân tích dữ liệu lớn, không dùng để lưu hồ sơ người dùng game.
- D: Ledger database dùng để lưu nhật ký giao dịch bất biến.

---

## Question 4

A developer is building an application that requires microsecond latency for a session store. Which purpose-built database should be used?

A. Amazon Neptune

B. Amazon DocumentDB

C. Amazon MemoryDB for Redis

D. Amazon RDS Proxy

✅ Correct Answer: C

### 🧠 Explanation

Amazon MemoryDB for Redis là cơ sở dữ liệu trong bộ nhớ (In-memory) cung cấp tốc độ truy cập cực nhanh (micro giây) và độ bền vững dữ liệu cao, lý tưởng cho việc lưu trữ Session hoặc Caching.

### ❌ Why Other Choices Are Wrong

- A: Neptune là Graph database dùng cho dữ liệu quan hệ phức tạp.
- B: DocumentDB là Document database dùng cho dữ liệu JSON.
- D: RDS Proxy là trình trung gian quản lý kết nối, không phải là cơ sở dữ liệu.

---

## Question 5

A global application needs to provide fast, local read and write performance to users in different geographic locations. Which feature of Amazon DynamoDB should be implemented?

A. Read Replicas

B. Multi-AZ Deployments

C. Global Tables

D. DynamoDB Streams

✅ Correct Answer: C

### 🧠 Explanation

DynamoDB Global Tables cung cấp khả năng sao chép đa vùng (Multi-Region), đa hoạt động (Multi-active) giúp người dùng ở khắp nơi trên thế giới truy cập dữ liệu tại địa phương với tốc độ cao.

### ❌ Why Other Choices Are Wrong

- A: Read Replicas thường dùng cho RDS để tăng khả năng đọc, không phải là tính năng chính của DynamoDB cho ghi đa vùng.
- B: Multi-AZ đảm bảo tính sẵn sàng cao trong một Region, không giải quyết vấn đề độ trễ toàn cầu.
- D: DynamoDB Streams dùng để ghi lại các thay đổi dữ liệu để kích hoạt sự kiện.

---

## Question 6

A company wants to migrate an on-premises Oracle database to Amazon Aurora PostgreSQL. Because the source and target database engines are different, which tool is required to convert the database schema?

A. AWS Database Migration Service (AWS DMS)

B. AWS Schema Conversion Tool (AWS SCT)

C. AWS DataSync

D. AWS Snowball Edge

✅ Correct Answer: B

### 🧠 Explanation

Trong quá trình di chuyển khác loại (Heterogeneous migration), AWS SCT được sử dụng để chuyển đổi lược đồ (Schema) và mã SQL từ Engine nguồn sang Engine đích tương ứng.

### ❌ Why Other Choices Are Wrong

- A: AWS DMS dùng để di chuyển dữ liệu thực tế sau khi Schema đã được chuyển đổi.
- C, D: Dùng để di chuyển file hoặc dữ liệu thô, không có chức năng chuyển đổi Schema DB.

---

## Question 7

A financial services company needs to maintain an accurate, immutable, and cryptographically verifiable history of all transactions. Which AWS service should they use?

A. Amazon RDS for SQL Server

B. Amazon QLDB

C. Amazon Timestream

D. Amazon DynamoDB with Global Tables

✅ Correct Answer: B

### 🧠 Explanation

Amazon Quantum Ledger Database (QLDB) là cơ sở dữ liệu sổ cái (Ledger) cung cấp nhật ký giao dịch bất biến, minh bạch và có thể xác minh bằng mã hóa.

### ❌ Why Other Choices Are Wrong

- A, D: Dữ liệu có thể bị xóa hoặc sửa đổi bởi người quản trị, không đảm bảo tính bất biến (immutability) tuyệt đối như QLDB.
- C: Timestream dành cho dữ liệu chuỗi thời gian (IoT).

---

## Question 8

An application's database is experiencing performance issues due to a high volume of short-lived connections from AWS Lambda functions. Which solution would best improve scalability and reduce resource exhaustion?

A. Upgrade to a larger RDS instance type

B. Enable Multi-AZ deployment

C. Implement Amazon RDS Proxy

D. Create an Amazon Redshift cluster

✅ Correct Answer: C

### 🧠 Explanation

Amazon RDS Proxy giúp gom cụm và chia sẻ các kết nối (connection pooling), giúp ứng dụng mở rộng tốt hơn và ngăn chặn việc cạn kiệt tài nguyên CPU/RAM do có quá nhiều kết nối mở đồng thời.

### ❌ Why Other Choices Are Wrong

- A: Chỉ là giải pháp tạm thời (Vertical scaling), không giải quyết được gốc rễ vấn đề quản lý kết nối hiệu quả.
- B: Tăng tính sẵn sàng cao, không giúp quản lý Connection.
- D: Redshift dùng cho phân tích, không phải database cho ứng dụng Lambda transactional.

---

## Question 10

A company is planning a disaster recovery strategy for its relational database. They need a solution that provides a low Recovery Time Objective (RTO) by maintaining a synchronous standby instance in a different area of the same region. What should they enable?

A. Read Replicas

B. Automated Snapshots

C. Multi-AZ Deployment

D. Cross-Region Replication

✅ Correct Answer: C

### 🧠 Explanation

Multi-AZ Deployment duy trì một bản sao đồng bộ (synchronous replication) tại một Availability Zone khác trong cùng một Region. Nếu instance chính lỗi, hệ thống sẽ tự động failover sang instance dự phòng với thời gian gián đoạn tối thiểu.

### ❌ Why Other Choices Are Wrong

- A: Read Replicas sử dụng cơ chế sao chép không đồng bộ (asynchronous), chủ yếu để scale khả năng đọc.
- B: Snapshots dùng để khôi phục dữ liệu nhưng thời gian khôi phục (RTO) lâu hơn nhiều so với failover tự động.
- D: Dùng cho dự phòng thảm họa cấp độ vùng (Region), không phải trong cùng 1 Region.

---

## Question 11

An IoT application collects temperature readings from thousands of sensors every minute. The data needs to be stored in a sequence of data points for time-series analysis. Which database is specifically built for this?

A. Amazon Keyspaces

B. Amazon Neptune

C. Amazon Timestream

D. Amazon DynamoDB

✅ Correct Answer: C

### 🧠 Explanation

**Amazon Timestream** là cơ sở dữ liệu chuỗi thời gian (**time-series database**) được thiết kế chuyên dụng để lưu trữ và phân tích hàng nghìn tỷ sự kiện mỗi ngày với tốc độ nhanh gấp 1000 lần và chi phí thấp hơn 1/10 so với các cơ sở dữ liệu quan hệ.

### ❌ Why Other Choices Are Wrong

- **A**: Dùng cho khối lượng công việc của Apache Cassandra (Wide-column).
- **B**: Dùng cho dữ liệu đồ thị (**Graph**).
- **D**: Mặc dù có thể lưu trữ dữ liệu thời gian, nhưng không được tối ưu chuyên dụng cho các truy vấn chuỗi thời gian phức tạp như Timestream.

---

## Question 12

A social media application needs to store and navigate complex relationships between users, such as "friends of friends." Which database model is most efficient for this pattern?

A. Key-value

B. Graph

C. Document

D. Relational

✅ Correct Answer: B

### 🧠 Explanation

Mô hình **Graph** (được cung cấp bởi **Amazon Neptune**) là hiệu quả nhất để lưu trữ và điều hướng các mối quan hệ phức tạp và có tính liên kết chặt chẽ. Nó cho phép thực hiện các truy vấn như "tìm bạn của bạn tôi" với độ trễ cực thấp.

### ❌ Why Other Choices Are Wrong

- **A**: Tối ưu cho việc tra cứu theo khóa đơn giản.
- **C**: Tối ưu cho dữ liệu dạng phân cấp/JSON.
- **D**: Yêu cầu các phép **JOIN** phức tạp và tốn tài nguyên khi xử lý các mối quan hệ đa tầng.

---

## Question 13

A company is migrating a critical SQL Server database to Amazon RDS. They must ensure that the data is encrypted while at rest to meet regulatory requirements. How can they achieve this?

A. Enable SSL/TLS in the application connection string

B. Use AWS Key Management Service (AWS KMS) during DB instance creation

C. Place the database instance in a Public Subnet

D. Use a Virtual Private Gateway (VGW)

✅ Correct Answer: B

### 🧠 Explanation

Để mã hóa dữ liệu tĩnh (**at rest**), bạn cần bật tính năng mã hóa khi tạo DB Instance và sử dụng các khóa được quản lý bởi **AWS Key Management Service (AWS KMS)**.

### ❌ Why Other Choices Are Wrong

- **A**: SSL/TLS dùng để mã hóa dữ liệu khi đang truyền tải (**in transit**), không phải khi lưu trữ.
- **C**: Đặt trong Public Subnet làm tăng rủi ro bảo mật, không liên quan đến mã hóa.
- **D**: VGW dùng cho kết nối VPN/Direct Connect.

---

---

## Question 14

A web application has a read-heavy workload. The database is an RDS for PostgreSQL instance. How can the architect scale the database to handle the increased read traffic without affecting write performance?

A. Enable Multi-AZ deployment

B. Add RDS Read Replicas

C. Increase the EBS volume size

D. Use AWS Database Migration Service

✅ Correct Answer: B

### 🧠 Explanation

RDS Read Replicas cho phép bạn tạo ra các bản sao chỉ đọc. Ứng dụng có thể chuyển các truy vấn đọc sang các replica này, giúp giảm tải cho database chính và giữ nguyên hiệu suất ghi.

---

## Question 15

A developer wants to build a serverless application that uses a NoSQL database. The database must support key-value pairs and deliver single-digit millisecond performance at any scale. Which service is the best choice?

A. Amazon Aurora Serverless

B. Amazon DynamoDB

C. Amazon DocumentDB

D. Amazon RDS for MariaDB

✅ Correct Answer: B

### 🧠 Explanation

**Amazon DynamoDB** là cơ sở dữ liệu NoSQL Key-value hoàn toàn không máy chủ (**serverless**), cung cấp hiệu suất ổn định ở mức mili giây một chữ số ở bất kỳ quy mô nào.

### ❌ Why Other Choices Are Wrong

- **A**: Là cơ sở dữ liệu quan hệ (**Relational**).
- **C**: Tương thích MongoDB, chuyên về Document, không phải mô hình Key-value thuần túy cho hiệu năng cao nhất của DynamoDB.
- **D**: Là dịch vụ cơ sở dữ liệu quan hệ có quản lý (không phải serverless hoàn toàn theo định nghĩa của DynamoDB).

---

## Question 16

A company needs to move its on-premises database to AWS with minimal downtime. The database is several terabytes in size. Which service helps manage the data transfer and handles the ongoing replication of changes?

A. AWS Snowball

B. AWS Application Discovery Service

C. AWS Database Migration Service (AWS DMS)

D. AWS Backup

✅ Correct Answer: C

### 🧠 Explanation

**AWS DMS** giúp bạn di chuyển cơ sở dữ liệu vào AWS một cách nhanh chóng và an toàn. Điểm mạnh nhất của nó là giữ cho cơ sở dữ liệu nguồn vẫn hoạt động trong suốt quá trình di chuyển, giúp giảm thiểu thời gian ngừng hoạt động (**minimal downtime**).

### ❌ Why Other Choices Are Wrong

- **A**: Dùng để vận chuyển vật lý dữ liệu khối lượng lớn, không hỗ trợ sao chép thay đổi liên tục (**ongoing replication**).
- **B**: Dùng để khám phá hạ tầng hiện có, không phải để di chuyển dữ liệu.
- **D**: Dùng để quản lý các bản sao lưu trên AWS.

---

## Question 17

To improve the security of an RDS database, a cloud architect wants to ensure that the database is not accessible from the internet. Which step should they take?

A. Deploy the RDS instance in a Private Subnet

B. Assign a Public IP address to the instance

C. Disable the VPC Peering connection

D. Use an Internet Gateway for all traffic

✅ Correct Answer: A

### 🧠 Explanation

Đưa DB Instance vào **Private Subnet** (phân vùng mạng riêng) là một bước bảo mật cơ bản để đảm bảo rằng cơ sở dữ liệu không có địa chỉ IP công cộng và không thể bị truy cập trực tiếp từ internet.

### ❌ Why Other Choices Are Wrong

- **B, D**: Các bước này khiến database có thể bị truy cập từ internet (mất bảo mật).
- **C**: VPC Peering dùng để kết nối 2 VPC, không trực tiếp giải quyết vấn đề truy cập từ internet.

---

## Question 18

A database administrator needs to restore an RDS instance to a specific point in time within the last 30 days. Which RDS feature makes this possible?

A. Database Snapshots

B. Automated Backups

C. Multi-AZ Failover

D. Read Replica Promotion

✅ Correct Answer: B

### 🧠 Explanation

Tính năng **Automated Backups** của RDS cho phép khôi phục cơ sở dữ liệu về bất kỳ thời điểm nào (**Point-in-time recovery - PITR**) trong khoảng thời gian lưu trữ (mặc định lên đến 35 ngày).

### ❌ Why Other Choices Are Wrong

- **A**: Snapshots là các bản sao lưu thủ công tại một thời điểm cố định.
- **C**: Dùng để đảm bảo tính sẵn sàng cao, không phải để khôi phục dữ liệu về quá khứ.
- **D**: Dùng để chuyển đổi bản sao chỉ đọc thành database chính.

---

## Question 19

An application requires a MongoDB-compatible database to store JSON documents. The team wants a fully managed service that provides the scalability and availability of AWS. Which service should they choose?

A. Amazon DynamoDB

B. Amazon DocumentDB

C. Amazon Keyspaces

D. Amazon Aurora

✅ Correct Answer: B

### 🧠 Explanation

**Amazon DocumentDB** (tương thích MongoDB) là dịch vụ cơ sở dữ liệu tài liệu được quản lý hoàn toàn, nhanh chóng, có thể mở rộng và có tính sẵn sàng cao để lưu trữ các khối lượng công việc JSON.

### ❌ Why Other Choices Are Wrong

- **A**: Mặc dù hỗ trợ JSON nhưng không tương thích với các API của MongoDB.
- **C**: Tương thích Apache Cassandra.
- **D**: Là cơ sở dữ liệu quan hệ (SQL).

---

## Question 20

A cloud architect is designing a database layer for a high-traffic e-commerce site. According to the AWS Well-Architected Framework, which principle should be applied when selecting a database?

A. Always use the most expensive instance for maximum durability

B. Use a data-driven approach to select the correct resource type and size

C. Stick to a single relational database for all microservices

D. Disable encryption to improve performance efficiency

✅ Correct Answer: B

### 🧠 Explanation

Nguyên tắc của **Well-Architected Framework** là sử dụng phương pháp tiếp cận dựa trên dữ liệu (**data-driven approach**) để lựa chọn loại tài nguyên và kích thước phù hợp dựa trên các yêu cầu về hiệu năng, chi phí và khả năng mở rộng.

### ❌ Why Other Choices Are Wrong

- **A**: Gây lãng phí chi phí, không tối ưu.
- **C**: Đi ngược lại nguyên tắc "Purpose-built database" (sử dụng database phù hợp cho từng nhu cầu riêng biệt).
- **D**: Đi ngược lại nguyên tắc bảo mật.

---

## 📊 Final Score

- **Score:** 100% (20/20)

## 🎯 Weak Topics

- **Không có:** Bạn đã hoàn thành xuất sắc tất cả các chủ đề. Hãy tiếp tục duy trì phong độ này cho các Module tiếp theo!

💡 **Mẹo nhớ nhanh Module 6:** - **RDS** = Relational/SQL/Joins.

- **DynamoDB** = NoSQL/Key-Value/Mili-second.
- **Redshift** = OLAP/Data Warehouse/Petabytes.
- **DMS** = Migrate data.
- **SCT** = Convert Schema (khác engine).
- **Multi-AZ** = High Availability (Sync).
- **Read Replica** = Scalability (Async).
