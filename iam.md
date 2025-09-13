# Lesson 02: AWS Identity and Access Management (IAM)

## Overview

AWS **Identity and Access Management (IAM)** is a global service that helps you securely manage access to AWS resources.  
It enables you to create and manage **users, groups, and permissions** through policies.

---

## 👤 IAM Users

- Represents a **person or application** that interacts with AWS.
- Each user has:
  - **Name**
  - **Credentials** (password for console, access keys for CLI/SDK)
- By default, new users have **no permissions**.

⚠️ **Best Practice (Critical): Never use the AWS root account for daily tasks.**  
Instead, create an **IAM admin user** and use **MFA (Multi-Factor Authentication)** on the root account.

✅ **Exam Tip**: Always create IAM users with the **least privilege principle**.

---

## 👥 IAM Groups

- A collection of IAM users.
- Used to **assign permissions to multiple users at once**.
- A user can belong to **multiple groups**.
- Groups cannot be nested (no groups inside groups).

✅ **Exam Tip**: Attach policies to **groups**, not individual users, for easier management.

---

## 📜 IAM Policies

- Define **permissions** (what actions are allowed or denied on which resources).

- Written in **JSON format** with key elements:

  - **Effect**: Allow / Deny
  - **Action**: Specific API calls
  - **Resource**: Target resource (ARNs)
  - **Principal**: (Optional) Who the policy applies to (used in resource-based policies and trust policies)
  - **Condition**: (Optional) Restrictions such as IP, time, MFA

- Types of Policies:

  - **Identity-based policies**: Attached to users, groups, or roles
  - **Resource-based policies**: Attached directly to AWS resources (e.g., S3 bucket policy)
  - **Permission boundaries**: Set the maximum permissions an identity can have
  - **Service control policies (SCPs)**: Organization-level guardrails
  - **Access control lists (ACLs)**: Legacy, mainly in S3 and networking

- Policies can be attached to:

  - Users
  - Groups
  - Roles

- Inheritance:

  - Policies are **cumulative** — a user gets all permissions from **attached policies + group memberships**
  - If multiple policies apply, the **result is the union of all permissions**
  - An **explicit Deny** always overrides any Allow
  - _Example_:
    - User `Alice` has a policy allowing `s3:ListBucket`
    - Her group has a policy allowing `s3:GetObject`
    - Effective permissions for Alice = **ListBucket + GetObject**
    - If either policy explicitly **Denies** `s3:GetObject`, she will **not** have `GetObject` access

**Example 1:** Identity-Based Policy (No Principal)

_Attached to a user, group, or role - Principal is implicit_

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowS3ReadOnly",
      "Effect": "Allow",
      "Action": ["s3:ListBucket", "s3:GetObject"],
      "Resource": [
        "arn:aws:s3:::my-company-data",
        "arn:aws:s3:::my-company-data/*"
      ]
    },
    {
      "Sid": "DenyDeleteActions",
      "Effect": "Deny",
      "Action": ["s3:DeleteObject", "s3:DeleteBucket"],
      "Resource": "*"
    }
  ]
}
```

**Example 2:** Resource-Based Policy (With Principal)

_S3 Bucket Policy - Principal must be explicitly defined_

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowCrossAccountReadAccess",
      "Effect": "Allow",
      "Principal": {
        "AWS": [
          "arn:aws:iam::123456789012:user/Alice",
          "arn:aws:iam::123456789012:role/DataAnalystRole"
        ]
      },
      "Action": ["s3:ListBucket", "s3:GetObject"],
      "Resource": [
        "arn:aws:s3:::my-company-data",
        "arn:aws:s3:::my-company-data/*"
      ]
    },
    {
      "Sid": "DenySensitiveFolderOutsideCorpNetwork",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-company-data/sensitive/*",
      "Condition": {
        "NotIpAddress": {
          "aws:SourceIp": ["203.0.113.0/24"]
        }
      }
    }
  ]
}
```

---

## 🎭 IAM Roles

- **IAM Roles** are a secure way to grant permissions to entities that you trust
- Unlike users, roles **don't have permanent credentials** (no passwords or access keys)
- Instead, roles provide **temporary security credentials** when assumed

### Key Concepts:

- **Role**: A set of permissions that can be assumed by trusted entities
- **Trust Policy**: Defines **who** can assume the role
- **Permission Policy**: Defines **what** the role can do (attached like user policies)
- **Assume Role**: The process of taking on a role's permissions temporarily

### Common Use Cases:

1. **EC2 Instance Roles** (Instance Profiles)
   - Allow EC2 instances to access AWS services securely
   - No need to store access keys on the instance
2. **Cross-Account Access**
   - Allow users/services in one AWS account to access resources in another
3. **AWS Service Roles**
   - Allow AWS services (Lambda, ECS, etc.) to access other AWS services on your behalf
4. **Identity Federation**
   - Allow external identity providers (Google, Facebook, SAML) to access AWS resources
5. **Temporary Access**
   - Provide time-limited access to resources

### How Roles Work:

1. **Create Role** with trust policy and permission policies
2. **Assume Role** using STS (Security Token Service)
3. **Receive temporary credentials** (Access Key ID, Secret Access Key, Session Token)
4. **Use credentials** to access AWS resources (credentials expire automatically)

✅ **Exam Tips**:

- Roles are **preferred over users** for AWS services and applications
- **Instance profiles** are the mechanism to attach roles to EC2 instances
- Roles can be assumed by **multiple entities** simultaneously
- **STS (Security Token Service)** is used for all role assumption operations
- Maximum session duration is **12 hours** (1 hour default for console)

---

## 🔑 Password Policies

- IAM allows you to define **password policies** for all IAM users in the account.
- You can enforce:
  - Minimum password length
  - Require specific character types (uppercase, lowercase, numbers, symbols)
  - Prevent password reuse
  - Set password expiration (e.g., every 90 days)
- Password policies apply **globally across the AWS account**.

⚠️ **Best Practice**: Always enforce a strong password policy + enable **MFA** for added security.

✅ **Exam Tip**: Remember that password policies apply only to **IAM users** (not the root account).

---

## 🔐 IAM MFA (Multi-Factor Authentication)

- **MFA** (Multi-Factor Authentication) adds an extra layer of security
- **MFA** = _Something you know_ (password or access key) + _Something you have_ (MFA device code)
- Types of MFA devices:

  - Virtual MFA apps (_Google Authenticator, Authy, AWS Virtual MFA app_) → **mobile app generating 6-digit codes**
  - U2F security keys (_hardware devices like YubiKey_) → **tap-based cryptographic device**
  - Hardware TOTP tokens (_physical devices generating 6-digit codes_) → **standalone hardware code generator**

- Benefits:
  - Protects against compromised credentials
  - Strongly recommended for **root user**, **high-privilege IAM users**, and **sensitive roles**
- How it works:
  - Console login requires entering an **MFA code**
  - CLI/SDK requires requesting **temporary session credentials** with:
    ```sh
    aws sts get-session-token --serial-number <MFA-ARN> --token-code <MFA-CODE>
    ```
- _Example policy requiring MFA_:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Deny",
        "Action": "*",
        "Resource": "*",
        "Condition": {
          "BoolIfExists": {
            "aws:MultiFactorAuthPresent": "false"
          }
        }
      }
    ]
  }
  ```

---

## 🔑 AWS Access Keys

- **Access Keys** are credentials used for **programmatic access** to AWS services (CLI, SDK, API calls)
- Consists of two parts:
  - **Access Key ID** (public identifier, like a username)
  - **Secret Access Key** (private key, like a password)

### Key Characteristics:

- **Never expire** (unless manually rotated or deleted)
- Can be **active** or **inactive**
- Each IAM user can have **up to 2 access keys** (for rotation purposes)
- **Cannot be retrieved** once created (only the Access Key ID is visible)

### Security Best Practices:

- **Never share** access keys or commit them to code repositories
- **Rotate regularly** (every 90 days recommended)
- Use **temporary credentials** when possible (STS, IAM roles)
- **Delete unused** access keys immediately
- **Monitor usage** with CloudTrail

⚠️ **Critical**: If access keys are compromised, **immediately deactivate/delete** them and create new ones.

✅ **Exam Tip**: Access keys are for **programmatic access only** - they cannot be used to sign into the AWS Console.

---

## IAM Credentials Report (Account-Level)

- **Account-wide report** that lists all users and the status of their various credentials
- Shows information about:
  - Password status and age
  - Access keys status and rotation
  - MFA device assignments
  - Certificate status
- **Generated on-demand** from IAM console or CLI
- Useful for **compliance audits** and security reviews

---

## IAM Access Advisor (User-Level)

- Shows the **service permissions granted** to a user and **when those services were last accessed**
- Helps identify **unused permissions** for better security posture
- Available for users, groups, and roles
- **Last accessed data** is available for the past 400 days
- Use this information to **revise policies** and apply principle of least privilege

✅ **Exam Tip**: Use **Credentials Report** for account-wide security overview and **Access Advisor** to optimize individual user permissions.

---
