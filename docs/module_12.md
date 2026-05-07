# Module 12: Caching Content

## 🧭 Overview

- Xác định cách **Caching** (Lưu trữ đệm) giúp cải thiện hiệu năng ứng dụng và giảm **Latency** (Độ trễ).
- Tìm hiểu cách sử dụng **Amazon CloudFront** để phân phối nội dung thông qua các **Edge Locations**.
- Sử dụng **Amazon ElastiCache** để tối ưu hóa hiệu suất cho cơ sở dữ liệu.
- Áp dụng các nguyên tắc của **AWS Well-Architected Framework** trong chiến lược lưu trữ đệm.

---

## 📘 Phần 1: Overview of Caching (Tổng quan về Caching)

### 🔑 Ý chính

- **Cache** (Bộ nhớ đệm): Là một lớp lưu trữ dữ liệu tốc độ cao, lưu giữ một phần dữ liệu (subset) để phục vụ các yêu cầu truy xuất trong tương lai nhanh hơn. ⚡
- **Performance** (Hiệu năng): Tăng tốc độ truy xuất dữ liệu và giảm bớt gánh nặng cho các lớp lưu trữ dữ liệu gốc có tốc độ chậm hơn.
- **Cost Optimization** (Tối ưu chi phí): Giảm thiểu số lượng truy cập vào hệ thống lưu trữ chính hoặc cơ sở dữ liệu, từ đó giảm chi phí vận hành. 💰

### 🧠 Giải thích

**Nội dung nên được đưa vào Cache:**

- **Static and frequently accessed data** (Dữ liệu tĩnh và được truy cập thường xuyên): Hình ảnh, CSS, file script.
- **Computationally intensive calculations** (Kết quả của các phép tính toán chuyên sâu): Tránh việc phải tính toán lại nhiều lần cùng một kết quả.
- **Complex database queries** (Các truy vấn cơ sở dữ liệu phức tạp): Lưu lại kết quả của các câu lệnh SQL tốn nhiều thời gian và tài nguyên.

**So sánh Lợi ích và Thách thức:**

| Benefit (Lợi ích)                                  | Challenge (Thách thức)                                  |
| :------------------------------------------------- | :------------------------------------------------------ |
| **Reduce response latency** (Giảm độ trễ phản hồi) | Đòi hỏi thêm nỗ lực kỹ thuật để thiết kế hệ thống.      |
| **Alleviate origin load** (Giảm tải cho nguồn gốc) | Cần xác định loại dữ liệu nào nên cache.                |
| **Improve availability** (Cải thiện tính sẵn sàng) | Phải xử lý vấn đề **Stale data** (Dữ liệu cũ/lỗi thời). |

**Các dịch vụ AWS hỗ trợ Caching:**

- **Amazon CloudFront**: Chuyên về **Static caching** (Lưu trữ đệm tĩnh) tại các **Edge location** để người dùng truy cập từ nơi gần nhất.
- **Amazon ElastiCache**: Chuyên về **Database caching** (Lưu trữ đệm cơ sở dữ liệu) dựa trên công nghệ **In-memory** (Lưu trữ trên RAM) để đạt tốc độ cực nhanh.

### 📌 Ghi nhớ nhanh

- Caching giúp cân bằng giữa **Performance** (Hiệu năng), **Cost** (Chi phí) và **Data Freshness** (Độ tươi mới của dữ liệu).
- Nguyên tắc cốt lõi: Đưa dữ liệu đến gần người dùng nhất có thể và lưu trữ ở nơi truy xuất nhanh nhất.

### 💡 Ví dụ (nếu có)

```example
Phép ẩn dụ về việc sửa chữa nhà:
- Hardware Store (Cửa hàng kim khí): Cách nhà 30 phút di chuyển (Giống như Database/Origin).
- Tool Shed (Kho chứa đồ tại nhà): Cách 2 phút di chuyển (Giống như Cache).

Thay vì mỗi lần cần một chiếc đinh lại chạy ra cửa hàng (30p), bạn lấy một túi đinh về để sẵn trong kho tại nhà (2p). Việc sửa chữa sẽ nhanh hơn rất nhiều.
```

---

## 📘 Phần 2: Caching using CloudFront

### 🔑 Ý chính

- **Amazon CloudFront**: Là dịch vụ **Content Delivery Network (CDN)** (Mạng lưới phân phối nội dung) toàn cầu, giúp phân phối dữ liệu, video và ứng dụng một cách an toàn với **Low latency** (Độ trễ thấp) và tốc độ truyền tải cao. 🌐
- **Edge Locations** (Điểm biên): Các trạm hiện diện tại nhiều nơi trên thế giới để lưu trữ nội dung gần nhất với người dùng cuối. 📍
- **Security** (Bảo mật): Tự động tích hợp khả năng giảm thiểu tấn công **DDoS** (Từ chối dịch vụ phân tán) thông qua **AWS Shield** và **AWS WAF**. 🛡️

### 🧠 Giải thích

**1. Thành phần của CloudFront Global Edge Network:**

| Đặc điểm             | Edge Locations                           | Regional Edge Caches                                         |
| :------------------- | :--------------------------------------- | :----------------------------------------------------------- |
| **Số lượng**         | Rất nhiều, phân bổ dày đặc.              | Ít hơn, nằm giữa Edge Location và Origin.                    |
| **Vị trí**           | Gần sát người dùng cuối.                 | Xa người dùng hơn so với Edge Location.                      |
| **Kích thước Cache** | Nhỏ hơn.                                 | Lớn hơn.                                                     |
| **Vai trò**          | Phục vụ các nội dung phổ biến cực nhanh. | Lưu trữ các nội dung ít phổ biến hơn để giảm tải cho Origin. |

**2. Phân loại nội dung trong Caching:**

- **Static Content** (Nội dung tĩnh): Có thể lưu trữ đệm như hình ảnh, video, tệp web. ✅
- **Dynamic Content** (Nội dung động): Không thể lưu trữ đệm bằng Edge Cache (ví dụ: dữ liệu cá nhân hóa cho từng người dùng, kết quả tìm kiếm theo thời gian thực). ❌

**3. Cách kiểm soát thời gian lưu trữ (TTL):**

- **Time to Live (TTL)**: Khoảng thời gian nội dung tồn tại trong cache trước khi trở thành **Stale** (Dữ liệu cũ).
- **Cache-control headers**: Cung cấp quyền kiểm soát chi tiết cho từng tệp cụ thể.
- **Invalidation requests**: Buộc CloudFront xóa/làm mới nội dung khỏi cache trước khi hết hạn TTL.
- **Content versioning**: Sử dụng tên tệp khác nhau (ví dụ: image_v1.jpg, image_v2.jpg) để buộc cache cập nhật tệp mới ngay lập tức.

### 📌 Ghi nhớ nhanh

- **Origin** (Nguồn gốc): Nơi lưu trữ gốc của tệp (thường là **Amazon S3** hoặc **EC2**).
- **Distribution** (Phân phối): Cấu hình CloudFront để phân tán dữ liệu đến các Edge locations.
- **Stale data** (Dữ liệu lỗi thời): Xảy ra khi dữ liệu tại Origin đã cập nhật nhưng dữ liệu trong Cache vẫn là bản cũ do chưa hết TTL.

### 💡 Ví dụ (nếu có)

```example
Quy trình truyền phát Video (Video Streaming):
1. Nội dung gốc (Source content) được tải lên Amazon S3.
2. Amazon Elastic Transcoder chuyển đổi video sang nhiều định dạng.
3. Các tệp video định dạng này được lưu tại S3 Bucket (Origin).
4. Người dùng yêu cầu xem video qua CloudFront URL.
5. CloudFront lấy video từ S3 và phân phối bản sao đến Edge Location gần người dùng nhất.
6. Người dùng xem video với tốc độ cao, không bị giật lag nhờ dữ liệu được truyền từ điểm biên ngay cạnh họ.
```

---

## 📘 Phần 3: Caching using ElastiCache

### 🔑 Ý chính

- **Amazon ElastiCache**: Dịch vụ kho lưu trữ dữ liệu **In-memory** (trong bộ nhớ RAM) được quản lý hoàn toàn, cung cấp hiệu suất truy xuất với độ trễ cực thấp (dưới một mili giây). ⚡
- **Database Caching** (Bộ nhớ đệm cơ sở dữ liệu): Cơ chế lưu trữ các kết quả truy vấn cơ sở dữ liệu phổ biến hoặc phức tạp để giảm tải cho database gốc và tiết kiệm chi phí. 📉
- **Managed Engines**: Hỗ trợ hai công cụ mã nguồn mở phổ biến là **Redis** và **Memcached**. 🛠️

### 🧠 Giải thích

**1. So sánh hai công cụ (Engines):**

| Đặc điểm          | **ElastiCache for Memcached**    | **ElastiCache for Redis**                                                               |
| :---------------- | :------------------------------- | :-------------------------------------------------------------------------------------- |
| **Mục đích**      | Mô hình đơn giản, hiệu năng cao. | Tính năng mạnh mẽ, đa dạng.                                                             |
| **Kiểu dữ liệu**  | Đơn giản.                        | Phức tạp (Lists, Sets, Sorted Sets).                                                    |
| **Tính bền vững** | Không hỗ trợ lưu trữ lâu dài.    | Hỗ trợ **Persistence** (Lưu trữ bền vững).                                              |
| **Tính sẵn sàng** | Scale ngang với Auto Discovery.  | **High availability** với Multi-AZ và **Automatic failover** (Tự động chuyển vùng lỗi). |
| **Khác**          | Đa luồng (Multi-threaded).       | Hỗ trợ **Pub/Sub messaging**, Geospatial.                                               |

**2. Thành phần hệ thống:**

- **Node**: Thành phần nhỏ nhất (khối xây dựng) trong một triển khai ElastiCache. 🧱
- **Cluster** (Cụm): Một nhóm logic chứa một hoặc nhiều Node. 🧊
- **Time to Live (TTL)**: Thời gian (giây/mili giây) quy định dữ liệu tồn tại trong cache trước khi hết hạn. ⏳

**3. Chiến lược xử lý dữ liệu (Caching Strategies):**

- **Lazy Loading** (Nạp khi cần):
  - Chỉ cập nhật cache khi có yêu cầu truy vấn từ ứng dụng.
  - **Cache hit**: Có dữ liệu trong cache -> Trả về ngay cho người dùng.
  - **Cache miss**: Không có dữ liệu -> Truy cập database gốc -> Sao chép vào cache cho lần sau.
- **Write-Through** (Ghi đè đồng bộ):
  - Cập nhật cache ngay lập tức khi ứng dụng ghi dữ liệu mới vào database chính.
  - Đảm bảo dữ liệu trong cache luôn **Up-to-date** (mới nhất) với database.

### 📌 Ghi nhớ nhanh

- Sử dụng **ElastiCache** khi database gốc (như Amazon RDS) bị quá tải do lượng yêu cầu đọc (**Read requests**) quá lớn. 📖
- **Memcached** phù hợp cho các node lớn cần nhiều lõi xử lý (core/thread).
- **Redis** là lựa chọn tốt nhất nếu bạn cần xếp hạng (ranking), nhắn tin hoặc cần khả năng khôi phục dữ liệu.

### 💡 Ví dụ (nếu có)

```example
Quy trình xử lý khi có Cache Miss (Lazy Loading):
1. User yêu cầu thông tin: Ứng dụng gửi truy vấn (Data request) đến ElastiCache.
2. Không tìm thấy: Cache trả về "Cache miss".
3. Truy xuất gốc: Ứng dụng lấy dữ liệu từ Database (ví dụ RDS).
4. Phản hồi & Lưu trữ: Ứng dụng trả dữ liệu cho User và ĐỒNG THỜI ghi dữ liệu đó vào ElastiCache.
5. Lần sau: Khi User yêu cầu lại, dữ liệu sẽ được lấy trực tiếp từ ElastiCache (Cache Hit).
```
