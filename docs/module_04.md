# Module 04: Adding a Storage Layer with Amazon S3

## 🧭 Overview

- Định nghĩa **Amazon S3** và cách thức hoạt động của dịch vụ này.
- Nhận diện các vấn đề thực tế mà **Amazon S3** có thể giải quyết.
- Mô tả cách di chuyển dữ liệu đi và đến **Amazon S3**.
- Quản lý nội dung lưu trữ hiệu quả và đưa ra các khuyến nghị dựa trên yêu cầu thực tế.
- Cấu hình **Static Website** (Trang web tĩnh) trên **Amazon S3**.
- Áp dụng các nguyên tắc của **AWS Well-Architected Framework** khi thiết kế lớp lưu trữ.

---

## 📘 Phần 1: Defining Amazon S3

### 🔑 Ý chính

- **Amazon S3 (Simple Storage Service)**: Dịch vụ lưu trữ đối tượng (**Object Storage**) được thiết kế để lưu trữ và truy xuất lượng dữ liệu bất kỳ từ bất cứ đâu.
- **Unstructured Data**: Lưu trữ dữ liệu phi cấu trúc với dung lượng không giới hạn (**Unlimited**).
- **Object-based**: Khác với lưu trữ dạng khối hay tệp tin, S3 quản lý dữ liệu dưới dạng các đối tượng riêng biệt.
- **Maximum File Size**: Kích thước tối đa của một **Object** đơn lẻ là **5 TB**.
- **Universal Namespace**: Tên **Bucket** phải là duy nhất trên toàn thế giới.

### 🧠 Giải thích

**So sánh các loại lưu trữ:**

- **Block Storage** (Lưu trữ khối): Dữ liệu được lưu thành các khối có kích thước cố định (thường dùng cho ổ đĩa hệ điều hành).
- **File Storage** (Lưu trữ tệp): Dữ liệu tổ chức theo cấu trúc phân cấp thư mục.
- **Object Storage** (Lưu trữ đối tượng): Dữ liệu được lưu kèm theo các thuộc tính (**Attributes**) và siêu dữ liệu (**Metadata**).

**Các thành phần chính:**

- **Bucket** (Thùng chứa): Là vật chứa các **Object**. Mỗi **Bucket** có một tên định danh và một **Endpoint** (điểm cuối) theo vùng địa lý (**Region**) cụ thể.
- **Object** (Đối tượng): Thực thể cơ bản lưu trong S3, bao gồm:
  - **Key**: Tên của đối tượng (định danh duy nhất trong bucket).
  - **Value**: Dữ liệu thực tế của tệp tin.
  - **Metadata**: Các cặp tên-giá trị chứa thông tin mô tả tệp.
  - **Version ID**: Định danh phiên bản của đối tượng.

**Cấu trúc thư mục ảo:**
Amazon S3 không có thư mục thực sự. Nó sử dụng **Prefixes** (Tiền tố) trong tên **Key** để tạo cảm giác về cấu trúc thư mục (Ví dụ: `photos/2024/beach.jpg`).

### 📌 Ghi nhớ nhanh

- 🛡️ **Durability** (Độ bền): Đạt mức **11 con số 9** (99.999999999%) đối với lớp lưu trữ Standard, giúp đảm bảo dữ liệu không bị mất mát.
- 🟢 **Availability** (Độ sẵn sàng): Đạt mức **4 con số 9** (99.99%) đối với lớp Standard, đảm bảo khả năng truy cập dữ liệu khi cần.
- 🚀 **High Performance**: Tự động mở rộng quy mô để xử lý hàng nghìn yêu cầu mỗi giây.

### 💡 Ví dụ (nếu có)

```example
Giả sử bạn có một bucket tên là "graphics-bucket", cấu trúc Prefix sẽ hoạt động như sau:

- Key: photos/2022/cat.jpg
- Key: photos/2022/dog.png
- Key: video/tutorial.mp4

Khi bạn thực hiện truy vấn với Prefix là "photos/2022", S3 sẽ trả về tất cả các đối tượng bắt đầu bằng cụm từ đó, tạo ra trải nghiệm giống như bạn đang mở thư mục năm 2022.
```

---

## 📘 Phần 2: Using Amazon S3

### 🔑 Ý chính

- **Media Hosting** (Lưu trữ và phân phối phương tiện): Lưu trữ các tệp video, ảnh và nhạc với quy mô lớn.
- **Static Website Hosting** (Lưu trữ trang web tĩnh): Chứa các tệp HTML, CSS, và JavaScript mà không cần máy chủ web.
- **Data Store for Computation and Analytics** (Kho dữ liệu cho tính toán và phân tích): Làm nơi lưu trữ dữ liệu thô (Raw data) để các dịch vụ khác xử lý.
- **Backup and Disaster Recovery** (Sao lưu và phục hồi sau thảm họa): Giải pháp lưu trữ an toàn, có khả năng sao chép dữ liệu giữa các vùng địa lý.

### 🧠 Giải thích

- **Phân phối nội dung Media**: S3 thường kết hợp với **Amazon CloudFront** (Mạng phân phối nội dung - CDN). Dữ liệu từ S3 Bucket được đẩy đến các **Edge Locations** (Điểm biên) giúp người dùng tải video hoặc hình ảnh với độ trễ thấp nhất. 🎥
- **Website tĩnh**: Phù hợp cho các trang web không yêu cầu xử lý phía máy chủ (Server-side processing). Bạn chỉ cần tải nội dung lên và kích hoạt tính năng hosting để có một URL công khai. 🌐
- **Phân tích dữ liệu lớn**: Dữ liệu thô được lưu trên S3, sau đó các cụm máy chủ như **Amazon EMR** hoặc **Amazon EC2** sẽ trích xuất (Extract), biến đổi (Transform) và nạp lại (Load) kết quả vào một Bucket khác. Các công cụ như **Amazon Athena** hoặc **QuickSight** có thể trực tiếp khai thác dữ liệu này để đưa ra biểu đồ báo cáo. 📊
- **Sao lưu và Lưu trữ**: Hỗ trợ bảo vệ các dữ liệu quan trọng của doanh nghiệp. Sử dụng tính năng **S3 Replication** (Sao chép S3) để tự động copy dữ liệu từ một **Region** (Vùng) này sang một **Region** khác nhằm phòng tránh sự cố vật lý tại một khu vực. 💾

### 📌 Ghi nhớ nhanh

- ⚡ **Spikes in demand** (Nhu cầu tăng đột biến): S3 tự động xử lý khi lưu lượng truy cập trang web hoặc tải file tăng cao bất ngờ.
- 🔄 **Data Integration** (Tích hợp dữ liệu): S3 đóng vai trò là "trung tâm" kết nối giữa dữ liệu thô và các công cụ phân tích thông minh.
- 🌍 **Global Reach** (Tiếp cận toàn cầu): Dễ dàng sao chép dữ liệu đi khắp thế giới chỉ với vài cấu hình đơn giản.

### 💡 Ví dụ (nếu có)

```example
Quy trình xử lý dữ liệu của một doanh nghiệp tài chính:
1. Salesperson tải tệp doanh số hàng ngày (CSV) lên S3 (Unprocessed raw data).
2. Một cụm máy chủ EC2 tự động quét tệp này, tính toán lợi nhuận (Transform).
3. Kết quả đã xử lý được lưu vào một S3 Bucket khác (Processed data).
4. Giám đốc sử dụng Amazon QuickSight để xem biểu đồ tăng trưởng dựa trên tệp kết quả đó.
```

---

## 📘 Phần 3: Moving data to and from Amazon S3

### 🔑 Ý chính

- **Storing Objects** (Lưu trữ đối tượng): Không giới hạn số lượng đối tượng trong một **Bucket**. Đối tượng được mã hóa mặc định (**Encrypted by default**).
- **Upload Options** (Các tùy chọn tải lên): Đa dạng cách thức từ giao diện đồ họa đến lập trình.
- **Multipart Upload** (Tải lên nhiều phần): Kỹ thuật chia nhỏ tệp lớn để tải lên song song, tăng tốc độ và độ tin cậy.
- **S3 Transfer Acceleration** (Tăng tốc chuyển dữ liệu S3): Sử dụng mạng lưới toàn cầu của AWS để tối ưu tốc độ truyền tải từ xa.
- **AWS Transfer Family**: Dịch vụ quản lý hoàn toàn cho các giao thức truyền tệp truyền thống như **SFTP**, **FTPS**, và **FTP**.

### 🧠 Giải thích

- **Các phương thức tải lên phổ biến**:
  - **AWS Management Console**: Giao diện web, hỗ trợ kéo thả, giới hạn tối đa **160 GB** cho mỗi lần tải lên. 🖱️
  - **AWS CLI**: Sử dụng lệnh từ terminal, phù hợp cho tự động hóa. 💻
  - **AWS SDKs**: Sử dụng mã nguồn để tải lên theo lập trình (Python, Java, v.v.). ⚙️
  - **Amazon S3 REST API**: Gửi các yêu cầu **HTTP PUT** trực tiếp.

- **Multipart Upload**:
  - Được khuyến nghị cho các đối tượng từ **100 MB** trở lên (Bắt buộc nếu tệp > 5 GB). 📦
  - **Ưu điểm**: Cải thiện thông lượng (**Throughput**), có thể tạm dừng và tiếp tục, phục hồi nhanh từ lỗi mạng (chỉ cần tải lại phần bị lỗi thay vì toàn bộ tệp).

- **S3 Transfer Acceleration**:
  - Sử dụng các **CloudFront Edge Locations** (Điểm biên) phân tán toàn cầu. ⚡
  - Dữ liệu đi từ client đến điểm biên gần nhất qua mạng internet, sau đó đi qua mạng xương sống tốc độ cao của AWS để đến **S3 Bucket**. Giúp tăng tốc từ 50% đến 500% cho việc truyền tải xuyên quốc gia.

- **AWS Transfer Family**:
  - Giúp chuyển dữ liệu vào/ra S3 hoặc **Amazon EFS** mà không cần thay đổi ứng dụng hiện tại hoặc quản lý hạ tầng máy chủ tệp. 🛡️
  - Hỗ trợ các giao thức bảo mật như **SFTP** (truyền tệp qua SSH).

### 📌 Ghi nhớ nhanh

| Tính năng                 | Khi nào sử dụng?                                                      |
| :------------------------ | :-------------------------------------------------------------------- |
| **Multipart Upload**      | Khi tệp lớn (>100MB) để đảm bảo độ tin cậy và tốc độ.                 |
| **Transfer Acceleration** | Khi người dùng ở xa vùng lưu trữ (Region) của Bucket.                 |
| **AWS Transfer Family**   | Khi cần tương thích với các hệ thống cũ dùng SFTP/FTP.                |
| **Encryption**            | Tự động mã hóa phía máy chủ (**Server-side encryption**) khi tải lên. |

### 💡 Ví dụ (nếu có)

```example
Kịch bản: Bạn cần tải một tệp video 500GB từ văn phòng ở Việt Nam lên một S3 Bucket đặt tại vùng Northern Virginia (Mỹ).

Giải pháp tối ưu:
1. Sử dụng S3 Transfer Acceleration để dữ liệu sớm đi vào mạng nội bộ AWS thông qua điểm biên gần nhất.
2. Sử dụng Multipart Upload để chia tệp video thành hàng nghìn phần nhỏ, giúp tải lên song song và tránh việc phải bắt đầu lại từ đầu nếu mạng bị ngắt giữa chừng.
```

---

## 📘 Phần 4: Storing content with Amazon S3

### 🔑 Ý chính

- **S3 Storage Classes** (Các lớp lưu trữ S3): Các tùy chọn lưu trữ khác nhau giúp tối ưu hóa chi phí dựa trên tần suất truy cập và mức độ quan trọng của dữ liệu.
- **S3 Lifecycle Management** (Quản lý vòng đời S3): Tự động hóa việc chuyển đổi lớp lưu trữ hoặc xóa dữ liệu để giảm chi phí.
- **S3 Versioning** (Phân bản S3): Lưu giữ nhiều phiên bản của một đối tượng để bảo vệ dữ liệu khỏi việc ghi đè hoặc xóa nhầm.
- **CORS - Cross-Origin Resource Sharing** (Chia sẻ tài nguyên giữa các nguồn khác nhau): Cơ chế cho phép các ứng dụng web ở một miền này tương tác với tài nguyên ở miền khác.
- **Data Consistency Model** (Mô hình nhất quán dữ liệu): Amazon S3 cung cấp tính nhất quán đọc-sau-ghi (**Read-after-write consistency**) cho tất cả các thao tác.

### 🧠 Giải thích

**So sánh các Storage Classes phổ biến:**

| Storage Class              | Availability Zones | Phù hợp cho                              | Lưu ý chi phí                                            |
| :------------------------- | :----------------- | :--------------------------------------- | :------------------------------------------------------- |
| **S3 Standard**            | ≥ 3                | Dữ liệu truy cập thường xuyên.           | Chi phí lưu trữ cao nhất, không phí truy xuất.           |
| **S3 Intelligent-Tiering** | ≥ 3                | Dữ liệu không rõ mẫu truy cập.           | Tự động tối ưu phí lưu trữ, có phí quản lý hàng tháng.   |
| **S3 Standard-IA**         | ≥ 3                | Dữ liệu ít dùng nhưng cần truy cập ngay. | Phí lưu trữ thấp, có phí truy xuất trên mỗi GB.          |
| **S3 One Zone-IA**         | 1                  | Dữ liệu không quan trọng, dễ tạo lại.    | Rẻ hơn IA 20%, rủi ro nếu AZ bị sự cố.                   |
| **S3 Glacier**             | ≥ 3                | Lưu trữ lưu trữ lâu dài (Archive).       | Rẻ nhất, thời gian lấy dữ liệu từ vài phút đến hàng giờ. |

**Cơ chế hoạt động:**

- **Lifecycle Policies**: Gồm hai loại hành động chính là **Transition actions** (Chuyển dữ liệu sang lớp rẻ hơn sau X ngày) và **Expiration actions** (Xóa vĩnh viễn dữ liệu sau Y ngày). 🔄
- **Versioning**: Khi được bật, nếu bạn xóa một tệp, S3 sẽ chèn một **Delete marker** (Dấu xóa) thay vì xóa thật. Bạn có thể khôi phục lại bất cứ lúc nào bằng cách xóa dấu này hoặc truy cập vào **Version ID** cũ. 🛡️
- **Read-after-write Consistency**: Ngay khi bạn tải lên (PUT) hoặc cập nhật một đối tượng thành công, các yêu cầu đọc (GET) ngay lập tức sau đó sẽ luôn nhận được phiên bản mới nhất. ⏱️

### 📌 Ghi nhớ nhanh

- 💰 **Tối ưu chi phí**: Luôn sử dụng **Lifecycle policies** để chuyển dữ liệu cũ sang các lớp **Standard-IA** hoặc **Glacier**.
- 🛡️ **An toàn dữ liệu**: Bật **Versioning** cho các Bucket chứa tài liệu quan trọng để chống lại lỗi do con người.
- 🌐 **CORS**: Cần cấu hình khi bạn muốn một website lưu trên S3 này truy cập vào script hoặc hình ảnh ở một S3 khác (khác domain).

### 💡 Ví dụ (nếu có)

```example
Thiết lập chính sách vòng đời (Lifecycle Policy) cho tệp Log của hệ thống:
1. Lưu tại S3 Standard trong 30 ngày đầu (truy cập thường xuyên để kiểm tra lỗi).
2. Sau 30 ngày, tự động chuyển sang S3 Standard-IA (ít khi xem nhưng cần giữ lại).
3. Sau 365 ngày, tự động chuyển vào S3 Glacier Deep Archive để lưu trữ pháp lý trong 7 năm.
4. Sau 7 năm, tự động xóa vĩnh viễn (Expiration) để không phát sinh chi phí.
```

---

## 📘 Phần 5: Designing with Amazon S3

### 🔑 Ý chính

- **Security by Default** (Bảo mật mặc định): Các **Buckets** và **Objects** mới tạo luôn ở chế độ riêng tư (**Private**) và được mã hóa mặc định (**Encryption configured by default**).
- **Access Management** (Quản lý truy cập): Sử dụng nhiều công cụ để kiểm soát ai có thể xem hoặc chỉnh sửa dữ liệu dựa trên nguyên tắc **Least Privilege** (Quyền hạn tối thiểu).
- **Region Selection** (Lựa chọn vùng): Quyết định nơi lưu trữ dữ liệu dựa trên luật pháp, độ trễ và chi phí.
- **Cost Optimization** (Tối ưu hóa chi phí): Chỉ trả tiền cho những gì bạn sử dụng (dung lượng lưu trữ, số lượng yêu cầu và dữ liệu truyền ra ngoài).
- **Amazon S3 Inventory** (Kiểm kê S3): Công cụ giúp kiểm tra và báo cáo trạng thái mã hóa, sao chép của các đối tượng để phục vụ mục đích tuân thủ (**Compliance**).

### 🧠 Giải thích

**Các công cụ bảo vệ dữ liệu:**

- **Server-side Encryption** (Mã hóa phía máy chủ): S3 tự động mã hóa dữ liệu trước khi lưu xuống đĩa và giải mã khi bạn tải về. **SSE-S3** là cấu hình mặc định. 🔐
- **IAM Policies**: Gán quyền cho người dùng hoặc nhóm người dùng cụ thể trong AWS. 👤
- **Bucket Policies**: Quy tắc áp dụng trực tiếp lên thùng chứa để cho phép hoặc chặn truy cập từ các tài khoản hoặc IP khác nhau. 🪣
- **Block Public Access**: Một lớp bảo vệ bổ sung để đảm bảo các tài khoản không vô tình công khai dữ liệu ra internet. 🚫
- **Preassigned URLs** (URL được ký trước): Cấp quyền truy cập tạm thời cho người khác trong một khoảng thời gian giới hạn thông qua một liên kết duy nhất. ⏳

**Cân nhắc khi chọn Region (Vùng):**

- **Compliance** (Tuân thủ): Một số quốc gia yêu cầu dữ liệu nhạy cảm không được rời khỏi biên giới lãnh thổ. ⚖️
- **Latency** (Độ trễ): Chọn vùng gần người dùng nhất để tăng tốc độ tải trang và trải nghiệm. 🏎️
- **Cost** (Chi phí): Giá lưu trữ và truyền dữ liệu có sự khác biệt giữa các vùng địa lý của AWS. 💰

### 📌 Ghi nhớ nhanh

| Loại chi phí                                      | Trạng thái                                       |
| :------------------------------------------------ | :----------------------------------------------- |
| **Data Transfer In** (Dữ liệu đi vào từ internet) | Miễn phí 🆓                                      |
| **Data Transfer Out** (Dữ liệu đi ra internet)    | Tính phí (sau 100GB đầu tiên/tháng) 💸           |
| **Storage** (Dung lượng lưu trữ)                  | Tính phí theo GB/tháng tùy theo Storage Class 📦 |
| **Requests** (Các yêu cầu PUT, POST, LIST,...)    | Tính phí theo số lượng yêu cầu 🔢                |
| **Transfer Out to CloudFront**                    | Miễn phí ⚡                                      |

### 💡 Ví dụ (nếu có)

```example
Bài toán thiết kế cho một ứng dụng ngân hàng:
1. Bảo mật: Bật Block Public Access cho toàn bộ Bucket. Sử dụng Bucket Policy để chỉ cho phép các máy chủ ứng dụng cụ thể được quyền đọc tệp.
2. Tuân thủ: Chọn Region "Asia Pacific (Singapore)" vì quy định của công ty yêu cầu dữ liệu phải nằm trong khu vực Đông Nam Á.
3. Tối ưu: Sử dụng S3 Inventory để hàng tuần xuất báo cáo danh sách các tệp chưa được mã hóa bằng khóa riêng của công ty nhằm đảm bảo an toàn tuyệt đối.
```

---

## 📝 Knowledge Check

### ❓ Question 1

Due to a company merger, a data engineer needs to increase their object storage capacity. They are not sure how much storage they will need. They want a highly scalable service that can store unstructured, semistructured, and structured data. Which service would be the most cost effective to accomplish this task?

A. AWS Storage Gateway

B. Amazon Elastic Block Store (Amazon EBS)

C. Amazon RDS

D. Amazon S3

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Amazon S3** (**dịch vụ object storage**) là giải pháp lưu trữ đối tượng có khả năng mở rộng không giới hạn (**Massive/unlimited**).
- Dịch vụ này hỗ trợ lưu trữ mọi loại dữ liệu từ phi cấu trúc (**Unstructured**) đến có cấu trúc mà không cần lo lắng về việc quản lý dung lượng ổ đĩa.

### ❌ Vì sao đáp án khác sai?

- **A**: **AWS Storage Gateway** là dịch vụ kết nối hạ tầng tại chỗ (On-premises) với đám mây, không phải là nơi lưu trữ chính cho dữ liệu phi cấu trúc dung lượng lớn.
- **B**: **Amazon EBS** là lưu trữ dạng khối (**Block storage**) có kích thước cố định, không phù hợp để lưu trữ dữ liệu phi cấu trúc dung lượng không xác định một cách tối ưu chi phí.
- **C**: **Amazon RDS** là dịch vụ cơ sở dữ liệu quan hệ, đòi hỏi cấu trúc chặt chẽ và không hiệu quả về chi phí khi lưu trữ khối lượng lớn dữ liệu phi cấu trúc.

💡 **Mẹo nhớ nhanh:** Cứ thấy **"Scalable"**, **"Unstructured data"** và **"Cost-effective"** cho dữ liệu lớn -> Chọn **Amazon S3**.

---

### ❓ Question 2

Amazon S3 provides a good solution for which use case?

A. Ledger data that is updated and accessed frequently

B. Hourly storage of frequently accessed temporary files

C. An internet-accessible storage location for video files that an external website can access

D. A data warehouse for business intelligence

✅ **Đáp án đúng:** C

### 🧠 Giải thích

- **Amazon S3** cực kỳ mạnh mẽ trong việc lưu trữ và phân phối các tệp tin phương tiện (**Media hosting**) như video, hình ảnh.
- Nhờ vào **Universal Namespace** (không gian tên toàn cầu), mỗi đối tượng đều có một URL duy nhất có thể truy cập qua internet nếu được cấp quyền.

### ❌ Vì sao đáp án khác sai?

- **A**: Dữ liệu sổ cái cần cập nhật thường xuyên không phù hợp với S3 vì S3 là lưu trữ đối tượng (Object), không tối ưu cho việc ghi/đọc dữ liệu thay đổi liên tục mức độ cao.
- **B**: Tệp tạm thời truy cập hàng giờ nên dùng **Amazon EBS** hoặc **Amazon EFS** để có độ trễ thấp hơn.
- **D**: Kho dữ liệu (**Data warehouse**) là nhiệm vụ của **Amazon Redshift**, không phải S3.

💡 **Mẹo nhớ nhanh:** S3 giống như một ổ cứng khổng lồ trên web, cực hợp để làm nơi chứa ảnh/video cho website.

---

### ❓ Question 3

A company is interested in using Amazon S3 to host their website instead of a traditional web server. Which types of content does Amazon S3 support for static web hosting? (Select THREE.)

A. HTML files and image files

B. Database engine

C. Dynamic HTML files

D. Client-side scripts

E. Video and sound files

F. Server-side scripts

✅ **Đáp án đúng:** A, D, E

### 🧠 Giải thích

- **Static Web Hosting** (**lưu trữ trang web tĩnh**) trên S3 chỉ hỗ trợ các tài nguyên không cần xử lý phía máy chủ.
- **HTML/Images**, **Client-side scripts** (như JavaScript) và **Video/Sound** là các tệp tĩnh mà trình duyệt có thể tự xử lý.

### ❌ Vì sao đáp án khác sai?

- **B, C, F**: Các thành phần như **Database engine**, **Dynamic HTML** (cần server render) và **Server-side scripts** (PHP, Python, ASP.NET) yêu cầu máy chủ có khả năng tính toán (như **Amazon EC2**), S3 không hỗ trợ thực thi các mã lệnh này.

---

### ❓ Question 4

A company wants to use an S3 bucket to store sensitive data. Which actions can they take to protect their data? (Select TWO.)

A. Enabling server-side encryption on the S3 bucket after uploading sensitive data

B. Enabling server-side encryption on the S3 bucket before uploading sensitive data

C. Using Secure File Transfer Protocol (SFTP) to connect directly to Amazon S3

D. Uploading unencrypted files to Amazon S3 because Amazon S3 encrypts the files by default

E. Using client-side encryption to protect data in transit before it is sent to Amazon S3

✅ **Đáp án đúng:** B, E

### 🧠 Giải thích

- **B**: Cấu hình **Server-side encryption** (mã hóa phía máy chủ) trên bucket trước khi tải lên đảm bảo mọi dữ liệu mới sẽ tự động được mã hóa khi lưu vào đĩa.
- **E**: **Client-side encryption** (mã hóa phía khách hàng) giúp bảo vệ dữ liệu ngay từ khi nó đang di chuyển (**In transit**) trước khi đến AWS.

### ❌ Vì sao đáp án khác sai?

- **A**: Mã hóa sau khi tải lên có nghĩa là dữ liệu đã nằm ở trạng thái không an toàn trên đĩa một khoảng thời gian.
- **C**: **SFTP** là giao thức truyền tệp, không phải là phương thức bảo vệ dữ liệu cốt lõi bên trong S3 (mặc dù có thể dùng **AWS Transfer Family** nhưng không phải là "hành động bảo vệ dữ liệu" chính yếu như mã hóa).
- **D**: Dù S3 hiện nay mã hóa mặc định, nhưng đối với dữ liệu nhạy cảm (**Sensitive data**), việc chủ động cấu hình hoặc dùng mã hóa phía khách hàng là phương pháp bảo mật tốt hơn theo nguyên tắc **Shared Responsibility Model**.

---

### ❓ Question 5

A company must create a common place to store shared files. Which requirements does Amazon S3 support? (Select TWO.)

A. Lock a file so that only one person at a time can edit it.

B. Compare file contents between files.

C. Recover deleted files.

D. Attach comments to files.

E. Maintain different versions of files.

✅ **Đáp án đúng:** C, E

### 🧠 Giải thích

- **C & E**: Thông qua tính năng **Versioning** (**phân bản**), S3 cho phép lưu giữ nhiều phiên bản của cùng một đối tượng. Điều này giúp khôi phục các tệp đã bị xóa nhầm hoặc ghi đè.

### ❌ Vì sao đáp án khác sai?

- **A**: S3 không hỗ trợ cơ chế khóa tệp (**File locking**) để chỉnh sửa đồng thời như một hệ thống tệp dùng chung truyền thống.
- **B, D**: S3 không có tính năng tích hợp sẵn để so sánh nội dung tệp hay đính kèm bình luận (ngoại trừ việc dùng siêu dữ liệu tùy chỉnh - Metadata, nhưng không phải tính năng quản lý tệp chuyên dụng).

---

### ❓ Question 6

A customer service team accesses case data daily for up to 30 days. Cases can be reopened and require immediate access for 1 year after they are closed. Reopened cases require 2 days to process. Which solution meets the requirements and is the most cost efficient?

A. Store all case data in S3 Standard so that it is available whenever it is needed.

B. Store case data in S3 Standard. Use a lifecycle policy to move the data into S3 Standard-Infrequent Access (S3 Standard-IA) after 30 days.

C. Store case data in S3 Standard. Use a lifecycle policy to move the data into Amazon S3 Glacier Flexible Retrieval after 30 days.

D. Store case data in S3 Intelligent-Tiering to automatically move data between tiers based on access frequency.

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **S3 Standard**: Dùng cho 30 ngày đầu khi truy cập hàng ngày (**Frequent access**).
- **S3 Standard-IA**: Sau 30 ngày, dữ liệu ít được dùng hơn nhưng khi cần phải có ngay lập tức (**Immediate access**). Đây là lựa chọn rẻ hơn Standard nhưng vẫn đảm bảo tốc độ truy cập nhanh.

### ❌ Vì sao đáp án khác sai?

- **A**: Quá đắt đỏ nếu lưu toàn bộ dữ liệu ít dùng trong lớp Standard suốt 1 năm.
- **C**: **Glacier** có chi phí rất rẻ nhưng thời gian lấy dữ liệu mất từ vài phút đến hàng giờ, không đáp ứng được yêu cầu "immediate access".
- **D**: **Intelligent-Tiering** tự động hóa tốt nhưng đối với yêu cầu có mốc thời gian cụ thể (sau 30 ngày), dùng **Lifecycle policy** chuyển sang **Standard-IA** sẽ giúp dự báo chi phí tốt hơn và thường rẻ hơn cho trường hợp này.

---

### ❓ Question 7

Which option takes advantage of edge locations in Amazon CloudFront to transfer files over long distances to an S3 bucket?

A. AWS Transfer Family

B. Amazon S3 Transfer Acceleration

C. Amazon S3 REST API

D. AWS SDKs

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Amazon S3 Transfer Acceleration** sử dụng các **Edge locations** (**điểm biên**) của mạng lưới CloudFront để tối ưu hóa đường truyền internet. Dữ liệu đi vào mạng nội bộ của AWS sớm nhất có thể để giảm độ trễ khi truyền đi xa.

---

### ❓ Question 8

A video producer must regularly transfer several video files to Amazon S3. The files range from 100–700 MB. The internet connection has been unreliable, causing some uploads to fail. Which solution provides the fastest, most reliable, and most cost-effective way to transfer these files to Amazon S3?

A. Amazon S3 Transfer Acceleration

B. Amazon S3 multipart uploads

C. AWS Transfer Family

D. AWS Management Console

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Multipart uploads** (**tải lên nhiều phần**) chia tệp lớn thành các phần nhỏ để tải lên song song.
- Nếu mạng bị ngắt (unreliable), bạn chỉ cần tải lại phần bị lỗi thay vì tải lại toàn bộ tệp từ đầu, giúp tăng độ tin cậy và tốc độ. Khuyên dùng cho tệp trên **100 MB**.

---

### ❓ Question 9

Which Amazon S3 storage class is designed for backup copies of on-premises data or easily re-creatable data?

A. S3 Standard-Infrequent Access (S3 Standard-IA)

B. S3 One Zone-Infrequent Access (S3 One Zone-IA)

C. S3 Intelligent-Tiering

D. S3 Glacier Instant Retrieval

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **S3 One Zone-IA** lưu trữ dữ liệu chỉ trong **1 Availability Zone** (**vùng khả dụng**) thay vì ít nhất 3 vùng.
- Vì độ bền thấp hơn (nếu vùng đó gặp sự cố vật lý), nó có giá rẻ hơn 20% và phù hợp nhất cho dữ liệu có bản sao lưu khác hoặc dữ liệu có thể tạo lại dễ dàng.

💡 **Mẹo nhớ nhanh:** Thấy **"Easily re-creatable"** (dễ tạo lại) -> Chọn **One Zone-IA**.

---

### ❓ Question 10

A company needs to retain records for regulatory purposes for a 7-year period. These records are rarely accessed (once or twice a year). What is the lowest-cost storage class for Amazon S3?

A. S3 One Zone-Infrequent Access (S3 One Zone-IA)

B. S3 Standard-Infrequent Access (S3 Standard-IA)

C. S3 Intelligent-Tiering

D. S3 Glacier Deep Archive

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **S3 Glacier Deep Archive** là lớp lưu trữ có chi phí thấp nhất của AWS.
- Được thiết kế cho dữ liệu lưu trữ dài hạn (từ 7-10 năm) và cực kỳ ít khi truy cập (hiếm khi cần lấy dữ liệu ngay lập tức).

💡 **Mẹo nhớ nhanh:** Thấy **"7-10 years"** + **"Lowest-cost"** -> Luôn là **Glacier Deep Archive**.

---

# Practice Test - Module 04: Adding a Storage Layer with Amazon S3

## Question 1

What is the maximum file size allowed for a single object in Amazon S3?

A. 1 TB

B. 5 GB

C. 5 TB

D. Unlimited

✅ Correct Answer: C

### 🧠 Explanation

Kích thước tối đa cho một **Object** (đối tượng) đơn lẻ được tải lên **Amazon S3** là **5 TB**. Đối với các tệp lớn, AWS khuyến khích sử dụng tính năng **Multipart upload**.

### ❌ Why Other Choices Are Wrong

- A: Sai thông số kỹ thuật.
- B: 5 GB là giới hạn cho một lần tải lên (PUT) đơn lẻ không dùng Multipart upload.
- D: S3 có thể lưu trữ tổng dung lượng không giới hạn, nhưng mỗi tệp riêng lẻ phải tuân thủ giới hạn 5 TB.

---

## Question 2

Which storage type organizes data in a hierarchical structure of folders and files?

A. Block storage

B. Object storage

C. Instance storage

D. File storage

✅ Correct Answer: D

### 🧠 Explanation

**File storage** (Lưu trữ tệp) sắp xếp dữ liệu theo cấu trúc phân cấp (thư mục và tệp tin). Đây là kiểu lưu trữ truyền thống giống như ổ cứng máy tính cá nhân hoặc NAS.

### ❌ Why Other Choices Are Wrong

- A: **Block storage** lưu dữ liệu dưới dạng các khối có kích thước cố định, không có cấu trúc phân cấp.
- B: **Object storage** (S3) lưu dữ liệu phẳng kèm theo metadata và key, không có thư mục thực sự (chỉ dùng prefix để mô phỏng).
- C: Đây là ổ lưu trữ tạm thời gắn liền với EC2.

---

## Question 3

A small café wants to host a landing page consisting only of HTML, CSS, and image files. Which AWS solution is the most cost-effective for this requirement?

A. Run a web server on an Amazon EC2 instance.

B. Use an Amazon S3 bucket configured for static website hosting.

C. Deploy the files to an Amazon EFS file system.

D. Use AWS Storage Gateway to sync files.

✅ Correct Answer: B

### 🧠 Explanation

**Amazon S3** là lựa chọn rẻ nhất và ít tốn công quản trị nhất để lưu trữ **Static Website** (trang web tĩnh). Bạn không cần quản lý máy chủ hay hệ điều hành.

### ❌ Why Other Choices Are Wrong

- A: Chạy **EC2** sẽ tốn kém hơn vì phải trả tiền cho instance chạy liên tục và tốn công quản lý.
- C: **Amazon EFS** là hệ thống tệp dùng chung cho máy chủ, không phải giải pháp hosting web trực tiếp.
- D: Dùng để kết nối hạ tầng tại chỗ với đám mây, không liên quan đến hosting website.

---

## Question 4

A cloud architect needs to protect a bucket's data from being permanently lost due to accidental user overwrites or deletions. Which feature should they enable?

A. S3 Lifecycle Management

B. S3 Transfer Acceleration

C. S3 Versioning

D. Server-side encryption

✅ Correct Answer: C

### 🧠 Explanation

**S3 Versioning** (**Phân bản**) cho phép giữ lại nhiều phiên bản của một đối tượng. Nếu người dùng xóa tệp, S3 chỉ chèn một **Delete marker**, bạn có thể khôi phục lại dễ dàng.

### ❌ Why Other Choices Are Wrong

- A: Dùng để tự động chuyển lớp lưu trữ hoặc xóa dữ liệu theo thời gian.
- B: Dùng để tăng tốc độ tải lên/tải về dữ liệu.
- D: Dùng để bảo mật dữ liệu tại chỗ, không giúp chống lại việc xóa nhầm.

---

## Question 5

Which statement is true regarding the durability of the Amazon S3 Standard storage class?

A. It provides 99.99% durability of objects.

B. It provides 99.9% durability of objects.

C. It provides 99.999999999% (11 nines) of durability.

D. It provides 100% durability across one Availability Zone.

✅ Correct Answer: C

### 🧠 Explanation

**S3 Standard** được thiết kế để cung cấp độ bền (**Durability**) lên đến **11 con số 9**, đảm bảo dữ liệu không bị mất mát ngay cả khi xảy ra sự cố tại nhiều trung tâm dữ liệu.

### ❌ Why Other Choices Are Wrong

- A & B: Đây là các con số liên quan đến tính sẵn sàng (**Availability**), không phải độ bền.
- D: S3 Standard lưu trữ dữ liệu trên tối thiểu 3 **Availability Zones**, không phải 1.

---

## Question 6

A company has a large number of documents that are accessed frequently for the first 30 days but are rarely accessed thereafter. They must be available immediately if requested. Which lifecycle transition is the most cost-effective?

A. S3 Standard to S3 Glacier Flexible Retrieval after 30 days

B. S3 Standard to S3 Standard-Infrequent Access (S3 Standard-IA) after 30 days

C. S3 Standard to S3 One Zone-IA after 30 days

D. S3 Standard to S3 Glacier Deep Archive after 30 days

✅ Correct Answer: B

### 🧠 Explanation

**S3 Standard-IA** phù hợp cho dữ liệu ít truy cập nhưng yêu cầu **Immediate access** (truy cập ngay lập tức). Đây là bước chuyển tiếp tối ưu chi phí sau 30 ngày.

### ❌ Why Other Choices Are Wrong

- A & D: Các lớp **Glacier** yêu cầu thời gian chờ để lấy dữ liệu (vài phút đến hàng giờ), không đáp ứng yêu cầu "truy cập ngay".
- C: **One Zone-IA** rẻ hơn nhưng chỉ lưu ở 1 AZ, không an toàn bằng IA cho các tài liệu quan trọng của công ty.

---

## Question 7

A media producer is uploading a 10 GB video file to Amazon S3 over an unstable internet connection. Which method would improve the reliability and speed of this upload?

A. S3 Transfer Acceleration

B. S3 Multipart upload

C. AWS SDKs using a single PUT request

D. AWS Management Console drag-and-drop

✅ Correct Answer: B

### 🧠 Explanation

**Multipart upload** chia nhỏ tệp thành nhiều phần. Nếu mạng bị ngắt, bạn chỉ cần tải lại phần bị lỗi thay vì toàn bộ 10 GB, giúp tăng độ tin cậy và tốc độ.

### ❌ Why Other Choices Are Wrong

- A: Chỉ tăng tốc đường truyền qua mạng AWS, không giải quyết vấn đề mạng không ổn định gây lỗi tải lên.
- C: Gửi một yêu cầu **PUT** duy nhất cho 10 GB là rất rủi ro và dễ thất bại nếu mạng yếu.
- D: Console có giới hạn tải lên và không tối ưu bằng việc dùng Multipart upload thông qua CLI/SDK cho tệp lớn.

---

## Question 8

Users in Europe need to upload large datasets to an S3 bucket located in the US-West-2 (Oregon) Region. Which feature can be enabled on the bucket to optimize the transfer speed across these long distances?

A. S3 Cross-Region Replication

B. Amazon CloudFront Content Delivery

C. S3 Transfer Acceleration

D. S3 Lifecycle Transition

✅ Correct Answer: C

### 🧠 Explanation

**S3 Transfer Acceleration** sử dụng các **Edge locations** của CloudFront để dữ liệu đi vào mạng xương sống của AWS sớm nhất, giúp tăng tốc độ truyền tải xuyên lục địa.

### ❌ Why Other Choices Are Wrong

- A: Dùng để sao chép dữ liệu giữa các vùng, không giúp tăng tốc quá trình tải lên ban đầu.
- B: **CloudFront** chủ yếu dùng để tối ưu việc tải về (Download/Distribution), không phải tải lên (Upload) trực tiếp cho S3.
- D: Liên quan đến quản lý vòng đời dữ liệu.

---

## Question 9

Which AWS service is a managed, serverless file transfer workflow service that supports protocols such as SFTP, FTPS, and FTP for Amazon S3?

A. AWS Snowball

B. AWS Storage Gateway

C. AWS Transfer Family

D. Amazon S3 REST API

✅ Correct Answer: C

### 🧠 Explanation

**AWS Transfer Family** là dịch vụ quản lý hoàn toàn cho các giao thức truyền tệp truyền thống (**SFTP, FTP, FTPS**), giúp chuyển dữ liệu thẳng vào **S3** hoặc **EFS**.

### ❌ Why Other Choices Are Wrong

- A: Thiết bị phần cứng để chuyển lượng lớn dữ liệu (Petabyte).
- B: Kết nối hạ tầng tại chỗ với Cloud qua giao thức iSCSI hoặc NFS.
- D: Giao diện lập trình để tương tác với S3, không phải một dịch vụ quản lý giao thức FTP.

---

## Question 10

A financial institution is required by law to retain transaction records for 7 years. These records are almost never accessed but must be preserved. Which storage class offers the lowest cost for this long-term archival?

A. S3 Standard-IA

B. S3 Glacier Instant Retrieval

C. S3 Glacier Deep Archive

D. S3 One Zone-IA

✅ Correct Answer: C

### 🧠 Explanation

**S3 Glacier Deep Archive** có chi phí thấp nhất trong tất cả các lớp lưu trữ của S3, cực kỳ phù hợp cho lưu trữ hồ sơ pháp lý trong nhiều năm mà hiếm khi cần truy cập.

### ❌ Why Other Choices Are Wrong

- A, B, D: Đều có chi phí lưu trữ hàng tháng cao hơn nhiều so với **Deep Archive**.

---

## Question 11

A cloud architect wants to provide a contractor with temporary access to download a private file from an S3 bucket without changing the bucket's public access settings. What should they use?

A. A Bucket Policy

B. An IAM User with permanent keys

C. A Preassigned (Presigned) URL

D. S3 Versioning

✅ Correct Answer: C

### 🧠 Explanation

**Preassigned (Presigned) URL** cung cấp quyền truy cập tạm thời vào các đối tượng riêng tư mà không yêu cầu người nhận phải có tài khoản AWS hay thay đổi quyền của Bucket.

### ❌ Why Other Choices Are Wrong

- A: Bucket Policy thường dùng cho quyền hạn rộng hoặc lâu dài, không linh hoạt cho truy cập tạm thời của 1 tệp.
- B: Tạo user mới và cấp key vĩnh viễn là vi phạm nguyên tắc bảo mật.
- D: Không liên quan đến việc cấp quyền truy cập.

---

## Question 12

A compliance team needs a scheduled report that audits the encryption status of all objects in a specific S3 bucket. Which tool should they use?

A. AWS Trusted Advisor

B. Amazon S3 Inventory

C. S3 Lifecycle Rules

D. Amazon CloudFront

✅ Correct Answer: B

### 🧠 Explanation

**Amazon S3 Inventory** cung cấp báo cáo theo lịch trình về danh sách các đối tượng và các thuộc tính của chúng, bao gồm cả trạng thái mã hóa (**Encryption status**).

### ❌ Why Other Choices Are Wrong

- A: Cung cấp các đề xuất tối ưu hóa chung, không xuất báo cáo chi tiết từng đối tượng trong bucket.
- C: Dùng để di chuyển hoặc xóa dữ liệu.
- D: Mạng phân phối nội dung.

---

## Question 13

Which model describes how Amazon S3 handles data updates, ensuring that a GET request immediately following a successful PUT of a new object will return the new data?

A. Eventual consistency

B. Read-after-write consistency

C. Write-after-read consistency

D. Delayed consistency

✅ Correct Answer: B

### 🧠 Explanation

S3 cung cấp mô hình **Read-after-write consistency** cho tất cả các thao tác (PUT, DELETE, LIST). Nghĩa là ngay khi bạn tải lên thành công, yêu cầu đọc sau đó sẽ trả về dữ liệu mới ngay lập tức.

### ❌ Why Other Choices Are Wrong

- A: Trước đây S3 có mô hình **Eventual consistency** cho việc ghi đè hoặc xóa, nhưng hiện nay đã được nâng cấp lên mức nhất quán mạnh mẽ.
- C & D: Không phải thuật ngữ kỹ thuật của AWS S3.

---

## Question 14

An application hosted on `www.example.com` needs to access a JSON file stored in an S3 bucket with a different domain name. Which configuration on the S3 bucket is necessary to allow this?

A. S3 Versioning

B. Cross-Origin Resource Sharing (CORS)

C. S3 Transfer Acceleration

D. Server-side encryption (SSE-S3)

✅ Correct Answer: B

### 🧠 Explanation

**CORS** cho phép các ứng dụng web chạy ở một domain này được phép truy cập tài nguyên ở một domain khác (trong trường hợp này là S3 Bucket).

### ❌ Why Other Choices Are Wrong

- A: Quản lý phiên bản.
- C: Tăng tốc độ truyền tải.
- D: Mã hóa dữ liệu.

---

## Question 15

A developer wants to store backup copies of data that can be easily re-created if lost. To save costs, they are willing to store the data in only one Availability Zone. Which storage class should they choose?

A. S3 Standard

B. S3 Standard-IA

C. S3 One Zone-IA

D. S3 Intelligent-Tiering

✅ Correct Answer: C

### 🧠 Explanation

**S3 One Zone-IA** lưu trữ dữ liệu trong duy nhất **1 Availability Zone**. Nó rẻ hơn 20% so với Standard-IA, phù hợp cho dữ liệu có thể tạo lại được (**Re-creatable**).

### ❌ Why Other Choices Are Wrong

- A, B, D: Đều lưu trữ dữ liệu trên tối thiểu 3 AZ để đảm bảo tính sẵn sàng cao.

---

## Question 16

Which of the following is a default security configuration for a newly created Amazon S3 bucket?

A. Public read access is enabled for everyone.

B. The bucket and its objects are private and protected.

C. Cross-Region Replication is enabled by default.

D. Server-side encryption is disabled until manually configured.

✅ Correct Answer: B

### 🧠 Explanation

Mặc định, mọi **Bucket** và **Object** mới tạo đều là **Private** (riêng tư). Chỉ chủ sở hữu tài khoản mới có quyền truy cập trừ khi họ thiết lập thêm quyền khác.

### ❌ Why Other Choices Are Wrong

- A: Truy cập công khai bị chặn mặc định để bảo mật.
- C: Replication phải được cấu hình thủ công.
- D: Hiện nay S3 đã áp dụng mã hóa phía máy chủ (**SSE-S3**) mặc định cho mọi đối tượng mới.

---

## Question 17

A company needs to store application logs in S3. The logs are needed for active analysis for 60 days, after which they should be deleted automatically to save space. What should be configured?

A. S3 Versioning with a delete marker

B. An S3 Lifecycle policy with an expiration action

C. S3 Intelligent-Tiering

D. S3 Object Lock

✅ Correct Answer: B

### 🧠 Explanation

**S3 Lifecycle policy** với hành động **Expiration** (hết hạn) cho phép bạn định nghĩa số ngày sau khi tạo để đối tượng tự động bị xóa vĩnh viễn.

### ❌ Why Other Choices Are Wrong

- A: Versioning giữ lại nhiều tệp, làm tăng dung lượng lưu trữ thay vì xóa bỏ.
- C: Chỉ di chuyển dữ liệu giữa các tầng, không tự động xóa sau 60 ngày.
- D: Ngăn chặn việc xóa dữ liệu (dùng cho mục đích tuân thủ), ngược lại với yêu cầu đề bài.

---

## Question 18

A business requires their data to be stored in a primary AWS Region and simultaneously backed up to a secondary AWS Region for disaster recovery. Which S3 feature automates this process?

A. S3 Transfer Acceleration

B. S3 Replication (Cross-Region Replication)

C. S3 Lifecycle Transition

D. S3 Multipart Upload

✅ Correct Answer: B

### 🧠 Explanation

**Cross-Region Replication (CRR)** tự động sao chép các đối tượng mới được tải lên từ bucket nguồn ở Region này sang bucket đích ở Region khác.

### ❌ Why Other Choices Are Wrong

- A: Tối ưu hiệu suất đường truyền.
- C: Di chuyển dữ liệu giữa các Storage Classes.
- D: Chia nhỏ tệp để tải lên.

---

## Question 19

What is the availability of the Amazon S3 Standard storage class?

A. 99.9%

B. 99.99%

C. 99.999999999%

D. 99.0%

✅ Correct Answer: B

### 🧠 Explanation

**S3 Standard** cung cấp độ sẵn sàng (**Availability**) là **99.99%** (4 con số 9).

### ❌ Why Other Choices Are Wrong

- A: Là độ sẵn sàng của lớp Standard-IA.
- C: Đây là độ bền (**Durability**), không phải độ sẵn sàng.
- D: Không phải thông số chuẩn của S3.

---

## Question 20

A company has data with changing or unknown access patterns. They want AWS to automatically move the data between frequent and infrequent access tiers to optimize costs without any performance impact. Which storage class should they use?

A. S3 Standard-IA

B. S3 Intelligent-Tiering

C. S3 Glacier Flexible Retrieval

D. S3 One Zone-IA

✅ Correct Answer: B

### 🧠 Explanation

**S3 Intelligent-Tiering** là lớp lưu trữ duy nhất tự động tối ưu hóa chi phí bằng cách di chuyển dữ liệu giữa các tầng truy cập khi mẫu truy cập thay đổi, mà không tốn phí truy xuất hay ảnh hưởng hiệu suất.

### ❌ Why Other Choices Are Wrong

- A & D: Yêu cầu bạn phải biết trước là dữ liệu ít truy cập để cấu hình thủ công.
- C: Là lớp lưu trữ lưu trữ (Archive), không phải tầng truy cập tự động cho dữ liệu đang hoạt động.

---

## 📊 Final Score

- **Score:** 20/20 (100%)
- **Status:** Excellent!

## 🎯 Weak Topics

- **None.** Bạn đã thể hiện sự am hiểu tuyệt vời về Amazon S3. Hãy tiếp tục duy trì phong độ này cho các module tiếp theo!
