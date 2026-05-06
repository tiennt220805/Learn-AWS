# Securing User, Application, and Data Access

## 🧭 Overview

- Module này tập trung vào việc quản lý quyền truy cập bảo mật cho người dùng, ứng dụng và dữ liệu trên nền tảng AWS.
- Mục tiêu chính:
  - Sử dụng **IAM users**, **groups**, và **roles** để quản lý **permissions** (quyền hạn).
  - Hiểu về sự khác biệt và cách áp dụng giữa **RBAC** và **ABAC**.
  - Nắm vững cách thiết kế hệ thống phân quyền có khả năng mở rộng (**scalable**).

---

## 📘 Phần 1: Managing permissions (Quản lý quyền truy cập)

### 🔑 Ý chính

- **IAM Groups** (Nhóm IAM): Sử dụng để gán cùng một loại quyền hạn cho nhiều người dùng cùng lúc thay vì gán trực tiếp cho từng cá nhân.
- **RBAC - Role-Based Access Control** (Kiểm soát truy cập dựa trên vai trò): Cấp quyền dựa trên chức năng công việc (ví dụ: Developer, Admin).
- **ABAC - Attribute-Based Access Control** (Kiểm soát truy cập dựa trên thuộc tính): Chiến lược cấp quyền linh hoạt dựa trên các thuộc tính (được gọi là **Tags** trên AWS).

### 🧠 Giải thích

#### 👥 Quản lý thông qua IAM Groups

- 🛠 **Cơ chế**: Thay vì đính kèm **policy** (chính sách) cho từng người dùng, bạn đính kèm chúng vào một **group**. Tất cả thành viên trong nhóm sẽ thừa hưởng (**inherit**) các quyền đó.
- 🚫 **Lưu ý**: Nhóm IAM không thể lồng nhau (**cannot be nested**). Một người dùng có thể thuộc nhiều nhóm khác nhau.
- ⚖️ **Ưu tiên**: Nếu một **policy** được đính kèm trực tiếp cho người dùng (**user policy**) có những hạn chế gắt gao hơn so với **group policy**, nó sẽ được ưu tiên áp dụng.

#### 🔄 So sánh RBAC và ABAC

| Tiêu chí             | RBAC (Role-Based)                                            | ABAC (Attribute-Based)                                                                        |
| :------------------- | :----------------------------------------------------------- | :-------------------------------------------------------------------------------------------- |
| **Cơ sở cấp quyền**  | Dựa trên vai trò/vị trí công việc.                           | Dựa trên thuộc tính (**Tags**) của người dùng và tài nguyên.                                  |
| **Độ linh hoạt**     | Thấp hơn, cần cập nhật chính sách mỗi khi có tài nguyên mới. | Cao hơn, không cần cập nhật chính sách khi thêm người dùng/tài nguyên mới có cùng thuộc tính. |
| **Khả năng mở rộng** | Khó quản lý khi số lượng người dùng và tài nguyên tăng lớn.  | Rất tốt (**Highly scalable**), giảm bớt số lượng chính sách cần quản lý.                      |
| **Cấu hình**         | Phải liệt kê cụ thể từng tài nguyên trong chính sách.        | Dùng một chính sách chung cho tất cả dựa trên logic so khớp thuộc tính.                       |

#### 🏷 Tầm quan trọng của Tags trong ABAC

- **Tags** là các cặp **Key-Value** (Khóa - Giá trị) được gắn vào tài nguyên (ví dụ: `Project: Secret`, `Env: Dev`).
- **ABAC** sử dụng các thẻ này để đưa ra quyết định cho phép hoặc từ chối truy cập một cách tự động và thông minh.

### 📌 Ghi nhớ nhanh

- 💡 **IAM Group**: Gom nhóm người dùng để dễ quản lý, kế thừa quyền hạn theo chức danh.
- 💡 **RBAC**: Quản lý kiểu truyền thống, liệt kê tài nguyên cụ thể.
- 💡 **ABAC**: Xu hướng hiện đại, dùng **Tags** để phân quyền, cực kỳ hiệu quả cho hệ thống lớn.
- 💡 **Explicit Deny** (Từ chối rõ ràng): Luôn có hiệu lực cao nhất, ngay cả khi có một chính sách khác cho phép.

### 💡 Ví dụ (nếu có)

```example
Ngữ cảnh: Công ty có 3 dự án là A, B, C.

- Nếu dùng RBAC: Admin phải tạo 3 chính sách riêng biệt liệt kê các Resource ID của từng dự án và gán cho từng nhóm tương ứng.
- Nếu dùng ABAC: Admin chỉ cần tạo 1 chính sách duy nhất với điều kiện: "Cho phép người dùng truy cập tài nguyên nếu Tag 'Project' của người dùng khớp với Tag 'Project' của tài nguyên".
```

---

## 📘 Phần 2: Federating users (Liên kết định danh người dùng)

### 🔑 Ý chính

- **Identity Federation** (Liên kết định danh): Một hệ thống tin cậy giữa hai bên để xác thực người dùng và truyền tải thông tin cần thiết nhằm cấp quyền truy cập tài nguyên.
- **Identity Provider - IdP** (Nhà cung cấp định danh): Bên chịu trách nhiệm xác thực danh tính người dùng.
- **Service Provider - SP** (Nhà cung cấp dịch vụ): Bên chịu trách nhiệm kiểm soát quyền truy cập vào các tài nguyên của mình.
- **AWS Security Token Service - AWS STS** (Dịch vụ cung cấp Token bảo mật): Cung cấp thông tin xác thực tạm thời và có đặc quyền hạn chế.

### 🧠 Giải thích

#### 🤝 Các thành phần trong Identity Federation

- **Identity Provider - IdP** (Nhà cung cấp định danh): Ví dụ như Google, Facebook (OpenID Connect) hoặc Active Directory (SAML).
- **Service Provider - SP** (Nhà cung cấp dịch vụ): Trong ngữ cảnh này là các dịch vụ của AWS.
- **Identity Broker** (Môi giới định danh): Một thành phần trung gian giữa **IdP** và **SP**, thực hiện yêu cầu thông tin xác thực tạm thời từ **AWS STS** cho người dùng.

#### 🛠 Các dịch vụ AWS hỗ trợ Federation

- **AWS IAM Identity Center** (Trung tâm danh tính IAM): Tiền thân là AWS Single Sign-On, giúp quản lý tập trung quyền truy cập vào nhiều tài khoản AWS và ứng dụng đám mây.
- **Amazon Cognito**: Dịch vụ quản lý định danh dành cho các ứng dụng web và di động, hỗ trợ đăng nhập qua các bên thứ ba như Facebook, Amazon, Google hoặc qua **SAML**.
- **AWS STS**: API cho phép yêu cầu các thông tin xác thực (credentials) tạm thời có thời hạn từ vài phút đến vài giờ.

#### ⚖️ So sánh SAML và OIDC IdPs

| Loại IdP | Tên đầy đủ                         | Ví dụ phổ biến                                             |
| :------- | :--------------------------------- | :--------------------------------------------------------- |
| **SAML** | Security Assertion Markup Language | Microsoft Active Directory Federation Services, Shibboleth |
| **OIDC** | OpenID Connect                     | Login with Amazon, Facebook, Google                        |

### 📌 Ghi nhớ nhanh

- 🚀 **Federation** cho phép người dùng sử dụng danh tính có sẵn (như tài khoản công ty) để vào AWS mà không cần tạo **IAM user** mới.
- ⏱ **Temporary Credentials** (Thông tin xác thực tạm thời): Do **STS** cấp, sẽ tự động hết hạn và không còn tác dụng sau đó, giúp tăng cường bảo mật.
- 📱 **Amazon Cognito**: Giải pháp hàng đầu khi muốn tích hợp đăng nhập mạng xã hội vào ứng dụng tự phát triển.

### 💡 Ví dụ (nếu có)

```example
Ngữ cảnh: Một tập đoàn lớn sử dụng Microsoft Active Directory để quản lý nhân viên.

Thay vì tạo 10,000 IAM users trên AWS, họ thiết lập Federation:
1. Nhân viên đăng nhập vào cổng thông tin nội bộ của công ty (IdP).
2. Cổng thông tin gửi một SAML Assertion đến AWS.
3. AWS STS xác thực Assertion và trả về Temporary Credentials.
4. Nhân viên dùng thông tin đó để truy cập trực tiếp vào AWS Management Console.
```

---

## 📘 Phần 3: Managing access to multiple accounts (Quản lý quyền truy cập vào nhiều tài khoản)

### 🔑 Ý chính

- **Multi-account strategy** (Chiến lược nhiều tài khoản): Sử dụng nhiều tài khoản AWS để tách biệt môi trường (Dev, Test, Prod) hoặc các đơn vị kinh doanh.
- **AWS Organizations**: Dịch vụ quản lý tài khoản cho phép hợp nhất nhiều tài khoản AWS vào một tổ chức được quản lý tập trung.
- **Organizational Unit - OU** (Đơn vị tổ chức): Nhóm các tài khoản lại với nhau để quản lý theo cấu trúc phân cấp.
- **Service Control Policy - SCP** (Chính sách kiểm soát dịch vụ): Thiết lập các rào cản (**guardrails**) kiểm soát quyền hạn tối đa cho tất cả tài khoản trong một **OU** hoặc toàn bộ tổ chức.
- **Permissions Boundary** (Ranh giới quyền hạn): Giới hạn quyền tối đa mà một chính sách dựa trên danh tính (**identity-based policy**) có thể cấp cho một thực thể IAM.

### 🧠 Giải thích

#### 🏗️ Mô hình quản lý tài nguyên

- **Multiple VPCs in a single account**: Nhiều mạng ảo trong một tài khoản duy nhất.
- **Multiple accounts, a VPC in each account**: Mỗi môi trường/dự án có một tài khoản riêng. Đây là mô hình được khuyến nghị vì tăng tính **Isolation** (Cách ly) và bảo mật.

#### 🏢 Cấu trúc AWS Organizations

1. **Root**: Gốc cao nhất của tổ chức.
2. **OU**: Các nhóm tài khoản (ví dụ: OU Engineering, OU Production).
3. **Member Accounts**: Các tài khoản AWS riêng lẻ nằm trong các OU.

#### ⚖️ So sánh SCP và Permissions Boundary

| Tiêu chí              | Service Control Policy (SCP)                                            | Permissions Boundary                                                  |
| :-------------------- | :---------------------------------------------------------------------- | :-------------------------------------------------------------------- |
| **Đối tượng áp dụng** | Toàn bộ tài khoản hoặc **OU**.                                          | Một **IAM user** hoặc **IAM role** cụ thể.                            |
| **Mục đích chính**    | Thiết lập **Guardrails** (Rào cản) cấp tổ chức.                         | Giới hạn quyền tối đa cho một thực thể đơn lẻ.                        |
| **Quyền hạn**         | Không tự cấp quyền, chỉ giới hạn quyền tối đa.                          | Không tự cấp quyền, chỉ giới hạn quyền tối đa.                        |
| **Khả năng ghi đè**   | Quản trị viên địa phương của tài khoản thành viên **không thể** ghi đè. | Được sử dụng để ngăn chặn việc người dùng tự cấp thêm quyền cho mình. |

#### 🔐 Effective Permissions (Quyền hạn thực tế)

Quyền hạn thực tế của một người dùng là **phần giao nhau** (intersection) giữa:

- **SCP** (từ Organizations)
- **Permissions Boundary** (nếu có)
- **Identity-based Policy** (IAM Policy gắn vào user/role)

> **Nguyên tắc**: Một hành động chỉ được phép nếu nó được **Allow** ở tất cả các cấp và không bị **Deny** ở bất kỳ cấp nào.

### 📌 Ghi nhớ nhanh

- 💡 **Consolidated Billing** (Hợp nhất hóa đơn): Giúp tiết kiệm chi phí nhờ gom chung mức sử dụng để hưởng chiết khấu theo số lượng.
- 💡 **Guardrails**: **SCP** không cấp quyền, nó chỉ định nghĩa những gì tài khoản **KHÔNG ĐƯỢC PHÉP** làm.
- 💡 **AWS Control Tower**: Dịch vụ giúp tự động thiết lập và quản trị một môi trường đa tài khoản an toàn theo các tiêu chuẩn tốt nhất (**best practices**).

### 💡 Ví dụ (nếu có)

```example
Ngữ cảnh: Bạn muốn đảm bảo không ai trong OU "Sandbox" có thể xóa Log của CloudTrail.

Giải pháp:
1. Tạo một SCP với hiệu lực "Deny" cho hành động "cloudtrail:StopLogging" và "cloudtrail:DeleteTrail".
2. Gán SCP này vào OU "Sandbox".
Kết quả: Ngay cả khi một User trong tài khoản con có quyền "AdministratorAccess", họ vẫn không thể xóa Log vì SCP đã chặn ở cấp cao hơn.
```

---

## 📘 Phần 4: Encrypting data at rest (Mã hóa dữ liệu lưu trữ)

### 🔑 Ý chính

- **Data at rest** (Dữ liệu lưu trữ): Dữ liệu được lưu giữ ổn định trên các thiết bị lưu trữ (ổ đĩa, băng từ, v.v.).
- **Encryption** (Mã hóa): Quá trình chuyển đổi **Plaintext** (dữ liệu thô) thành **Ciphertext** (dữ liệu đã mã hóa) bằng một thuật toán và một khóa bảo mật.
- **Symmetric encryption** (Mã hóa đối xứng): Sử dụng cùng một khóa để mã hóa và giải mã.
- **Asymmetric encryption** (Mã hóa bất đối xứng): Sử dụng một cặp khóa gồm **Public key** (khóa công khai) để mã hóa và **Private key** (khóa riêng tư) để giải mã.
- **Envelope encryption** (Mã hóa phong bì): Phương pháp mã hóa dữ liệu bằng một **Data key** (khóa dữ liệu), sau đó mã hóa chính khóa dữ liệu đó bằng một khóa khác cao cấp hơn.
- **AWS Key Management Service - AWS KMS** (Dịch vụ quản lý khóa của AWS): Dịch vụ quản lý các khóa mã hóa một cách tập trung và an toàn.

### 🧠 Giải thích

#### 🛡️ Tại sao cần mã hóa dữ liệu lưu trữ?

- Đảm bảo tính **Confidentiality** (Bảo mật) và **Integrity** (Toàn vẹn) của thông tin.
- Cung cấp thêm một lớp bảo vệ trong trường hợp hệ thống lưu trữ vật lý bị xâm nhập.

#### ⚖️ So sánh các loại mã hóa

| Đặc điểm     | Symmetric (Đối xứng)                        | Asymmetric (Bất đối xứng)                                                   |
| :----------- | :------------------------------------------ | :-------------------------------------------------------------------------- |
| **Khóa**     | Chỉ dùng 1 khóa duy nhất.                   | Dùng 1 cặp khóa (Public/Private).                                           |
| **Tốc độ**   | Nhanh, tốn ít tài nguyên.                   | Chậm hơn.                                                                   |
| **Ứng dụng** | Mã hóa khối lượng dữ liệu lớn, dùng nội bộ. | Chia sẻ dữ liệu ra ngoài, yêu cầu tính chống chối bỏ (**Non-repudiation**). |

#### 📦 Client-side vs Server-side Encryption

- **Client-side encryption - CSE** (Mã hóa phía máy khách): Ứng dụng tự mã hóa dữ liệu **trước khi** gửi lên AWS. Người dùng nắm toàn quyền kiểm soát khóa và thuật toán.
- **Server-side encryption - SSE** (Mã hóa phía máy chủ): AWS thay mặt bạn mã hóa dữ liệu **sau khi** nhận được. Quá trình này diễn ra minh bạch (**transparently**) khi ghi vào đĩa.

#### 🗝️ AWS Key Management Service (AWS KMS)

- Sử dụng các thiết bị phần cứng chuyên dụng **Hardware Security Modules - HSMs** để bảo vệ khóa.
- Tích hợp sâu với hầu hết các dịch vụ AWS khác (S3, EBS, RDS, v.v.).
- Các dịch vụ tích hợp với KMS thường chỉ hỗ trợ **Symmetric KMS keys** để mã hóa dữ liệu.

### 📌 Ghi nhớ nhanh

- 💡 **Envelope encryption** giống như việc bỏ viên kim cương (dữ liệu) vào hộp, khóa lại bằng chìa khóa (data key), rồi bỏ chìa khóa đó vào một chiếc két sắt (master key).
- 💡 **Symmetric** = Nhanh + Dùng 1 khóa.
- 💡 **Asymmetric** = Bảo mật cao + Dùng 2 khóa.
- 💡 **SSE** (Server-side) là cách dễ nhất vì AWS lo toàn bộ quá trình mã hóa/giải mã tự động.

### 💡 Ví dụ (nếu có)

```example
Ngữ cảnh: Mã hóa một ổ đĩa Amazon EBS volume.

1. Bạn tạo một KMS key trong AWS Console.
2. Khi tạo EBS volume, bạn chọn "Enable encryption" và chọn khóa vừa tạo.
3. Khi EC2 ghi dữ liệu vào ổ đĩa: Dữ liệu được mã hóa tự động trước khi lưu xuống phần cứng.
4. Khi EC2 đọc dữ liệu: Dữ liệu được giải mã tự động trong bộ nhớ.
-> Toàn bộ quá trình diễn ra mà không làm thay đổi cách ứng dụng của bạn hoạt động.
```

---

## 📘 Phần 5: AWS security services for securing user, application, and data access (Các dịch vụ bảo mật của AWS cho người dùng, ứng dụng và dữ liệu)

### 🔑 Ý chính

- **Defense in depth** (Phòng thủ đa lớp): Chiến lược sử dụng nhiều lớp bảo mật để bảo vệ hệ thống.
- **Identity and access management** (Quản lý danh tính và truy cập): Quản lý quyền hạn quy mô lớn (**IAM**, **IAM Identity Center**, **Cognito**, **Organizations**).
- **Detection and response** (Phát hiện và phản ứng): Giám sát và xử lý sự cố (**CloudTrail**, **Inspector**, **Detective**, **Security Hub**).
- **Network and application protection** (Bảo vệ mạng và ứng dụng): Kiểm soát lưu lượng truy cập (**WAF**, **Shield**, **Network Firewall**).
- **Data protection** (Bảo vệ dữ liệu): Bảo mật dữ liệu và tài khoản (**KMS**, **Macie**, **Secrets Manager**).

### 🧠 Giải thích

#### 🛠️ Các dịch vụ bảo mật trọng tâm

| Dịch vụ                 | Chức năng chính                                                                                              |
| :---------------------- | :----------------------------------------------------------------------------------------------------------- |
| **AWS WAF**             | **Web Application Firewall**: Giám sát yêu cầu HTTP/HTTPS, chặn các cuộc tấn công web phổ biến.              |
| **Amazon Macie**        | Sử dụng **Machine Learning** để phát hiện dữ liệu nhạy cảm (như thông tin cá nhân) trong **Amazon S3**.      |
| **Amazon Inspector**    | Tự động quét các lỗ hổng phần mềm và phơi nhiễm mạng trên **EC2**, **Lambda**, và **Container images**.      |
| **Amazon Detective**    | Phân tích và điều tra nguyên nhân gốc rễ (**root cause**) của các phát hiện bảo mật hoặc hoạt động nghi vấn. |
| **AWS Security Hub**    | Trung tâm quản trị, thu thập dữ liệu bảo mật từ nhiều tài khoản và dịch vụ để đưa ra cái nhìn tổng thể.      |
| **AWS Trusted Advisor** | Kiểm tra tài khoản và đưa ra các khuyến nghị tối ưu theo 5 danh mục (trong đó có bảo mật).                   |

#### 🛡️ Mô hình phòng thủ "Defense in Depth"

- **Defend your borders** (Bảo vệ biên giới): Sử dụng **AWS WAF** và **AWS Shield** để ngăn chặn tấn công từ internet.
- **Protect your data** (Bảo vệ dữ liệu): Dùng **Amazon Macie** để phân loại và bảo vệ dữ liệu quan trọng.
- **Detect and respond** (Phát hiện và phản hồi): Kết hợp **Inspector**, **Detective** và **Security Hub** để theo dõi và xử lý các mối đe dọa bên trong.

### 📌 Ghi nhớ nhanh

- 💡 **WAF**: Chặn request xấu (IP, Header) trước khi vào ứng dụng.
- 💡 **Macie**: Tìm "kim" (dữ liệu nhạy cảm) trong "đống cỏ" (**S3**).
- 💡 **Inspector**: Tìm lỗi bảo mật trong phần mềm/hệ điều hành đã cài đặt.
- 💡 **Detective**: "Thám tử" điều tra lịch sử và mối quan hệ giữa các sự kiện bảo mật.
- 💡 **Security Hub**: Bảng điều khiển (**Dashboard**) tập trung tất cả cảnh báo bảo mật.

### 💡 Ví dụ (nếu có)

```example
Ngữ cảnh: Một ứng dụng thương mại điện tử cần bảo mật toàn diện.

1. AWS WAF: Chặn các bot cố gắng tạo tài khoản ảo hàng loạt trên trang đăng ký.
2. Amazon Inspector: Định kỳ quét các máy chủ EC2 để tìm các bản vá phần mềm bị thiếu.
3. Amazon Macie: Quét các S3 bucket chứa ảnh CMND/CCCD của khách hàng để đảm bảo chúng không bị công khai.
4. AWS Security Hub: Quản trị viên chỉ cần vào một nơi duy nhất để xem các cảnh báo từ tất cả các dịch vụ trên.
```

---

## 📝 Knowledge Check

### ❓ Question 1

A company has two separate AWS accounts for testing workloads: one for performance testing and the other for integration testing. The accounts are grouped into an AWS Organizations organizational unit (**đơn vị tổ chức**), and each account has a Tester role defined.

The company wants to enforce the following security rules on users in the Tester role (testers) in both accounts:

- Testers can only access the Amazon EC2 and Amazon RDS services.
- Testers can only start and stop EC2 instances (**thể hiện máy chủ ảo**).
- Testers have read and write permissions to RDS databases (**cơ sở dữ liệu quan hệ**).

Which tasks does a system administrator need to perform to implement these requirements? (Select TWO).

A. Create a service control policy (SCP) to deny all actions on all AWS services except for the Amazon EC2 and Amazon RDS services, and attach it to the Tester role in both accounts.

B. Create an AWS Identity and Access Management (IAM) policy with the required EC2 and RDS permissions, and attach it to the organizational unit.

C. Create a service control policy (SCP) to deny all actions on all AWS services except for the Amazon EC2 and Amazon RDS services, and attach it to the organizational unit (**OU**).

D. Create an AWS Identity and Access Management (IAM) policy in both accounts with the required EC2 and RDS permissions, and attach it to the Tester role (**vai trò IAM**).

E. Create a service control policy (SCP) in both accounts with the required EC2 and RDS permissions, and attach it to the Tester role.

✅ **Đáp án đúng:** C và D

### 🧠 Giải thích

- **C đúng:** **Service Control Policy (SCP)** được sử dụng để thiết lập **Guardrails** (rào cản) tối đa ở cấp độ tài khoản hoặc **Organizational Unit (OU)**. Việc gắn SCP vào OU giúp đảm bảo rằng không ai trong các tài khoản thành viên có thể vượt qua các giới hạn dịch vụ đã định (chỉ cho phép EC2 và RDS).
- **D đúng:** **IAM Policy** là nơi định nghĩa các quyền cụ thể (Permissions). Để Testers có thể thực hiện các hành động như start/stop EC2 hoặc read/write RDS, bạn cần một IAM Policy chi tiết gắn trực tiếp vào **IAM Role** mà họ sử dụng.

### ❌ Vì sao đáp án khác sai?

- **A & E:** **SCP** không thể gắn trực tiếp vào các thực thể IAM như **User** hay **Role**. SCP chỉ có thể gắn vào **Root**, **OU**, hoặc **Account**.
- **B:** **IAM Policy** không thể gắn trực tiếp vào **Organizational Unit (OU)**. IAM Policy chỉ áp dụng cho các thực thể bên trong một tài khoản cụ thể (User, Group, Role).

💡 **Mẹo nhớ nhanh:** - **SCP** = Chốt chặn vòng ngoài (gắn vào OU/Account).

- **IAM Policy** = Cấp quyền vòng trong (gắn vào User/Role).
- Quyền thực tế là phần **Giao nhau** (Intersection) giữa cả hai.

---

## 📝 Knowledge Check

### ❓ Question 1

Which are characteristics of an AWS Identity and Access Management (IAM) group? (Select TWO.)

A. A user can belong to more than one group.

B. A group can have security credentials.

C. New users added to a group inherit the group’s permissions.

D. A group can belong to another group.

E. Permissions in a group policy always override permissions in a user policy.

✅ **Đáp án đúng:** A và C [cite: 1388, 1392]

### 🧠 Giải thích

- **A đúng:** Một người dùng có thể là thành viên của nhiều nhóm **IAM** khác nhau để đảm nhận các chức năng công việc khác nhau. [cite: 1392]
- **C đúng:** Khi một người dùng mới được thêm vào một nhóm, họ sẽ ngay lập tức được thừa hưởng (**inherit**) tất cả các quyền hạn được quy định trong các chính sách gắn liền với nhóm đó. [cite: 1388]

### ❌ Vì sao đáp án khác sai?

- **B:** Nhóm không phải là một danh tính thực sự có thể đăng nhập; chỉ người dùng mới có thông tin xác thực (**security credentials**). [cite: 1370]
- **D:** Các nhóm **IAM** không thể lồng vào nhau (**cannot be nested**). [cite: 1392]
- **E:** Ngược lại, các chính sách được gắn trực tiếp cho người dùng (**user policy**) sẽ ghi đè lên quyền của nhóm nếu chúng mang tính hạn chế hơn. [cite: 1393]

💡 **Mẹo nhớ nhanh:** **IAM Group** không phải là "người", nó là cái "thùng" chứa người để chia sẻ quyền hạn. [cite: 1359, 1370]

---

### ❓ Question 2

What is an advantage of using attribute-based access control (ABAC) over role-based access control (RBAC)? [cite: 1478]

A. ABAC will likely require fewer policies than RBAC.

B. ABAC requires less testing than RBAC.

C. ABAC permissions explicitly identify the resources that they protect.

D. ABAC permissions are more secure than RBAC permissions.

✅ **Đáp án đúng:** A [cite: 1479, 1531]

### 🧠 Giải thích

- **ABAC** cho phép bạn gom nhiều quyền hạn vào một chính sách duy nhất dựa trên các thuộc tính (**tags**), giúp giảm đáng kể số lượng chính sách cần quản lý so với **RBAC** (nơi thường phải tạo chính sách riêng cho từng vai trò hoặc tài nguyên). [cite: 1531]

### ❌ Vì sao đáp án khác sai?

- **B:** Cả hai mô hình đều cần được kiểm tra kỹ lưỡng để đảm bảo tính bảo mật.
- **C:** **RBAC** mới là mô hình liệt kê cụ thể các tài nguyên; **ABAC** xác định tài nguyên thông qua các thuộc tính. [cite: 1451, 1473]
- **D:** Cả hai đều an toàn nếu được cấu hình đúng, nhưng **ABAC** có lợi thế hơn về khả năng mở rộng (**scalability**). [cite: 1483]

💡 **Mẹo nhớ nhanh:** **ABAC** = Dùng **Tags** (thuộc tính) để phân quyền linh hoạt và gọn nhẹ hơn. [cite: 1475, 1479]

---

### ❓ Question 3

A developer is a member of an AWS Identity and Access Management (IAM) group that has a group policy attached to it. The group policy allows access to Amazon S3 and Amazon EC2 and denies access to Amazon Elastic Container Service (Amazon ECS). The developer also has a user policy attached which allows access to Amazon ECS and Amazon CloudFront. Which option describes the user’s access? [cite: 1422, 1445]

A. Access to Amazon S3, Amazon EC2, Amazon ECS, and Amazon CloudFront

B. Access to Amazon S3, Amazon EC2, and Amazon CloudFront, but no access to Amazon ECS

C. Access to Amazon ECS and Amazon CloudFront, but no access to Amazon S3 and Amazon EC2

D. Access to Amazon S3 and Amazon EC2, but no access to Amazon ECS and Amazon CloudFront

✅ **Đáp án đúng:** B [cite: 1422, 1445]

### 🧠 Giải thích

- Trong **AWS**, một lệnh từ chối rõ ràng (**explicit deny**) luôn ghi đè lên lệnh cho phép (**allow**). [cite: 1445] Vì nhóm đã từ chối truy cập **ECS**, quyền cho phép **ECS** trong chính sách người dùng sẽ bị vô hiệu hóa. [cite: 1445] Các quyền **S3**, **EC2** (từ nhóm) và **CloudFront** (từ người dùng) không bị xung đột nên được cho phép. [cite: 1431]

### ❌ Vì sao đáp án khác sai?

- **A:** Sai vì lệnh **Deny** đối với **ECS** có quyền ưu tiên cao nhất. [cite: 1445]
- **C & D:** Sai vì người dùng vẫn được cấp các quyền khác từ cả hai chính sách nếu chúng không bị từ chối. [cite: 1431]

💡 **Mẹo nhớ nhanh:** **Explicit Deny** là "trùm cuối", nó sẽ chặn mọi quyền cho phép khác đối với cùng một hành động/tài nguyên. [cite: 1445, 2128]

---

### ❓ Question 4

What is a benefit of identity federation with the AWS Cloud? [cite: 1540, 1784]

A. It centralizes the storage and management of user identities inside of the AWS Cloud.

B. It eliminates the need for defining permissions in AWS Identity and Access Management (IAM) to secure the access to AWS resources.

C. It enables the use of an external identity provider to authenticate workforce users and give them access to AWS resources.

D. It assigns roles to authenticated users to control their access to AWS resources.

✅ **Đáp án đúng:** C [cite: 1542, 1787]

### 🧠 Giải thích

- **Liên kết định danh (Identity federation)** cho phép bạn sử dụng các hệ thống quản lý người dùng hiện có (như Google, Facebook hoặc Active Directory của công ty) để xác thực người dùng mà không cần tạo lại danh tính đó trong **AWS**. [cite: 1540, 1575]

### ❌ Vì sao đáp án khác sai?

- **A:** Federation làm ngược lại, nó giữ danh tính ở **bên ngoài** AWS. [cite: 1542]
- **B:** Bạn vẫn phải định nghĩa quyền hạn trong **IAM** thông qua các **Roles** để kiểm soát những gì người dùng liên kết có thể làm. [cite: 1786]
- **D:** Mặc dù Federation có gán vai trò, nhưng lợi ích chính (**benefit**) được nhắc đến thường là việc tận dụng các nhà cung cấp định danh bên ngoài (**external IdP**). [cite: 1542]

💡 **Mẹo nhớ nhanh:** **Federation** = Dùng chìa khóa nhà (tài khoản bên ngoài) để mở cửa văn phòng (AWS). [cite: 1787]

---

### ❓ Question 5

Which service enables identity federation for accessing a web application running in the AWS Cloud? [cite: 1714, 1788]

A. AWS Key Management Service (AWS KMS)

B. Amazon Cognito

C. AWS WAF

D. AWS CloudHSM

✅ **Đáp án đúng:** B [cite: 1717, 1788]

### 🧠 Giải thích

- **Amazon Cognito** là dịch vụ quản lý danh tính dành riêng cho các ứng dụng web và di động, hỗ trợ xác thực người dùng qua các nhà cung cấp mạng xã hội (Google, Facebook) hoặc qua các chuẩn công nghiệp như **SAML**. [cite: 1718, 1788]

### ❌ Vì sao đáp án khác sai?

- **A:** **KMS** dùng để quản lý khóa mã hóa. [cite: 2257]
- **C:** **WAF** là tường lửa ứng dụng web để chặn các cuộc tấn công mạng. [cite: 2401]
- **D:** **CloudHSM** là thiết bị lưu trữ khóa bảo mật phần cứng chuyên dụng. [cite: 2258]

💡 **Mẹo nhớ nhanh:** Nhắc đến đăng nhập/xác thực cho **App di động/Web** -> Nghĩ ngay đến **Cognito**. [cite: 1717]

---

### ❓ Question 6

Which service helps centrally manage billing, control access, compliance and security, and share resources across multiple AWS accounts? [cite: 1864, 2125]

A. Amazon Cognito

B. AWS Systems Manager

C. AWS Organizations

D. AWS Identity and Access Management (IAM)

✅ **Đáp án đúng:** C [cite: 1867, 2125]

### 🧠 Giải thích

- **AWS Organizations** cho phép bạn hợp nhất nhiều tài khoản **AWS** vào một tổ chức duy nhất để quản lý tập trung việc thanh toán (**consolidated billing**), kiểm soát chính sách và bảo mật trên quy mô lớn. [cite: 1867, 1869]

### ❌ Vì sao đáp án khác sai?

- **A:** Chỉ dùng cho định danh ứng dụng. [cite: 1717]
- **B:** Dùng cho việc vận hành và cấu hình hệ thống.
- **D:** **IAM** chủ yếu dùng để quản lý quyền hạn trong nội bộ **một** tài khoản duy nhất. [cite: 1559]

💡 **Mẹo nhớ nhanh:** Quản lý **Nhiều tài khoản (Multiple accounts)** + **Hợp nhất hóa đơn (Billing)** -> **AWS Organizations**. [cite: 1867, 1869]

---

### ❓ Question 7

A technology company has multiple production accounts grouped into a production organizational unit (OU) in AWS Organizations. The company wants to prevent all AWS Identity and Access Management (IAM) users in the production accounts from deleting AWS CloudTrail logs. How can a system administrator enforce this restriction? [cite: 1952, 1965]

A. Create an IAM policy and attach it to each IAM user in the production accounts.

B. Create a service control policy (SCP), and attach it to the production OU.

C. Create a tag policy and attach it to the production accounts.

D. Create an Amazon S3 bucket policy and associate with all buckets containing AWS CloudTrail logs.

✅ **Đáp án đúng:** B [cite: 1953, 1988]

### 🧠 Giải thích

- **Service Control Policy (SCP)** được sử dụng để thiết lập các rào cản bảo mật (**guardrails**) ở cấp độ tài khoản hoặc **OU**. [cite: 1956] Một khi **SCP** từ chối hành động xóa log, quản trị viên địa phương của tài khoản đó cũng không thể ghi đè được. [cite: 1958]

### ❌ Vì sao đáp án khác sai?

- **A:** Rất khó quản lý và dễ sai sót khi phải gắn chính sách cho từng người dùng ở nhiều tài khoản khác nhau. [cite: 1960]
- **C:** **Tag policy** chỉ dùng để chuẩn hóa việc gắn thẻ tài nguyên, không dùng để chặn quyền xóa dịch vụ.
- **D:** **Bucket policy** chỉ bảo vệ ở cấp độ lưu trữ, không ngăn chặn được các hành động can thiệp trực tiếp từ dịch vụ **CloudTrail**.

💡 **Mẹo nhớ nhanh:** Để đặt "luật tối cao" cho cả một nhóm tài khoản (**OU**) -> Dùng **SCP**. [cite: 1953, 1958]

---

### ❓ Question 8

A developer is writing a client application that encrypts sensitive data using a data key before sending it to a server application. The client application sends the data key to the server application so that the server application can decrypt the sensitive information. The developer is concerned that the confidentiality of the sensitive data might be compromised if the data key is stolen. Which type of encryption should the developer use to fully protect the sensitive information? [cite: 2199, 2345]

A. Envelope encryption

B. Server-side encryption

C. Asymmetric encryption

D. Symmetric encryption

✅ **Đáp án đúng:** A [cite: 2211, 2345]

### 🧠 Giải thích

- **Mã hóa phong bì (Envelope encryption)** bảo vệ chính cái khóa đang dùng để mã hóa dữ liệu (**data key**) bằng cách mã hóa nó dưới một khóa cấp cao hơn (**key-encryption key**). [cite: 2211, 2345] Điều này đảm bảo rằng dù kẻ trộm có lấy được gói dữ liệu, chúng cũng không thể sử dụng khóa dữ liệu vì nó đã được mã hóa. [cite: 2214, 2345]

### ❌ Vì sao đáp án khác sai?

- **B:** **Server-side encryption** là phương pháp mã hóa tại đích đến (AWS), không giải quyết được vấn đề rò rỉ khóa trong quá trình ứng dụng truyền tin. [cite: 2347]
- **C & D:** Đây chỉ là các thuật toán mã hóa cơ bản; nếu khóa (**key**) bị đánh cắp ở dạng thô (**plaintext**), dữ liệu vẫn sẽ bị lộ. [cite: 2151, 2179]

💡 **Mẹo nhớ nhanh:** **Envelope Encryption** = Mã hóa cái chìa khóa để đảm bảo dù bị mất chìa khóa, kẻ trộm cũng không mở được khóa. [cite: 2211, 2345]

---

### ❓ Question 9

Which functions does the AWS Key Management Service (AWS KMS) provide? (Select TWO.) [cite: 2257, 2348]

A. Rotate keys

B. Authenticate external users

C. Create symmetric and asymmetric keys

D. Create AWS Identity and Access Management (IAM) access keys

E. Store encrypted data

✅ **Đáp án đúng:** A và C [cite: 2263, 2349]

### 🧠 Giải thích

- **A đúng:** **KMS** hỗ trợ tự động xoay vòng khóa (**key rotation**) để tăng tính bảo mật cho hệ thống.
- **C đúng:** **KMS** cho phép tạo và quản lý cả khóa đối xứng (**symmetric**) và khóa bất đối xứng (**asymmetric**). [cite: 2267, 2268]

### ❌ Vì sao đáp án khác sai?

- **B:** Xác thực người dùng là nhiệm vụ của **IAM** hoặc **Cognito**. [cite: 1717, 1788]
- **D:** **IAM Access Keys** (dùng cho CLI/SDK) được quản lý trong dịch vụ **IAM**, không phải **KMS**.
- **E:** **KMS** chỉ lưu giữ và quản lý các **khóa mã hóa**, nó không phải là dịch vụ lưu trữ dữ liệu (như S3). [cite: 2257]

💡 **Mẹo nhớ nhanh:** **KMS** là cái tủ sắt chứa "chìa khóa" (**Keys**), không phải chứa "đồ" (Dữ liệu). [cite: 2257, 2348]

---

### ❓ Question 10

Which AWS service discovers and protects sensitive information stored on Amazon S3 in an AWS account? [cite: 2411, 2478]

A. AWS Audit Manager

B. Amazon Macie

C. Amazon Detective

D. AWS Resource Access Manager (AWS RAM)

✅ **Đáp án đúng:** B [cite: 2413, 2478]

### 🧠 Giải thích

- **Amazon Macie** sử dụng trí tuệ nhân tạo và khớp mẫu để tự động tìm kiếm các dữ liệu nhạy cảm (như mã số thuế, số thẻ tín dụng) đang nằm trong các thùng lưu trữ **Amazon S3**. [cite: 2413, 2415]

### ❌ Vì sao đáp án khác sai?

- **A:** Dùng để quản lý quá trình kiểm thử tuân thủ (**compliance audits**).
- **C:** Dùng để điều tra và tìm nguyên nhân gốc rễ của các sự cố bảo mật. [cite: 2440]
- **D:** Dùng để chia sẻ tài nguyên **AWS** giữa các tài khoản với nhau.

💡 **Mẹo nhớ nhanh:** Quét tìm dữ liệu nhạy cảm trên **S3** -> Nhớ đến "cô nàng thám tử" **Macie**. [cite: 2413]

---
