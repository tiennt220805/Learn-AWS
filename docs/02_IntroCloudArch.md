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
