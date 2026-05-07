# Module 11: Automating Your Architecture (Tự động hóa kiến trúc)

## 🧭 Overview

- Hiểu rõ lý do và thời điểm cần áp dụng **Architecture Automation** (tự động hóa kiến trúc).
- Tìm hiểu chiến lược **Infrastructure as Code - IaC** (cơ sở hạ tầng dưới dạng mã) để cung cấp và quản lý tài nguyên.
- Cách sử dụng các công cụ: **AWS CloudFormation**, **AWS Quick Starts**, và **Amazon Q Developer**.
- Áp dụng các nguyên tắc của **AWS Well-Architected Framework** vào chiến lược tự động hóa.

---

## 📘 Phần 1: Reasons to Automate (Lý do cần tự động hóa)

### 🔑 Ý chính

Việc xây dựng kiến trúc thủ công thông qua **AWS Management Console** (giao diện quản lý AWS) tiềm ẩn nhiều rủi ro và không đáp ứng được nhu cầu của một doanh nghiệp hiện đại, linh hoạt.

### 🧠 Giải thích

#### ⚠️ Rủi ro từ Manual Processes (Quá trình thủ công)

- **Error prone** (dễ xảy ra sai sót): Con người rất dễ mắc lỗi khi thực hiện các bước lặp đi lặp lại.
- **Inconsistent configurations** (cấu hình không nhất quán): Các môi trường (Development, Test, Production) có thể khác nhau do cấu hình tay không đồng bộ.
- **No version control** (không có quản lý phiên bản): Khó theo dõi ai đã thay đổi gì và không thể quay lại trạng thái cũ một cách dễ dàng.
- **Lacks audit trails** (thiếu nhật ký kiểm tra): Khó xác định lịch sử thay đổi của hệ thống.
- **Not scalable** (không thể mở rộng): Không thể lặp lại việc xây dựng kiến trúc ở quy mô lớn một cách nhanh chóng.

#### ✅ Lợi ích của Automation (Tự động hóa)

- **Reduce manual intervention** (giảm thiểu sự can thiệp thủ công): Hạn chế quyền truy cập trực tiếp và lỗi do con người.
- **Reproducible environments** (môi trường có thể tái lập): Đảm bảo các môi trường luôn giống hệt nhau.
- **Improve productivity** (cải thiện năng suất): Đội ngũ kỹ thuật tập trung vào phát triển thay vì thiết lập hạ tầng.
- **Rapid deployment** (triển khai nhanh chóng): Xây dựng và thay đổi kiến trúc chỉ trong vài phút.

### 📊 So sánh: Thủ công vs. Tự động hóa

| Đặc điểm             | Manual Process (Thủ công) | Automation (Tự động)   |
| :------------------- | :------------------------ | :--------------------- |
| **Tốc độ**           | Chậm, tốn thời gian       | Rất nhanh              |
| **Độ tin cậy**       | Thấp (dễ sai sót)         | Cao (nhất quán)        |
| **Khả năng lặp lại** | Khó khăn                  | Dễ dàng                |
| **Quản lý thay đổi** | Khó theo dõi              | Có **Version control** |

### 📌 Ghi nhớ nhanh

- Quá trình thủ công là **Unreliable** (không đáng tin cậy) và **Inadequate** (không đầy đủ) cho một doanh nghiệp ưu tiên sự linh hoạt (**Agile business**).
- Tự động hóa giúp loại bỏ các bước thừa, giúp hệ thống **Predictable** (có thể dự đoán được) và an toàn hơn.

### 💡 Ví dụ (nếu có)

```example
Thay vì phải đăng nhập vào Console để tạo từng VPC, Subnet, EC2 Instance và RDS Database (mất khoảng 30-60 phút và dễ nhầm IP),
bạn chỉ cần chạy một tệp tin script hoặc template để tự động dựng toàn bộ hệ thống này trong vài phút với độ chính xác 100%.
```

---

## 📘 Phần 2: Using infrastructure as code (Sử dụng cơ sở hạ tầng dưới dạng mã)

### 🔑 Ý chính

- **Infrastructure as Code - IaC** (Cơ sở hạ tầng dưới dạng mã) là quá trình viết các tệp tin cấu hình (template) để tự động cung cấp và quản lý tài nguyên đám mây. 🏗️
- Thay vì cấu hình thủ công, bạn định nghĩa mọi thứ bằng mã máy tính, giúp hệ thống có thể được sao chép, triển khai lại và thay đổi một cách nhất quán. 🔄
- **AWS CloudFormation** là dịch vụ cốt lõi của AWS để thực hiện chiến lược IaC. ☁️

### 🧠 Giải thích

#### ⚙️ Cách thức hoạt động của IaC

- **Human readable** (con người có thể đọc được): Các tệp mã thường dùng định dạng YAML hoặc JSON, rất dễ hiểu đối với các kỹ sư. 📝
- **Machine consumable** (máy tính có thể xử lý): AWS sẽ đọc các tệp này và tự động tạo ra các tài nguyên thực tế. 🤖
- **Template** (mẫu cấu hình): Một tệp văn bản chứa định nghĩa của tất cả tài nguyên AWS mà bạn muốn.
- **Stack** (ngăn xếp tài nguyên): Một tập hợp các tài nguyên AWS được tạo ra và quản lý như một đơn vị duy nhất từ một template.

#### 🌟 Lợi ích then chốt của IaC

- **Reusability** (khả năng tái sử dụng): Một template có thể dùng để tạo ra nhiều môi trường giống hệt nhau (Dev, Test, Prod).
- **Repeatability** (khả năng lặp lại): Đảm bảo việc triển khai lần thứ 100 luôn giống hệt lần đầu tiên.
- **Maintainability** (khả năng bảo trì): Thay đổi hạ tầng bằng cách sửa mã, giúp việc cập nhật trở nên an toàn và có kiểm soát.
- **Version Control** (quản lý phiên bản): Mã hạ tầng có thể được lưu trữ trên Git để theo dõi lịch sử thay đổi.

#### 🛠️ Các dịch vụ AWS sử dụng IaC

- **AWS CloudFormation**: Dịch vụ cơ bản nhất, dùng template để quản lý Stack.
- **AWS Cloud Development Kit - AWS CDK**: Sử dụng các ngôn ngữ lập trình quen thuộc (Python, TypeScript, Java...) để định nghĩa hạ tầng. 💻
- **AWS Serverless Application Model - AWS SAM**: Phiên bản rút gọn dành riêng cho việc xây dựng ứng dụng không máy chủ (**Serverless**).
- **AWS Amplify**: Framework để phát triển ứng dụng web và di động Full-stack nhanh chóng.
- **AWS Quick Starts**: Các kiến trúc mẫu chuẩn "vàng" được AWS xây dựng sẵn để triển khai nhanh các giải pháp phức tạp.

### 📊 So sánh: CloudFormation vs. AWS CDK

| Đặc điểm        | AWS CloudFormation             | AWS CDK                                     |
| :-------------- | :----------------------------- | :------------------------------------------ |
| **Ngôn ngữ**    | YAML hoặc JSON (Dạng khai báo) | Ngôn ngữ lập trình (Python, JS...)          |
| **Độ phức tạp** | Cao hơn khi viết các file dài  | Thấp hơn nhờ tính trừu tượng của mã         |
| **Đối tượng**   | Quản trị viên hệ thống         | Lập trình viên (Developer)                  |
| **Cơ chế**      | Là nền tảng thực thi chính     | Biên dịch mã ra CloudFormation rồi mới chạy |

### 📌 Ghi nhớ nhanh

- **IaC = Code -> Template -> Stack**. 🚀
- **CloudFormation** không tính thêm phí (bạn chỉ trả tiền cho các tài nguyên như EC2, RDS mà nó tạo ra). 💰
- **Consistency** (sự nhất quán) là giá trị lớn nhất mà IaC mang lại cho doanh nghiệp.

### 💡 Ví dụ (nếu có)

```example
Một công ty muốn mở rộng kinh doanh sang khu vực Châu Âu.
Thay vì cử nhân viên ngồi cấu hình lại hàng trăm tài nguyên trên Region mới,
họ chỉ cần lấy "Template" cũ, đổi tham số vùng địa lý và nhấn "Deploy".
Toàn bộ hạ tầng tại Châu Âu sẽ hiện lên giống hệt hạ tầng tại Mỹ chỉ sau vài phút.
```

---

## 📘 Phần 3: Customizing with CloudFormation (Tùy chỉnh với CloudFormation)

### 🔑 Ý chính

- **CloudFormation Template** (Bản mẫu CloudFormation) là một tệp văn bản ở định dạng **JSON** hoặc **YAML** dùng để mô tả tất cả các tài nguyên và thuộc tính bạn muốn triển khai. 📄
- Tài nguyên trong template được quản lý theo nhóm gọi là **Stack** (Ngăn xếp). Khi bạn cập nhật template, CloudFormation sẽ cập nhật Stack tương ứng. 🏗️
- Công cụ này hỗ trợ các tính năng kiểm soát nâng cao như **Change Sets** (Tập hợp thay đổi) và **Drift Detection** (Phát hiện sai lệch). 🔍

### 🧠 Giải thích

#### ⌨️ Cú pháp Template: YAML vs JSON

- **YAML**: Ưu tiên tính dễ đọc (**Readability**), ngắn gọn hơn và hỗ trợ ghi chú (**Comments**). 📝
- **JSON**: Phổ biến cho các hệ thống máy tính và API, nhưng khó đọc và viết thủ công hơn đối với con người. 🤖

#### 🧬 Anatomy of a Template (Cấu trúc của một bản mẫu)

Một template bao gồm nhiều phần, trong đó **Resources** là phần duy nhất bắt buộc:

- **AWSTemplateFormatVersion**: Phiên bản định dạng bản mẫu.
- **Description**: Mô tả ngắn gọn về chức năng của template.
- **Parameters**: Cho phép người dùng nhập giá trị khi tạo Stack (ví dụ: chọn loại Instance). 📥
- **Mappings**: Bảng tra cứu dữ liệu tĩnh (ví dụ: ánh xạ AMI ID cho từng Region).
- **Conditions**: Logic điều kiện để quyết định tài nguyên nào được tạo (ví dụ: chỉ tạo bản sao lưu trong môi trường Prod). ⚖️
- **Resources**: Định nghĩa chính xác các tài nguyên AWS (EC2, S3, RDS...). **[Bắt buộc]** 🛠️
- **Outputs**: Trả về các giá trị sau khi tạo Stack (ví dụ: IP của máy chủ). 📤

#### 🛠️ Các công cụ hỗ trợ quản lý

- **AWS CloudFormation Designer**: Giao diện đồ họa để kéo-thả và thiết kế kiến trúc trực quan, sau đó tự động sinh mã. 🎨
- **Change Sets** (Tập hợp thay đổi): Cho phép bạn xem trước (**Preview**) các tác động của bản cập nhật đối với tài nguyên hiện có trước khi thực hiện. ✅
- **Drift Detection** (Phát hiện sai lệch): Xác định xem có ai đó đã thay đổi tài nguyên thủ công bên ngoài CloudFormation hay không (Trạng thái **MODIFIED** hoặc **IN_SYNC**). 🚨
- **DeletionPolicy** (Chính sách xóa): Bảo vệ hoặc sao lưu tài nguyên quan trọng ngay cả khi Stack bị xóa. 🛡️

### 📊 So sánh: Parameters vs. Mappings vs. Conditions

| Thành phần     | Mục đích chính                      | Thời điểm xác định                |
| :------------- | :---------------------------------- | :-------------------------------- |
| **Parameters** | Nhập dữ liệu tùy biến từ người dùng | Khi bắt đầu tạo/cập nhật Stack    |
| **Mappings**   | Tra cứu giá trị cố định theo bảng   | Được khai báo sẵn trong mã        |
| **Conditions** | Điều khiển logic "Nếu - Thì"        | Dựa trên tham số đầu vào lúc chạy |

### 📌 Ghi nhớ nhanh

- **Resources** là phần duy nhất **không thể thiếu** trong một template. ❗
- **Drift Detection** giúp đảm bảo hạ tầng thực tế khớp với mã nguồn.
- Sử dụng **Conditions** giúp bạn dùng chung **một template** cho nhiều môi trường (Dev, Test, Prod) khác nhau. 🔄

### 💡 Ví dụ (nếu có)

```example
Giả sử bạn muốn dùng một template duy nhất cho cả môi trường "Development" và "Production":
- Sử dụng "Parameters" để người dùng chọn loại môi trường.
- Sử dụng "Conditions" để quy định: Nếu là "Production", hãy tạo 2 Availability Zones; nếu là "Development", chỉ tạo 1 Availability Zone để tiết kiệm chi phí.
```

---

## 📘 Phần 4: Using AWS Quick Starts (Sử dụng AWS Quick Starts)

### 🔑 Ý chính

- **AWS Quick Starts** là các giải pháp triển khai tiêu chuẩn vàng (**Gold-standard deployments**) được xây dựng bởi các kiến trúc sư giải pháp của AWS và các đối tác. 🥇
- Giúp thiết lập các kiến trúc phức tạp trên AWS chỉ trong thời gian ngắn (thường dưới 1 giờ) mà vẫn đảm bảo tuân thủ các **Best practices** (thực hành tốt nhất). ⏱️
- Mỗi Quick Start bao gồm hai thành phần chính: một **CloudFormation template** để tự động hóa việc triển khai và một **Deployment guide** (hướng dẫn triển khai) chi tiết. 📖

### 🧠 Giải thích

#### 🌟 Đặc điểm của AWS Quick Starts

- **Best practices**: Được thiết kế sẵn với các tiêu chuẩn cao nhất về **Security** (bảo mật) và **High availability** (độ khả dụng cao). 🛡️
- **Accelerate development** (tăng tốc phát triển): Thay vì tự viết template từ đầu, bạn có thể sử dụng Quick Starts làm nền tảng để tùy chỉnh theo nhu cầu riêng của doanh nghiệp. 🚀
- **Experimentation** (thử nghiệm): Rất hữu ích cho việc chạy thử các công nghệ hoặc phần mềm mới trong môi trường AWS mà không tốn nhiều công sức thiết lập. 🧪

#### 🛠️ Cách thức hoạt động

1. **Chọn giải pháp**: Tìm kiếm giải pháp phù hợp (ví dụ: SAP, hồ sơ dữ liệu, hoặc kiến trúc mạng).
2. **Đọc hướng dẫn**: Xem qua **Deployment guide** để hiểu các tham số và yêu cầu hệ thống.
3. **Triển khai**: Chạy **CloudFormation template** trực tiếp vào tài khoản AWS của bạn. CloudFormation sẽ tự động dựng lên toàn bộ **Stack** tài nguyên. 🏗️

### 📊 So sánh: Tự viết CloudFormation vs. Dùng Quick Starts

| Tiêu chí          | Tự viết Template                        | AWS Quick Starts                     |
| :---------------- | :-------------------------------------- | :----------------------------------- |
| **Công sức**      | Tốn nhiều thời gian thiết kế và viết mã | Triển khai ngay lập tức              |
| **Độ chuyên gia** | Đòi hỏi kiến thức sâu về từng dịch vụ   | Tận dụng kiến thức từ chuyên gia AWS |
| **Tính tùy biến** | Rất cao (kiểm soát 100%)                | Trung bình (dựa trên mẫu có sẵn)     |
| **Mục đích**      | Các yêu cầu đặc thù của doanh nghiệp    | Các kiến trúc phổ biến, chuẩn mực    |

### 📌 Ghi nhớ nhanh

- **Quick Starts = Template + Deployment Guide**. 📦
- Là cách nhanh nhất để học hỏi các **Patterns** (mô hình) thiết kế chuẩn trên AWS.
- Giúp giảm thiểu rủi ro sai sót trong cấu hình nhờ vào việc sử dụng các thiết lập đã được kiểm chứng. ✅

### 💡 Ví dụ (nếu có)

```example
Bạn cần triển khai một cụm cơ sở dữ liệu Microsoft SQL Server có tính sẵn sàng cao trên nhiều Availability Zones.
Thay vì tự ngồi cấu hình VPC, Subnets, Security Groups, Failover Cluster và cài đặt phần mềm (mất vài ngày),
bạn sử dụng "AWS Quick Start for Microsoft SQL Server" để tự động tạo ra toàn bộ môi trường chuẩn chỉ trong khoảng 45 phút.
```

---

## 📘 Phần 5: Customizing with Amazon Q Developer (Tùy chỉnh với Amazon Q Developer)

### 🔑 Ý chính

- **Amazon Q Developer** là một trợ lý lập trình sử dụng **Generative AI** (AI tạo sinh) giúp các nhà phát triển và chuyên gia IT xây dựng ứng dụng trên AWS nhanh chóng hơn. 🤖
- Công cụ này hỗ trợ xuyên suốt **Software Development Lifecycle - SDLC** (Vòng đời phát triển phần mềm) từ khâu lập kế hoạch đến vận hành và bảo trì. 🔄
- Tích hợp trực tiếp vào môi trường lập trình để cung cấp các gợi ý mã thời gian thực cho **CloudFormation templates**. ⚡

### 🧠 Giải thích

#### 🛠️ Giải quyết các thách thức khi viết IaC

- **Human error** (Lỗi do con người): Giảm thiểu các lỗi cú pháp hoặc cấu hình sai sót khi viết mã thủ công.
- **Differing skill levels** (Trình độ kỹ năng khác biệt): Hỗ trợ những người mới bắt đầu có thể tạo được các bản mẫu phức tạp thông qua gợi ý thông minh.
- **Size and complexity of templates** (Quy mô và độ phức tạp của bản mẫu): Giúp quản lý và tạo ra các tệp tài nguyên lớn một cách hiệu quả.
- **Security vulnerabilities** (Lỗ hổng bảo mật): Tự động quét mã để tìm các điểm yếu bảo mật và đưa ra gợi ý khắc phục. 🛡️

#### 🔄 Hỗ trợ trong các giai đoạn SDLC

- **Plan** (Lập kế hoạch): Trả lời các câu hỏi kỹ thuật và giải thích các đoạn mã phức tạp bằng ngôn ngữ tự nhiên.
- **Create** (Tạo): Cung cấp **In-line code recommendations** (gợi ý mã ngay trên dòng) dựa trên các bình luận hoặc mã đang viết dở. 💻
- **Test and secure** (Kiểm tra và bảo mật): Tạo các bản **Unit tests** (kiểm thử đơn vị) và quét lỗ hổng bảo mật trong dự án.
- **Operate** (Vận hành): Hỗ trợ xử lý lỗi (**Troubleshoot**) hệ thống và mạng (ví dụ: dùng VPC Reachability Analyzer).
- **Maintain and modernize** (Bảo trì và hiện đại hóa): Hỗ trợ nâng cấp và chuyển đổi mã nguồn cũ sang các phiên bản mới hơn.

### 📌 Ghi nhớ nhanh

- **Amazon Q Developer** được thiết kế **Secure and private by design** (Bảo mật và riêng tư ngay từ đầu).
- Giúp tăng tốc độ viết **CloudFormation** bằng cách gợi ý toàn bộ khối tài nguyên (như EC2, S3) chỉ từ vài dòng mô tả. 🚀
- Khả năng quét mã giúp đảm bảo hạ tầng tuân thủ các tiêu chuẩn an toàn trước khi triển khai thực tế. ✅

### 💡 Ví dụ (nếu có)

```example
Trong file YAML, bạn gõ dòng chú thích: "# EC2 Instance".
Amazon Q Developer sẽ hiển thị gợi ý (Suggestion) bao gồm đầy đủ cấu trúc:
- Type: AWS::EC2::Instance
- Properties:
    ImageId: XXXXXXXXX
    InstanceType: t2.micro
    KeyName: Ref KeyPair
Bạn chỉ cần nhấn "Tab" để chấp nhận và điền đoạn mã này vào template ngay lập tức.
```

---

## 📝 Knowledge Check

### ❓ Question 1

Which are reasons to use automation to provision resources? (Select TWO.)

A. Automation requirement for high availability

B. Alignment with the reliability design principle

C. Greater expense with manual processes

D. Automation requirement for creating some resources

E. Lack of version control with manual processes

✅ **Đáp án đúng:** B, E

### 🧠 Giải thích

- **Alignment with the reliability design principle** (**Tuân thủ nguyên tắc thiết kế độ tin cậy**): Tự động hóa là một phần cốt lõi của trụ cột **Reliability** trong **Well-Architected Framework**, giúp giảm thiểu sai sót và đảm bảo hệ thống hoạt động đúng như mong đợi.
- **Lack of version control with manual processes** (**Thiếu quản lý phiên bản với quy trình thủ công**): Đây là một rủi ro lớn khi làm thủ công. Tự động hóa (IaC) cho phép bạn lưu trữ kiến trúc dưới dạng mã và quản lý phiên bản qua Git.

### ❌ Vì sao đáp án khác sai?

- **A, D**: Tự động hóa giúp đạt được **High availability** và tạo tài nguyên dễ dàng hơn, nhưng nó không phải là "yêu cầu bắt buộc" (requirement) duy nhất để tạo tài nguyên.
- **C**: Mặc dù làm thủ công tốn thời gian (productivity loss), nhưng lý do kỹ thuật quan trọng nhất vẫn là tính nhất quán và quản lý phiên bản.

---

### ❓ Question 2

Which are benefits of using infrastructure as code (IaC) over manual processes? (Select TWO.)

A. Propagate updates from a single environment to all environments.

B. Automate system-wide security scans.

C. Deploy environments with configuration consistency.

D. Manage all account users.

E. Protect environments from deletion.

✅ **Đáp án đúng:** A, C

### 🧠 Giải thích

- **Propagate updates...** (**Lan truyền cập nhật từ một môi trường sang tất cả môi trường**): Khi bạn sửa mã trong template, bạn có thể áp dụng thay đổi đó cho tất cả các **Stacks** (ngăn xếp) một cách đồng bộ.
- **Configuration consistency** (**Sự nhất quán về cấu hình**): Đảm bảo môi trường Dev, Test và Prod luôn giống hệt nhau về mặt thông số, loại bỏ lỗi "chạy được trên máy tôi nhưng không chạy được trên server".

### ❌ Vì sao đáp án khác sai?

- **B**: Quét bảo mật hệ thống thường là nhiệm vụ của các dịch vụ như **Amazon Inspector** hoặc **AWS GuardDuty**, IaC chỉ hỗ trợ quét lỗi trong mã nguồn template.
- **D**: Việc quản lý người dùng là nhiệm vụ của **IAM** (**Identity and Access Management**).
- **E**: IaC không tự động bảo vệ tài nguyên khỏi việc bị xóa (cần dùng **DeletionPolicy** hoặc **IAM policy** cụ thể).

---

### ❓ Question 3

A cloud architect wants to quickly set up a secure implementation of an Amazon FSx for Windows File Server that follows AWS best practices. Which solution should they use?

A. AWS CloudFormation Designer

B. An AWS CloudFormation template that was downloaded from the internet

C. An Amazon Machine Image (AMI) on AWS Marketplace

D. An AWS Quick Start

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **AWS Quick Start**: Đây là giải pháp bao gồm các **CloudFormation template** và hướng dẫn triển khai được AWS xây dựng sẵn theo **Best practices** (thực hành tốt nhất) để giúp khách hàng dựng các kiến trúc phức tạp một cách nhanh chóng và an toàn.

### ❌ Vì sao đáp án khác sai?

- **A**: Designer chỉ là công cụ vẽ kiến trúc trực quan.
- **B**: Template từ Internet không đảm bảo tính bảo mật và tuân thủ các quy chuẩn của AWS.
- **C**: **AMI** chỉ là bản sao lưu hệ điều hành và phần mềm cho **EC2**, không phải là một giải pháp kiến trúc tổng thể cho **FSx**.

---

### ❓ Question 4

What is Amazon Q Developer?

A. An integrated development environment (IDE)

B. An artificial intelligence (AI)-powered coding companion

C. A set of automated reference architectures

D. A template for rapid application deployment

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Amazon Q Developer**: Là một trợ lý lập trình sử dụng **Generative AI** (AI tạo sinh) giúp lập trình viên viết mã, giải thích mã, kiểm tra bảo mật và tối ưu hóa tài nguyên AWS.

### ❌ Vì sao đáp án khác sai?

- **A**: Nó không phải là một **IDE** (như VS Code), mà là một công cụ tích hợp vào IDE.
- **C**: Đây là mô tả của **AWS Quick Starts**.
- **D**: Đây là mô tả của **CloudFormation Template**.

---

### ❓ Question 5

Which are reasons to use Amazon Q Developer? (Select TWO.)

A. Automate for high availability.

B. Write compliance tests.

C. Accelerate coding tasks.

D. Enhance application security.

E. Share open-source code.

✅ **Đáp án đúng:** C, D

### 🧠 Giải thích

- **Accelerate coding tasks** (**Tăng tốc tác vụ lập trình**): Nhờ khả năng gợi ý mã trực tiếp (**In-line suggestions**).
- **Enhance application security** (**Tăng cường bảo mật ứng dụng**): Khả năng quét mã nguồn để tìm lỗ hổng bảo mật và đề xuất cách sửa chữa.

### ❌ Vì sao đáp án khác sai?

- **A**: Việc tự động hóa cho tính sẵn sàng cao là do thiết kế kiến trúc (ví dụ: **Auto Scaling**, **Multi-AZ**).
- **B**: Viết kiểm thử tuân thủ thường dùng **AWS Config**.
- **E**: Amazon Q không phải là nền tảng chia sẻ mã nguồn.

---

### ❓ Question 6

What is AWS CloudFormation?

A. A description of best practices for designing an AWS implementation

B. A template that describes your infrastructure

C. An AWS service that you can use to create, model, and manage AWS resources

D. A package of all the information that is needed to launch an Amazon EC2 instance

✅ **Đáp án đúng:** C

### 🧠 Giải thích

- **AWS CloudFormation**: Là một dịch vụ giúp bạn mô hình hóa (**model**), khởi tạo (**create**) và quản lý một tập hợp tài nguyên AWS một cách có trật tự và dự đoán được.

### ❌ Vì sao đáp án khác sai?

- **A**: Đây là mô tả của **Well-Architected Framework**.
- **B**: Bản thân CloudFormation là một "dịch vụ", còn template chỉ là một phần cấu thành của nó.
- **D**: Đây là mô tả của **Amazon Machine Image (AMI)**.

💡 **Mẹo nhớ nhanh:** CloudFormation = Service. Template = File. Stack = Running Resources.

---

### ❓ Question 7

What is AWS CloudFormation Designer?

A. A graphical design interface for creating AWS CloudFormation templates

B. A collection of reusable templates

C. A tool for automating deployments

D. A source code repository for AWS CloudFormation templates

✅ **Đáp án đúng:** A

### 🧠 Giải thích

- **AWS CloudFormation Designer**: Là giao diện đồ họa giúp bạn kéo-thả các biểu tượng tài nguyên để thiết kế kiến trúc và tự động sinh ra mã template tương ứng (JSON/YAML).

### ❌ Vì sao đáp án khác sai?

- **B**: Đây là các **AWS Quick Starts**.
- **D**: Kho lưu trữ mã nguồn thường là **AWS CodeCommit** hoặc **GitHub**.

---

### ❓ Question 8

Which option can be used to accomplish deployment-specific differences in an AWS CloudFormation template?

A. Use drift detection.

B. Use change sets.

C. Use AWS CloudFormation Designer.

D. Use conditions.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Conditions** (**Điều kiện**): Dùng để xử lý logic "Nếu - Thì". Giúp bạn dùng một template duy nhất nhưng có thể tạo ra các kết quả khác nhau tùy theo đầu vào (ví dụ: Prod thì tạo máy chủ lớn, Dev thì tạo máy chủ nhỏ).

### ❌ Vì sao đáp án khác sai?

- **A**: **Drift detection** để kiểm tra sai lệch cấu hình.
- **B**: **Change sets** để xem trước thay đổi trước khi thực thi.

---

### ❓ Question 9

Which option is a good way to preview changes before implementing them in AWS CloudFormation Designer?

A. Run Detect Drift.

B. Create a change set.

C. Visually inspect the template.

D. Run Update Stack.

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Change set** (**Tập hợp thay đổi**): Cho phép bạn xem trước các tài nguyên nào sẽ bị thay đổi, xóa hoặc thêm mới trước khi bạn chính thức xác nhận việc cập nhật Stack.

### ❌ Vì sao đáp án khác sai?

- **A**: Chỉ để kiểm tra xem tài nguyên hiện tại có bị ai sửa thủ công hay không.
- **D**: Chạy trực tiếp sẽ thực hiện thay đổi luôn mà không cho xem trước.

---

### ❓ Question 10

Which option is a good way to know which resources in an application environment were manually modified if the environment was created by running an AWS CloudFormation stack?

A. Run a comparison in AWS CloudFormation Designer on the stack.

B. Run conditions on the stack.

C. Run a change set on the stack.

D. Run drift detection on the stack.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Drift detection** (**Phát hiện sai lệch**): Xác định xem các tài nguyên trong Stack có còn giữ nguyên cấu hình như lúc được tạo bởi CloudFormation hay đã bị ai đó sửa đổi thủ công (**Manually modified**) bên ngoài hệ thống.

### ❌ Vì sao đáp án khác sai?

- **A, B, C**: Đều không có chức năng so sánh trạng thái "thực tế" (Live) với trạng thái "đã định nghĩa" trong mã nguồn template.

💡 **Mẹo nhớ nhanh:** Manual change = **Drift**. Preview change = **Change set**. Logic change = **Conditions**.
