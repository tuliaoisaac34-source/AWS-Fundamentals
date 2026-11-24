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
- **Memory Optimized (`R`, `X`):** Large RAM-to-vCPU ratios designed for in-memory caches, real-time analytics, and high-throughput databases.
- **Storage Optimized (`I`, `D`):** High sequential read/write IOPS built for distributed file systems, NoSQL databases, and data warehousing.

For a lightweight **Nginx web server**, a **General Purpose** instance (such as `t3.micro`) provides an efficient, cost-effective starting point.

---

## 5. EC2 Networking

An EC2 instance attaches to a **Virtual Private Cloud (VPC)** via an Elastic Network Interface (ENI). Its network posture depends on several coordinated components:

- **VPC & Subnet:** Places the instance inside a specific network boundary and Availability Zone.
- **Private IP Address:** Internal IP used for communication within the VPC.
- **Public IP Address (Optional):** External IPv4 address enabling direct internet reachability when paired with proper routing.
- **Route Table:** Directs outbound and inbound packet flow (for example, routing `0.0.0.0/0` through an Internet Gateway for public subnets).
- **Security Group:** Filters permitted traffic at the instance interface level.

> **Key Note:** Assigning a public IP address alone does not grant internet access—the subnet's route table and the attached Security Group must also allow the traffic.

---

## 6. Security Groups

**Security Groups** act as stateful virtual firewalls attached to EC2 network interfaces (if inbound traffic is allowed, the return outbound response is automatically permitted).

### Example Web Server Rules

| Direction | Protocol / Port | Source / Destination | Purpose |
| :--- | :--- | :--- | :--- |
| **Inbound** | `TCP 443` (HTTPS) | `0.0.0.0/0` | Allows encrypted public web traffic to Nginx |
| **Inbound** | `TCP 22` (SSH) | Authorized Admin IP (`/32`) | Restricts administrative terminal access to trusted IPs |

Following the **principle of least privilege**, only ports required by the workload should be opened.

---

## 7. Proposed Nginx EC2 Configuration

| Setting | Proposed Value |
| :--- | :--- |
| **Operating System** | Ubuntu Server |
| **Instance Family** | General Purpose (e.g., `t3.micro` — 2 vCPU, 1 GiB RAM) |
| **VPC** | Week 1 VPC |
| **Subnet** | Public Web-A |
| **Private IP** | `10.20.1.10` |
| **Application** | Nginx |
| **Storage** | Amazon EBS (`8 GiB gp3` root volume) |
| **Web Access** | HTTPS (`TCP 443`) |
| **Administration** | Restricted SSH (`TCP 22`) |

---

## 8. Practical Exploration & Console Evidence

Explored the EC2 **Launch an instance** workflow in the AWS Management Console to inspect each configuration stage prior to provisioning:

- [x] Reviewed the EC2 dashboard and launch wizard layout.
- [x] Inspected the **AMI selection** catalog (`Amazon Linux 2023` default view and `Ubuntu Server` options).
- [x] Reviewed **Instance type** specifications (`t3.micro`: `2 vCPU`, `1 GiB Memory`, Free tier eligible).
- [x] Examined **Key pair (login)** requirements for secure SSH authentication.
- [x] Reviewed **Network settings** (default VPC `vpc-08c45f92e09a8fc12`, auto-assign public IP, and `launch-wizard-1` Security Group rules).
- [x] Inspected **Configure storage** defaults (`1x 8 GiB gp3` root volume, `3000 IOPS`).

### Lab Screenshots (Google Drive)

> **Folder:** [View All EC2 Lab Screenshots on Google Drive](https://drive.google.com/drive/folders/1rzFoSXBmRrXsyXghUaQT6uQSk4hAd6EG)

1. **[02-ami-selection.png](https://drive.google.com/file/d/1Bi5DmOB434cWDPCZykSIpEWEQYRfwT3y/view?usp=drivesdk)** — Inspecting default AMI metadata (`Amazon Linux 2023`, `hvm`, ENA enabled, `ebs` root device).
2. **[03-instance-type.png](https://drive.google.com/file/d/1VxVjAj_WNr-R7XWyylJPcZPDdA3rWJxp/view?usp=drivesdk)** — Reviewing `t3.micro` compute specs (`2 vCPU`, `1 GiB Memory`) and On-Demand pricing details.
3. **[04-key-pair.png](https://drive.google.com/file/d/1MhoyoBYigyckkQ9G5jrTmsf0g_FO4Ac9/view?usp=drivesdk)** — Reviewing the Key Pair login selector and creation workflow for secure instance access.
4. **[05-network-settings.png](https://drive.google.com/file/d/1L-7reGm7X7kFekgZ3zok6Esfrwwh_4pj/view?usp=drivesdk)** — Inspecting VPC assignment, public IP auto-assignment, and default `launch-wizard-1` Security Group rules (`SSH`, `HTTPS`, `HTTP`).
5. **[06-storage-settings.png](https://drive.google.com/file/d/1wsH6XePNzcL9adHObcLwurS3kbXp_PRK/view?usp=drivesdk)** — Reviewing the default `8 GiB gp3` EBS root volume configuration (`3000 IOPS`).

---

## 9. Limitations

This exercise focused on AWS EC2 architecture concepts and console configuration exploration. No live EC2 instance was launched, billed, or provisioned during this activity.

---

## 10. Key Takeaways

- **Amazon EC2** provides scalable virtual compute capacity tailored by instance family and size.
- Launching a production-ready server requires aligning five core layers: **AMI**, **Instance Type**, **Key Pair**, **VPC/Security Group Networking**, and **EBS Storage**.
- Locking down **Security Groups** (restricting SSH to trusted IPs while exposing only required web ports) is essential before exposing an instance to the internet.
