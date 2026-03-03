<div align="center">
  <h1>Introducing Cloud Architecting</h1>
  <small>
    <strong>Author:</strong> Nguyen Thanh Tien
  </small> <br />
  <sub>Last Updated: March 2026</sub>
</div>

---

# Module 02 - Introducing Cloud Architecting

## Overview

This module introduces the fundamentals of **cloud architecting** and best practices for designing architectures on AWS.

Topics covered:

- Cloud architecture fundamentals
- AWS Well-Architected Framework
- AWS best practices
- AWS Global Infrastructure

## Module Objectives

After completing this module, I should be able to:

- Define cloud architecture
- Understand the AWS Well-Architected Framework
- Apply AWS best practices
- Choose appropriate AWS resource locations

---

# 1. Cloud Architecting

## What is Cloud Architecture?

Cloud architecture is the practice of **_applying cloud characteristics_** to a solution that uses cloud services and features to meet an organization's **_technical needs and business use cases_**.

You can use AWS services to create highly
**_available, scalable, and reliable_** architectures.

## Role of a Cloud Architect

A Cloud Architect is responsible for managing an organization's cloud computing architecture through three primary phases:

- **Plan**: Set technical cloud strategy with business leads and analyze solutions for business needs
- **Research**: Investigate cloud service specifications, review existing workload architectures, and design prototype solutions
- **Build**: Design the transformation roadmap (milestones/owners) and manage adoption/migration

---

# 2. AWS Well-Architected Framework

## Six Pillars

<p align="center">
  <img src="https://d2908q01vomqb2.cloudfront.net/972a67c48192728a34979d9a35164c1295401b71/2024/09/30/well-architected-framework.png" style="width:90%;" alt="Variables">
</p>

### 1. Operational Excellence

- **_Monitor systems_** to deliver business value
- Continually **_improve process_**
- **_View the entire workload as code_**

### 2. Security

- Strong identity foundation
- **_Apply security at all layers_**
- Maintain traceability
- Implement risk assessment and mitigation strategies

### 3. Reliability

- Recover quickly from failures
- Dynamically meet compute demand
- Mitigate (_reduce_) disruptions

### 4. Performance Efficiency

- Use efficient resources
- Democratize (_dân chủ hóa_) advanced technologies

### 5. Cost Optimization

- Measure efficiency
- Avoiding unnecessary costs
- **_Adopt the right consumption model_**
- **_Use managed services_**

### 6. Sustainability

- **_Maximize utilization_**
- Establish goals
- **_Choose efficient hardware/software_**
- Minimizing environmental impacts

---

# 3. Best Practices for Building Solutions

### 1. Design Trade-offs

Architectures always involve trade-offs.

Examples:

- Performance vs Cost
- Consistency vs Latency
- Speed vs Cost

Design decisions should be based on empirical data (_dữ liệu thực nghiệm_)

### 2. Scalability

Architectures should handle increasing demand.

Example:

- Auto Scaling automatically adds servers

### 3. Automation

Use **_Infrastructure as Code (IaC)_** templates to rapidly deploy duplicate environments and reduce manual configuration errors

### 4. Treating resources as disposable (Tài nguyên dùng 1 lần rồi bỏ)

Resources should be easy to replace.

Best practices:

- Automatically create resources
- Stop unused resources
- Replace instead of fixing

### 5. Loose Coupling

Design independent components

Best practices:

- Use **_Elastic Load Balancing (ELB)_** to decouple web servers from application servers to prevent a single failure from cascading

### 6. Designing services, not servers

Use managed AWS services when possible.

Examples:

- Amazon S3 for storage
- Lambda for compute
- SQS for messaging

### 7. Choosing the right database solution

Choose databases based on requirements:

- Storage size
- Read and write patterns
- Latency requirements
- Number of users

### 8. Avoid Single Points of Failure

Systems should continue working even if a component fails

Example:

- Primary database + backup database

### 9. Cost Optimization

Improve cost efficiency:

- Use correct instance size
- Stop unused resources
- Use managed services

### 10. Use Caching

Caching improves performance and reduces cost

Example:

- CloudFront caching service

### 11. Security Best Practices

Security should be implemented at every layer

Best practices:

- **_Least privilege access_**
- Encryption
- Logging
- **_MFA (Multi-factor authentication)_**

--

# 4. AWS Global Infrastructure

AWS Global Infrastructure consists of:

- Regions
- Availability Zones
- Edge Locations

<p align="center">
  <img src="https://cloudyforsure.com/wp-content/uploads/2021/10/image-1-1024x757.png" style="width:90%;" alt="Variables">
</p>

## 1. Regions

A Region is a geographical area.

Each Region contains multiple Availability Zones (at least 2 AZs).

Choose Regions based on:

- Latency
- Compliance
- Cost

## 2. Availability Zones

An Availability Zone:

- Contains one or more data centers
- Is isolated from failures
- Physically separate and have redundant power and networking

## 3. Data Centers

- Data centers store and process data
- Each data center contains thousands of servers

## 4. Points of Presence (PoPs)

<p align="center">
  <img src="https://docs.aws.amazon.com/images/AmazonCloudFront/latest/DeveloperGuide/images/regional-edge-caches.png" style="width:90%;" alt="Variables">
</p>

- **_Edge Locations_**: Data centers located close to customers to deliver services with the lowest latency possible.
- **_Regional Edge Cache_**: Sits between the origin server and edge location to hold content longer.

---

<br />

# Module 02 – Knowledge Check & Practice Questions

---

# Part 1 – Knowledge Check Answers

---

## 1. Which is the best definition of cloud architecture?

A. Relocating traditional on-premises data centers to vendor-managed data centers.

B. Designing applications in shared cloud infrastructure using virtual machines and fault-tolerant storage.

C. Applying cloud characteristics to a solution that uses cloud services and features to meet technical and business requirements.

D. Combining frontend and backend components to create scalable web services.

**Correct Answer:** C

**Explanation:**

Cloud architecture is the practice of applying cloud characteristics to a solution that uses cloud services and features to meet both technical and business requirements.

It is not simply moving servers to the cloud, but designing scalable and reliable solutions using cloud services.

---

## 2. The AWS Well-Architected Framework has six pillars. Three of the pillars are security, operational excellence, and sustainability. What are two of the other pillars? (Select TWO.)

A. Cost optimization

B. Governance

C. Reliability

D. Risk management

E. Privacy

**Correct Answers:** A, C

**Explanation:**

The six pillars of the AWS Well-Architected Framework are:

- Operational Excellence
- Security
- Reliability
- Performance Efficiency
- Cost Optimization
- Sustainability

---

## 3. Which actions are consistent with the Operational Excellence pillar? (Select TWO.)

A. Ensure operations personnel document infrastructure changes.

B. Apply software engineering principles to infrastructure as code.

C. Review and improve processes and procedures continuously.

D. Manage hardware lifecycle.

E. Evaluate organizational skill gaps.

**Correct Answers:** B, C

**Explanation:**

The Operational Excellence pillar focuses on:

- Treating infrastructure as code
- Applying software engineering practices
- Continuous improvement of processes

---

## 4. Which design most closely follows AWS best practices for a multi-tier application?

A. Full mesh communication between web and application servers.

B. Combine web and application tiers in the same instance.

C. Dedicated application server per web server.

D. Web tier communicates with application tier through Elastic Load Balancing (ELB).

**Correct Answer:** D

**Explanation:**

This design follows the **Loose Coupling** best practice.

Using Elastic Load Balancing separates tiers and improves scalability and fault tolerance.

---

## 5. Which process for handling server failures most closely follows AWS best practices?

A. Operations staff detects failure and triggers automation.

B. Operations staff detects failure and manually provisions a server.

C. Amazon CloudWatch detects failure and automatically provisions a new server.

D. CloudWatch detects failure and notifies administrator for manual provisioning.

**Correct Answer:** C

**Explanation:**

AWS best practice is **Automation**.

CloudWatch monitoring combined with automated provisioning (such as Auto Scaling) allows automatic recovery without manual intervention.

---

## 6. Moving to the cloud to increase cost efficiency. Which approach follows AWS best practices?

A. Provision servers when needed and stop services when not in use.

B. Run servers continuously.

C. Maintain on-premises infrastructure.

D. Replicate on-premises architecture in the cloud.

**Correct Answer:** A

**Explanation:**

Cost optimization in AWS depends on flexible resource usage.

Stopping unused resources significantly reduces costs.

---

## 7. Global users accessing read-only data in S3. Which design decision is best?

A. Use the Region with lowest average latency.

B. Use an S3 bucket near headquarters and deliver content through CloudFront.

C. Use a bucket near headquarters only.

D. Replicate buckets worldwide.

**Correct Answer:** B

**Explanation:**

Using **Amazon CloudFront caching** improves performance and reduces latency for global users.

It avoids unnecessary data replication across Regions.

---

## 8. Granting a consultant temporary access (1 day) to a large S3 object

A. Create a presigned URL that expires in 24 hours.

B. Create an IAM user.

C. Copy object to a public bucket.

D. Make the bucket public.

**Correct Answer:** A

**Explanation:**

This follows the **Principle of Least Privilege**.

Temporary presigned URLs provide secure access without permanent credentials.

---

## 9. Which are main considerations that influence which AWS Regions to use? (Select TWO.)

A. Security and access control

B. Latency reduction for end users

C. Compliance with laws and regulations

D. Application resiliency

E. Natural disaster protection

**Correct Answers:** B, C

**Explanation:**

Region selection is mainly based on:

- Data residency and compliance requirements
- Latency reduction for end users

---

## 10. Which are main considerations that influence which Availability Zones to use? (Select TWO.)

A. Compliance with regulations

B. Security and access control

C. Latency reduction

D. Protection against localized disasters

E. Application resiliency during failures

**Correct Answers:** D, E

**Explanation:**

Availability Zones provide **fault isolation**.

Deploying across multiple AZs improves reliability and protects against localized failures.

---

# Part 2 – Practice Questions & Explanations

---

## Question 1: What is the primary focus of the Sustainability pillar in the AWS Well-Architected Framework?

A. Minimizing the cost of running cloud resources  
B. Maximizing the speed of application deployment  
C. Minimizing the environmental impacts of running cloud workloads  
D. Ensuring the security of user data

**Correct Answer:** C

**Explanation:**

The **Sustainability pillar** focuses on minimizing the environmental impact of running cloud workloads.

This includes:

- Maximizing resource utilization
- Using efficient hardware and services
- Reducing overall energy consumption

---

## Question 2: Which AWS service is primarily used to implement scalability by automatically adding or removing EC2 instances?

A. Amazon S3  
B. Amazon EC2 Auto Scaling  
C. Amazon CloudFront  
D. AWS IAM

**Correct Answer:** B

**Explanation:**

**Amazon EC2 Auto Scaling** automatically adjusts the number of EC2 instances based on demand.

This allows architectures to scale dynamically and maintain performance during traffic changes.

---

## Question 3: What does Infrastructure as Code (IaC) allow you to do?

A. Write software only for servers  
B. Provision and manage computing infrastructure using configuration files  
C. Replace the need for internet connectivity  
D. Manually configure servers through the command line

**Correct Answer:** B

**Explanation:**

**Infrastructure as Code (IaC)** allows infrastructure to be provisioned and managed using configuration files instead of manual processes.

Benefits include:

- Repeatable deployments
- Faster provisioning
- Reduced configuration errors

---

## Question 4: In AWS architecture, what is a Region?

A. A single data center  
B. A physical server rack  
C. A geographical area consisting of multiple Availability Zones  
D. A specialized hardware protocol

**Correct Answer:** C

**Explanation:**

An **AWS Region** is a geographical area that contains multiple **Availability Zones (AZs)**.

Regions are selected based on:

- Latency requirements
- Compliance requirements
- Service availability

---

## Question 5: Which best practice suggests that you should not limit your infrastructure to servers?

A. Scaling out  
B. Designing services, not servers  
C. Caching  
D. Disposable resources

**Correct Answer:** B

**Explanation:**

The **Design services, not servers** best practice encourages the use of AWS managed services instead of relying only on servers.

Examples include:

- Amazon S3 for storage
- AWS Lambda for compute
- Amazon SQS for messaging

---

## Question 6: What is the main advantage of an Availability Zone being physically separate from others?

A. It reduces electricity cost  
B. It provides fault isolation against localized disasters  
C. It allows faster hardware delivery  
D. It simplifies console usage

**Correct Answer:** B

**Explanation:**

**Availability Zones** are physically separated to provide **fault isolation**.

This protects applications against localized failures such as:

- Power outages
- Fires
- Floods
- Network disruptions

---

## Question 7: Which phase of the Cloud Architect role involves investigating cloud service specifications?

A. Plan  
B. Build  
C. Research  
D. Deploy

**Correct Answer:** C

**Explanation:**

During the **Research phase**, a cloud architect:

- Investigates AWS service specifications
- Evaluates workload requirements
- Reviews existing architectures
- Designs prototype solutions

---

## Question 8: What is an Edge Location primarily used for?

A. Running complex database queries  
B. Storing long-term backups  
C. Caching content closer to end users to reduce latency  
D. Hosting AWS headquarters

**Correct Answer:** C

**Explanation:**

An **Edge Location** is a Point of Presence (PoP) used by services such as **Amazon CloudFront**.

Edge Locations cache content closer to users to:

- Reduce latency
- Improve performance
- Reduce origin server load

---

## Question 9: Which pillar emphasizes traceability and applying security at all layers?

A. Reliability  
B. Security  
C. Operational Excellence  
D. Performance Efficiency

**Correct Answer:** B

**Explanation:**

The **Security pillar** includes principles such as:

- Maintaining traceability through logging and monitoring
- Applying security at all layers
- Implementing identity and access management
- Protecting data in transit and at rest

---

## Question 10: What is a Local Zone in AWS?

A. A private cloud for home use  
B. An extension of a Region that places resources closer to users  
C. A deprecated Region  
D. A physical office hard drive

**Correct Answer:** B

**Explanation:**

An **AWS Local Zone** is an extension of an AWS Region that places compute, storage, and other services closer to end users.

Local Zones are designed for:

- Low-latency workloads
- Real-time applications
- Media processing
- Gaming workloads

---

## Question 11: The practice of "Assume everything fails" leads to which design decision?

A. Using a single server  
B. Avoiding single points of failure  
C. Stopping automation  
D. Using on-premises only

**Correct Answer:** B

**Explanation:**

The principle **"Assume everything fails"** leads to architectures that avoid **single points of failure**.

This includes:

- Multi-AZ deployments
- Redundant resources
- Automated failover

---

## Question 12: Which AWS tool helps compare workloads against AWS architectural best practices?

A. Amazon S3  
B. AWS Well-Architected Tool  
C. AWS Marketplace  
D. Amazon CloudWatch

**Correct Answer:** B

**Explanation:**

The **AWS Well-Architected Tool** helps evaluate workloads against AWS architectural best practices.

It provides:

- Architecture reviews
- Improvement recommendations
- Action plans

---

## Question 13: What is the benefit of loosely coupled components?

A. Components depend on each other  
B. Failures do not affect the entire system  
C. Fewer components are required  
D. Updates become manual

**Correct Answer:** B

**Explanation:**

**Loose coupling** means components operate independently.

Benefits include:

- Improved fault tolerance
- Easier scaling
- Easier maintenance

---

## Question 14: Which principle encourages automating deployment of identical resources?

A. Security at all layers  
B. Treating resources as disposable  
C. Measuring efficiency  
D. Data replication

**Correct Answer:** B

**Explanation:**

**Treating resources as disposable** means resources should be easy to replace.

This encourages:

- Automated provisioning
- Consistent environments
- Faster recovery

---

## Question 15: What is a key consideration when choosing a database solution on AWS?

A. Logo color  
B. Distance to AWS headquarters  
C. Latency and durability requirements  
D. Number of employees

**Correct Answer:** C

**Explanation:**

Database selection should be based on workload requirements such as:

- Latency requirements
- Durability requirements
- Access patterns
- Query patterns

---

## Question 16: "Democratizing advanced technologies" belongs to which pillar?

A. Cost Optimization  
B. Performance Efficiency  
C. Sustainability  
D. Operational Excellence

**Correct Answer:** B

**Explanation:**

The **Performance Efficiency pillar** promotes **democratizing advanced technologies**.

This means enabling organizations to use advanced technologies such as:

- Machine Learning
- Big Data analytics
- Serverless computing

---

## Question 17: What does Scaling Out involve?

A. Increasing CPU power of one instance  
B. Adding more instances  
C. Moving to another Region  
D. Shutting down servers

**Correct Answer:** B

**Explanation:**

**Scaling Out (Horizontal Scaling)** means adding more instances of the same type to handle increased workload demand.

This improves:

- Availability
- Scalability
- Fault tolerance

---

## Question 18: Which AWS infrastructure component is used by Amazon CloudFront?

A. Availability Zones  
B. Edge Locations  
C. Data Centers  
D. Local Zones

**Correct Answer:** B

**Explanation:**

**Amazon CloudFront** uses **Edge Locations** to deliver content with low latency.

Edge Locations cache content closer to end users.

---

## Question 19: Infrastructure as Code is intended to reduce what?

A. RAM usage  
B. Configuration errors  
C. AWS accounts  
D. Internet speed

**Correct Answer:** B

**Explanation:**

Infrastructure as Code reduces **configuration errors** caused by manual provisioning.

It ensures:

- Consistent environments
- Reliable deployments
- Repeatable configurations

---

## Question 20: What was Amazon's early 2000s challenge that led to AWS?

A. Lack of customers  
B. Building ecommerce services for third-party sellers  
C. Data loss  
D. Moving headquarters

**Correct Answer:** B

**Explanation:**

In the early 2000s, Amazon needed to build an **ecommerce platform for third-party sellers**.

This challenge led to the development of reusable infrastructure components that eventually became **Amazon Web Services (AWS)**.
