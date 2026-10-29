# AWS VPC Network Design — Global E-Commerce Architecture

## 1. Project Overview
This document defines an enterprise-grade IPv4 addressing and subnet allocation plan for a scalable, global E-Commerce platform. 

The architecture is engineered to support a high-traffic retail storefront, an isolated order-processing application layer, and a highly secure payment/inventory database backend. The infrastructure is distributed across two Availability Zones to eliminate single points of failure.

* **Design Status:** Planned architecture framework. No live AWS resources have been deployed or provisioned.

## 2. AWS Region and Availability Zones
* **AWS Region:** Europe (Ireland)
* **Region Code:** `eu-west-1`
* **Availability Zone A:** `eu-west-1a`
* **Availability Zone B:** `eu-west-1b`

> [!NOTE]
> Physical Availability Zone mappings vary dynamically across individual AWS accounts. These labels designate the logical architectural design; map and verify actual AZ identifiers within the target AWS organization prior to deployment.

## 3. VPC Configuration
* **VPC Name:** `ecommerce-production-vpc`
* **IPv4 CIDR Block:** `10.50.0.0/16`
* **Total IPv4 Addresses:** 65,536
* **Purpose:** Establish a logically isolated network perimeter for production e-commerce operations using the private RFC 1918 address space allocation.

## 4. Subnet Allocation Plan

### Availability Zone A — `eu-west-1a`

| Subnet Name | CIDR Block | Intended Purpose |
| :--- | :--- | :--- |
| Public-Storefront-A | `10.50.1.0/24` | Public-facing web servers and Public Load Balancers |
| Private-OrderApp-A | `10.50.11.0/24` | Internal e-commerce business logic & processing |
| Private-SecureDB-A | `10.50.21.0/24` | Customer accounts, inventory, and payment database records |

### Availability Zone B — `eu-west-1b`

| Subnet Name | CIDR Block | Intended Purpose |
| :--- | :--- | :--- |
| Public-Storefront-B | `10.50.2.0/24` | Mirror public storefront tier for high availability |
| Private-OrderApp-B | `10.50.12.0/24` | Mirror internal business logic tier for high availability |
| Private-SecureDB-B | `10.50.22.0/24` | Mirror database tier with active-passive replication |

## 5. CIDR and Addressing Explanation
* The primary VPC network boundary uses a `/16` network prefix, creating a massive address pool from which isolated subnets are provisioned.
* Each architectural tier is allocated a `/24` CIDR block.
* A standard `/24` segment provides 256 total IPv4 addresses. Because AWS reserves five addresses per subnet for internal control planes, each subnet yields exactly 251 usable host addresses.
* All six allocated subnet ranges are mathematically isolated, preventing routing overlap while maintaining contiguous unassigned spaces inside the `10.50.0.0/16` block for future service expansions.

## 6. AWS-Reserved IPv4 Addresses
For any given subnet tier—using `Public-Storefront-A` (`10.50.1.0/24`) as the baseline model—AWS reserves five structural IP addresses that cannot be assigned to compute resources:

| IP Address | AWS Infrastructure Reservation Role |
| :--- | :--- |
| `10.50.1.0` | **Network Address:** Defines the foundational boundary of the subnet room. |
| `10.50.1.1` | **VPC Router:** The default gateway managing internal and external room data routing. |
| `10.50.1.2` | **AWS DNS Server:** Provides Amazon Provided DNS resolution capabilities for hosts. |
| `10.50.1.3` | **Future Use Space:** Permanently reserved by AWS for upcoming core utility functions. |
| `10.50.1.255` | **Network Broadcast Simulation:** Reserved by AWS; traditional IPv4 broadcast is disabled. |

> [!IMPORTANT]
> This precise address reservation matrix applies across all six active `/24` subnets within their respective mathematical blocks. 

## 7. Public and Private Subnet Isolation Strategy

### Public Storefront Subnets
* Designed to host internet-facing Application Load Balancers (ALBs) and caching layers.
* **Routing Path:** Associated with a custom route table pointing a default route (`0.0.0.0/0`) out through an active AWS Internet Gateway (IGW).
* **Ingress Access:** Authorized public HTTP/HTTPS traffic is accepted.

### Private Order-Application Subnets
* Houses core containerized shopping cart components, order APIs, and microservices.
* **Routing Path:** Locked away from direct external ingress paths. 
* **Ingress Access:** Accepts network traffic exclusively originating from the Public Storefront tier.

### Private Secure-Database Subnets
* Holds critical transactional systems, relational database clusters, and persistent state stores.
* **Routing Path:** Completely air-gapped network segment. No internet routes permitted.
* **Ingress Access:** Access is locked down down to the absolute minimum, accepting ingress traffic strictly from the Private Order-Application tier over designated database ports (e.g., PostgreSQL `5432` or MySQL `3306`).

## 8. Architectural Validation Checklist
- [x] Primary VPC master CIDR set to `10.50.0.0/16`.
- [x] Six independent `/24` network subnets explicitly calculated.
- [x] All subnets reside structurally inside the master VPC range boundaries.
- [x] Subnet IP boundaries checked: 100% free of network overlap conflicts.
- [x] Even distribution across two geographic Availability Zones (`eu-west-1a` / `eu-west-1b`).
- [x] Zero live infrastructure has been deployed; documentation denotes a planned architectural framework.

## 9. Next Steps and Iterative Enhancements
* Design network ACL (NACL) state boundaries to block malicious traffic at the subnet line.
* Map secure outbound patching paths for the private tiers using a high-availability NAT Gateway configuration.
* Model the internal Security Groups to enforce the principle of least privilege between infrastructure components.
* Generate an Infrastructure as Code (IaC) template using Terraform to build out this plan automatically.
