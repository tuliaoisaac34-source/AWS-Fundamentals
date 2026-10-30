# AWS VPC Architecture: Route Table & Internet Gateway Design

## 1. Project Overview
This document outlines the network and routing architecture for a fictional corporate AWS VPC in the **Asia Pacific (Singapore) region (`ap-southeast-1`)**. 

The main goal of this design is clear network isolation: separating public-facing web servers from backend application and database components. The architecture utilizes a single VPC spanning two Availability Zones (AZs) with six total subnets.

* **VPC CIDR:** `10.20.0.0/16`
* **Status:** Proposed / Review stage. No resources have been deployed or tested in a live environment.

---

## 2. Network Design Summary
The network is split into three functional tiers across two AZs to enforce security boundaries:

* **Public Web Tier:** Handles incoming, internet-facing traffic (e.g., ALBs, public reverse proxies).
* **Private App Tier:** Houses the internal business logic, microservices, and backend application servers.
* **Private DB Tier:** Strictly isolates database workloads. It has zero direct communication paths to or from the public internet.

**Design Philosophy:** Keep attack surfaces minimal. Each tier has a dedicated routing purpose. The web tier explicitly allows internet traffic, while the application and database tiers are completely locked down from direct public routing.

---

## 3. Public Route Table
* **Name:** `company-public-rt`
* **Subnet Associations:**
  * `Public Web-A` (`10.20.1.0/24`)
  * `Public Web-B` (`10.20.2.0/24`)

### Routing Table

| Destination | Target | Purpose |
| :--- | :--- | :--- |
| `10.20.0.0/16` | `local` | Internal VPC communication |
| `0.0.0.0/0` | `igw-xxxxxxxxxxxxxxxxx` | Routes all other IPv4 traffic to the Internet Gateway |

> *Note: `igw-xxxxxxxxxxxxxxxxx` is a placeholder for the actual Internet Gateway ID (e.g., `igw-0123456789abcdef0`).*

### Implementation Notes
* The default route (`0.0.0.0/0`) pointing to the IGW is what makes these subnets "public." 
* A route table rule alone doesn't automatically expose an EC2 instance. For full internet connectivity, the IGW must be attached to the VPC, and instances require public IPv4 addresses (or Elastic IPs) along with matching Security Group and Network ACL rules.

---

## 4. Private Application Route Table
* **Name:** `company-private-app-rt`
* **Subnet Associations:**
  * `Private App-A` (`10.20.3.0/24`)
  * `Private App-B` (`10.20.4.0/24`)

### Routing Table

| Destination | Target | Purpose |
| :--- | :--- | :--- |
| `10.20.0.0/16` | `local` | Internal VPC communication |
| `0.0.0.0/0` | `nat-xxxxxxxxxxxxxxxxx` | *(Optional)* Outbound-only traffic for patches/updates via NAT Gateway |

### Implementation Notes
* This tier does not have a route to the IGW, meaning resources cannot be reached directly from the internet.
* If backend applications require outbound internet access to download patches, API integrations, or third-party dependencies, a **NAT Gateway** should be deployed in the Public Web tier and referenced here as the target for `0.0.0.0/0`.

---

## 5. Private Database Route Table
* **Name:** `company-private-db-rt`
* **Subnet Associations:**
  * `Private DB-A` (`10.20.5.0/24`)
  * `Private DB-B` (`10.20.6.0/24`)
 ### Routing Table

| Destination | Target | Purpose |
| :--- | :--- | :--- |
| `10.20.0.0/16` | `local` | Internal VPC communication |

### Implementation Notes
* **Complete Egress Isolation:** Omitting a default route prevents any direct, unintended internet exposure. The databases should exclusively handle connections initiated by authorized backend applications.
* **Defense in Depth:** The local route simply allows communication at the network layer. Granular access must still be enforced using Security Groups, Network ACLs, and database-level user authentication.

---

## 6. End-to-End Traffic Flow: Browser to Public Web Subnet
This scenario traces an HTTPS request from a client browser to a resource running inside `Public Web-A`.

1. **DNS Resolution:** The user enters the website domain (e.g., `www.example.com`). The browser resolves this domain to its public IP address via Amazon Route 53 or an external DNS provider.
2. **Initiating the Request:** The browser sends traffic over TCP Port 443 (HTTPS) directed toward the resolved public IP.
3. **Internet Transit:** Standard internet routing infrastructure forwards the packets across the web until they target the AWS network edge.
4. **Ingress via Internet Gateway:** The packets reach the VPC's attached Internet Gateway (IGW). AWS automatically translates the target public IPv4 address to the internal, private IPv4 address assigned to the web resource.
5. **VPC Routing & Delivery:** The VPC evaluates its internal routing map. Because the target private IP falls within the `10.20.0.0/16` CIDR, the traffic is forwarded directly to the public subnet.
6. **Firewall Evaluation:** AWS evaluates the network boundary. The subnet's Network ACL and the instance's Security Group must both explicitly allow inbound TCP 443 traffic (and permit ephemeral ports on the return path).
7. **Application Processing:** The target web server (e.g., NGINX, Apache, or an ALB) receives the packet, processes the request, and generates a response.
8. **Return Path:** The response follows the inverse path. The public route table evaluates the destination as outside the VPC, hitting the `0.0.0.0/0` rule which pushes the traffic back out through the IGW to the client browser.

> **Crucial Clarification:** An IGW rule in a route table enables *outbound* routing logic for non-VPC destinations. True inbound access requires a cohesive combination of public IP mappings, correct VPC routing tables, proper resource addressing, and permissive firewall rules.

---

## 7. Strategic Isolation of Private Tiers
The application and database route tables explicitly exclude a default route (`0.0.0.0/0`) for three core reasons:

* **Minimized Attack Surface:** Backend components are only accessible to internal peers, neutralizing random internet-based scans or brute-force attempts.
* **Architectural Boundaries:** Keeping tiers logically isolated creates distinct checkpoints where traffic must cross security boundaries (like internal security groups) rather than blending into a flat network.
* **Controlled Access:** Outbound connectivity is treated as an intentional architectural addition rather than a default convenience. 

---

## 8. Engineering Decisions & Architecture Trade-offs

* **Decision 1: Isolated Route Tables by Tier**
  * *Rationale:* Splitting public, app, and database layers into distinct route tables makes the routing topology explicit, highly auditable, and easier to review for security compliance.
* **Decision 2: Restricted Inbound Access for the App Tier**
  * *Rationale:* Mid-tier applications rarely require a public footprint. Enforcing an internal-only perimeter ensures they only interact with the upstream public web tier.
* **Decision 3: Hard Isolation of the Database Tier**
  * *Rationale:* Database engines should never be exposed to the public internet. Restricting this tier to a local-only route keeps data layers tucked securely behind the application tier.
* **Decision 4: Postponed NAT Gateway Deployment**
  * *Rationale:* NAT Gateways incur fixed hourly costs and data processing fees. By leaving them out of the initial baseline, the architecture avoids unnecessary expenditures until an outbound dependency is explicitly confirmed.
* **Decision 5: Design-First Validation**
  * *Rationale:* Reviewing all subnet masks, IGW mappings, routing logic, and security rules on paper prevents costly deployment reworks and immediate configurations errors in live cloud accounts.

---

## 9. Known Design Limitations
* **Proposed Blueprint:** This is an un-deployed, theoretical architecture meant for architectural reviews.
* **Placeholder Objects:** Resource IDs like `igw-xxxxxxxxxxxxxxxxx` are structural examples and must be updated with active AWS resource identifiers upon creation.
* **Unverified Connectivity:** No live connection tests, performance evaluations, or security audits have been performed against physical resources.
* **No Outbound Egress:** Private tiers are completely dark to external resources. They cannot fetch external software updates or connect to public APIs without structural modifications (e.g., adding a NAT Gateway or VPC Endpoints).

---

## 10. Pre-Deployment Validation Checklist
- [ ] Public route table includes both the `local` route and the `0.0.0.0/0` IGW route.
- [ ] Public route table is correctly associated with `Web-A` and `Web-B` subnets.
- [ ] Private application route table contains only the `local` route.
- [ ] Private application route table is correctly associated with `App-A` and `App-B` subnets.
- [ ] Private database route table contains only the `local` route.
- [ ] Private database route table is correctly associated with `DB-A` and `DB-B` subnets.
- [ ] End-to-end browser-to-web-subnet traffic paths have been logically mapped.
- [ ] Return routing paths and accompanying firewall dependencies are accounted for.
- [ ] Key architectural trade-offs, engineering decisions, and cost metrics are fully documented.
- [ ] The document prominently flags that the architecture is a pre-production proposal.
