# AWS Security Rules Matrix — Security Groups & NACLs

**Project:** AWS VPC Network Design  
**VPC CIDR:** `10.20.0.0/16`  
**Region:** `ap-southeast-1`  
**Date:** November 5, 2026

---

## 1. Objective

This document defines the security rules for the Web, Application, and Database tiers of the AWS VPC architecture.

The objective is to implement layered network security while following the **Principle of Least Privilege**.

Each tier should only accept the network traffic required for its function.

## 2. Security Groups Rules Matrix

Security Groups are **stateful virtual firewalls** associated with AWS network interfaces and resources.

### Inbound and Outbound Rules

| Security Group | Direction | Protocol / Port | Source / Destination | Purpose |
|---|---|---|---|---|
| `sg-web` | Inbound | TCP 443 | `0.0.0.0/0` | Public HTTPS access |
| `sg-web` | Outbound | All | `0.0.0.0/0` | Allow outbound traffic |
| `sg-app` | Inbound | TCP 8080 | `sg-web` | Web-to-app communication only |
| `sg-app` | Outbound | All | `0.0.0.0/0` | Allow outbound traffic |
| `sg-db` | Inbound | TCP 3306 | `sg-app` | MySQL access |
| `sg-db` | Inbound | TCP 5432 | `sg-app` | PostgreSQL access |
| `sg-db` | Outbound | None | None | No outbound rules |

### Important Security Group Considerations

- Security Groups support **ALLOW rules only**. They do not support explicit DENY rules.
- For `sg-db`, remove the default outbound allow-all rule and leave the outbound rules empty.
- This prevents new outbound connections from being initiated through that Security Group.
- Response traffic for permitted inbound connections is still automatically allowed because Security Groups are stateful.

## 3. Explanation of Each Security Group

### A. `sg-web` — Public Web Tier

**Inbound:**
- Protocol: TCP
- Port: `443`
- Source: `0.0.0.0/0`

**Outbound:**
- Allow all outbound traffic.

**Reason:**

The Web tier is the public entry point of the architecture. HTTPS allows users to access the application through an encrypted connection.

Only TCP port `443` is exposed in this design.

### B. `sg-app` — Private Application Tier

**Inbound:**
- Protocol: TCP
- Port: `8080`
- Source: `sg-web` only.

**Outbound:**
- Allow all outbound traffic.

**Reason:**

The Application tier should not accept direct public internet connections.

Only resources associated with `sg-web` should be allowed to communicate with the Application tier through TCP port `8080`.

### C. `sg-db` — Private Database Tier

**Inbound:**
- Protocol: TCP
- Port: `3306`
- Source: `sg-app` only.
- Protocol: TCP
- Port: `5432`
- Source: `sg-app` only.

**Outbound:**
- No outbound rules.

**Reason:**

- Port `3306` is commonly used by MySQL.
- Port `5432` is commonly used by PostgreSQL.

The Database tier should only accept connections from the Application tier.

**Engineering note:** In an actual deployment, only the port required by the selected database engine should be enabled.

## 4. Administrative Security Policy

The following unrestricted inbound access rules are **PROHIBITED**:

### SSH (Secure Shell)

- Protocol: TCP
- Port: `22`
- Prohibited source: `0.0.0.0/0`

### RDP (Remote Desktop Protocol)

- Protocol: TCP
- Port: `3389`
- Prohibited source: `0.0.0.0/0`

**Reason:**

Unrestricted SSH and RDP expose remote administration services to the public internet and increase the risk of unauthorized access.

Administrative access should use an approved and controlled method.

Where supported, **AWS Systems Manager Session Manager** can be considered.

If SSH or RDP is necessary, access should be restricted to an approved and trusted IP range.

## 5. Security Groups vs. Network ACLs

Security Groups and Network ACLs (NACLs) provide different layers of network security in AWS.

| Feature | Security Groups | Network ACLs |
|---|---|---|
| Applied to | Network interfaces / resources | Subnets |
| Behavior | Stateful | Stateless |
| Rules | Allow only | Allow and Deny |
| Rule evaluation | All applicable rules | Ascending rule number |
| Return traffic | Automatically allowed | Must be explicitly allowed |

### Why Security Groups Automatically Allow Return Traffic

Security Groups are **STATEFUL**.

When an inbound connection is permitted, AWS tracks the connection state.

**Example:**

1. A user connects to the Web server using HTTPS on TCP port `443`.
2. `sg-web` allows the inbound connection.
3. The Web server processes the request.
4. The server sends a response.
5. The response traffic is automatically permitted.

A separate outbound rule is not required solely to allow the response to that permitted inbound connection.

### Why NACLs Require Explicit Return-Traffic Rules

Network ACLs are **STATELESS**.

They do not track established connections.

**Example:**

1. A client connects to a Web server on TCP port `443`.
2. The inbound NACL must allow TCP port `443`.
3. The server sends a response to the client's temporary source port.
4. The outbound NACL must allow that response traffic.
5. If the outbound NACL blocks the client's ephemeral port, the connection can fail even if inbound TCP `443` is allowed.

A commonly used broad ephemeral port example is TCP `1024–65535`.

The actual ephemeral port range depends on the client operating system and application.

## 6. NACL Security Considerations

NACLs provide an additional subnet-level security layer.

For this VPC design:

- Public Web subnets must permit inbound HTTPS and corresponding outbound response traffic.
- Private Application subnets must permit approved application traffic and return traffic.
- Private Database subnets must permit approved database traffic and return traffic.
- Both inbound and outbound NACL rules must be reviewed.
- NACL rules are evaluated in ascending numerical order.
- Avoid unrestricted allow rules unless there is a documented requirement.

**Important:**

The Security Group matrix in this document is not a complete production NACL configuration.

Exact NACL rules must account for:

- Source and destination CIDR ranges.
- Ephemeral ports.
- Traffic direction.
- Required application communication flows.
- Return-traffic requirements.

## 7. Personal Engineering Decisions

The following decisions were made when designing the VPC security architecture:

1. **Public Web Access:** Designed the Web tier to accept public HTTPS traffic while keeping the Application and Database tiers restricted.

2. **Application Communication:** Selected TCP port `8080` for communication between the Web and Application tiers.

3. **Database Communication:** Selected TCP ports `3306` and `5432` for database communication, depending on the database engine.

4. **Security Group References:** Used Security Group references instead of broad CIDR access for internal communication. This makes the architecture more restrictive and easier to maintain.

5. **Database Outbound Restrictions:** Removed the default outbound allow-all rule from the Database Security Group to reduce unnecessary outbound access.

6. **Administrative Access Restrictions:** Prohibited unrestricted SSH and RDP access to reduce exposure to unauthorized remote access.

These decisions follow the principle of least privilege and establish a foundation for a more secure AWS network architecture.

## 8. Validation Checklist

Use this checklist to verify the intended security configuration.

### Security Group Validation

- [ ] `sg-web` allows inbound TCP `443` from `0.0.0.0/0`.
- [ ] `sg-web` allows all outbound traffic.
- [ ] `sg-app` allows inbound TCP `8080` from `sg-web` only.
- [ ] `sg-app` allows all outbound traffic.
- [ ] `sg-db` allows inbound TCP `3306` from `sg-app` only.
- [ ] `sg-db` allows inbound TCP `5432` from `sg-app` only.
- [ ] `sg-db` has no outbound allow-all rule.

### Administrative Security Validation

- [ ] No unrestricted SSH (TCP `22`) rule exists.
- [ ] No unrestricted RDP (TCP `3389`) rule exists.
- [ ] Administrative access follows an approved security method.

### Network ACL Validation

- [ ] Stateful Security Group behavior is explained.
- [ ] Stateless NACL behavior is explained.
- [ ] NACL return-traffic requirements are documented.
- [ ] NACL inbound and outbound rules are considered.
- [ ] Ephemeral port requirements are documented.

## 9. Project Status

**Status:** Security design and documentation completed.

**Deployment Status:** Not deployed or connectivity-tested in AWS.

This document represents the intended security architecture, not a live AWS configuration.

Actual AWS implementation and connectivity testing will be performed in a future deployment phase.

---

**Project:** AWS VPC Network Design  
**Documentation:** Security Groups & Network ACLs  
**Repository:** AWS Fundamentals / Cloud Infrastructure Portfolio
