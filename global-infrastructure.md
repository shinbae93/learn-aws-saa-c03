# 🌍 Lesson 01: AWS Global Infrastructure

## Overview

- AWS provides a **global cloud infrastructure** designed for **high availability, scalability, and fault tolerance**.
- It consists of **Regions**, **Availability Zones (AZs)**, and **Edge Locations**.

---

## 🌎 Regions

- A **Region** is a **physical location** around the world where AWS clusters data centers.
- Each Region is **independent** and contains **multiple Availability Zones**.
- Choose Regions based on:
  - **Compliance** (e.g., GDPR, data residency laws)
  - **Latency** (closer to users = faster)
  - **Service Availability** (not all services are in every Region)
  - **Cost** (prices vary between Regions)

✅ **Exam Tip**: Most services are **regional** (e.g., EC2, RDS, VPC), but some are **global** (e.g., IAM, Route 53, CloudFront).

---

## 🏢 Availability Zones (AZs)

- Each AWS Region consists of a **minimum of three Availability Zones (AZs)**—physically separate data centers within the region.
- AZs are isolated from each other to minimize failure risk, yet are connected by ultra-low-latency, high-bandwidth networks.
- Distributing resources across multiple AZs enhances **fault tolerance** and ensures **high availability**.

✅ **Exam Tip**: Design your applications to span **at least two AZs**, and remember that AWS Regions now guarantee **at least three AZs**.

---

## 📦 Edge Locations

- Part of the **AWS Content Delivery Network (CDN)** via **Amazon CloudFront**.
- Edge Locations are used for:
  - **Caching content** (faster delivery to users)
  - **Reducing latency** for global apps
- There are **hundreds of Edge Locations** worldwide.

✅ **Exam Tip**: Don’t confuse Edge Locations with Regions or AZs. They are for **content delivery (CloudFront, Route 53)**.

---

## 📊 Summary Table

| Component             | Description                                 | Example        |
| --------------------- | ------------------------------------------- | -------------- |
| **Region**            | Geographical area with multiple AZs         | `us-east-1`    |
| **Availability Zone** | Isolated data center(s) in a Region         | `us-east-1a`   |
| **Edge Location**     | CDN endpoint for caching & content delivery | CloudFront POP |

---

## ✅ Key Takeaways

- **Region = Geography**, **AZ = Data Center**, **Edge = CDN**.
- Always use **multiple AZs** for high availability.
- Services can be **Regional** (EC2, RDS), **Global** (IAM, Route 53), or **Edge-based** (CloudFront).
