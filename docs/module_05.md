# Module 05: Adding a Compute Layer Using Amazon EC2

## 🧭 Overview

- **Mục tiêu**: Cung cấp kiến thức về cách xây dựng lớp tính toán (Compute Layer) trong kiến trúc đám mây sử dụng dịch vụ Amazon EC2.
- **Nội dung sẽ học**:
  - Cách sử dụng **Amazon EC2** trong kiến trúc hệ thống.
  - Giá trị của **Amazon Machine Images (AMIs)**.
  - Lựa chọn **Instance types** (loại thực thể) và **Storage** (lưu trữ) phù hợp.
  - Cấu hình **User data** và hiểu về các lựa chọn **Pricing** (giá cả).
  - Áp dụng các nguyên tắc của **AWS Well-Architected Framework**.

---

## 📘 Phần 1: Adding compute with Amazon EC2

### 🔑 Ý chính

- **Amazon EC2 (Amazon Elastic Compute Cloud)** là dịch vụ cung cấp các **Virtual Machines - VMs** (máy ảo) trên nền tảng đám mây.
- Cho phép người dùng toàn quyền kiểm soát tài nguyên tính toán.
- Khả năng **Scale** (mở rộng) linh hoạt và mô hình thanh toán **Pay-as-you-go** (dùng bao nhiêu trả bấy nhiêu).

### 🧠 Giải thích

- **Virtualization (Ảo hóa)**: Một **EC2 instance** bản chất là một máy ảo chạy trên một **Physical host server** (máy chủ vật lý). Quá trình này được quản lý bởi một lớp phần mềm gọi là **Hypervisor** (trình quản lý ảo hóa) để phân chia tài nguyên CPU, RAM, Network từ phần cứng đến các instance.
- **Compute Choice Differentiators (Sự khác biệt giữa các lựa chọn tính toán)**:
  - **VMs (EC2)**: Mang lại **Higher infrastructure control** (khả năng kiểm soát hạ tầng cao hơn) và tùy biến sâu.
  - **Serverless (Lambda)** / **PaaS (Elastic Beanstalk)**: Tập trung vào **Faster application deployment** (tốc độ triển khai ứng dụng nhanh hơn) nhưng ít quyền kiểm soát hạ tầng hơn.
- **Khả năng tự động hóa**: EC2 có thể **Automatically scale** (tự động tăng/giảm quy mô) tùy theo nhu cầu thực tế của ứng dụng, giúp tối ưu hiệu suất và chi phí.

### 📌 Ghi nhớ nhanh

- ⚡ **Provision in minutes**: Khởi tạo máy chủ chỉ trong vài phút thay vì vài tuần như phần cứng vật lý.
- 🛠️ **Complete control**: Bạn có quyền chọn hệ điều hành (OS), loại bộ vi xử lý và cấu hình bảo mật.
- 💰 **Cost optimization**: Chỉ trả phí cho phần dung lượng bạn thực tế sử dụng.

### 💡 Ví dụ (nếu có)

```example
Các trường hợp sử dụng phổ biến của Amazon EC2:
1. Web server (Máy chủ web cho các trang tin tức, bán hàng).
2. Application server (Máy chủ chạy logic ứng dụng doanh nghiệp).
3. Database server (Máy chủ chạy cơ sở dữ liệu như MySQL, SQL Server).
4. Generative AI applications (Chạy các ứng dụng trí tuệ nhân tạo tạo sinh).
5. Game servers (Máy chủ cho các trò chơi trực tuyến).
```

---

## 📘 Phần 2: Choosing an AMI to launch an EC2 instance

### 🔑 Ý chính

- **AMI (Amazon Machine Image)** là một template (khuôn mẫu) chứa các thông tin cần thiết để khởi chạy một **EC2 instance**.
- Một **AMI** bao gồm:
  - **Root volume template** (Mẫu ổ đĩa gốc): Chứa hệ điều hành (OS) và các ứng dụng cài sẵn.
  - **Launch permissions** (Quyền khởi chạy): Kiểm soát tài khoản AWS nào có quyền sử dụng AMI.
  - **Block device mapping** (Ánh xạ thiết bị khối): Chỉ định các ổ cứng lưu trữ sẽ gắn vào khi instance khởi chạy.

### 🧠 Giải thích

- **Giá trị của AMI**:
  - **Repeatability** (Khả năng lặp lại): Giúp khởi chạy nhiều máy ảo giống hệt nhau một cách nhanh chóng.
  - **Reusability** (Khả năng tái sử dụng): Cấu hình một lần, sử dụng mãi mãi.
  - **Recoverability** (Khả năng khôi phục): Dùng làm bản sao lưu để khôi phục nhanh khi máy chủ gốc gặp sự cố.
- **Nguồn cung cấp AMI**:
  - **Quick Start**: Các bản Linux/Windows phổ biến do AWS cung cấp.
  - **My AMIs**: Các bản do chính bạn tạo ra từ các máy ảo đã cấu hình.
  - **AWS Marketplace**: Các template từ bên thứ ba (đã cài sẵn phần mềm chuyên dụng).
  - **Community AMIs**: Các bản do cộng đồng chia sẻ (cần thận trọng khi dùng).
- **EC2 Image Builder**: Dịch vụ giúp tự động hóa việc tạo, quản lý và cập nhật các bản **Golden AMI** (bản mẫu chuẩn) một cách an toàn và tuân thủ các chính sách bảo mật.

### ⚖️ So sánh: EBS-Backed vs. Instance Store-Backed

| Đặc tính                                | Amazon EBS-Backed                          | Instance Store-Backed                        |
| :-------------------------------------- | :----------------------------------------- | :------------------------------------------- |
| **Boot time** (Tốc độ khởi động)        | Nhanh hơn                                  | Chậm hơn                                     |
| **Dung lượng tối đa**                   | 16 TiB                                     | 10 GiB                                       |
| **Ability to Stop** (Khả năng tạm dừng) | **Có thể Stop**                            | **Không thể Stop** (Chỉ chạy hoặc Terminate) |
| **Data Persistence** (Độ bền dữ liệu)   | Dữ liệu bền vững ngay cả khi instance dừng | Dữ liệu bị mất khi instance bị dừng/lỗi      |
| **Chi phí**                             | Tính phí instance + phí lưu trữ EBS        | Chỉ tính phí instance + lưu trữ AMI trên S3  |

### 📌 Ghi nhớ nhanh

- 🖼️ **AMI** = Snapshot của hệ điều hành + Cấu hình phần mềm.
- ⚡ **HVM (Hardware Virtual Machine)**: Nên chọn loại ảo hóa này để có hiệu suất tốt nhất.
- 🔄 **Instance Lifecycle** (Vòng đời):
  - **Pending** (Chờ) ➔ **Running** (Đang chạy).
  - **Stopping/Stopped** (Chỉ có ở EBS-backed).
  - **Shutting-down** ➔ **Terminated** (Bị xóa vĩnh viễn).
- 🛠️ **Golden AMI**: Là bản AMI đã được cấu hình hoàn chỉnh ("fully baked") giúp instance sẵn sàng phục vụ ngay sau khi khởi chạy.

### 💡 Ví dụ (nếu có)

```example
Quy trình tạo Golden AMI:
1. Khởi chạy 1 instance từ Quick Start (Linux).
2. Cài đặt Web server (Apache), cấu hình bảo mật, cập nhật phần mềm.
3. Tạo 1 AMI từ instance đã cấu hình này.
4. Từ nay về sau, khi cần thêm 10 Web server giống hệt, bạn chỉ cần chọn AMI này để khởi chạy thay vì phải cài lại từng máy.
```

---

## 📘 Phần 3: Selecting an Amazon EC2 instance type

### 🔑 Ý chính

- **EC2 instance type** (Loại thực thể EC2) xác định cấu hình phần cứng bao gồm **vCPU** (Bộ vi xử lý ảo), **Memory** (Bộ nhớ RAM), **Storage** (Lưu trữ) và **Network performance** (Hiệu suất mạng).
- Có hơn 270 loại instance khác nhau được chia thành các **Families** (Họ) để tối ưu hóa cho từng loại workload (khối lượng công việc) cụ thể.
- Quy tắc đặt tên giúp nhận diện nhanh đặc điểm phần cứng (Ví dụ: `c7gn.xlarge`).

### 🧠 Giải thích

- **Cấu trúc tên gọi của Instance**:
  - **Family** (Họ): Chữ cái đầu tiên (ví dụ: `c` cho Compute), xác định mục đích sử dụng chính.
  - **Generation** (Thế hệ): Con số tiếp theo (ví dụ: `7`), số càng cao thì công nghệ càng mới và hiệu suất càng tốt.
  - **Processor family** (Họ vi xử lý): (Ví dụ: `g` cho chip AWS Graviton).
  - **Additional capabilities** (Khả năng bổ sung): (Ví dụ: `n` cho Network optimized - tối ưu mạng).
  - **Size** (Kích cỡ): Phần sau dấu chấm (ví dụ: `xlarge`), xác định lượng tài nguyên (CPU, RAM) được cấp phát.
- **Các nhóm Instance phổ biến**:
  - **General purpose** (Đa năng): Cân bằng giữa tài nguyên tính toán, bộ nhớ và mạng.
  - **Compute optimized** (Tối ưu tính toán): Dành cho các tác vụ cần nhiều CPU.
  - **Memory optimized** (Tối ưu bộ nhớ): Dành cho các tác vụ xử lý tập dữ liệu lớn trong RAM.
  - **Storage optimized** (Tối ưu lưu trữ): Dành cho các tác vụ yêu cầu đọc/ghi dữ liệu (I/O) tốc độ cao trên ổ đĩa cục bộ.
  - **Accelerated computing** (Tính toán tăng tốc): Sử dụng phần cứng tăng tốc như GPU hoặc FPGA.
- **Công cụ hỗ trợ lựa chọn**:
  - **AWS Compute Optimizer**: Sử dụng trí tuệ nhân tạo để phân tích lịch sử sử dụng và đưa ra khuyến nghị thay đổi loại instance để tiết kiệm chi phí hoặc tăng hiệu suất.

### ⚖️ So sánh các Instance Families

| Nhóm (Family)   | Mục đích sử dụng          | Ví dụ Workload                                          |
| :-------------- | :------------------------ | :------------------------------------------------------ |
| **T / M**       | **General purpose**       | Web server, môi trường phát triển (Dev/Test).           |
| **C**           | **Compute optimized**     | Xử lý hàng loạt (Batch processing), phân tích phân tán. |
| **R / X / Z**   | **Memory optimized**      | Cơ sở dữ liệu hiệu năng cao, In-memory caches.          |
| **I / D / H**   | **Storage optimized**     | Cơ sở dữ liệu NoSQL, kho dữ liệu (Data warehouses).     |
| **P / G / Inf** | **Accelerated computing** | Machine Learning, xử lý đồ họa, AI.                     |

### 📌 Ghi nhớ nhanh

- 🚀 **Latest generation** (Thế hệ mới nhất): Luôn ưu tiên chọn các thế hệ mới vì chúng thường có tỷ lệ **Price-to-performance** (Giá trên hiệu suất) tốt nhất.
- 📉 **AWS Compute Optimizer**: Là "trợ lý" đắc lực giúp tìm ra các instance bị **Over-provisioned** (dư thừa tài nguyên) để hạ cấp và tiết kiệm tiền.
- 🐳 **T2/T3 instances**: Sử dụng cơ chế **Burstable performance** (hiệu suất có thể tăng đột biến), phù hợp cho các ứng dụng có lưu lượng không ổn định.

### 💡 Ví dụ (nếu có)

```example
Phân tích tên gọi Instance: c7gn.xlarge
- c: Họ Compute optimized (Tối ưu tính toán).
- 7: Thế hệ thứ 7 (mới nhất tại thời điểm tài liệu).
- g: Sử dụng bộ vi xử lý AWS Graviton.
- n: Tối ưu hóa cho băng thông mạng cao.
- xlarge: Kích cỡ lớn (thường có 4 vCPU và 8 GiB RAM).
```

---

## 📘 Phần 4: Adding storage to an Amazon EC2 instance

### 🔑 Ý chính

- **Lưu trữ trên EC2** được chia thành hai loại chính: **Root volume** (Ổ đĩa gốc chứa hệ điều hành) và **Data volumes** (Ổ đĩa bổ sung để lưu trữ dữ liệu ứng dụng).
- Có 4 giải pháp lưu trữ phổ biến:
  - **Instance store**: Lưu trữ tạm thời, gắn trực tiếp vào máy chủ vật lý.
  - **Amazon EBS (Elastic Block Store)**: Lưu trữ khối bền vững, kết nối qua mạng.
  - **Amazon EFS (Elastic File System)**: Hệ thống tệp chia sẻ cho Linux.
  - **Amazon FSx for Windows File Server**: Hệ thống tệp chia sẻ cho Windows.

### 🧠 Giải thích

- **Instance store**:
  - Đây là **Ephemeral storage** (lưu trữ tạm thời). Dữ liệu sẽ **bị mất** nếu instance bị **Stopped** (tạm dừng) hoặc **Terminated** (bị xóa).
  - Phù hợp cho các dữ liệu không cần bảo tồn lâu dài như **Buffers**, **Caches**, hoặc **Scratch data** (dữ liệu nháp).
- **Amazon EBS**:
  - Là **Persistent block storage** (lưu trữ khối bền vững). Dữ liệu vẫn tồn tại ngay cả khi instance bị dừng.
  - Hoạt động trong một **Availability Zone (AZ)** cụ thể. Bạn có thể tạo **Snapshots** (bản sao lưu) để lưu trữ trên S3.
  - Phân loại:
    - **SSD-backed**: Gồm **gp2/gp3** (đa năng) và **io1/io2** (hiệu năng cao/IOPS lớn).
    - **HDD-backed**: Gồm **st1** (ưu tiên băng thông) và **sc1** (chi phí thấp nhất, ít truy cập).
- **Shared File Systems (Hệ thống tệp chia sẻ)**:
  - **Amazon EFS**: Dành cho **Linux**. Nó có khả năng tự động mở rộng (Elastic) và cho phép hàng nghìn instance cùng truy cập một lúc thông qua giao thức **NFS**.
  - **Amazon FSx for Windows**: Sử dụng giao thức **SMB**, tương thích hoàn toàn với các ứng dụng chạy trên Windows và tích hợp với **Microsoft Active Directory**.

### ⚖️ So sánh các loại lưu trữ

| Đặc điểm             | Instance Store       | Amazon EBS           | Amazon EFS            | Amazon FSx (Win)       |
| :------------------- | :------------------- | :------------------- | :-------------------- | :--------------------- |
| **Tính bền vững**    | **Không** (Tạm thời) | **Có** (Bền vững)    | **Có** (Bền vững)     | **Có** (Bền vững)      |
| **Giao thức**        | Block (Cục bộ)       | Block (Mạng)         | File (NFS)            | File (SMB)             |
| **Chia sẻ**          | 1 instance duy nhất  | Thường là 1 instance | Nhiều instance Linux  | Nhiều instance Windows |
| **Khả năng mở rộng** | Cố định theo máy     | Thủ động thay đổi    | **Tự động (Elastic)** | Cấu hình linh hoạt     |

### 📌 Ghi nhớ nhanh

- 🛑 **Instance Store**: Chỉ dùng cho dữ liệu tạm thời. Mất máy là mất dữ liệu.
- 🧱 **Amazon EBS**: Giống như ổ cứng rời gắn vào máy tính qua mạng. Có thể tháo ra từ máy này gắn vào máy khác trong cùng AZ.
- 📂 **Amazon EFS**: Giống như ổ đĩa mạng chung cho toàn bộ công ty (chỉ dùng cho Linux).
- 🖼️ **EBS-optimized instances**: Một số loại máy ảo cung cấp băng thông mạng riêng biệt cho EBS để đảm bảo hiệu năng đọc/ghi tối đa.

### 💡 Ví dụ (nếu có)

```example
Lựa chọn lưu trữ theo nhu cầu:
1. Chạy hệ điều hành Linux: Sử dụng Amazon EBS (gp3).
2. Lưu trữ dữ liệu Log tạm thời trong vài giờ: Sử dụng Instance Store.
3. Hệ thống quản lý nội dung (CMS) cần 10 máy chủ web cùng truy cập vào một thư mục ảnh chung: Sử dụng Amazon EFS.
4. Chạy cơ sở dữ liệu SQL Server lớn trên Windows cần chia sẻ tệp tin: Sử dụng Amazon FSx for Windows File Server.
```

---

## 📘 Phần 5: Other EC2 configuration considerations

### 🔑 Ý chính

- **User data** (Dữ liệu người dùng): Cho phép chạy các kịch bản tự động hóa ngay khi thực thể khởi tạo.
- **Instance metadata** (Siêu dữ liệu thực thể): Dữ liệu về chính thực thể đó, có thể truy cập từ bên trong máy ảo.
- **AMI deployment models** (Các mô hình triển khai AMI): Các chiến lược chuẩn bị bản mẫu từ cơ bản đến hoàn thiện.
- **Placement groups** (Nhóm vị trí): Kiểm soát vị trí vật lý của các thực thể để tối ưu hiệu suất hoặc độ tin cậy.

### 🧠 Giải thích

- **User data**:
  - Là các **Shell scripts** hoặc **Cloud-init directives** được cung cấp lúc khởi chạy instance.
  - Giúp tự động hóa các tác vụ như cập nhật phần mềm, cài đặt dịch vụ (ví dụ: Apache) ngay khi máy vừa bật lên lần đầu.
- **Instance metadata**:
  - Chứa thông tin như **instance-id**, **public-ipv4**, **local-ipv4**, **mac**.
  - Được truy cập thông qua một URL đặc biệt bên trong máy: `http://169.254.169.254/latest/meta-data/`.
- **Placement groups**:
  - **Cluster** (Cụm): Các instance nằm sát nhau trong cùng một **Availability Zone** để có **Low-latency** (độ trễ thấp) và **High-throughput** (băng thông cao).
  - **Partition** (Phân vùng): Các instance nằm trên các phân vùng phần cứng riêng biệt để tránh lỗi lan truyền.
  - **Spread** (Phân tán): Mỗi instance nằm trên một tủ rack phần cứng riêng lẻ (tối đa 7 instance mỗi AZ).

### ⚖️ So sánh: Các mô hình triển khai AMI

| Đặc điểm                                | Basic AMI                               | Silver AMI                        | Golden AMI                        |
| :-------------------------------------- | :-------------------------------------- | :-------------------------------- | :-------------------------------- |
| **Cấu hình**                            | Chỉ có **OS-only** (Hệ điều hành thuần) | **Half baked** (Cấu hình một nửa) | **Fully baked** (Cấu hình đầy đủ) |
| **Build time** (Thời gian tạo bản mẫu)  | Rất nhanh                               | Trung bình                        | Lâu nhất                          |
| **Boot time** (Thời gian khởi động máy) | Chậm nhất (vì phải cài thêm phần mềm)   | Trung bình                        | **Nhanh nhất**                    |
| **Tính linh hoạt**                      | Cao nhất                                | Trung bình                        | Thấp nhất (bản mẫu cố định)       |

### 📌 Ghi nhớ nhanh

- 📝 **User data**: Chỉ chạy **một lần duy nhất** khi instance khởi tạo lần đầu (trừ khi cấu hình lại thủ công).
- 🔑 **169.254.169.254**: Địa chỉ IP "thần thánh" để lấy thông tin máy ảo mà không cần quyền Admin bên ngoài.
- 🏗️ **Golden AMI**: Là cách tốt nhất để đảm bảo tính nhất quán giữa hàng trăm máy ảo.
- 📍 **Placement groups**: Dùng **Cluster** cho tính toán hiệu năng cao (HPC), dùng **Spread** cho các ứng dụng quan trọng cần độ bền cao.

### 💡 Ví dụ (nếu có)

```example
Đoạn mã User data đơn giản để biến EC2 thành Web server ngay khi khởi tạo:
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello from EC2</h1>" > /var/www/html/index.html
```

---

## 📘 Phần 6: Amazon EC2 pricing options

### 🔑 Ý chính

- **EC2 pricing models** (Các mô hình định giá EC2) cung cấp nhiều chiến lược mua hàng khác nhau để tối ưu hóa chi phí dựa trên đặc thù của khối lượng công việc.
- Các nhóm mô hình chính bao gồm:
  - **Purchase models** (Mô hình mua): Tập trung vào tiết kiệm chi phí thông qua cam kết sử dụng hoặc tận dụng tài nguyên dư thừa.
  - **Capacity reserved models** (Mô hình dự phòng dung lượng): Đảm bảo bạn luôn có sẵn tài nguyên khi cần.
  - **Dedicated models** (Mô hình chuyên dụng): Đáp ứng các yêu cầu khắt khe về bảo mật và tuân thủ trên phần cứng riêng biệt.

### 🧠 Giải thích

- **Các mô hình mua phổ biến**:
  - **On-Demand Instances** (Thực thể theo yêu cầu): Thanh toán theo giây hoặc giờ, không có cam kết dài hạn. Phù hợp cho các workload không ổn định hoặc giai đoạn thử nghiệm.
  - **Reserved Instances - RI** (Thực thể đặt trước): Cam kết sử dụng trong **1 hoặc 3 năm** để nhận mức chiết khấu lớn (lên tới 72%). Phù hợp cho các workload chạy ổn định lâu dài.
  - **Savings Plans** (Gói tiết kiệm): Linh hoạt hơn RI, cho phép áp dụng mức giảm giá tương đương RI nhưng dựa trên cam kết chi tiêu theo số tiền (ví dụ: 10$/giờ) thay vì cam kết theo loại thực thể cụ thể.
  - **Spot Instances** (Thực thể giao ngay): Tận dụng dung lượng EC2 dư thừa của AWS với mức giá cực rẻ (giảm tới 90%). Tuy nhiên, AWS có thể thu hồi thực thể này bất cứ lúc nào với thông báo trước 2 phút.
- **Các lựa chọn chuyên dụng**:
  - **Dedicated Instances** (Thực thể chuyên dụng): Chạy trên phần cứng dành riêng cho một khách hàng duy nhất (đơn quyền sở hữu phần cứng).
  - **Dedicated Hosts** (Máy chủ vật lý riêng): Cấp cho bạn quyền kiểm soát toàn bộ máy chủ vật lý, bao gồm khả năng xem thông tin về socket, core và hỗ trợ mang theo giấy phép phần mềm riêng (**BYOL - Bring Your Own License**).

### ⚖️ So sánh các lựa chọn mua EC2

| Mô hình             | Đặc điểm chính                     | Trường hợp sử dụng tốt nhất                                          |
| :------------------ | :--------------------------------- | :------------------------------------------------------------------- |
| **On-Demand**       | Linh hoạt tuyệt đối, giá chuẩn.    | Workload ngắn hạn, spiky (đột biến).                                 |
| **Reserved**        | Giảm giá sâu, cam kết 1-3 năm.     | Workload ổn định (**Steady-state**).                                 |
| **Savings Plans**   | Giảm giá sâu + linh hoạt cấu hình. | Workload cần thay đổi loại thực thể thường xuyên.                    |
| **Spot**            | Rẻ nhất, có thể bị gián đoạn.      | Workload chịu lỗi tốt (**Fault-tolerant**), xử lý dữ liệu hàng loạt. |
| **Dedicated Hosts** | Toàn bộ máy chủ vật lý riêng.      | Yêu cầu tuân thủ pháp lý cao, dùng License riêng.                    |

### 📌 Ghi nhớ nhanh

- 💰 **AWS Free Tier**: Cung cấp **750 giờ** mỗi tháng cho các loại thực thể nhỏ (như t2.micro hoặc t3.micro tùy vùng) trong 12 tháng đầu tiên.
- ⚡ **Per-second billing**: Tính phí theo giây hiện áp dụng cho hầu hết các thực thể chạy **Linux** hoặc **Ubuntu**.
- 🔄 **Cost Optimization Guideline**: Bí quyết tối ưu chi phí là kết hợp:
  - **RI/Savings Plans** cho phần tài nguyên chạy nền ổn định.
  - **On-Demand** cho các phần tăng trưởng đột xuất.
  - **Spot** cho các tác vụ xử lý có thể bị ngắt quãng.

### 💡 Ví dụ (nếu có)

```example
Chiến lược tối ưu chi phí cho ứng dụng Thương mại điện tử:
1. Cơ sở dữ liệu chạy 24/7: Mua Reserved Instances để giảm 50-70% chi phí.
2. Máy chủ Web chạy ổn định ban ngày: Dùng Savings Plans để linh hoạt thay đổi cấu hình.
3. Chạy thêm máy chủ vào các dịp Sale lớn: Dùng On-Demand để đáp ứng tức thời.
4. Tác vụ xử lý ảnh sản phẩm hoặc báo cáo hàng đêm: Dùng Spot Instances để tiết kiệm 90% chi phí.
```

---

## 📝 Knowledge Check

### ❓ Question 1

Which attributes are reasons to choose Amazon EC2? (Select TWO.)

A. AWS management of operating system (OS) security

B. AWS management of operating system (OS) patches

C. Ability to run any type of workload

D. Ability to run serverless applications

E. Complete control of computing resources

✅ **Đáp án đúng:** C, E

### 🧠 Giải thích

- **Ability to run any type of workload** (**Khả năng chạy mọi loại khối lượng công việc**): Amazon EC2 cung cấp các máy chủ ảo linh hoạt, cho phép bạn cài đặt bất kỳ phần mềm hoặc ứng dụng nào từ web server đến cơ sở dữ liệu.
- **Complete control of computing resources** (**Kiểm soát hoàn toàn tài nguyên tính toán**): Bạn có quyền quản trị cao nhất (root/administrator), tự do cấu hình hệ điều hành và ngăn xếp phần mềm.

### ❌ Vì sao đáp án khác sai?

- **A & B**: Việc quản lý bảo mật và vá lỗi (patching) hệ điều hành trên EC2 là trách nhiệm của **khách hàng**, không phải của AWS.
- **D**: Amazon EC2 là dịch vụ máy chủ ảo (Virtual Machines), không phải là dịch vụ **Serverless** (như AWS Lambda).

💡 **Mẹo nhớ nhanh:** EC2 thuộc mô hình **IaaS** (Hạ tầng như một dịch vụ). Hãy nhớ: **Your OS = Your Responsibility** (Hệ điều hành của bạn = Trách nhiệm của bạn).

---

### ❓ Question 2

What are the benefits of using an Amazon Machine Image (AMI)? (Select THREE.)

A. Selling or sharing software solutions packaged as an AMI

B. Updating systems by patching their AMI

C. Automating security group settings for instances

D. Using an AMI as a server backup for Amazon EC2 instances

E. Launching instances with the same configuration

F. Migrating data from on premises to Amazon EC2 instances

✅ **Đáp án đúng:** A, D, E

### 🧠 Giải thích

- **Selling or sharing** (**Bán hoặc chia sẻ**): Bạn có thể đóng gói phần mềm vào AMI và bán trên **AWS Marketplace**.
- **Server backup** (**Sao lưu máy chủ**): AMI lưu lại trạng thái hệ thống, đóng vai trò như một bản backup để khôi phục khi cần.
- **Launching instances with the same configuration** (**Khởi chạy các thực thể với cùng cấu hình**): Đảm bảo tính đồng nhất khi triển khai hàng loạt máy chủ.

### ❌ Vì sao đáp án khác sai?

- **B**: Bạn không "vá lỗi" trực tiếp lên AMI cũ; bạn phải tạo một AMI mới sau khi đã vá lỗi trên một instance đang chạy.
- **C**: **Security group** (Nhóm bảo mật) được cấu hình riêng biệt, không phải là thành phần bên trong file image của AMI.
- **F**: AMI là khuôn mẫu máy chủ, không phải là công cụ chuyên dụng để di chuyển dữ liệu (Data Migration).

💡 **Mẹo nhớ nhanh:** Hãy coi **AMI** như một cái **Khuôn bánh** — giúp bạn tạo ra hàng nghìn chiếc bánh giống hệt nhau một cách nhanh chóng.

---

### ❓ Question 3

A system administrator must change the instance types of multiple running Amazon EC2 instances. The instances were launched with a mix of Amazon Elastic Block Store (Amazon EBS) backed Amazon Machine Images (AMIs) and instance-store-backed AMIs. Which method is a valid way to change the instance type?

A. Change the instance type of an Amazon EBS backed instance without stopping it.

B. Change the instance type of an instance-store-backed instance without stopping it.

C. Stop an instance-store-backed instance, change its instance type, and start the instance.

D. Stop an Amazon EBS backed instance, change its instance type, and start the instance.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- Để thay đổi **Instance type** (Loại thực thể), thực thể đó phải ở trạng thái **Stopped**. Chỉ những thực thể sử dụng **Amazon EBS-backed** mới hỗ trợ tính năng dừng (Stop) và khởi động lại (Start) trong khi vẫn giữ nguyên dữ liệu.

### ❌ Vì sao đáp án khác sai?

- **A & B**: Bạn không thể thay đổi phần cứng (CPU/RAM) khi máy chủ đang chạy (**Running**).
- **C**: Thực thể **Instance-store-backed** không hỗ trợ trạng thái "Stop". Nếu bạn tắt nó, thực thể sẽ bị **Terminated** (xóa bỏ) và dữ liệu sẽ mất sạch.

💡 **Mẹo nhớ nhanh:** Muốn đổi máy, phải tắt máy. Chỉ **EBS** mới "Stop" được, **Instance Store** chỉ có "Terminate".

---

### ❓ Question 4

A workload requires high read/write access to large local datasets. Which instance types would perform BEST for this workload? (Select TWO.)

A. Compute optimized

B. Memory optimized

C. Storage optimized

D. Accelerated computing

E. General purpose

✅ **Đáp án đúng:** A, C

### 🧠 Giải thích

- **Storage optimized** (**Tối ưu lưu trữ**): Thiết kế riêng cho các tác vụ yêu cầu hàng chục nghìn thao tác đọc/ghi (IOPS) trên các tập dữ liệu cực lớn lưu cục bộ.
- **Compute optimized** (**Tối ưu tính toán**): Hỗ trợ xử lý nhanh các khối lượng công việc đòi hỏi hiệu suất CPU cao đi kèm với khả năng xử lý dữ liệu đầu vào/đầu ra lớn.

### ❌ Vì sao đáp án khác sai?

- **B, D, E**: Không tập trung mạnh nhất vào khả năng đọc/ghi (I/O) trực tiếp lên ổ đĩa cục bộ như dòng **Storage optimized**.

💡 **Mẹo nhớ nhanh:** Đề bài nhắc đến **"High read/write access"** ➔ Ưu tiên hàng đầu là **Storage optimized** (dòng I hoặc D).

---

### ❓ Question 5

An application requires the media access control (MAC) address of the host Amazon EC2 instance. The architecture uses an AWS Auto Scaling group to dynamically launch and terminate instances. What is the BEST way for the application to obtain the MAC address?

A. Write the MAC address in the application configuration file of each instance.

B. Include the MAC address in a custom AMI for each instance in the AWS Auto Scaling group.

C. Include the MAC address in the Amazon Machine Image (AMI) that is used to launch all of the instances in the AWS Auto Scaling group.

D. Use the user data of each instance to access the MAC address through the instance metadata.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Instance metadata** (**Siêu dữ liệu thực thể**): Là nơi lưu trữ mọi thông tin kỹ thuật của thực thể (bao gồm địa chỉ MAC, IP, ID). Ứng dụng có thể lấy thông tin này trực tiếp từ bên trong máy ảo thông qua đường dẫn `http://169.254.169.254/latest/meta-data/`.

### ❌ Vì sao đáp án khác sai?

- **A, B, C**: Địa chỉ MAC là duy nhất cho mỗi máy ảo. Bạn không thể dự đoán hoặc ghi cứng nó vào file cấu hình hay AMI trước khi máy được khởi tạo.

💡 **Mẹo nhớ nhanh:** Muốn lấy thông tin "nội tại" của máy (như MAC, IP) ➔ Luôn tìm đến **Instance Metadata**.

---

### ❓ Question 6

Which statements about user data are correct? (Select TWO.)

A. The cloud architect must run the /var/lib/cloud/instance/scripts/part-001 command for the user data script to run again.

B. User data cannot be run while the instance is stopped.

C. By default, user data runs after every instance restart.

D. By default, user data runs only once, when an instance is launched.

E. The cloud architect must remove the config_user_scripts file to rerun the user data scripts.

✅ **Đáp án đúng:** B, D

### 🧠 Giải thích

- **User data** (**Dữ liệu người dùng**): Theo mặc định, script này chỉ chạy **duy nhất một lần** trong lần khởi chạy (**launch**) đầu tiên của thực thể.
- **Cannot be run while stopped**: Script cần hệ điều hành đang trong quá trình boot để thực thi, nên không thể chạy khi máy đang tắt.

### ❌ Vì sao đáp án khác sai?

- **C**: Sai, nó không tự động chạy lại sau mỗi lần khởi động lại (reboot) trừ khi được cấu hình đặc biệt.
- **A & E**: Đây không phải là các lệnh chuẩn hoặc cách thức thông thường để quản lý việc tái thực thi user data trong AWS.

💡 **Mẹo nhớ nhanh:** **User Data = One-time setup** (Cài đặt một lần duy nhất lúc mới mua máy).

---

### ❓ Question 7

A transactional workload on an Amazon EC2 instance performs high amounts of frequent read and write operations. Which Amazon Elastic Block Store (Amazon EBS) volume type is BEST for this workload?

A. Cold hard disk drive (HDD)

B. Throughput Optimized hard disk drive (HDD)

C. General Purpose solid state drive (SSD)

D. Provisioned IOPS solid state drive (SSD)

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Provisioned IOPS SSD** (io1/io2): Là loại ổ đĩa cao cấp nhất, cho phép bạn chỉ định chính xác số lượng **IOPS** (thao tác đọc ghi mỗi giây). Phù hợp nhất cho các cơ sở dữ liệu **Transactional** (giao dịch) yêu cầu độ trễ cực thấp.

### ❌ Vì sao đáp án khác sai?

- **A & B (HDD)**: HDD có tốc độ đọc ghi ngẫu nhiên rất chậm, không phù hợp cho giao dịch (transaction).
- **C (General Purpose)**: Dùng cho các mục đích chung, không đảm bảo hiệu suất cực cao cho các tác vụ nặng về đọc ghi liên tục.

💡 **Mẹo nhớ nhanh:** Database nặng/Giao dịch nhiều ➔ **Provisioned IOPS**. Lưu trữ phim/dữ liệu ít dùng ➔ **HDD**.

---

### ❓ Question 8

Amazon Elastic File System (Amazon EFS) provide compared to creating an NFS share on an EC2 instance? (Select TWO.)

A. File locking

B. Strong consistency

C. No need for backups

D. High availability

E. Automatic scaling

✅ **Đáp án đúng:** D, E

### 🧠 Giải thích

- **High availability** (**Độ sẵn sàng cao**): EFS lưu trữ dữ liệu trên nhiều **Availability Zones** (AZs), trong khi một thực thể EC2 đơn lẻ là một điểm lỗi duy nhất (Single point of failure).
- **Automatic scaling** (**Tự động mở rộng**): EFS tự động tăng/giảm dung lượng dựa trên dữ liệu bạn lưu, bạn không cần quản lý ổ đĩa thủ công.

### ❌ Vì sao đáp án khác sai?

- **A & B**: Cả NFS trên EC2 và EFS đều hỗ trợ các tính năng này.
- **C**: Dù là dịch vụ managed, bạn vẫn nên thực hiện **Backup** (sao lưu) để bảo vệ dữ liệu khỏi lỗi do người dùng hoặc ứng dụng.

💡 **Mẹo nhớ nhanh:** **EFS = Managed + Elastic**. Bạn không phải lo hết ổ đĩa, không phải lo hỏng máy chủ chứa file.

---

### ❓ Question 9

Which feature does Amazon FSx for Windows File Server provide?

A. Microsoft Active Directory server for Windows file servers

B. Amazon management agent for Windows file servers

C. Backup solution for on-premises Windows file servers

D. Fully managed Windows file servers

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Fully managed Windows file servers**: Đây là dịch vụ cung cấp hệ thống tệp tin Windows (SMB) được AWS quản lý hoàn toàn, tương thích tuyệt đối với các ứng dụng Windows hiện có mà không cần bạn tự cài máy chủ Windows.

### ❌ Vì sao đáp án khác sai?

- **A**: FSx **tích hợp** với Active Directory nhưng nó không phải là dịch vụ cung cấp server AD.
- **C**: FSx là giải pháp lưu trữ trên mây, không phải là một giải pháp chuyên biệt để backup cho máy chủ on-premises.

💡 **Mẹo nhớ nhanh:** Thấy chữ **Windows** + **File Server** ➔ Chọn **Amazon FSx for Windows**.

---

### ❓ Question 10

Which descriptions of Amazon EC2 pricing options are correct? (Select TWO.)

A. With On-Demand Instances, customers can pay for compute capacity by usage time with no long-term commitments.

B. Dedicated Hosts are servers that are dedicated to one purpose, such as a firewall.

C. Savings Plans are budgeting tools that help customers manage Amazon EC2 costs.

D. Spot Instances offer spare compute capacity at discounted prices and can be interrupted.

E. Reserved Instances are physical servers that are reserved exclusively for customer use.

✅ **Đáp án đúng:** A, D

### 🧠 Giải thích

- **On-Demand** (**Theo yêu cầu**): Dùng bao nhiêu trả bấy nhiêu, không cần cam kết thời gian.
- **Spot Instances** (**Thực thể giao ngay**): Giá rẻ nhất (giảm tới 90%) nhưng AWS có thể thu hồi bất cứ lúc nào.

### ❌ Vì sao đáp án khác sai?

- **B**: **Dedicated Hosts** là máy chủ vật lý dành riêng cho một khách hàng, không phải giới hạn cho một "mục đích" như firewall.
- **C**: **Savings Plans** là một mô hình giảm giá dựa trên cam kết chi tiêu, không đơn thuần là công cụ lập ngân sách (budgeting tool).
- **E**: **Reserved Instances** là một hình thức thanh toán/đặt chỗ về mặt tài chính, không phải là máy chủ vật lý riêng biệt (đó là Dedicated Host).

💡 **Mẹo nhớ nhanh:** **On-demand** = Linh hoạt. **Reserved/Savings** = Cam kết để rẻ. **Spot** = Rẻ nhất nhưng có rủi ro bị ngắt.

---

# Practice Test - Module 05: Adding a Compute Layer Using Amazon EC2

## Question 1

Which of the following is a responsibility of the customer when using Amazon EC2 under the AWS Shared Responsibility Model?

A. Managing the virtualization layer

B. Patching the guest operating system

C. Maintaining physical hardware

D. Securing the underlying host server

✅ Correct Answer: B

### 🧠 Explanation

Trong mô hình **Shared Responsibility Model** (Trách nhiệm chung), AWS quản lý hạ tầng vật lý và lớp ảo hóa. Khách hàng có quyền kiểm soát hoàn toàn hệ điều hành trên thực thể EC2, do đó họ có trách nhiệm thực hiện **Patching** (vá lỗi) và bảo mật cho hệ điều hành đó.

### ❌ Why Other Choices Are Wrong

- A: Quản lý lớp ảo hóa (**Hypervisor**) là trách nhiệm của AWS.
- C: Bảo trì phần cứng vật lý tại trung tâm dữ liệu là trách nhiệm của AWS.
- D: Bảo mật máy chủ vật lý (**Host server**) là trách nhiệm của AWS.

---

## Question 2

What information is included in an Amazon Machine Image (AMI)?

A. The VPC configuration for the instance

B. A template for the root volume (OS and applications)

C. The IAM roles associated with the instance

D. The Public IP address assigned to the instance

✅ Correct Answer: B

### 🧠 Explanation

**AMI** là một khuôn mẫu (template) chứa thông tin phần mềm cơ bản để khởi chạy instance, bao gồm hệ điều hành, máy chủ ứng dụng và các ứng dụng đi kèm trên **Root volume**.

### ❌ Why Other Choices Are Wrong

- A, C, D: Cấu hình mạng (**VPC**), quyền hạn (**IAM roles**), và địa chỉ IP là các thông số thuộc về cấu hình lúc khởi chạy (**Launch configuration**), không nằm bên trong file image của AMI.

---

## Question 3

When an Amazon EBS-backed instance is in the "Stopped" state, what happens to the data on the EBS root volume?

A. The data is deleted automatically

B. The data remains intact on the volume

C. The data is moved to Amazon S3

D. The data is transferred to a new instance store

✅ Correct Answer: B

### 🧠 Explanation

Khác với **Instance store** (tạm thời), **Amazon EBS** là ổ đĩa lưu trữ bền vững (**Persistent storage**). Khi bạn **Stop** instance, dữ liệu vẫn được giữ nguyên trên ổ đĩa và sẵn sàng khi bạn **Start** lại máy.

### ❌ Why Other Choices Are Wrong

- A: Dữ liệu chỉ bị xóa nếu bạn **Terminate** instance và đã bật tính năng "Delete on termination".
- C: AWS không tự động chuyển dữ liệu EBS sang S3 trừ khi bạn tạo **Snapshot**.
- D: **Instance store** không thể nhận dữ liệu chuyển từ EBS một cách tự động.

---

## Question 4

A user needs to retrieve the instance ID and local IP address from within an EC2 instance. Which URL should they use?

A. http://169.254.169.254/latest/meta-data/

B. http://127.0.0.1/latest/user-data/

C. http://status.aws.amazon.com/

D. http://192.168.0.1/meta-data/

✅ Correct Answer: A

### 🧠 Explanation

**Instance metadata** (siêu dữ liệu thực thể) được truy cập từ bên trong máy ảo thông qua địa chỉ IP Link-local là `169.254.169.254`.

### ❌ Why Other Choices Are Wrong

- B: `127.0.0.1` là địa chỉ localhost thông thường, và **User data** không chứa ID hay IP của máy.
- C: Đây là trang kiểm tra trạng thái dịch vụ toàn cầu của AWS.
- D: Địa chỉ IP này không phải là địa chỉ chuẩn để truy cập metadata của AWS.

---

## Question 5

Which EC2 feature allows you to run a script to install software automatically when the instance boots for the first time?

A. Instance Metadata

B. Amazon Machine Image

C. User Data

D. Placement Groups

✅ Correct Answer: C

### 🧠 Explanation

**User Data** cho phép bạn truyền các script (như Shell script) vào instance để thực hiện **Bootstrapping** (tự động cài đặt/cấu hình) khi máy ảo khởi chạy lần đầu tiên.

### ❌ Why Other Choices Are Wrong

- A: **Metadata** dùng để lấy thông tin máy, không dùng để chạy script.
- B: **AMI** là bản sao có sẵn, không linh hoạt bằng việc chạy script tùy chỉnh lúc khởi tạo.
- D: **Placement Groups** dùng để cấu hình vị trí vật lý của máy chủ.

---

## Question 6

Which placement group strategy is designed to provide the lowest possible network latency by placing instances close together in a single Availability Zone?

A. Partition

B. Spread

C. Cluster

D. Shared

✅ Correct Answer: C

### 🧠 Explanation

**Cluster placement group** đóng gói các instance sát nhau trong cùng một tầng mạng vật lý thuộc 1 **Availability Zone**, giúp đạt được **Lowest network latency** (độ trễ mạng thấp nhất) và băng thông cao.

### ❌ Why Other Choices Are Wrong

- A: **Partition** dùng để chia các nhóm máy trên các phân vùng phần cứng khác nhau để giảm thiểu rủi ro lỗi phần cứng đồng loạt.
- B: **Spread** đặt mỗi máy trên một tủ rack riêng biệt để tối đa tính an toàn.

---

## Question 7

A company has a legacy application that must run on a physical server dedicated for their use to meet specific licensing requirements. Which EC2 purchasing option should they choose?

A. Dedicated Instances

B. Spot Instances

C. On-Demand Instances

D. Dedicated Hosts

✅ Correct Answer: D

### 🧠 Explanation

**Dedicated Hosts** cung cấp một máy chủ vật lý hoàn toàn dành riêng cho khách hàng. Nó cung cấp quyền kiểm soát mức độ phần cứng (như số core, socket), hỗ trợ tốt nhất cho các yêu cầu về **Compliance** và **BYOL** (Bring Your Own License).

### ❌ Why Other Choices Are Wrong

- A: **Dedicated Instances** chạy trên phần cứng riêng nhưng khách hàng không có quyền kiểm soát sâu mức độ vật lý như Dedicated Hosts.
- B, C: Chạy trên hạ tầng chia sẻ (**Shared tenancy**).

---

## Question 8

A developer wants to run a large-scale data processing job that is fault-tolerant and can be interrupted at any time. The primary goal is to minimize costs as much as possible. Which instance type is most appropriate?

A. Reserved Instances

B. Spot Instances

C. Dedicated Instances

D. On-Demand Instances

✅ Correct Answer: B

### 🧠 Explanation

**Spot Instances** sử dụng dung lượng dư thừa của AWS với giá rẻ hơn tới 90%. Điều kiện là ứng dụng phải chịu được việc bị ngắt quãng (**Fault-tolerant/Interrupted**), rất phù hợp cho xử lý dữ liệu hàng loạt.

### ❌ Why Other Choices Are Wrong

- A: Yêu cầu cam kết 1-3 năm, không phù hợp cho các task ngắn hạn đột xuất.
- D: Giá cao hơn nhiều so với Spot.

---

## Question 9

A startup is building a social media platform and needs a web server that balances compute, memory, and networking resources. Which instance family should they use?

A. C-family (Compute Optimized)

B. R-family (Memory Optimized)

C. M-family (General Purpose)

D. I-family (Storage Optimized)

✅ Correct Answer: C

### 🧠 Explanation

Họ thực thể **M-family** (như M5, M6g) thuộc nhóm **General Purpose**, được thiết kế để cân bằng giữa CPU, RAM và Network, lý tưởng cho các ứng dụng web thông thường.

### ❌ Why Other Choices Are Wrong

- A: **C-family** tập trung vào CPU mạnh.
- B: **R-family** tập trung vào dung lượng RAM lớn.
- D: **I-family** tập trung vào tốc độ đọc ghi ổ đĩa.

---

## Question 10

A business is running a production database on EC2 and expects the workload to be steady and consistent for the next 3 years. Which pricing model offers the highest discount for this long-term commitment?

A. On-Demand

B. Spot Instances

C. Reserved Instances

D. Standard Instances

✅ Correct Answer: C

### 🧠 Explanation

Khi có một **Steady workload** (khối lượng công việc ổn định) và cam kết dài hạn (1 hoặc 3 năm), **Reserved Instances (RI)** cung cấp mức giảm giá đáng kể so với trả phí theo yêu cầu.

### ❌ Why Other Choices Are Wrong

- A: Phắt nhất, dùng cho ngắn hạn.
- B: Không dùng cho ứng dụng quan trọng cần chạy ổn định 24/7 vì có thể bị AWS thu hồi.

---

## Question 11

An organization needs to share files between several Linux-based EC2 instances simultaneously. The storage should scale automatically as files are added. Which service is best?

A. Amazon EBS

B. Instance Store

C. Amazon EFS

D. Amazon FSx for Windows

✅ Correct Answer: C

### 🧠 Explanation

**Amazon EFS** là một dịch vụ file system quản lý hoàn toàn cho **Linux**, hỗ trợ giao thức NFS và cho phép hàng nghìn máy ảo truy cập đồng thời. Nó cũng có tính năng **Elastic** (tự động mở rộng).

### ❌ Why Other Choices Are Wrong

- A: **EBS** thường chỉ gắn được vào 1 instance tại một thời điểm (trừ tính năng Multi-attach nhưng có giới hạn).
- D: **FSx for Windows** dùng giao thức SMB cho môi trường Windows.

---

## Question 12

A media company is transcoding large video files, a task that requires high CPU performance but relatively low memory. Which instance family is the most cost-effective choice?

A. Memory Optimized

B. Compute Optimized

C. Storage Optimized

D. Accelerated Computing

✅ Correct Answer: B

### 🧠 Explanation

**Compute Optimized** (dòng C) có tỷ lệ CPU trên Memory cao nhất, cực kỳ phù hợp cho các tác vụ cần nhiều sức mạnh xử lý như **Transcoding** (mã hóa video), phân tích dữ liệu.

### ❌ Why Other Choices Are Wrong

- A: Dùng cho database lớn trong RAM.
- D: Dùng cho GPU (đồ họa, AI), có thể quá đắt cho việc chỉ cần CPU để transcoding.

---

## Question 13

A system administrator needs to increase the CPU and RAM of an existing EBS-backed EC2 instance to handle a larger workload. What must be done before changing the instance type?

A. The instance must be terminated

B. The instance must be stopped

C. The instance must be rebooted

D. No action is needed; it can be changed while running

✅ Correct Answer: B

### 🧠 Explanation

Để thay đổi kích cỡ phần cứng (**Instance type**), bạn phải đưa instance về trạng thái **Stopped**. Sau khi thay đổi cấu hình, bạn khởi động lại máy để nhận tài nguyên mới.

### ❌ Why Other Choices Are Wrong

- A: **Terminate** sẽ xóa máy vĩnh viễn.
- D: AWS không cho phép đổi cấu hình phần cứng vật lý khi máy đang chạy.

---

## Question 14

An application requires extremely high, consistent IOPS for a transactional database. Which EBS volume type should the architect recommend?

A. General Purpose SSD (gp3)

B. Throughput Optimized HDD (st1)

C. Provisioned IOPS SSD (io2)

D. Cold HDD (sc1)

✅ Correct Answer: C

### 🧠 Explanation

**Provisioned IOPS SSD** (io1/io2) là lựa chọn hiệu năng cao nhất, cho phép cấu hình chính xác số lượng IOPS để đảm bảo độ trễ thấp cho các ứng dụng cực kỳ khắt khe về dữ liệu.

### ❌ Why Other Choices Are Wrong

- A: **gp3** tốt nhưng có giới hạn về mức độ ổn định khi lên tới hàng chục nghìn IOPS so với dòng io.
- B, D: **HDD** không phù hợp cho các tác vụ truy cập ngẫu nhiên nhanh (transactional).

---

## Question 15

A company is moving its Windows-based file share to AWS. They require full compatibility with the SMB protocol and integration with Microsoft Active Directory. Which service should they use?

A. Amazon S3

B. Amazon EFS

C. Amazon FSx for Windows File Server

D. Amazon EBS with Multi-Attach

✅ Correct Answer: C

### 🧠 Explanation

**Amazon FSx for Windows File Server** cung cấp dịch vụ lưu trữ file SMB quản lý hoàn toàn, hỗ trợ đầy đủ các tính năng của Windows như NTFS và tích hợp **Active Directory**.

### ❌ Why Other Choices Are Wrong

- B: **EFS** dùng cho Linux (NFS).
- A: **S3** là Object storage, không hỗ trợ giao thức SMB trực tiếp cho file share truyền thống.

---

## Question 16

To improve the reliability of a critical application, an architect wants to ensure that EC2 instances are placed on distinct racks with separate power and network sources within an AZ. Which placement group should be used?

A. Cluster

B. Partition

C. Spread

D. Local

✅ Correct Answer: C

### 🧠 Explanation

**Spread placement group** đảm bảo mỗi instance được đặt trên các tủ rack (giá đỡ thiết bị) vật lý riêng biệt, có nguồn điện và mạng riêng để tránh việc một lỗi phần cứng duy nhất làm sập toàn bộ hệ thống.

### ❌ Why Other Choices Are Wrong

- A: **Cluster** đặt máy sát nhau (tăng rủi ro nếu rack đó lỗi).
- B: **Partition** chia theo nhóm, không đảm bảo mức độ phân tán đến từng máy đơn lẻ như Spread.

---

## Question 17

A genomic research project needs to process massive datasets in-memory to improve processing speed. Which instance family should be selected?

A. C-family

B. R-family

C. T-family

D. D-family

✅ Correct Answer: B

### 🧠 Explanation

**R-family** (Memory Optimized) cung cấp lượng RAM rất lớn cho mỗi vCPU, cực kỳ hiệu quả cho các bài toán **In-memory processing**.

---

## Question 18

An architect notices several EC2 instances are consistently underutilized. Which AWS tool provides recommendations for the most cost-optimal instance types based on historical usage?

A. AWS Trusted Advisor

B. AWS Compute Optimizer

C. Amazon Inspector

D. AWS Cost Explorer

✅ Correct Answer: B

### 🧠 Explanation

**AWS Compute Optimizer** sử dụng máy học để phân tích dữ liệu lịch sử sử dụng (CPU, RAM, Network) và đưa ra khuyến nghị chính xác nên chuyển sang loại máy nào để tiết kiệm tiền mà không giảm hiệu năng.

### ❌ Why Other Choices Are Wrong

- A: **Trusted Advisor** có đưa ra một số gợi ý chi phí nhưng không chuyên sâu về việc "đúng cỡ" (right-sizing) như Compute Optimizer.

---

## Question 19

A developer is testing a new application on EC2. They only need the instance for a few hours a day and want to avoid any upfront costs or long-term commitments. Which pricing model is best?

A. Savings Plans

B. Reserved Instances

C. On-Demand Instances

D. Spot Instances

✅ Correct Answer: C

### 🧠 Explanation

**On-Demand** là lựa chọn linh hoạt nhất cho việc test ứng dụng: dùng giờ nào trả giờ đó, tắt máy là ngừng tính tiền, không cần đặt cọc hay cam kết.

---

## Question 20

An application needs to run complex 3D graphics rendering and scientific simulations. Which category of instance types would provide the necessary hardware acceleration?

A. General Purpose

B. Storage Optimized

C. Accelerated Computing

D. Compute Optimized

✅ Correct Answer: C

### 🧠 Explanation

**Accelerated Computing** (như dòng P hoặc G) sử dụng các bộ tăng tốc phần cứng như **GPU** để xử lý các tác vụ đồ họa 3D, mô phỏng khoa học hiệu quả hơn nhiều so với CPU thông thường.

---

## 📊 Final Score: 100% (20/20)

## 🎯 Weak Topics: None.

Bạn đã làm rất tốt. Hãy tiếp tục duy trì phong độ này cho các Module tiếp theo nhé!
