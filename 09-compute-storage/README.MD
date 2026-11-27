## 11. Amazon Machine Images (AMI)

### Objective

Understand how Amazon Machine Images (AMIs) are used to launch EC2 instances and establish standardized server deployments.

---

### What Is an AMI?

An **Amazon Machine Image (AMI)** is a reusable launch template that provides the core configuration required to start an EC2 instance:

- **Operating System Image:** Supplies the base OS (such as Linux or Windows), pre-installed packages, and system configuration.
- **Launch Permissions:** Controls which AWS accounts can discover and launch instances from the image.
- **Block Device Mappings:** Defines the root volume and any additional Amazon EBS or instance store volumes attached at launch.

Because an AMI acts as a versioned blueprint, multiple EC2 instances can be provisioned from the same image with an identical starting baseline.

---

### AMI vs. EC2 Instance

| Aspect | AMI | EC2 Instance |
| :--- | :--- | :--- |
| **Role** | Reusable template used to launch instances | Provisioned virtual server created from an AMI |
| **Contents** | OS image, launch permissions, and block device mappings | Virtual compute resources (vCPU, memory, networking, storage) running workloads |
| **Lifecycle** | Created, shared, copied, deregistered, or deleted | Launched, started, stopped, rebooted, hibernated, or terminated |

---

### Operating System Comparison

| Operating System | Description | Typical Fit |
| :--- | :--- | :--- |
| **Amazon Linux** | AWS-maintained Linux distribution optimized with native AWS tools, kernels, and packages | General AWS workloads and tight AWS service integrations |
| **Ubuntu Server** | Debian-based Linux distribution maintained by Canonical with broad documentation and community support | Web servers, application stacks, and cross-platform Linux environments |

---

### Selected Operating System

- **Selected OS:** Ubuntu Server 26.04 LTS (HVM), SSD Volume Type
- **AMI ID:** `ami-0c30b7300f5fb34cd`
- **Architecture:** `64-bit (x86_64)`
- **Virtualization / Root Device:** `hvm` / `ebs` (ENA enabled)
- **Default Login User:** `ubuntu`
- **Target Workload:** Nginx web server
- **Selection Rationale:** Ubuntu Server aligns with prior Linux administration workflows and offers extensive documentation and package support for Nginx deployments. While Amazon Linux is also well-suited for native AWS workloads, Ubuntu Server was selected for consistency with the existing Linux lab environment.

---

### AMI Sources

1. **Public AMIs:** Official images published by AWS or verified vendors (such as Canonical) for general use.
2. **AWS Marketplace AMIs:** Pre-packaged software images from third-party vendors, which may include software licensing or hourly/monthly subscription fees.
3. **Custom AMIs:** Private images captured from configured EC2 instances to standardize packages, configurations, and security baselines.

---

### AMI and EBS Snapshot Relationship

An AMI and an **Amazon Elastic Block Store (EBS)** snapshot serve distinct roles:

- **AMI:** Defines the launch blueprint (OS metadata, launch permissions, and block device mappings) used to instantiate a virtual server.
- **EBS Snapshot:** Captures a point-in-time block-level backup of an EBS volume stored in Amazon S3.

For **EBS-backed AMIs**, the AMI references the backing EBS snapshot(s) and block device mappings used to provision the instance's storage volumes upon launch.

---

### Custom AMI Workflow

1. **Launch Base Instance:** Provision an EC2 instance from a verified base AMI.
2. **Configure Environment:** Install required services (such as Nginx), apply system hardening, and verify functionality.
3. **Create Custom AMI:** Capture the configured instance as a new AMI, automatically generating backing EBS snapshots for attached volumes.
4. **Launch Standardized Instances:** Deploy repeatable EC2 instances from the custom AMI across environments.
5. **Maintain and Version:** Apply periodic OS/package updates, create a new AMI revision, and deregister outdated images.

---

### Practical Exploration & Lab Evidence

Reviewed the EC2 **Launch an instance** AMI catalog to inspect image metadata, architecture compatibility, and storage defaults before provisioning:

- **AMI Name:** `Ubuntu Server 26.04 LTS (HVM), SSD Volume Type`
- **Publisher / Owner:** Canonical (`Verified provider`, Free tier eligible)
- **AMI ID:** `ami-0c30b7300f5fb34cd` (`x86_64`) / `ami-098ede342a5440ac0` (`arm64`)
- **Creation Date:** `2026-11-20`
- **Root Device Type:** `ebs` (General Purpose SSD, HVM virtualization, ENA enabled)

#### Lab Screenshots (Google Drive)

> **Folder:** [View All AMI Lab Screenshots on Google Drive](https://drive.google.com/drive/folders/1Esb8sHEhbFG6xFrKDJRQgHEk75haOhGa)

1. **[02-ami-selection.png](https://drive.google.com/file/d/1f8nlIkVKEcv-9JhAyGoHP0w6D_bOgFSZ/view?usp=drivesdk)** — Selecting Ubuntu Server 26.04 LTS from the EC2 Quick Start AMI catalog.
2. **[02-ubuntu-ami-details-and-architecture.png](https://drive.google.com/file/d/1OoIgBcf9l6NGW_gIfOtHZMnhNXk_wEO8/view?usp=drivesdk)** — Verifying the `64-bit (x86)` architecture, AMI ID (`ami-0c30b7300f5fb34cd`), creation date, default `ubuntu` username, and verified Canonical publisher badge.
3. **[02-ami-review.png](https://drive.google.com/file/d/1Kln6TsdiB3WjAcZsYqfLHNC3tKwKuwVN/view?usp=drivesdk)** — Reviewing the selected Ubuntu Server AMI alongside the `t3.micro` instance type configuration prior to launch.

**Deployment Status:** Conceptual review and AMI catalog inspection completed; no live EC2 instance or custom AMI was provisioned during this step.

---

### Key Takeaways

- An **AMI** is a reusable launch template; an **EC2 instance** is the virtual server created from it.
- AMIs are sourced from **Public**, **AWS Marketplace**, or **Custom** catalogs.
- **EBS-backed AMIs** pair launch configurations and block device mappings with **EBS snapshots** to provision instance storage.
- **Custom AMIs** enable consistent, repeatable server deployments across environments.

---

## 12. Amazon Elastic Block Store (EBS)

### Objective

Understand how Amazon EBS provides persistent, low-latency block storage volumes for EC2 instances.

---

### What Is EBS?

**Amazon Elastic Block Store (EBS)** is a high-performance block storage service designed for use with Amazon EC2. Unlike instance store volumes (which are ephemeral), EBS volumes persist independently of the instance lifecycle and store operating systems, applications, databases, and file systems.

---

### EC2 vs. EBS

| Component | Role | Persistence & Scope |
| :--- | :--- | :--- |
| **EC2 Instance** | Virtual compute server executing OS and application workloads | Compute state is tied to the running/stopped instance |
| **EBS Volume** | Network-attached block storage device mounted to an EC2 instance | Persists independently of instance stop/termination; bound to a single Availability Zone (AZ) |

---

### EBS Volume Types

| Volume Type | Category | Typical Use Case |
| :--- | :--- | :--- |
| **`gp3`** | General Purpose SSD | Boot volumes, web servers, development environments, and balanced transactional workloads |
| **`io2`** | Provisioned IOPS SSD | Mission-critical, latency-sensitive databases requiring sustained high IOPS and durability |
| **`st1`** | Throughput Optimized HDD | Big data, data warehouses, and sequential log processing (cannot be used as a boot volume) |

---

### Proposed Nginx Storage Configuration

| Setting | Proposed Value |
| :--- | :--- |
| **Operating System** | Ubuntu Server |
| **Storage Type** | EBS `gp3` (General Purpose SSD) |
| **Volume Size** | `20 GiB` |
| **Encryption** | Enabled (AWS KMS) |
| **Backup Strategy** | Automated EBS Snapshots |

---

### EBS Snapshots & Security

- **EBS Snapshots:** Incremental, point-in-time backups of EBS volumes stored durably in Amazon S3. Snapshots enable volume restoration, cross-AZ/cross-region migration, and disaster recovery from accidental changes or data corruption.
- **EBS Encryption:** Protects data at rest on the volume, data in transit between EC2 and EBS, and all derived snapshots using **AWS Key Management Service (KMS)** keys without requiring application-level changes.

**Deployment Status:** Proposed storage architecture only; no billable EBS volume or EC2 instance was provisioned.

---

### Key Takeaways

- **EBS** provides persistent, Availability Zone-scoped block storage for EC2 instances.
- **`gp3`** offers predictable baseline performance and cost efficiency for an Nginx web server root volume.
- **EBS Snapshots** and **AWS KMS encryption** provide essential durability, recovery, and data-at-rest protection.

---

## 13. Amazon Simple Storage Service (S3)

### Objective

Understand how Amazon S3 provides scalable object storage independently of EC2 compute resources.

---

### What Is S3?

**Amazon Simple Storage Service (S3)** is a managed object storage service built to store and retrieve any amount of data from anywhere over HTTPS.

- **Bucket:** A globally uniquely named container within an AWS Region that holds objects and enforces access/lifecycle policies.
- **Object:** The fundamental entity stored in S3, consisting of file data, a unique key (name/path), and system or custom metadata.

---

### EBS vs. S3

| Aspect | Amazon EBS | Amazon S3 |
| :--- | :--- | :--- |
| **Storage Model** | Block storage (mounted as a file system volume) | Object storage (flat namespace accessed via REST API/SDK/CLI) |
| **Primary Use** | OS boot volumes, databases, and active application storage for EC2 | Backups, static assets, logs, archives, and data lakes |
| **Scope & Availability** | Bound to a single Availability Zone (AZ) | Regional service replicated across multiple AZs |
| **Coupling** | Must be attached to an EC2 instance to be read/written | Fully independent of EC2; accessible concurrently by multiple services |

---

### S3 Storage Classes

| Storage Class | Typical Purpose |
| :--- | :--- |
| **S3 Standard** | Frequently accessed data requiring millisecond latency and high throughput |
| **S3 Intelligent-Tiering** | Workloads with unknown, changing, or unpredictable access patterns |
| **S3 Standard-IA** | Infrequently accessed data requiring rapid retrieval when needed |
| **S3 One Zone-IA** | Recreable, infrequently accessed data stored in a single AZ at lower cost |
| **S3 Glacier Instant Retrieval** | Long-lived archive data needing millisecond retrieval once per quarter |
| **S3 Glacier Flexible Retrieval** | Archival backups with configurable retrieval times (minutes to hours) |
| **S3 Glacier Deep Archive** | Long-term compliance and regulatory archives (retrieval within 12–48 hours) |

---

### Proposed Private Backup Bucket

| Setting | Proposed Value |
| :--- | :--- |
| **Purpose** | Nginx configuration and website backups |
| **AWS Region** | `ap-southeast-1` (Singapore) |
| **Storage Class** | S3 Standard |
| **Access Level** | Private |
| **Block Public Access** | Enabled (all 4 settings active) |
| **Default Encryption** | `SSE-S3` (AES-256) |
| **Versioning** | Enabled |

---

### S3 Security & Architecture Integration

- **IAM & Bucket Policies:** Govern identity-based and resource-based permissions using least-privilege principles.
- **Block Public Access:** Account- and bucket-level safeguard preventing accidental public exposure via ACLs or bucket policies.
- **Encryption & Versioning:** `SSE-S3` encrypts objects at rest, while bucket versioning preserves previous object states against accidental overwrites or deletions.
- **EC2-to-S3 Integration:** Rather than storing static AWS access keys on the server, the Ubuntu EC2 instance assumes an **IAM Instance Profile (IAM Role)** with tightly scoped `s3:PutObject` permissions to upload backup archives to the designated bucket.

**Deployment Status:** Proposed storage architecture only; no S3 bucket or billable resources were created.

---

### Key Takeaways

- **S3** provides decoupled, regionally durable object storage distinct from **EBS** block volumes.
- Selecting the right **S3 Storage Class** balances retrieval speed against storage cost.
- Private backup buckets should enforce **Block Public Access**, **encryption**, **versioning**, and **IAM role-based access**.

---

## 14. Integrated EC2 + EBS + S3 Web Server Design

### Objective

Design a cohesive, security-conscious AWS web server architecture combining Amazon EC2, EBS, and S3.

---

### Architecture Overview

The proposed design deploys an **Ubuntu Server EC2 instance** running **Nginx** inside a public subnet to serve web traffic. **Amazon EBS** provides encrypted, persistent block storage for the OS and active web files, while **Amazon S3** acts as an independent, off-instance target for automated backups.

```text
[ Internet Users ]
        │  HTTPS (TCP 443)
        ▼
[ Internet Gateway ]
        │
        ▼
┌───────────────────────────────────────────────────────────┐
│  VPC (10.20.0.0/16)                                       │
│  ┌─────────────────────────────────────────────────────┐  │
│  │  Public Subnet: Public Web-A                        │  │
│  │  ┌───────────────────────────────────────────────┐  │  │
│  │  │  Security Group (TCP 443 Public, TCP 22 Admin)│  │  │
│  │  │  ┌─────────────────────────────────────────┐  │  │  │
│  │  │  │  EC2 Instance (t3.micro / Ubuntu Nginx) │  │  │  │
│  │  │  │  Private IP: 10.20.1.10                 │  │  │  │
│  │  │  └──────┬──────────────────────────┬───────┘  │  │  │
│  │  └─────────┼──────────────────────────┼──────────┘  │  │
│  └────────────┼──────────────────────────┼─────────────┘  │
└───────────────┼──────────────────────────┼────────────────┘
                │                          │
                ▼                          ▼ (IAM Role — No Hardcoded Keys)
     [ EBS Volume (20 GiB gp3) ]   [ Private S3 Backup Bucket ]
     (Encrypted + EBS Snapshots)   (ap-southeast-1, SSE-S3, Versioned)
