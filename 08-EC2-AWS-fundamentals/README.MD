# EC2 Fundamentals – Virtual Servers

## 1. Objective

Understand how Amazon EC2 provides scalable virtual computing resources for hosting applications and services in AWS, and review the core configuration settings required before launching an instance.

---

## 2. What Is Amazon EC2?

**Amazon EC2 (Elastic Compute Cloud)** is an AWS Infrastructure-as-a-Service (IaaS) offering that provides resizable virtual servers in the cloud.

An EC2 instance runs a chosen operating system, hosts applications, processes workloads, and communicates with internal or external resources through a configured virtual network.

---

## 3. Core EC2 Components

| Component | Purpose |
| :--- | :--- |
| **Instance Type** | Defines hardware capacity (CPU, memory, storage, and network performance) |
| **vCPU** | Virtual processing threads allocated to execute application workloads |
| **RAM** | Volatile working memory used by the OS and active processes |
| **AMI** | Reusable machine image template containing the OS and launch configuration |
| **Key Pair** | Public/private cryptographic key pair used to authenticate SSH or RDP access |
| **EBS** | Persistent network-attached block storage volumes for the OS and application data |
| **Security Group** | Stateful virtual firewall controlling inbound and outbound network traffic |
| **VPC** | Isolated virtual network environment where the instance operates |

---

## 4. Instance Families

- **General Purpose (`T`, `M`):** Balanced compute, memory, and networking resources—well-suited for web servers, small-to-medium databases, and development workloads.
- **Compute Optimized (`C`):** High-performance processors tailored for CPU-intensive tasks such as batch processing, media transcoding, and gaming servers.
- **Memory Optimized (`R`, `X`):** Large RAM-to-vCPU ratios designed for in-memory caches
