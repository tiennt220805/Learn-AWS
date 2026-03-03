<div align="center">
  <h1>Securing Access</h1>
  <small>
    <strong>Author:</strong> Nguyen Thanh Tien
  </small> <br />
  <sub>Last Updated: March 2026</sub>
</div>

---

# Module 3 – Securing Access

## Overview

This module explains how to **secure access to AWS resources** by using AWS security principles and **AWS Identity and Access Management (IAM)**.

After completing this module, I should be able to:

- Describe AWS security principles
- Understand the AWS Shared Responsibility Model
- Explain IAM users, groups, and roles
- Understand IAM policies and permissions
- Analyze IAM policy structures

---

# Part 1 – Security Principles

## 1. AWS Shared Responsibility Model

<p align="center">
  <img src="https://d1.awsstatic.com/onedam/marketing-channels/website/aws/en_US/product-categories/security-identity-compliance/compliance/approved/images/7a404923-5572-409c-b30e-6d44706bcd89.094727e5c591e9a96edf10578d0bc1172d9e4553.jpeg" style="width:90%;" alt="Variables">
</p>

AWS security follows the **Shared Responsibility Model**, where both AWS and customers share security responsibilities.

### Security **OF** the Cloud (AWS Responsibility)

AWS is responsible for protecting the infrastructure that runs AWS services:

- Data centers
- Hardware
- Networking
- Global infrastructure
- Regions, Availability Zones and Edge Locations

Examples:

- Physical security of data centers
- Hardware maintenance
- Network infrastructure
- Managed services infrastructure

### Security **IN** the Cloud (Customer Responsibility)

Customers are responsible for protecting what they deploy on AWS:

- Customer data
- Applications
- IAM configuration
- Operating systems
- Network configuration
- Encryption settings

Examples:

- Configuring security groups
- Managing IAM users and roles
- Encrypting data
- Patching EC2 instances

> AWS secures the infrastructure, customers secure their workloads.

## 2. Security Pillar of the Well-Architected Framework

Security is one of the pillars of the **AWS Well-Architected Framework**.

Security focuses on:

- **_Implement a strong identity foundation_**: Centralize identity management and rely on a "least privilege" model.

- **_Maintain traceability_**: Monitor, alert, and audit actions and changes to your environment in real-time.

- **_Apply security at all layers_**: Use multiple defense-in-depth controls (e.g., VPC, subnets, and individual resource policies).

- **_Protect data in transit and at rest_**: Use encryption to ensure data cannot be read by unauthorized parties.

## 3. Security Design Principles

### Implement a Strong Identity Foundation

Use IAM to control access.

Examples:

- IAM users
- IAM roles
- IAM policies
- Multi-Factor Authentication (MFA)

---

### Apply the Principle of Least Privilege

Grant only the permissions required to perform a task.

Best practices:

- Start with minimum permissions
- Add permissions when needed
- Remove unused permissions

Example:

- Developer → Limited EC2 access
- Administrator → Full access

Least privilege reduces security risks.

---

### Protect Data in Transit

Data moving across networks should be encrypted.

Examples:

- HTTPS
- TLS encryption

Client → TLS → AWS Service

---

### Protect Data at Rest

Data stored in AWS should be encrypted.

Two main methods:

---

#### Client-Side Encryption

The client encrypts data before uploading.

Process:

1. Client encrypts data
2. Upload encrypted data
3. Download encrypted data
4. Client decrypts data

Advantages:

- Maximum control over encryption

Disadvantages:

- Customer must manage encryption keys

---

#### Server-Side Encryption

AWS encrypts data automatically.

Process:

1. Client uploads data
2. AWS encrypts data
3. AWS decrypts data when requested

Advantages:

- Easy to use
- Fully managed by AWS

---

### Maintain Traceability

Monitor user actions.

Tools:

- AWS CloudTrail
- Amazon CloudWatch Logs

---

### Automate Security

Use automation to improve security.

Examples:

- Automatic patching
- Automatic resource replacement
- Security monitoring

---

### Prepare for Security Events

Prepare for incidents such as:

- Data breaches
- System failures
- Security attacks

Examples:

- Backup strategies
- Incident response plans

---

# Part 2 – Authentication and Authorization

## 1. Authentication vs Authorization

### Authentication

Authentication answers:

> Who is requesting access?

Authentication verifies identity using:

- Username and password
- Access keys
- Certificates

Users can be:

- Humans
- Applications
- AWS services

### Authorization

Authorization answers:

> What actions are allowed?

Authorization determines:

- Allow access
- Deny access

Authorization is controlled by **IAM policies**.

## 2. AWS Identity and Access Management (IAM)

IAM is the main AWS service used to manage access.

IAM capabilities:

- Manage users and roles
- Control permissions
- Enable MFA
- Provide federated access
- Fine-grained access control

IAM integrates with almost all AWS services.

## 3. IAM Terminology

### IAM Resource

Objects stored in IAM:

- Users
- Groups
- Roles
- Policies
- Identity providers

### IAM Entity

Entities used for **_authentication_**:

- Users
- Roles

### IAM Identity

Entities that can be **_authorized_**:

- User
- Group
- Role

### Principal

A **Principal** is an entity that makes a request to AWS.

Examples:

- IAM user
- IAM role
- Application

## 4. IAM Components

### IAM User

Represents:

- A person
- An application

Authentication methods:

- Username and password
- Access keys

Example:

- Developer account

### IAM Group

Collection of IAM users.

All users in a group share permissions.

Example:

Group: Developers

Permissions:

- EC2 access
- S3 access

### IAM Role

An IAM role:

- Provides **temporary credentials**
- Is not tied to a specific user
- Can be assumed by users or services

Common uses:

- EC2 accessing S3
- Cross-account access
- Mobile applications

Roles improve security by avoiding long-term credentials.

### IAM Policy

IAM policies define permissions.

Policies specify:

- Allowed actions
- Denied actions
- Resources
- Conditions

Policies use **JSON format**

> Best Practice: Attach IAM policies to IAM groups rather than individual users. This makes management easier as you can simply move users in and out of groups to change their permissions

## 5. IAM Credentials

### AWS Management Console

Requires:

- Username
- Password

---

### AWS CLI and SDK

Requires:

- Access Key ID
- Secret Access Key

These are called **AWS Access Keys**.

---

# Part 3 – IAM Security Best Practices

## Enable MFA

Multi-Factor Authentication increases security.

Example:

- Password + mobile verification code

## Use Temporary Credentials

Use IAM roles instead of access keys when possible.

Advantages:

- More secure
- Automatic expiration

## Rotate Access Keys

Regularly update access keys.

Example:

- Every 90 days

## Use Strong Passwords

Best practices:

- Long passwords
- Complex characters

## Enable CloudTrail

AWS CloudTrail records API activity.

Useful for:

- Auditing
- Security investigations

## Protect the Root User

The root user has **full access** to the AWS account.

Best practices:

- Enable MFA
- **_Do not use root daily_**
- Store credentials securely

---

# Part 4 – IAM Users and Roles Best Practices

## Use Groups Instead of Individual Policies

Best practice:

Attach policies to groups instead of individual users.

Example:

Group: Developers

Policy:

- EC2FullAccess

Users:

- Alice
- Bob
- Charlie

Advantages:

- Easier management
- Scalable access control

## IAM Roles Use Cases

### EC2 Instance Role

Allows EC2 instances to access AWS services securely.

Example:

EC2 → S3 access without access keys

### Cross-Account Role

Allows users in one AWS account to access another account.

### Mobile Application Role

Allows mobile applications to use temporary credentials.

---

# Part 5 – Authorizing Users

## 1. IAM Policies

Policies define permissions.

Policies specify:

- Actions
- Resources
- Allow or deny

Policies follow the **least privilege principle**.

---

## 2. Types of Policies

### Identity-Based Policies

Attached to:

- User
- Group
- Role

Question answered:

> What can this identity access?

---

### Resource-Based Policies

Attached to:

- S3 buckets
- SQS queues
- Lambda functions

Question answered:

> Who can access this resource?

---

# Part 6 – IAM Policy Structure

IAM policies use **JSON format**.

Main elements:

## Version

Version of the policy language that you want to use.

Example:

```
"Version": "2012-10-17"
```

## Statement

Defines what is allowed or denied based on conditions

## Effect

Permission result:

- Allow
- Deny

Example:

```
"Effect": "Allow"
```

## Principal

Defines who is allowed.

Used in resource-based policies.

Example:

```
"Principal": {
    "AWS": "123456789012"
}
```

## Action

Action that is allowed or denied

Example:

```
"Action": "s3:GetObject"
```

## Resource

Resource or resources that the action applies to

Example:

```
"Resource": "arn:aws:sqs:us-west-2:123456789012:queue1"
```

## Condition

Optional restrictions.

Examples:

- IP address restriction
- Time restriction

Example:

```
"Condition": {
    "IpAddress": {
        "aws:SourceIp": "192.168.1.0/24"
    }
}
```

## Example

### Identity-Based Policy

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-photo-bucket/*"
    }
  ]
}
```

Policy này sẽ gắn vào IAM User hoặc IAM Role

Trong **_Amazon Web Services_**, policy này có thể attach vào:

- IAM User
- IAM Role
- IAM Group

**_Ý nghĩa_**: User (hoặc Role) này được phép đọc file trong bucket my-photo-bucket

---

### Resource-Based Policy

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/AnNhien"
      },
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-photo-bucket/*"
    }
  ]
}
```

Policy này sẽ gắn vào S3 Bucket

**_Ý nghĩa_**: Bucket my-photo-bucket cho phép user AnNhien đọc file

---

---

# Part 2 – Model Knowledge Check

## Knowledge Check Answers & Explanations

### Question 1

**Which statement reflects a design principle of the Security pillar of the Well-Architected Framework?**

A. Ensure that staff are actively monitoring potential risks manually.

B. Decentralize privilege management.

C. Apply security at all layers of an architecture.

D. Do not deploy a solution to production until you're certain that no security risks exist.

**Correct Answer:** C

**Explanation:**

**Applying security at all layers** is a core design principle of the **Security pillar** of the AWS Well-Architected Framework.

Instead of protecting only the outer perimeter, security controls should be implemented at multiple layers, including:

- Network layer
- Compute layer
- Application layer
- Data layer

This approach is known as **defense in depth**.

---

### Question 2

**Which statements about responsibility are accurate based on the AWS Shared Responsibility Model? (Select TWO.)**

A. AWS is responsible for host-based firewall configurations.

B. AWS is responsible for the configuration of security groups.

C. Customers are responsible for the installation, maintenance, and decommissioning of the hardware that they use in the AWS data center.

D. AWS is responsible for the physical security of data centers.

E. Customers are responsible for managing their user data.

**Correct Answers:** D & E

**Explanation:**

According to the **AWS Shared Responsibility Model**:

AWS is responsible for **security OF the cloud**, including:

- Physical data center security
- Hardware infrastructure
- Networking infrastructure
- Global infrastructure

Customers are responsible for **security IN the cloud**, including:

- User data
- IAM configuration
- Operating systems
- Security groups
- Applications

---

### Question 3

**Which options are characteristics of the principle of least privilege? (Select TWO.)**

A. Craft security policies that limit access to specific tasks.

B. Monitor actions and changes.

C. Grant access only as needed.

D. Use encryption.

E. Always use groups.

**Correct Answers:** A & C

**Explanation:**

The **Principle of Least Privilege** requires granting only the minimum permissions necessary to perform a task.

Best practices include:

- Creating policies that limit access to specific actions
- Granting permissions only when required
- Removing unused permissions

Least privilege reduces the risk of unauthorized access.

---

### Question 4

**Which statement about AWS Identity and Access Management (IAM) is true?**

A. IAM provides an extra layer of security by offering anomaly detection on resources.

B. With IAM, you can manage encryption for items that require encryption at rest.

C. With IAM, you can grant principals granular access to resources.

D. IAM provides an audit trail of who performed an action, what action they performed, and when they performed it.

**Correct Answer:** C

**Explanation:**

AWS Identity and Access Management (IAM) allows you to grant **granular permissions** to **principals**, including:

- IAM users
- IAM roles
- AWS services
- Applications

Permissions specify:

- Which actions are allowed
- Which resources can be accessed
- Under which conditions access is allowed

Audit trails are typically provided by **AWS CloudTrail**, not IAM.

---

### Question 5

**Which statements describe AWS Identity and Access Management (IAM) roles? (Select TWO.)**

A. They provide permanent security credentials.

B. They are uniquely associated with an individual.

C. They provide temporary security credentials.

D. Individuals, applications, and services can assume roles.

E. They can only be used by accounts that are associated with the person who creates the role.

**Correct Answers:** C & D

**Explanation:**

IAM roles:

- Provide **temporary security credentials**
- Are **not permanently associated** with a specific user
- Can be assumed by:
  - Users
  - Applications
  - AWS services

Roles improve security by eliminating the need for long-term credentials.

---

### Question 6

**Which statement reflects a best practice for the root user on an AWS account?**

A. To avoid getting locked out of the account, do not enable multi-factor authentication (MFA) on the root account.

B. Create two root users with separate credentials and distribute them to two different individuals.

C. Create an admin user and perform most admin tasks with this user instead of the root user.

D. Remove unneeded permissions from the root user account.

**Correct Answer:** C

**Explanation:**

AWS best practices recommend:

- Using the **root user only for critical tasks**
- Creating an **IAM administrator user**
- Performing daily administrative tasks using the admin user instead of the root account

Additional best practices include:

- Enabling MFA on the root account
- Storing root credentials securely

---

### Question 7

**How does AWS Identity and Access Management (IAM) evaluate a policy?**

A. An explicit deny statement does not override an explicit allow statement.

B. It checks for explicit deny statements before it checks for explicit allow statements.

C. It checks for explicit allow statements before it checks for explicit deny statements.

D. If the policy doesn't have any explicit deny statements or explicit allow statements, users have access by default.

**Correct Answer:** B

**Explanation:**

IAM permission evaluation logic works in this order:

1. Check for **Explicit Deny**

   If found → Access denied

2. Check for **Explicit Allow**

   If found → Access allowed

3. Otherwise → **Implicit Deny**

Key rule:

**Explicit Deny always overrides Allow.**

---

### Question 8

**Which statement about AWS Identity and Access Management (IAM) policies is accurate?**

A. Resource-based policies allow access by default.

B. Identity-based policies can only be attached to a single entity.

C. Resource-based policies are attached to a user, group, or role.

D. Identity-based policies are attached to a user, group, or role.

**Correct Answer:** D

**Explanation:**

**Identity-based policies** are attached to:

- IAM users
- IAM groups
- IAM roles

They define:

> What actions an identity can perform on which resources.

**Resource-based policies** are attached directly to resources such as:

- Amazon S3 buckets
- SQS queues
- Lambda functions

---

### Question 9

**Which AWS Identity and Access Management (IAM) policy element includes information about whether to allow or deny a request?**

A. Condition

B. Effect

C. Action

D. Principal

**Correct Answer:** B

**Explanation:**

The **Effect** element specifies whether a request is:

- **Allowed**
- **Denied**

Example:

```
"Effect": "Allow"
```

or

```
"Effect": "Deny"
```

The Effect element determines the final permission result.

---

### Question 10

**Which option accurately describes the statement element in an AWS Identity and Access Management (IAM) policy?**

A. The statement element contains other elements that together define what is allowed or denied.

B. The statement element does not apply to identity-based policies.

C. The statement element is an optional part of an IAM policy.

D. A policy can only have one statement element.

**Correct Answer:** A

**Explanation:**

The **Statement** element is the main building block of an IAM policy.

Each Statement contains elements such as:

- Effect
- Action
- Resource
- Principal
- Condition

A policy can contain **multiple statements**, each defining different permissions.

## Practice Questions & Explanations

### Question 1

**According to the AWS Shared Responsibility Model, which of the following is a responsibility of the customer?**

A. Physical security of the data center  
B. Maintenance of the cloud infrastructure hardware  
C. Configuration of network firewalls and Security Groups  
D. Decommissioning of old storage disks

**Correct Answer:** C

**Explanation:**

According to the **AWS Shared Responsibility Model**, customers are responsible for **security IN the cloud**.

This includes:

- Configuring Security Groups
- Managing network firewalls
- Managing IAM permissions
- Protecting customer data

AWS is responsible for **security OF the cloud**, including physical infrastructure.

---

### Question 2

**Which design principle of the Well-Architected Framework Security pillar involves monitoring and alerting on actions and changes in real time?**

A. Implement a strong identity foundation  
B. Maintain traceability  
C. Protect data in transit and at rest  
D. Prepare for security events

**Correct Answer:** B

**Explanation:**

The **Maintain traceability** principle requires monitoring and logging actions and changes in AWS environments.

This includes:

- Logging API activity
- Monitoring system changes
- Generating alerts

Typical services include:

- AWS CloudTrail
- Amazon CloudWatch

---

### Question 3

**What is the primary purpose of the Principle of Least Privilege?**

A. To grant users full administrative access to ensure they can complete any task  
B. To grant only the minimum permissions required to perform a specific task  
C. To allow all users within a group to have the same access as the root user  
D. To simplify management by giving everyone access to all S3 buckets

**Correct Answer:** B

**Explanation:**

The **Principle of Least Privilege** requires granting only the permissions necessary to complete a task.

Benefits include:

- Reduced security risk
- Reduced attack surface
- Better access control

Permissions should be added only when necessary.

---

### Question 4

**Which IAM entity is best described as a collection of users that are granted identical authorization?**

A. IAM Role  
B. IAM Policy  
C. IAM Group  
D. IAM Principal

**Correct Answer:** C

**Explanation:**

An **IAM Group** is a collection of IAM users that share the same permissions.

Policies attached to the group apply to all users in that group.

This simplifies permission management.

---

### Question 5

**What are the two components of an AWS access key used for programmatic calls to AWS?**

A. Username and password  
B. Access Key ID and Secret Access Key  
C. Role name and session token  
D. ARN and policy ID

**Correct Answer:** B

**Explanation:**

An **AWS Access Key** consists of:

- Access Key ID
- Secret Access Key

These credentials are used for:

- AWS CLI
- SDKs
- Programmatic API calls

---

### Question 6

**Which IAM identity provides temporary security credentials and can be assumed by an application running on an EC2 instance?**

A. IAM User  
B. IAM Group  
C. IAM Role  
D. Root User

**Correct Answer:** C

**Explanation:**

An **IAM Role** provides **temporary security credentials**.

Roles can be assumed by:

- Users
- Applications
- AWS services

EC2 instances commonly use IAM roles to access AWS services securely.

---

### Question 7

**In which format are IAM policy documents written?**

A. XML  
B. HTML  
C. YAML  
D. JSON

**Correct Answer:** D

**Explanation:**

IAM policies are written in **JSON format**.

Policies define:

- Actions
- Resources
- Effects
- Conditions

---

### Question 8

**If a request is made and there is no explicit Allow and no explicit Deny in any applicable policy, what is the final decision?**

A. The request is allowed by default  
B. The request is implicitly denied  
C. AWS will ask the root user for permission  
D. The request is placed in a pending state

**Correct Answer:** B

**Explanation:**

IAM permission evaluation follows these rules:

1. Explicit Deny → Deny
2. Explicit Allow → Allow
3. Otherwise → Implicit Deny

If no Allow exists, the request is **implicitly denied**.

---

### Question 9

**If an identity-based policy allows access to an S3 bucket but a resource-based policy explicitly denies access to that same bucket, what is the result?**

A. Access is allowed because identity-based policies have higher priority  
B. Access is denied because an explicit deny overrides any allow  
C. Access is allowed if the user is an Administrator  
D. The result is Implicit Deny

**Correct Answer:** B

**Explanation:**

In IAM policy evaluation:

**Explicit Deny always overrides Allow.**

This rule applies across:

- Identity-based policies
- Resource-based policies

---

### Question 10

**Which element of an IAM policy statement specifies the specific API operations that are allowed or denied (for example, s3:PutObject)?**

A. Effect  
B. Principal  
C. Action  
D. Resource

**Correct Answer:** C

**Explanation:**

The **Action** element specifies the API operations that are allowed or denied.

Examples:

"Action": "s3:PutObject"

or

"Action": "ec2:StartInstances"

The **Effect** element only specifies Allow or Deny.

---

### Question 11

**What is the recommended best practice for the AWS account root user?**

A. Use the root user for all daily administrative tasks  
B. Share the root user credentials with the entire IT team  
C. Delete the root user account after creating the AWS account  
D. Use the root user only for tasks that cannot be performed by any other user

**Correct Answer:** D

**Explanation:**

AWS recommends:

- Using the root account only when necessary
- Avoiding daily usage
- Enabling MFA
- Creating an IAM administrator user

---

### Question 12

**Which security principle involves using cryptographic protocols like TLS to protect data while it moves between a client and an S3 bucket?**

A. Protect data at rest  
B. Protect data in transit  
C. Implement a strong identity foundation  
D. Automate security best practices

**Correct Answer:** B

**Explanation:**

**Protect data in transit** means encrypting data while it moves across networks.

Common methods include:

- HTTPS
- TLS encryption

---

### Question 13

**Which IAM policy element is used in a resource-based policy to specify which account or user is allowed access?**

A. Action  
B. Condition  
C. Principal  
D. Version

**Correct Answer:** C

**Explanation:**

The **Principal** element specifies:

- IAM users
- IAM roles
- AWS accounts

that are allowed or denied access in a **resource-based policy**.

---

### Question 14

**What is an ARN in the context of an IAM policy?**

A. Access Role Name  
B. Amazon Resource Name  
C. Authorized Resource Network  
D. AWS Registered Number

**Correct Answer:** B

**Explanation:**

An **ARN (Amazon Resource Name)** uniquely identifies AWS resources.

Example:

```
arn:aws:s3:::my-bucket
```

---

### Question 15

**Which of the following is a key takeaway regarding IAM Roles?**

A. They provide permanent credentials for human users  
B. They are associated with only one specific person  
C. They provide temporary security credentials for users or services  
D. They are used to sign in to the AWS Management Console with a password

**Correct Answer:** C

**Explanation:**

IAM Roles:

- Provide temporary credentials
- Are not tied to a specific user
- Improve security
- Are commonly used by AWS services

---

### Question 16

**When a user needs to run commands from the AWS Command Line Interface (CLI), which credentials should they use?**

A. Username and Password  
B. MFA code only  
C. AWS Access Key (Access Key ID and Secret Access Key)  
D. Root user email address

**Correct Answer:** C

**Explanation:**

Programmatic access requires:

- Access Key ID
- Secret Access Key

Used by:

- AWS CLI
- SDKs
- API calls

---

### Question 17

**Which design principle encourages the use of code to manage and verify security configurations?**

A. Apply security at all layers  
B. Automate security best practices  
C. Keep people away from data  
D. Prepare for security events

**Correct Answer:** B

**Explanation:**

**Automate security best practices** means using code and automation tools to manage security configurations.

Benefits:

- Reduced human error
- Improved consistency
- Better scalability

---

### Question 18

**In IAM evaluation logic, what happens immediately if an Explicit Deny is found?**

A. AWS continues checking other policies  
B. The evaluation stops and the request is denied  
C. The request is sent for manual review  
D. The Deny is ignored if the user is an administrator

**Correct Answer:** B

**Explanation:**

IAM evaluation order:

1. Check Explicit Deny
2. Check Allow
3. Otherwise Implicit Deny

If an **Explicit Deny** is found:

→ Evaluation stops  
→ Access denied

---

### Question 19

**Which service should be enabled to maintain traceability by logging all API actions in an AWS account?**

A. AWS IAM  
B. Amazon S3  
C. AWS CloudTrail  
D. AWS Organizations

**Correct Answer:** C

**Explanation:**

**AWS CloudTrail** records:

- API calls
- User actions
- Resource changes

CloudTrail supports:

- Auditing
- Security analysis
- Compliance

---

### Question 20

**According to IAM best practices, what should you do when a user no longer needs certain permissions?**

A. Do nothing  
B. Revoke the unnecessary permissions immediately  
C. Move the user to the root user group  
D. Change the user's password

**Correct Answer:** B

**Explanation:**

Following the **Principle of Least Privilege**, unnecessary permissions should be removed immediately.

This reduces:

- Security risks
- Attack surface
- Unauthorized access

---
