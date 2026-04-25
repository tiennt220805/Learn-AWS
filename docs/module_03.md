# Module 03: Securing Access

## 🧭 Overview

Module này cung cấp kiến thức nền tảng về cách bảo mật truy cập trong môi trường AWS:

- Tìm hiểu các nguyên tắc bảo mật cốt lõi trong AWS Cloud.
- Hiểu rõ vai trò của **IAM users**, **groups**, và **roles**.
- Cách thức **IAM policies** quản lý và phân quyền trong tài khoản AWS.

---

## 📘 Phần 1: Security principles (Các nguyên tắc bảo mật)

### 🔑 Ý chính

- **AWS Shared Responsibility Model** (Mô hình trách nhiệm chung): Bảo mật là nỗ lực chung của cả AWS và khách hàng.
- **Security Pillar** (Cột trụ bảo mật): Một phần quan trọng trong **AWS Well-Architected Framework** để thiết kế giải pháp an toàn.
- **Principle of Least Privilege** (Nguyên tắc quyền hạn tối thiểu): Chỉ cấp đúng và đủ quyền cần thiết để hoàn thành công việc.
- **Data Protection** (Bảo vệ dữ liệu): Sử dụng mã hóa để bảo vệ dữ liệu ở cả hai trạng thái nghỉ và đang truyền tải.

### 🧠 Giải thích

#### 1. Mô hình trách nhiệm chung (Shared Responsibility Model)

- **AWS (Security OF the Cloud)**: Chịu trách nhiệm bảo mật cho hạ tầng toàn cầu bao gồm phần cứng, phần mềm, mạng lưới và các cơ sở vận hành các dịch vụ AWS Cloud (vùng, vùng sẵn sàng, vị trí biên).
- **Khách hàng (Security IN the Cloud)**: Chịu trách nhiệm cho những gì họ cấu hình và lưu trữ như dữ liệu khách hàng, quản lý danh tính (IAM), hệ điều hành, cấu hình tường lửa và mã hóa.

#### 2. 7 nguyên tắc thiết kế bảo mật

- **Strong identity foundation**: Thực hiện quản lý danh tính mạnh mẽ.
- **Maintain traceability**: Duy trì khả năng truy vết và giám sát mọi hoạt động.
- **Apply security at all layers**: Áp dụng bảo mật ở mọi lớp (mạng, hệ điều hành, ứng dụng).
- **Automate security best practices**: Tự động hóa các biện pháp bảo mật tốt nhất.
- **Protect data in transit and at rest**: Bảo vệ dữ liệu khi đang truyền và khi đang lưu trữ.
- **Keep people away from data**: Hạn chế con người tiếp xúc trực tiếp với dữ liệu thô.
- **Prepare for security events**: Sẵn sàng ứng phó với các sự kiện bảo mật.

#### 3. Mã hóa dữ liệu (Encryption)

- **In transit (Đang truyền tải)**: Sử dụng các giao thức mã hóa như **TLS** để bảo vệ dữ liệu khi di chuyển giữa Client và AWS.
- **At rest (Trạng thái nghỉ)**:
  - **Client-side encryption**: Khách hàng mã hóa dữ liệu trước khi gửi lên AWS.
  - **Server-side encryption**: AWS tự động mã hóa dữ liệu khi nó được lưu trữ vào hệ thống và giải mã khi có yêu cầu hợp lệ.

### ⚖️ So sánh: Các loại mã hóa dữ liệu nghỉ

| Đặc điểm              | Client-side Encryption                  | Server-side Encryption                                |
| :-------------------- | :-------------------------------------- | :---------------------------------------------------- |
| **Nơi mã hóa**        | Thực hiện tại phía máy khách (Client)   | Thực hiện bên trong dịch vụ AWS                       |
| **Trạng thái truyền** | Dữ liệu được gửi đi dưới dạng đã mã hóa | Dữ liệu truyền đi có thể chưa mã hóa (cần TLS hỗ trợ) |
| **Trách nhiệm**       | Khách hàng quản lý hoàn toàn quá trình  | AWS quản lý việc mã hóa/giải mã tự động               |

### 📌 Ghi nhớ nhanh

- 🛡️ **Bảo mật là trách nhiệm của mọi người**: AWS lo hạ tầng, bạn lo dữ liệu.
- 🤏 **Quyền hạn tối thiểu**: Luôn bắt đầu với quyền ít nhất có thể, chỉ thêm khi cần và thu hồi ngay khi không dùng nữa.
- 🔐 **Mã hóa là then chốt**: Luôn bảo vệ dữ liệu bằng **Encryption** dù đang lưu kho hay đang gửi đi.

### 💡 Ví dụ (nếu có)

```example
Nguyên tắc Quyền hạn tối thiểu (Least Privilege):
- John (Quản trị viên): Được cấp IAM Policy có toàn quyền truy cập S3.
- Mary (Marketing): Chỉ được cấp quyền Read-only đối với S3 bucket 1 và
bị chặn hoàn toàn (Explicit Deny) đối với S3 bucket 2.
=> Việc phân chia này giúp giảm thiểu rủi ro nếu tài khoản Mary bị lộ.
```

---

## 📘 Phần 2: Authenticating and securing access (Xác thực và bảo mật truy cập)

### 🔑 Ý chính

- **Authentication** (Xác thực) và **Authorization** (Ủy quyền) là hai bước tách biệt nhưng bổ trợ cho nhau.
- **IAM** (Identity and Access Management) là dịch vụ trung tâm để quản lý quyền truy cập.
- Các thành phần cốt lõi của IAM bao gồm **Users**, **Groups**, **Roles** và **Policies**.
- Bảo vệ tài khoản thông qua các cơ chế **Credentials** phù hợp và bảo vệ tuyệt đối **Root user**.

### 🧠 Giải thích

#### 1. Authentication vs. Authorization (Xác thực và Ủy quyền)

Đây là quy trình kiểm soát truy cập gồm 2 bước:

- **Authentication** (Xác thực): Xác minh danh tính. Trả lời câu hỏi "Bạn là ai?". Sử dụng thông tin đăng nhập (**Credentials**) để thiết lập danh tính cho người hoặc ứng dụng.
- **Authorization** (Ủy quyền): Trả lời câu hỏi "Bạn được phép làm gì?". Sau khi xác thực, hệ thống dựa vào các chính sách để cho phép (**Allow**) hoặc từ chối (**Deny**) hành động cụ thể.

| Khái niệm          | Câu hỏi trọng tâm | Mục đích                       |
| :----------------- | :---------------- | :----------------------------- |
| **Authentication** | Who are you?      | Thiết lập danh tính (Identity) |
| **Authorization**  | What can you do?  | Xác định quyền hạn thực thi    |

#### 2. Thuật ngữ IAM cốt lõi

- **IAM Principal** (Thực thể chính): Một người hoặc ứng dụng có thể thực hiện yêu cầu truy cập vào AWS.
- **IAM User** (Người dùng): Đại diện cho một người hoặc dịch vụ cụ thể.
- **IAM Group** (Nhóm): Tập hợp các **IAM Users**. Gán chính sách cho nhóm để tất cả thành viên trong nhóm có cùng quyền hạn.
- **IAM Role** (Vai trò): Một danh tính cung cấp **Temporary security credentials** (Thông tin xác thực tạm thời). Không gắn cố định với một người, có thể được "đảm nhận" (**Assumed**) bởi người dùng, ứng dụng hoặc dịch vụ.
- **IAM Policy** (Chính sách): Tài liệu JSON định nghĩa quyền hạn, dùng để gán cho các thực thể IAM.

#### 3. Credentials (Thông tin xác thực)

Tùy vào cách truy cập mà bạn sử dụng loại thông tin khác nhau:

- **AWS Management Console**: Sử dụng **Username** và **Password**.
- **AWS CLI / Programmatic calls**: Sử dụng **AWS Access Key** (gồm **Access Key ID** và **Secret Access Key**).

#### 4. Protecting the Root User (Bảo vệ người dùng gốc)

- **Root user**: Là tài khoản được tạo khi lần đầu đăng ký AWS, có toàn quyền truy cập (**Full access**) không thể bị giới hạn.
- **Best Practice**:
  - Không sử dụng **Root user** cho các tác vụ hàng ngày.
  - Tạo một **Administrative user** thông qua **IAM Identity Center** để quản lý.
  - Luôn bật **MFA** (Multifactor authentication) cho tài khoản Root.

### 📌 Ghi nhớ nhanh

- 👤 **IAM User**: 1 người/app = 1 danh tính lâu dài.
- 👥 **IAM Group**: Gom nhóm để quản lý quyền hàng loạt.
- 🎭 **IAM Role**: Quyền hạn tạm thời, dùng để ủy quyền truy cập chéo tài khoản hoặc cho dịch vụ AWS.
- 🔐 **MFA**: Là lớp bảo mật bắt buộc phải có cho tài khoản quan trọng.
- 🗝️ **Access Key**: Cẩn thận không để lộ Secret Key, vì nó cho phép truy cập qua code/terminal.

### 💡 Ví dụ (nếu có)

```example
Quy trình thiết lập chuẩn cho một Cloud Architect mới:
1. Đăng nhập bằng Root User.
2. Bật MFA cho Root User ngay lập tức.
3. Tạo một IAM User tên "Admin-John" với quyền Administrator.
4. Tải Access Key của John để dùng cho CLI.
5. Thoát Root User và từ đó về sau chỉ dùng tài khoản "Admin-John".
```

---

## 📘 Phần 3: Authorizing users (Ủy quyền người dùng)

### 🔑 Ý chính

- **IAM Policy** là tài liệu định nghĩa quyền hạn, xác định hành động nào được phép (**Allow**) hoặc bị từ chối (**Deny**).
- Các chính sách được viết dưới định dạng **JSON** (JavaScript Object Notation).
- Tuân thủ nghiêm ngặt **Principle of least privilege** (Nguyên tắc quyền hạn tối thiểu).
- Cơ chế đánh giá ưu tiên: **Explicit Deny** (Từ chối rõ ràng) luôn có quyền ưu tiên cao nhất.

### 🧠 Giải thích

#### 1. Phân loại Policies

AWS chia chính sách thành hai loại chính dựa trên đối tượng mà chúng được gắn vào:

- **Identity-based policy** (Chính sách dựa trên danh tính): Được gán trực tiếp cho một **IAM user**, **group**, hoặc **role**. Nó xác định thực thể đó có thể làm gì.
- **Resource-based policy** (Chính sách dựa trên tài nguyên): Được gắn trực tiếp vào một tài nguyên AWS (ví dụ: S3 bucket). Nó xác định ai (Principal) có quyền truy cập vào tài nguyên đó.

#### 2. Cơ chế đánh giá quyền hạn (Policy Evaluation Logic)

Khi một thực thể gửi yêu cầu đến AWS, hệ thống sẽ kiểm tra quyền theo trình tự sau:

1. **Default Deny**: Mọi yêu cầu ban đầu đều bị từ chối ngầm định (**Implicit deny**).
2. **Explicit Deny**: Nếu có bất kỳ chính sách nào ghi "Deny" cho hành động đó, yêu cầu bị từ chối ngay lập tức.
3. **Explicit Allow**: Nếu không có lệnh Deny, hệ thống tìm lệnh "Allow". Nếu tìm thấy, yêu cầu được chấp nhận.
4. **Final Implicit Deny**: Nếu không tìm thấy lệnh "Allow" nào, yêu cầu vẫn bị từ chối.

#### 3. So sánh Identity-based và Resource-based

| Đặc điểm          | Identity-based policy             | Resource-based policy                                          |
| :---------------- | :-------------------------------- | :------------------------------------------------------------- |
| **Đối tượng gắn** | Gắn vào User, Group, Role         | Gắn vào Resource (S3, SQS, v.v.)                               |
| **Mục đích**      | "Tôi (User) có thể làm được gì?"  | "Ai có thể truy cập vào tôi (Resource)?"                       |
| **Phạm vi**       | Chỉ áp dụng cho thực thể được gắn | Có thể cho phép truy cập từ tài khoản khác (**Cross-account**) |

### 📌 Ghi nhớ nhanh

- 🛑 **Explicit Deny > Explicit Allow**: Dù bạn được cho phép ở 10 chỗ nhưng chỉ cần bị từ chối ở 1 chỗ, bạn sẽ không thể thực hiện hành động đó.
- 📁 **JSON format**: Mọi chính sách đều là một file văn bản có cấu trúc máy tính đọc được.
- 🚫 **Mặc định là cấm**: Nếu bạn không tạo chính sách cho phép, người dùng sẽ không có quyền gì cả.

### 💡 Ví dụ (nếu có)

```example
Trường hợp xung đột chính sách của Bob:
- Identity-based policy (của Bob): Cho phép (Allow) hành động PUT vào Bucket X.
- Resource-based policy (trên Bucket X): Từ chối (Deny) Bob thực hiện hành động PUT.

=> Kết quả: Bob KHÔNG THỂ thực hiện lệnh PUT vào Bucket X vì lệnh Deny rõ ràng (Explicit Deny) đã ghi đè lệnh Allow.
```

---

## 📘 Phần 4: Parts of an IAM policy (Các thành phần của một chính sách IAM)

### 🔑 Ý chính

- Một tài liệu **IAM Policy** được cấu trúc dưới định dạng **JSON** và bao gồm một hoặc nhiều câu lệnh (**Statements**).
- Mỗi thành phần trong chính sách đóng một vai trò cụ thể trong việc xác định ai có thể làm gì trên tài nguyên nào.
- Các phần tử chính bao gồm: **Version**, **Effect**, **Principal**, **Action**, **Resource**, và **Condition**.

### 🧠 Giải thích

#### 1. Các thành phần cơ bản của Policy Statement

- **Version** (Phiên bản): Xác định phiên bản của ngôn ngữ chính sách (thường sử dụng "2012-10-17").
- **Statement** (Câu lệnh): Là phần tử chính chứa các quy tắc bảo mật. Một chính sách có thể có nhiều câu lệnh.
- **Effect** (Hiệu lực): Xác định kết quả của câu lệnh là cho phép (**Allow**) hay từ chối (**Deny**).
- **Action** (Hành động): Danh sách các thao tác cụ thể của dịch vụ AWS được phép hoặc bị cấm (ví dụ: `s3:GetObject`, `dynamodb:PutItem`).
- **Resource** (Tài nguyên): Đối tượng mà hành động sẽ tác động lên, được xác định bằng định dạng **ARN** (Amazon Resource Name).
- **Condition** (Điều kiện): Các tiêu chí bổ sung để chính sách có hiệu lực (ví dụ: chỉ cho phép từ một dải IP cụ thể).

#### 2. Thành phần đặc biệt: Principal (Thực thể chính)

Thành phần **Principal** có sự khác biệt tùy thuộc vào loại chính sách:

| Loại Policy        | Cách sử dụng Principal                                                                                            |
| :----------------- | :---------------------------------------------------------------------------------------------------------------- |
| **Identity-based** | **Ngầm định** (Implied): Chính là thực thể (user/role) mà chính sách đang được gán vào.                           |
| **Resource-based** | **Bắt buộc** (Explicit): Phải ghi rõ tài khoản, người dùng hoặc vai trò nào được phép truy cập vào tài nguyên đó. |

#### 3. ARN (Amazon Resource Name)

Đây là cấu trúc định danh chuẩn cho mọi tài nguyên trong AWS.

- Định dạng chung: `arn:aws:service:region:account-id:resource-id`.
- Ví dụ: `arn:aws:s3:::my-bucket/*` (Đại diện cho tất cả đối tượng trong một S3 bucket).

### 📌 Ghi nhớ nhanh

- 📝 **JSON**: Luôn ghi nhớ chính sách là một file văn bản có cấu trúc cặp key-value.
- 🎯 **Action & Resource**: Luôn đi đôi với nhau để xác định "Làm việc gì" trên "Cái gì".
- 🆔 **Sid** (Statement ID): Là mã định danh tùy chọn để bạn dễ dàng phân biệt các câu lệnh trong cùng một chính sách.
- 🌐 **Wildcards** (\*): Có thể sử dụng dấu sao để đại diện cho "tất cả" hành động hoặc tài nguyên.

### 💡 Ví dụ (nếu có)

```example
Một chính sách cho phép người dùng quản lý thông tin của chính họ:
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "iam:*LoginProfile",
        "iam:*AccessKey*"
      ],
      "Resource": "arn:aws:iam::123456789012:user/${aws:username}"
    }
  ]
}
=> Giải thích: Cho phép (Allow) các hành động liên quan đến Login và Access Key đối với đúng tài nguyên User có tên khớp với tên người dùng đang đăng nhập.
```

---

## 📝 Knowledge Check

### ❓ Question 1

Which statement reflects a design principle of the security pillar of the Well-Architected Framework?

A. Ensure that staff are actively monitoring potential risks manually.

B. Apply security at all layers of an architecture.

C. Do not deploy a solution to production until you're certain that no security risks exist.

D. Decentralize privilege management.

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Apply security at all layers** (Áp dụng bảo mật ở mọi lớp) là một trong 7 nguyên tắc thiết kế cốt lõi của **Security Pillar**. Thay vì chỉ bảo vệ vòng ngoài, bạn cần bảo mật từ mạng, hệ điều hành đến ứng dụng và dữ liệu.

### ❌ Vì sao đáp án khác sai?

- **A**: Sai vì AWS khuyến khích **Automate security best practices** (Tự động hóa) thay vì làm thủ công.
- **C**: Trong thực tế không bao giờ có "không có rủi ro", nguyên tắc là phải chuẩn bị cho các sự kiện bảo mật (**Prepare for security events**).
- **D**: Nguyên tắc đúng là **Implement a strong identity foundation** (Củng cố nền tảng định danh tập trung), không phải phi tập trung quản lý đặc quyền.

💡 **Mẹo nhớ nhanh:** Bảo mật AWS giống như củ hành, phải có nhiều lớp (**Layers**) bảo vệ lẫn nhau.

---

### ❓ Question 2

Which statements about responsibility are accurate based on the AWS shared responsibility model? (Select TWO.)

A. Customers are responsible for managing their user data.

B. AWS is responsible for host-based firewall configurations.

C. Customers are responsible for the installation, maintenance, and decommissioning of the hardware that they use in the AWS data center.

D. AWS is responsible for the physical security of data centers.

E. AWS is responsible for the configuration of security groups.

✅ **Đáp án đúng:** A, D

### 🧠 Giải thích

- **A**: Khách hàng chịu trách nhiệm bảo mật **IN the cloud**, bao gồm dữ liệu người dùng (**Customer data**).
- **D**: AWS chịu trách nhiệm bảo mật **OF the cloud**, bao gồm an ninh vật lý cho các trung tâm dữ liệu (**Physical security**) và hạ tầng toàn cầu.

### ❌ Vì sao đáp án khác sai?

- **B & E**: Việc cấu hình tường lửa (**Firewall/Security Groups**) là trách nhiệm của khách hàng nhằm bảo vệ tài nguyên của họ.
- **C**: Khách hàng không bao giờ chạm vào phần cứng; AWS lo toàn bộ vòng đời của thiết bị vật lý.

💡 **Mẹo nhớ nhanh:** AWS lo cái "nhà" (hạ tầng), khách hàng lo "đồ đạc" bên trong (dữ liệu/cấu hình).

---

### ❓ Question 3

Which options are characteristics of the principle of least privilege? (Select TWO.)

A. Craft security policies that limit access to specific tasks.

B. Monitor actions and changes.

C. Use encryption.

D. Grant access only as needed.

E. Always use groups.

✅ **Đáp án đúng:** A, D

### 🧠 Giải thích

- **A & D**: **Least privilege** (Quyền hạn tối thiểu) nghĩa là chỉ cấp đúng những quyền cần thiết để thực hiện một công việc cụ thể và chỉ cấp khi thực sự cần (**Grant only as needed**).

### ❌ Vì sao đáp án khác sai?

- **B**: Đây là nguyên tắc **Traceability** (Truy vết), không phải phân quyền.
- **C**: **Encryption** là bảo vệ dữ liệu, một nguyên tắc bảo mật khác.
- **E**: Sử dụng nhóm (**Groups**) là một **Best practice** để quản lý, nhưng không phải là đặc điểm định nghĩa của quyền hạn tối thiểu.

---

### ❓ Question 4

Which statement about AWS Identity and Access Management (IAM) is true?

A. IAM provides an extra layer of security by offering anomaly detection on resources.

B. IAM provides an audit trail of who performed an action, what action they performed, and when they performed it.

C. With IAM, you can manage encryption for items that require encryption at rest.

D. With IAM, you can grant principals granular access to resources.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- IAM cho phép quản lý quyền truy cập chi tiết (**Granular access**) đến từng tài nguyên AWS cho các đối tượng (**Principals**).

### ❌ Vì sao đáp án khác sai?

- **A**: IAM không tự phát hiện hành vi bất thường (đó là vai trò của các dịch vụ như GuardDuty).
- **B**: Đây là chức năng của **AWS CloudTrail**, không phải tính năng cốt lõi của IAM.
- **C**: Việc quản lý mã hóa thường thuộc về dịch vụ **KMS** (Key Management Service).

---

### ❓ Question 5

Which statements describe AWS Identity and Access Management (IAM) roles? (Select TWO.)

A. They can only be used by accounts that are associated to the person who creates the role.

B. They provide temporary security credentials.

C. They are uniquely associated to an individual.

D. Individuals, applications, and services can assume roles.

E. They provide permanent security credentials.

✅ **Đáp án đúng:** B, D

### 🧠 Giải thích

- **B**: **IAM Roles** không có mật khẩu hay access key vĩnh viễn; chúng cung cấp thông tin xác thực tạm thời (**Temporary security credentials**).
- **D**: Roles có thể được "đảm nhận" (**Assumed**) bởi con người, ứng dụng hoặc các dịch vụ AWS (như EC2) để thực hiện tác vụ.

### ❌ Vì sao đáp án khác sai?

- **A**: Roles có thể được dùng cho truy cập chéo tài khoản (**Cross-account access**).
- **C**: Khác với IAM User, Role không gắn cố định với một cá nhân duy nhất.
- **E**: Role hoàn toàn không dùng thông tin xác thực vĩnh viễn (**Permanent**).

💡 **Mẹo nhớ nhanh:** **IAM User** là "thẻ nhân viên", **IAM Role** là "chiếc nón" ai đội vào cũng có quyền của chức vụ đó trong chốc lát.

---

### ❓ Question 6

Which statement reflects a best practice for the root user on an AWS account?

A. Remove unneeded permissions from the root user account.

B. Create an admin user and perform most admin tasks with this user instead of the root user.

C. To avoid getting locked out of the account, do not enable multi-factor authentication (MFA) on the root account.

D. Create two root users with separate credentials and distribute them to two different individuals.

✅ **Đáp án đúng:** B

### 🧠 Giải thích

- **Best practice** hàng đầu là không dùng **Root user** cho công việc hàng ngày. Bạn nên tạo một người dùng quản trị (**Admin user**) và dùng tài khoản đó để thao tác.

### ❌ Vì sao đáp án khác sai?

- **A**: Quyền của Root user là toàn năng và không thể bị tước bỏ.
- **C**: Ngược lại, việc bật **MFA** cho Root là bắt buộc để đảm bảo an toàn tối đa.
- **D**: Mỗi tài khoản AWS chỉ có duy nhất một Root user.

---

### ❓ Question 7

How does AWS Identity and Access Management (IAM) evaluate a policy?

A. It checks for explicit deny statements before it checks for explicit allow statements.

B. If the policy doesn't have any explicit deny statements or explicit allow statements, users have access by default.

C. An explicit deny statement does not override an explicit allow statement.

D. It checks for explicit allow statements before it checks for explicit deny statements.

✅ **Đáp án đúng:** A

### 🧠 Giải thích

- Quy trình đánh giá của IAM luôn ưu tiên tìm lệnh từ chối rõ ràng (**Explicit deny**) trước. Nếu có bất kỳ lệnh Deny nào, yêu cầu sẽ bị bác bỏ ngay lập tức dù có lệnh Allow ở nơi khác.

### ❌ Vì sao đáp án khác sai?

- **B**: Mặc định mọi thứ đều bị cấm (**Implicit deny**), không phải được truy cập.
- **C**: Sai hoàn toàn, **Explicit Deny** luôn ghi đè (**Override**) Explicit Allow.
- **D**: Thứ tự đúng là Deny trước, Allow sau.

---

### ❓ Question 8

Which statement about AWS Identity and Access Management (IAM) policies is accurate?

A. Resource-based policies are attached to a user, group, or role.

B. Resource-based policies allow access by default.

C. Identity-based policies are attached to a user, group, or role.

D. Identity-based policies can only be attached to a single entity.

✅ **Đáp án đúng:** C

### 🧠 Giải thích

- **Identity-based policies** là loại chính sách phổ biến nhất, được gắn vào các đối tượng danh tính như **User**, **Group**, hoặc **Role**.

### ❌ Vì sao đáp án khác sai?

- **A**: Loại gắn vào user/role là Identity-based, còn **Resource-based** là gắn vào tài nguyên (như S3 bucket).
- **B**: Không có chính sách nào cho phép mặc định; tất cả phải được cấu hình rõ ràng.
- **D**: Một chính sách có thể được gắn cho nhiều thực thể cùng lúc.

---

### ❓ Question 9

Which AWS Identity and Access Management (IAM) policy element includes information about whether to allow or deny a request?

A. Principal

B. Action

C. Condition

D. Effect

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- Phần tử **Effect** xác định kết quả của chính sách là **Allow** (Cho phép) hay **Deny** (Từ chối).

### ❌ Vì sao đáp án khác sai?

- **A**: **Principal** là ai được áp dụng chính sách.
- **B**: **Action** là hành động gì được thực hiện.
- **C**: **Condition** là các điều kiện đi kèm (như thời gian, IP).

---

### ❓ Question 10

Which option accurately describes the statement element in an AWS Identity and Access Management (IAM) policy?

A. The statement element does not apply to identity-based policies.

B. A policy can only have one statement element.

C. The statement element is an optional part of an IAM policy.

D. The statement element contains other elements that together define what is allowed or denied.

✅ **Đáp án đúng:** D

### 🧠 Giải thích

- **Statement** là "trái tim" của chính sách JSON, nó bao gồm các phần tử con như Effect, Action, Resource... để cùng nhau tạo nên một quy tắc hoàn chỉnh.

### ❌ Vì sao đáp án khác sai?

- **A**: Mọi loại chính sách IAM đều cần Statement.
- **B**: Một chính sách có thể chứa danh sách nhiều Statements để gộp nhiều quy tắc bảo mật.
- **C**: Statement là thành phần bắt buộc để chính sách có ý nghĩa thực thi.

---

# Practice Test - Module 03: Securing Access

## 📊 Summary Score: 100% (20/20)

---

## Question 1

Under the AWS Shared Responsibility Model, which of the following is the customer's responsibility?

A. Physical security of the data center

B. Managing the host-based operating system

C. Decommissioning storage hardware

D. Managing global infrastructure regions

✅ **Correct Answer: B**

### 🧠 Explanation

Theo mô hình trách nhiệm chung, khách hàng chịu trách nhiệm bảo mật "TRONG" đám mây. Việc quản lý hệ điều hành (Operating System) trên các thực thể như EC2, bao gồm vá lỗi và bảo mật, thuộc về khách hàng.

### ❌ Why Other Choices Are Wrong

- **A, C, D**: Đều thuộc trách nhiệm bảo mật "CỦA" đám mây (Infrastructure), do AWS quản lý hoàn toàn.

---

## Question 2

What is the main purpose of an IAM Role?

A. To provide permanent access keys for an application developer

B. To grant temporary security credentials to a user or service

C. To group multiple IAM users together for billing purposes

D. To replace the need for an AWS account root user

✅ **Correct Answer: B**

### 🧠 Explanation

**IAM Role** không có thông tin xác thực lâu dài (như mật khẩu). Thay vào đó, nó cung cấp thông tin xác thực bảo mật tạm thời (**Temporary security credentials**) cho người dùng, ứng dụng hoặc dịch vụ cần truy cập tài nguyên AWS.

### ❌ Why Other Choices Are Wrong

- **A**: Access keys vĩnh viễn dùng cho IAM User, không phải Role.
- **C**: Billing được quản lý qua AWS Organizations hoặc các công cụ chi phí, không phải IAM Role.
- **D**: Role không thay thế Root user; Root user là tài khoản tạo ra lúc đầu với toàn quyền.

---

## Question 3

Which security principle from the Well-Architected Framework focuses on maintaining a record of who did what and when?

A. Keep people away from data

B. Protect data in transit

C. Maintain traceability

D. Automate security best practices

✅ **Correct Answer: C**

### 🧠 Explanation

**Maintain traceability** (Duy trì khả năng truy vết) là nguyên tắc thiết kế nhằm giám sát, ghi lại và kiểm tra mọi thay đổi cũng như hành động trong môi trường AWS.

### ❌ Why Other Choices Are Wrong

- **A**: Tập trung vào việc giảm thiểu tương tác trực tiếp của con người với dữ liệu thô.
- **B**: Tập trung vào mã hóa dữ liệu khi di chuyển.
- **D**: Tập trung vào việc sử dụng code để triển khai các biện pháp bảo mật tự động.

---

## Question 4

How does IAM handle an evaluation if there is no explicit 'Allow' and no explicit 'Deny' in any applicable policy?

A. The request is allowed by default

B. The request is allowed if it comes from an Admin user

C. The request is implicitly denied

D. The request is sent to the root user for approval

✅ **Correct Answer: C**

### 🧠 Explanation

Trong AWS, mọi yêu cầu mặc định đều bị từ chối. Nếu không tìm thấy lệnh cho phép rõ ràng (**Explicit Allow**) và cũng không có lệnh từ chối rõ ràng, hệ thống sẽ áp dụng **Implicit Deny** (Từ chối ngầm định).

### ❌ Why Other Choices Are Wrong

- **A**: Sai, mặc định là Deny.
- **B**: Dù là Admin vẫn cần có chính sách gán quyền rõ ràng (thường là quyền "\*").
- **D**: Quá trình đánh giá chính sách diễn ra tự động và tức thì, không cần phê duyệt thủ công.

---

## Question 5

Which of the following is a recommended best practice for the AWS account root user?

A. Use the root user for all administrative daily tasks

B. Share root credentials with your team for redundancy

C. Enable Multi-Factor Authentication (MFA)

D. Avoid creating an IAM Admin user to save costs

✅ **Correct Answer: C**

### 🧠 Explanation

Bảo vệ **Root user** là cực kỳ quan trọng. Best practice là luôn bật **MFA** (Xác thực đa yếu tố) để thêm một lớp bảo vệ nếu mật khẩu bị lộ.

### ❌ Why Other Choices Are Wrong

- **A**: Không dùng Root cho tác vụ hàng ngày; nên dùng IAM Admin.
- **B**: Tuyệt đối không chia sẻ thông tin Root.
- **D**: Việc tạo IAM User không tốn thêm chi phí và là yêu cầu bắt buộc để bảo mật.

---

## Question 6

Which type of policy is attached directly to an AWS service resource, such as an Amazon S3 bucket, rather than to an IAM entity?

A. Identity-based policy

B. Resource-based policy

C. Organization unit policy

D. Management console policy

✅ **Correct Answer: B**

### 🧠 Explanation

**Resource-based policy** (Chính sách dựa trên tài nguyên) được gắn trực tiếp vào tài nguyên (như S3 bucket) để xác định ai (Principal) có quyền truy cập vào tài nguyên đó.

### ❌ Why Other Choices Are Wrong

- **A**: Gắn vào IAM User, Group hoặc Role.
- **C, D**: Không phải là các loại chính sách quyền hạn cơ bản trong IAM.

---

## Question 7

A developer needs to access an S3 bucket from an application running on an Amazon EC2 instance. Which is the most secure way to grant this access?

A. Hardcode the developer's IAM access keys into the application code

B. Store the root user's password in a text file on the EC2 instance

C. Assign an IAM role with the necessary permissions to the EC2 instance

D. Create an IAM group for EC2 and put the root user in it

✅ **Correct Answer: C**

### 🧠 Explanation

Gán **IAM Role** cho EC2 instance là cách an toàn nhất vì nó sử dụng thông tin xác thực tạm thời, loại bỏ rủi ro lộ **Access Keys** vĩnh viễn trong mã nguồn.

### ❌ Why Other Choices Are Wrong

- **A**: Rất nguy hiểm vì keys có thể bị lộ nếu code bị đánh cắp.
- **B**: Vi phạm nghiêm trọng nguyên tắc bảo vệ Root user.
- **D**: IAM Group chỉ dành cho User, không dùng cho EC2 instance.

---

## Question 8

A company wants to ensure that its employees can only see the contents of an S3 bucket but cannot delete any files. Which IAM policy setting should they use?

A. AdministratorAccess

B. Explicit Deny on all S3 actions

C. A policy with "Effect": "Allow" and "Action": "s3:Get*" and "s3:List*"

D. A policy with "Effect": "Allow" and "Action": "s3:\*"

✅ **Correct Answer: C**

### 🧠 Explanation

Để chỉ cho phép xem (Read-only), chính sách cần liệt kê các hành động lấy thông tin dữ liệu (`Get*`) và liệt kê dữ liệu (`List*`).

### ❌ Why Other Choices Are Wrong

- **A, D**: Cấp quá nhiều quyền, bao gồm cả quyền xóa.
- **B**: Sẽ chặn mọi quyền truy cập, không xem được gì.

💡 **Mẹo nhớ nhanh:** Luôn cấp quyền tối thiểu (**Least Privilege**) vừa đủ để hoàn thành công việc.

---

## Question 9

A security architect wants to ensure that data being sent between a client and an S3 bucket is protected from interception. What mechanism should be used?

A. Server-side encryption at rest

B. Multi-Factor Authentication

C. Transport Layer Security (TLS)

D. Client-side encryption at rest

✅ **Correct Answer: C**

### 🧠 Explanation

Để bảo vệ dữ liệu "khi đang di chuyển" (**Data in transit**) tránh bị đánh chặn, cần sử dụng giao thức mã hóa như **TLS** (thường qua HTTPS).

### ❌ Why Other Choices Are Wrong

- **A, D**: Dành cho dữ liệu "ở trạng thái nghỉ" (At rest), không bảo vệ khi đang truyền tải.
- **B**: Là cơ chế xác thực danh tính, không phải mã hóa đường truyền.

---

## Question 10

A user has an identity-based policy that allows "s3:PutObject". However, the S3 bucket has a resource-based policy that explicitly denies that user from uploading files. What will happen when the user tries to upload a file?

A. The upload will succeed because the identity-based policy allows it

B. The upload will fail because the explicit deny in the bucket policy overrides the allow

C. The upload will succeed but the user will receive a warning email

D. The user will be prompted to enter their MFA code to bypass the deny

✅ **Correct Answer: B**

### 🧠 Explanation

Trong logic đánh giá của AWS, lệnh **Explicit Deny** luôn có quyền ưu tiên cao nhất và sẽ ghi đè lên mọi lệnh Allow.

### ❌ Why Other Choices Are Wrong

- **A**: Sai vì Deny thắng Allow.
- **C, D**: AWS không hoạt động theo cơ chế cảnh báo hay dùng MFA để bypass lệnh Deny rõ ràng trong chính sách.

---

## Question 11

An organization has five different developers who all need the exact same administrative permissions for EC2. What is the most efficient way to manage these permissions?

A. Create one IAM user and share the password with all five developers

B. Create five IAM users and attach the same policy to each user individually

C. Create an IAM group, attach the policy to the group, and add the five users to that group

D. Use the root user credentials for all five developers

✅ **Correct Answer: C**

### 🧠 Explanation

Sử dụng **IAM Group** giúp quản lý quyền hạn tập trung. Bạn chỉ cần thay đổi chính sách của nhóm một lần thay vì chỉnh sửa từng user riêng lẻ.

### ❌ Why Other Choices Are Wrong

- **A, D**: Chia sẻ tài khoản là vi phạm bảo mật nghiêm trọng và làm mất khả năng truy vết (**Traceability**).
- **B**: Không hiệu quả khi quy mô nhân sự tăng lên.

---

## Question 12

A company needs to allow an external auditor from a different AWS account to read logs stored in their S3 bucket for a limited time. What is the best solution?

A. Send the root user credentials to the auditor via encrypted email

B. Create an IAM role for cross-account access and allow the auditor to assume it

C. Create an IAM user in the local account and give the auditor the password

D. Make the S3 bucket public so anyone can read the logs

✅ **Correct Answer: B**

### 🧠 Explanation

**IAM Role** được thiết kế cho các tình huống ủy quyền truy cập chéo tài khoản (**Cross-account access**) mà không cần tạo thông tin xác thực lâu dài cho người ngoài.

### ❌ Why Other Choices Are Wrong

- **A**: Tuyệt đối không chia sẻ Root.
- **C**: Tạo User cho người ngoài gây khó khăn trong việc quản lý vòng đời tài khoản.
- **D**: Công khai dữ liệu là rủi ro bảo mật cực lớn.

---

## Question 13

A startup is setting up their first AWS account. They want to prevent unauthorized access even if a password is stolen. What should they implement?

A. IAM Groups

B. Multi-Factor Authentication (MFA)

C. Resource-based policies

D. Server-side encryption

✅ **Correct Answer: B**

### 🧠 Explanation

**MFA** yêu cầu lớp xác thực thứ hai (như mã từ điện thoại), đảm bảo rằng dù kẻ tấn công có mật khẩu vẫn không thể đăng nhập được.

### ❌ Why Other Choices Are Wrong

- **A, C, D**: Không trực tiếp ngăn chặn việc đăng nhập bằng mật khẩu bị đánh cắp.

---

## Question 14

A developer is using the AWS CLI to manage resources. What type of credentials do they need to provide for authentication?

A. Username and Password

B. An IAM Role name and an EC2 instance ID

C. An Access Key ID and a Secret Access Key

D. A digital certificate issued by a local government

✅ **Correct Answer: C**

### 🧠 Explanation

Để sử dụng **AWS CLI** hoặc gọi API bằng code, bạn cần **AWS Access Key**, bao gồm một ID và một Secret Key.

### ❌ Why Other Choices Are Wrong

- **A**: Dùng cho AWS Management Console (giao diện web).
- **B, D**: Không phải thông tin xác thực tiêu chuẩn cho CLI.

---

## Question 15

An application requires sensitive data to be encrypted before it leaves the local corporate network and is uploaded to AWS. Which encryption method is required?

A. Server-side encryption with AWS managed keys

B. Client-side encryption

C. Encryption in transit using TLS only

D. Server-side encryption with customer provided keys

✅ **Correct Answer: B**

### 🧠 Explanation

**Client-side encryption** (Mã hóa phía máy khách) cho phép khách hàng mã hóa dữ liệu cục bộ trước khi gửi lên đám mây, đảm bảo dữ liệu đã được bảo vệ trước khi rời khỏi mạng nội bộ.

### ❌ Why Other Choices Are Wrong

- **A, D**: **Server-side** nghĩa là dữ liệu lên đến AWS rồi mới được mã hóa.
- **C**: TLS bảo vệ đường truyền nhưng dữ liệu khi đến S3 có thể vẫn ở dạng thô nếu không dùng mã hóa trạng thái nghỉ.

---

## Question 16

A manager wants to follow the "Principle of Least Privilege" for a new intern. The intern only needs to read data from a DynamoDB table. What should the manager do?

A. Give the intern the root user credentials for one week

B. Grant the intern "AdministratorAccess" but ask them to be careful

C. Create a policy that only allows "dynamodb:GetItem" and "dynamodb:Scan" on that specific table

D. Add the intern to the "IT" group which has full access to all services

✅ **Correct Answer: C**

### 🧠 Explanation

**Least Privilege** có nghĩa là chỉ cấp đúng hành động cần thiết (`GetItem/Scan`) trên đúng tài nguyên cần thiết (table đó).

### ❌ Why Other Choices Are Wrong

- **A, B, D**: Cấp quá nhiều quyền dư thừa, vi phạm nguyên tắc bảo mật.

---

## Question 17

A cloud architect is designing a system where an AWS Lambda function needs to write logs to CloudWatch. How should the function get the necessary permissions?

A. Hardcode the access keys of the account owner into the function code

B. Assign an IAM execution role to the Lambda function with a policy allowing log writes

C. The Lambda function automatically has all permissions by default

D. Create a resource-based policy on the Lambda function itself to allow it to speak to CloudWatch

✅ **Correct Answer: B**

### 🧠 Explanation

Các dịch vụ như Lambda cần một **Execution Role** (Vai trò thực thi) để có quyền tương tác với các dịch vụ AWS khác một cách an toàn.

### ❌ Why Other Choices Are Wrong

- **A**: Không bảo mật.
- **C**: Mọi thứ trong AWS mặc định là không có quyền.
- **D**: Resource-based policy trên Lambda dùng để cho phép _ai đó gọi Lambda_, không phải cho phép Lambda gọi dịch vụ khác.

---

## Question 18

You have a policy that allows "ec2:\*" actions. You add another statement to the same policy that explicitly denies "ec2:TerminateInstances" if the request does not come from a specific IP address. A user tries to terminate an instance from a different IP address. What is the result?

A. The instance is terminated because the first statement allows all actions

B. The instance is not terminated because the explicit deny overrides the allow

C. The user is asked to log in again from the correct IP address

D. The termination is delayed for 24 hours

✅ **Correct Answer: B**

### 🧠 Explanation

Khi có lệnh **Explicit Deny** (do không khớp IP), nó sẽ ngăn chặn hành động xóa ngay cả khi có lệnh Allow tất cả (`ec2:*`) ở phần khác[cite: 360, 386].

### ❌ Why Other Choices Are Wrong

- **A**: Allow không bao giờ thắng được Deny.
- **C, D**: Không phải cơ chế thực thi của IAM.

---

## Question 19

A company wants to automate their security checks and ensure that any changes to IAM policies are logged. Which AWS service should they use for logging API calls?

A. IAM Identity Center

B. Amazon S3

C. AWS CloudTrail

D. AWS Organizations

✅ **Correct Answer: C**

### 🧠 Explanation

**AWS CloudTrail** ghi lại mọi hoạt động API trong tài khoản, giúp bạn biết "Ai đã làm gì, vào lúc nào" để phuc vụ kiểm toán và bảo mật.

### ❌ Why Other Choices Are Wrong

- **A**: Dùng để quản lý đăng nhập một lần (SSO).
- **B**: Dùng để lưu trữ dữ liệu.
- **D**: Dùng để quản lý nhiều tài khoản AWS tập trung.

---

## Question 20

A developer has left the company. What is the most important security step to take regarding their IAM access?

A. Change the company's root user password

B. Delete the IAM user associated with that developer

C. Update the S3 bucket policies to mention the developer's name

D. Turn off MFA for the entire account

✅ **Correct Answer: B**

### 🧠 Explanation

Xóa **IAM User** của nhân viên đã nghỉ việc là bước quan trọng nhất để thu hồi mọi quyền truy cập và đảm bảo an toàn danh tính cho hệ thống.

### ❌ Why Other Choices Are Wrong

- **A**: Root không liên quan đến User của cá nhân.
- **C**: Không hiệu quả và khó quản lý.
- **D**: Đây là hành động làm giảm tính bảo mật của hệ thống.

---

## 🎯 Weak Topics Analysis

- Bạn không có chủ đề yếu. Các câu hỏi về **Deny vs Allow**, **Roles vs Users**, và **Encryption** bạn đều xử lý rất tốt. Hãy tiếp tục duy trì phong độ này cho các module tiếp theo!
