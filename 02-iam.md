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
  - **Condition**: (Optional) Restrictions such as IP, time, MFA4

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

- Example Policy (JSON):

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
      "Sid": "DenySensitiveFolderOutsideCorpNetwork",
      "Effect": "Deny",
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
