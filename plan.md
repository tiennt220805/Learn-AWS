# 📚 CHECKLIST ÔN THI AWS CLOUD ARCHITECTING

> **Hướng dẫn:** Các chủ đề được sắp xếp theo trình tự Module từ 1 đến 17. Đánh dấu vào ô vuông sau khi bạn hoàn thành việc ôn tập Module tương ứng.

---

### 1. Architecture (Kiến trúc tổng quan)

- [x] **Module 2:** Introducing Cloud Architecting (Các nguyên tắc thiết kế)
- [ ] **Module 16:** Planning for Disaster (Lập kế hoạch khắc phục thảm họa)

**_Dịch vụ trọng tâm_**: Well-Architected Framework, High Availability, Disaster Recovery

### 2. Security/IAM (Bảo mật và Định danh)

- [ ] **Module 3:** Securing Access (IAM cơ bản)
- [ ] **Module 9:** Securing User, Application, and Data Access (Bảo mật nâng cao)

**_Dịch vụ trọng tâm_**: IAM Users/Roles/Policies, KMS, Shield, WAF

### 3. Storage (Lưu trữ)

- [ ] **Module 4:** Adding a Storage Layer with Amazon S3 (S3, EBS, EFS)

**_Dịch vụ trọng tâm_**: Amazon S3, EBS, EFS

### 4. Compute (EC2/ASG/LB - Tính toán & Cân bằng tải)

- [ ] **Module 5:** Adding a Compute Layer Using Amazon EC2
- [ ] **Module 10:** Implementing Monitoring, Elasticity, and High Availability (Auto Scaling & ELB)

**_Dịch vụ trọng tâm_**: EC2, Auto Scaling, Elastic Load Balancing (ALB/NLB)

### 5. Database (Cơ sở dữ liệu)

- [ ] **Module 6:** Adding a Database Layer (RDS, DynamoDB, Aurora)

**_Dịch vụ trọng tâm_**: RDS, DynamoDB, Aurora

### 6. Networking/VPC (Mạng và VPC)

- [ ] **Module 7:** Creating a Networking Environment (VPC, Subnet)
- [ ] **Module 8:** Connecting Networks (Peering, VPN, Direct Connect)

**_Dịch vụ trọng tâm_**: VPC, Subnets, Route Tables, NAT Gateway, VPN, Direct Connect

### 7. IaC + Monitoring (Tự động hóa & Giám sát)

- [ ] **Module 10:** Implementing Monitoring (CloudWatch - tiếp nối phần Compute)
- [ ] **Module 11:** Automating Your Architecture (CloudFormation)

**_Dịch vụ trọng tâm_**: IaC: CloudFormation; Monitoring: CloudWatch, CloudTrail

### 8. CDN (Mạng phân phối nội dung)

- [ ] **Module 12:** Caching Content (Amazon CloudFront)

**_Dịch vụ trọng tâm_**: Amazon CloudFront (Edge locations)

### 9. Messaging (Truyền tin & Giải nối kết)

- [ ] **Module 13:** Building Decoupled Architectures (SQS, SNS)

**_Dịch vụ trọng tâm_**: SQS, SNS, Amazon MQ (Decoupling)

### 10. Serverless/Lambda (Kiến trúc không máy chủ)

- [ ] **Module 14:** Building Serverless Architectures and Microservices (AWS Lambda)

**_Dịch vụ trọng tâm_**: AWS Lambda, API Gateway

### 11. Containers (Container hóa)

- [ ] **Module 14:** Building Serverless Architectures and Microservices (ECS, EKS, Fargate)

**_Dịch vụ trọng tâm_**: ECS, EKS, Fargate (thường đi kèm Microservices)

### 12. BigQuery (Trong AWS là Athena/Redshift)

- [ ] **Module 15:** Data Engineering Patterns (Truy vấn dữ liệu lớn)

**_Dịch vụ trọng tâm_**: Đây là tên dịch vụ của Google Cloud. Trong AWS, tương ứng là Amazon Athena hoặc Redshift

### 13. Big Data (Dữ liệu lớn)

- [ ] **Module 15:** Data Engineering Patterns (EMR, Glue, Kinesis)

**_Dịch vụ trọng tâm_**: EMR, Glue, Kinesis, Lake Formation

---

### 🏁 Tổng kết & Luyện thi

- [ ] **Module 17:** Bridging to Certification (Ôn tập tổng hợp & Câu hỏi mẫu)

## Cách sử dụng Checklist này hiệu quả:

- **Ôn tập theo chiều dọc**: Bắt đầu từ mục số 1 (Architecture) vì nó cung cấp cái nhìn tổng thể về các trụ cột của AWS.
- **Lưu ý sự giao thoa**:
  - **Module 10** xuất hiện ở cả mục Compute (về tính đàn hồi) và Monitoring (về CloudWatch), bạn nên check cả hai sau khi học xong module này.
  - **Module 14** bao gồm cả Serverless và Containers, đây là xu hướng kiến trúc hiện đại.
- **BigQuery vs AWS**: Khi ôn mục số 12, hãy nhớ tập trung vào **Amazon Redshift** (Kho dữ liệu) và **Amazon Athena** (Truy vấn trực tiếp trên S3) vì đây là các dịch vụ tương đương của AWS.
