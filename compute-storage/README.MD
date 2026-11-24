## 11. Amazon Machine Images (AMI)

### Objective

Understand how Amazon Machine Images (AMIs) are used to create EC2 instances and support standardized server deployments.

### What Is an AMI?

An **Amazon Machine Image (AMI)** is a template that provides the information required to launch an EC2 instance:

- **Operating system image:** Supplies the base OS (such as Linux or Windows), applications, and pre-installed configuration.
- **Launch permissions:** Controls which AWS accounts can use the AMI to launch instances.
- **Block device mappings:** Defines the root volume and any additional EBS or instance store volumes attached at launch.

Because an AMI acts as a reusable blueprint, you can launch multiple EC2 instances from the same image with a consistent starting configuration.

### AMI vs. EC2 Instance

| Aspect | AMI | EC2 Instance |
| :--- | :--- | :--- |
| **Role** | Reusable template used to launch instances | Running or provisioned virtual server created from an AMI |
| **Contents** | OS image, launch permissions, and block device mappings | Virtual compute resources (vCPU, memory, networking, storage) running workloads |
| **Lifecycle** | Created, shared, copied, deregistered, or deleted | Launched, started, stopped, rebooted, hibernated, or terminated |

### Operating System Comparison

| Operating System | Description | Typical Fit |
| :--- | :--- | :--- |
| **Amazon Linux** | AWS-maintained Linux distribution with tight integration for AWS tools, kernels, and packages | General AWS workloads and native AWS service integrations |
| **Ubuntu Server** | Widely used Debian-based Linux distribution with broad documentation and community support | Web servers, application stacks, and cross-platform Linux workflows |

### Selected Operating System

- **Selected OS:** Ubuntu Server
- **Architecture:** `x86_64`
- **Target Workload:** Nginx web server
- **Selection Rationale:** Ubuntu Server aligns with prior Linux administration practice and offers extensive documentation and community support for configuring and maintaining Nginx. While Amazon Linux is also well-suited for AWS workloads, Ubuntu Server was chosen for consistency with the existing Linux environment.

### AMI Sources

1. **Public AMIs:** Images published by AWS or community publishers for general use.
2. **AWS Marketplace AMIs:** Pre-configured images from third-party vendors, which may include software licensing or hourly/monthly subscription charges.
3. **Custom AMIs:** Images built from a configured EC2 instance to capture tailored packages, settings, or hardening baselines.

### AMI and EBS Snapshot Relationship

An AMI and an **Amazon Elastic Block Store (EBS)** snapshot serve distinct roles:

- **AMI:** Defines the launch template (OS, launch settings, and block device mappings) required to start an instance.
- **EBS Snapshot:** Captures a point-in-time backup of an EBS volume stored in Amazon S3.

For **EBS-backed AMIs**, the AMI definition references the underlying EBS snapshot(s) and block device mappings used to provision the instance's storage volumes upon launch.

### Custom AMI Workflow

1. **Launch base instance:** Start an EC2 instance from a trusted base AMI.
2. **Configure environment:** Install required packages (such as Nginx), apply system settings, and verify configuration.
3. **Create custom AMI:** Capture the configured instance as a new AMI (which creates backing EBS snapshots for EBS-backed root/data volumes).
4. **Launch standardized instances:** Provision new EC2 instances from the custom AMI for repeatable deployments.
5. **Maintain and version:** Update packages or configurations periodically, create a new AMI revision, and retire outdated images.

### Practical Exploration

Reviewed the EC2 AMI catalog to examine how images are structured and compared before provisioning:

- **AMI Name:** Human-readable identifier describing the OS, release, and build details.
- **Publisher / Owner:** Source account or organization maintaining the image.
- **AMI ID:** Unique regional identifier (for example, `ami-...`) used when launching instances or referencing in automation.
- **Architecture:** Processor architecture such as `x86_64` or `arm64`, which must match the target instance type.
- **Root Device Type:** Indicates whether the root volume is backed by **EBS** or **instance store**.

**Deployment Status:** Conceptual review and AMI catalog exploration only; no EC2 instance or custom AMI was launched in this step.

### Key Takeaways

- An **AMI** is a reusable launch template; an **EC2 instance** is the virtual server created from it.
- AMIs can come from **Public**, **AWS Marketplace**, or **Custom** sources.
- **EBS-backed AMIs** link launch configurations and block device mappings to **EBS snapshots** for volume creation.
- **Custom AMIs** enable consistent, repeatable server provisioning across environments.

### AMI Selection

![AMI Selection](images/02-ami-selection.png)

### Ubuntu AMI Details and Architecture

![Ubuntu AMI Details and Architecture](images/02-ubuntu-ami-details-and-architecture.png)

### AMI and Instance Type Review

![AMI and Instance Type Review](images/02-ami-review.png)
