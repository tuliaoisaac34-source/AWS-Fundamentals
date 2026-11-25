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

## 12. Amazon Elastic Block Store (EBS)

### Objective

Understand how Amazon EBS provides persistent block storage for EC2 instances.

### What is EBS?

Amazon Elastic Block Store (EBS) is a block storage service designed for use with EC2 instances.

It provides persistent storage for operating systems, applications, and files.

### EC2 vs. EBS

- EC2: Virtual computing instance.
- EBS: Persistent storage volume attached to an EC2 instance.

### EBS Volume Types

| Volume Type | Purpose |
|---|---|
| gp3 | General-purpose SSD storage |
| io2 | High-performance SSD storage with provisioned IOPS |
| st1 | Throughput-optimized HDD storage |

### Proposed Nginx Storage Configuration

| Setting | Proposed Value |
|---|---|
| Operating System | Ubuntu Server |
| Storage Type | EBS gp3 |
| Volume Size | 20 GiB |
| Encryption | Enabled |
| Backup | EBS snapshots |

### EBS Snapshots

An EBS snapshot is a point-in-time backup of an EBS volume. Snapshots can be used to restore volumes and support recovery from accidental changes or data loss.

### EBS Security

EBS encryption helps protect stored data and snapshots. AWS KMS can be used for encryption key management.

### Key Takeaways

- EBS provides persistent block storage for EC2.
- EC2 is the virtual server; EBS is its storage device.
- gp3 is a suitable general-purpose option for a small Nginx website.
- EBS volumes are Availability Zone-specific.
- Snapshots support backup and recovery.
- EBS encryption helps protect data.

**Deployment status:** Proposed configuration only. No billable EBS volume or EC2 instance was created.

## 13. Amazon Simple Storage Service (S3)

### Objective

Understand how Amazon S3 provides object storage independently of EC2 instances.

### What is S3?

Amazon Simple Storage Service (S3) is an object storage service used to store and retrieve files and other data.

### Buckets and Objects

- Bucket: Container for storing objects.
- Object: File and its associated metadata.

### EBS vs. S3

| EBS | S3 |
|---|---|
| Block storage | Object storage |
| Used as storage volumes for EC2 | Used for files, backups, and objects |
| AZ-specific volumes | Regional object storage service |
| Supports block-level operations | Accessed through APIs and supported integrations |

### S3 Storage Classes

| Storage Class | Typical Purpose |
|---|---|
| S3 Standard | Frequently accessed data |
| S3 Intelligent-Tiering | Changing or unpredictable access patterns |
| S3 Standard-IA | Infrequently accessed data |
| S3 One Zone-IA | Infrequently accessed data in one AZ |
| S3 Glacier Instant Retrieval | Archival data requiring fast retrieval |
| S3 Glacier Flexible Retrieval | Archival data with flexible retrieval |
| S3 Glacier Deep Archive | Long-term archival |

### Proposed Private Backup Bucket

| Setting | Proposed Value |
|---|---|
| Purpose | Nginx website backups |
| Region | ap-southeast-1 |
| Storage Class | S3 Standard |
| Access | Private |
| Block Public Access | Enabled |
| Encryption | SSE-S3 |
| Versioning | Enabled |

### S3 Security

- IAM policies control permissions assigned to identities.
- Bucket policies define resource-based access permissions.
- Block Public Access helps prevent unintended public exposure.
- Encryption protects stored objects.
- Versioning retains multiple versions of objects.

### Proposed Architecture

An Ubuntu EC2 instance runs Nginx. EBS provides persistent block storage for the server, while a private S3 bucket stores website backup files.

The EC2 instance can use an IAM role with limited permissions to upload backup objects to the designated bucket.

### Key Takeaways

- S3 is object storage.
- A bucket is a container for objects.
- EBS and S3 serve different storage purposes.
- S3 storage classes support different access patterns.
- Private backup buckets should have Block Public Access enabled.
- IAM and bucket policies control access.
- Encryption and versioning provide additional protection.

**Deployment status:** Proposed configuration only. No S3 bucket or billable resources were created.
