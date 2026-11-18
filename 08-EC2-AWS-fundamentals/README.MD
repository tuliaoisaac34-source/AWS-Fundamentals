# EC2 Fundamentals – Virtual Servers

## 1. Objective

Understand how Amazon EC2 provides virtual computing resources for hosting applications and services in AWS.

## 2. What is Amazon EC2?

Amazon EC2 (Elastic Compute Cloud) is an AWS service that provides scalable virtual servers in the cloud.

An EC2 instance can run an operating system, host applications, process workloads, and communicate with other resources through a configured network.

## 3. EC2 Components

| Component | Purpose |
|---|---|
| Instance type | Defines computing capacity |
| vCPU | Virtual processing resources |
| RAM | Temporary working memory |
| AMI | Template used to launch an instance |
| Key pair | Secure authentication for supported access methods |
| EBS | Persistent block storage |
| Security Group | Controls inbound and outbound network traffic |
| VPC | Isolated virtual network |

## 4. Instance Families

- General purpose: Balanced computing resources.
- Compute optimized: Designed for CPU-intensive workloads.
- Memory optimized: Designed for workloads requiring more RAM.
- Storage optimized: Designed for high-performance storage workloads.

For a basic Nginx web server, a general-purpose instance is a reasonable starting point.

## 5. EC2 Networking

An EC2 instance connects to a VPC through a network interface.

Its network configuration includes:
- VPC
- Subnet
- Private IP address
- Optional public IP address
- Route table
- Security Group

A public IP address alone does not guarantee internet connectivity. Routing and security configurations must also permit access.

## 6. Security Groups

Security Groups are stateful virtual firewalls associated with EC2 network interfaces.

Example web server rules:
- TCP 443: HTTPS access.
- TCP 22: SSH access restricted to an authorized IP address.

Only necessary traffic should be allowed.

## 7. Proposed Nginx EC2 Configuration

| Setting | Proposed Value |
|---|---|
| Operating system | Ubuntu Server |
| Instance family | General purpose |
| VPC | Week 1 VPC |
| Subnet | Public Web-A |
| Private IP | 10.20.1.10 |
| Application | Nginx |
| Storage | EBS |
| Web access | HTTPS, TCP 443 |
| Administration | Restricted SSH access |

## 8. Practical Exploration

- [ ] Reviewed the EC2 dashboard.
- [ ] Identified the AMI selection section.
- [ ] Reviewed instance type options.
- [ ] Identified key pair configuration.
- [ ] Reviewed network settings.
- [ ] Reviewed storage configuration.
- [ ] Identified Security Group settings.

## 9. Limitations

This exercise focused on AWS EC2 concepts and console exploration. No EC2 instance was launched as part of this activity.

## 10. Key Takeaways

EC2 provides the computing resources required to run applications in AWS. Its configuration depends on the operating system image, instance type, storage, networking, and security controls.


## EC2 Instance Launch Walkthrough

### 1. EC2 Dashboard
Navigate to the EC2 Dashboard in the AWS Management Console (`us-east-1` / N. Virginia) and click **Launch instance**.

![EC2 Dashboard](./01-ec2-dashboard.png)

---

### 2. Amazon Machine Image (AMI)
Select **Amazon Linux 2023 kernel-6.18 AMI** (64-bit x86, Free tier eligible) as the operating system template.

![Amazon Machine Image](./02-ami.png)

---

### 3. Instance Type
Choose **t3.micro** (2 vCPU, 1 GiB Memory, Free tier eligible) to define the virtual hardware capacity.

![Instance Type](./03-instance-type.png)

---

### 4. Key Pair (Login)
Select an existing key pair or click **Create new key pair** to enable secure SSH authentication to the instance.

![Key Pair](./04-key-pair.png)

---

### 5. Network Settings
Configure the default VPC, enable **Auto-assign public IP**, and create a security group (`launch-wizard-1`) allowing SSH traffic.

![Network Settings](./05-network-settings.png)

---

### 6. Configure Storage
Keep the default **8 GiB gp3** root volume (3000 IOPS) with no additional file systems attached.

![Storage Settings](./06-storage-settings.png)
