## 11. Amazon Machine Images (AMI)

### Objective 

Understand how Amazon Machine Images (AMIs) are used to create EC2 instances and how they support standardized server deployments.

### What is an AMI?

An Amazon Machine Image (AMI) is a template that provides the information required to launch an EC2 instance.

It contains the operating system image and launch-related storage configuration, including block device mappings.

An AMI can be reused to create multiple EC2 instances with a consistent starting configuration.

### AMI vs. EC2 Instance

| AMI | EC2 Instance |
|---|---|
| Template used to launch an instance | Virtual server created from an AMI |
| Contains operating system and launch configuration information | Provides computing resources to run applications |
| Can be reused | Can be launched, stopped, restarted, or terminated |

### Operating System Comparison

| Operating System | Description |
|---|---|
| Amazon Linux | AWS-maintained Linux distribution designed for AWS workloads |
| Ubuntu Server | Popular Linux distribution with extensive documentation and community support |

### Selected Operating System

**Selected OS:** Ubuntu Server

**Architecture:** x86_64

**Purpose:** Nginx web server

**Reason for selection:**

Ubuntu Server was selected because it is familiar from previous Linux administration exercises and provides extensive documentation and support for Nginx deployments.

Amazon Linux is also a suitable option for AWS workloads. Ubuntu was selected for consistency with the existing Linux learning environment.

### AMI Sources

1. Public AMIs — Images published by AWS or other publishers.
2. AWS Marketplace AMIs — Images provided by third-party vendors, potentially with additional licensing or subscription costs.
3. Custom AMIs — Images created from configured EC2 instances.

### AMI and EBS Snapshot Relationship

An AMI and an EBS snapshot serve different purposes.

- AMI: Provides the information needed to launch an EC2 instance.
- EBS snapshot: Provides a point-in-time backup of an EBS volume.

EBS-backed AMIs reference the necessary snapshots and block device mappings used to provide storage when launching an instance.

### Custom AMI Workflow

1. Launch an EC2 instance.
2. Install and configure the required software.
3. Create a custom AMI from the configured instance.
4. Use the custom AMI to launch additional instances.
5. Maintain and update the image as required.

### Practical Exploration

The EC2 AMI catalog was reviewed to understand available operating system images and their configuration details.

Items to compare:
- AMI name
- Publisher
- AMI ID
- Architecture
- Root device type

**Deployment status:** No EC2 instance or custom AMI was created. This activity focused on understanding AMI concepts and exploring available image options.

### Key Takeaways

- AMI stands for Amazon Machine Image.
- An AMI is a template used to launch EC2 instances.
- EC2 is the virtual server created from an AMI.
- Public, Marketplace, and custom AMIs are common image sources.
- EBS snapshots are related to the storage configuration of EBS-backed AMIs.
- Custom AMIs help organizations standardize server configurations.
