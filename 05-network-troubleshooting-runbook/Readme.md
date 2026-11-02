# AWS Network Support & Troubleshooting Runbook

**Project:** AWS VPC Network Design  
**VPC CIDR:** `10.20.0.0/16`  
**Region:** `ap-southeast-1`  
**Date:** November 6, 2026  
**Support Levels:** L1 / L2 IT Support

---

## 1. Objective

This document outlines five common AWS networking incidents and their troubleshooting procedures.

The objective is to apply a structured IT Support approach by identifying possible causes, gathering evidence, investigating network configurations, applying corrective actions, and verifying service restoration.

The troubleshooting process follows:

1. Identify the reported issue.
2. Determine the affected resources and business impact.
3. Gather initial evidence.
4. Investigate the network configuration.
5. Identify the root cause.
6. Apply an approved corrective action.
7. Verify connectivity and service restoration.
8. Document the resolution.

---

## 2. Scenario 1: Unreachable Web Server

**Support Level:** L1 → L2  
**Affected Tier:** Public Web  
**Expected Port:** TCP 443 (HTTPS)

### Reported Issue

A user cannot access the company web application. The browser displays a connection timeout.

### Possible Causes

- Internet Gateway is not attached to the VPC.
- Public route table is missing the default route.
- Security Group does not allow inbound HTTPS.
- EC2 instance does not have the required public IPv4 address or Elastic IP.
- Network ACL blocks inbound or return traffic.
- Web server service is unavailable.

### L1 Investigation

- Confirm the application URL.
- Determine whether one user or multiple users are affected.
- Check whether other websites are accessible.
- Test DNS resolution.
- Test TCP 443 connectivity.
- Record error messages and timestamps.

Example commands:

```bash
nslookup example.com
```

```powershell
Test-NetConnection example.com -Port 443
```

### L2 Investigation

**Step 1: Check Internet Gateway**

- Confirm an Internet Gateway is attached to the correct VPC.

**Step 2: Check Public Route Table**

Verify the default route:

| Destination | Target |
|---|---|
| `10.20.0.0/16` | `local` |
| `0.0.0.0/0` | `igw-id` |

**Step 3: Check Security Group**

Verify `sg-web` allows:

- Inbound TCP 443
- Source: `0.0.0.0/0`

**Step 4: Check Public IP Address**

- Confirm the EC2 instance has an appropriate public IPv4 address or Elastic IP.
- Verify the address is associated with the correct resource.

### Corrective Action

- Attach the correct Internet Gateway if missing.
- Restore the default route to the Internet Gateway.
- Correct the inbound HTTPS Security Group rule.
- Associate the required public IPv4 address or Elastic IP.
- Investigate NACL restrictions or web service availability.

### Verification

- Test TCP 443 connectivity.
- Access the application URL.
- Confirm the web application loads successfully.

---

## 3. Scenario 2: Private Instance Fails OS Update

**Support Level:** L1 → L2  
**Affected Tier:** Private Application  
**Expected Connectivity:** Outbound internet access

### Reported Issue

An administrator reports that a private EC2 instance cannot download operating system updates or access package repositories.

### Possible Causes

- NAT Gateway is missing.
- Private Application Route Table has no default route to the NAT Gateway.
- NAT Gateway is unavailable.
- NAT Gateway is not deployed in a properly configured public subnet.
- Security Group or NACL blocks required traffic.
- DNS resolution is unavailable.

### L1 Investigation

- Confirm the EC2 instance is running.
- Record the package update error.
- Determine whether other instances experience the same issue.
- Test DNS resolution.
- Test access to the package repository.

Example commands:

```bash
nslookup archive.ubuntu.com
```

```bash
curl -I https://archive.ubuntu.com
```

### L2 Investigation

**Step 1: Check Private Application Route Table**

Expected routes:

| Destination | Target |
|---|---|
| `10.20.0.0/16` | `local` |
| `0.0.0.0/0` | `nat-id` |

**Step 2: Check NAT Gateway**

- Confirm the NAT Gateway exists.
- Confirm its state is Available.
- Confirm it is deployed in a public subnet.
- Confirm it has an Elastic IP.

**Step 3: Check Public Route Table**

The public subnet hosting the NAT Gateway should have:

| Destination | Target |
|---|---|
| `10.20.0.0/16` | `local` |
| `0.0.0.0/0` | `igw-id` |

**Step 4: Check Security Groups and NACLs**

- Verify outbound traffic is permitted where required.
- Verify NACLs permit return traffic.
- Confirm DNS resolution works.

### Corrective Action

- Create or restore the NAT Gateway if required and approved.
- Add the missing default route to the Private Application Route Table.
- Correct the public subnet route if necessary.
- Review Security Group and NACL restrictions.

**Note:** NAT Gateways allow private subnet resources to initiate outbound IPv4 connections. They do not provide unsolicited inbound internet access to private instances. NAT Gateways also incur AWS charges.

### Verification

- Retry the operating system update.
- Confirm package repositories are reachable.
- Confirm the update completes successfully.

---

## 4. Scenario 3: Application Fails to Reach Amazon RDS

**Support Level:** L1 → L2  
**Affected Tiers:** Application and Database  
**Expected Ports:** TCP 3306 / TCP 5432

### Reported Issue

The application is running, but users receive database connection errors.

The application cannot retrieve or store information in Amazon RDS.

### Possible Causes

- `sg-db` does not allow inbound traffic from `sg-app`.
- Incorrect database port.
- Incorrect database endpoint.
- Database instance is unavailable.
- NACL blocks the connection or return traffic.
- Database authentication or configuration issue.

### L1 Investigation

- Confirm whether the application is accessible.
- Determine whether all users are affected.
- Record the database connection error.
- Confirm the database engine and expected port.

Common database ports:

| Database Engine | Port |
|---|---|
| MySQL | TCP 3306 |
| PostgreSQL | TCP 5432 |

### L2 Investigation

**Step 1: Inspect `sg-db` Inbound Rules**

Expected configuration:

| Protocol | Port | Source |
|---|---|---|
| TCP | 3306 | `sg-app` |
| TCP | 5432 | `sg-app` |

Only enable the port required by the actual database engine.

**Step 2: Verify Security Group Reference**

The source should reference `sg-app`, not simply a broad CIDR such as `10.20.0.0/16`.

This restricts database access to resources associated with the specified Security Group, subject to AWS Security Group reference requirements.

**Step 3: Check RDS Configuration**

- Confirm the database endpoint.
- Confirm the database status is Available.
- Verify the correct database port.
- Confirm the database is deployed in the intended VPC.

**Step 4: Check Network Connectivity**

- Verify the VPC local route.
- Inspect NACL inbound and outbound rules.
- Confirm return traffic is permitted.
- Review database logs if available.

**Step 5: Separate Network and Authentication Issues**

A successful TCP connection does not guarantee successful database authentication.

If network connectivity succeeds, investigate credentials, database permissions, connection limits, and application configuration.

### Corrective Action

- Correct the `sg-db` inbound rule.
- Use `sg-app` as the source instead of an unnecessarily broad CIDR.
- Correct the database endpoint or port.
- Restore the database service if unavailable.
- Escalate authentication issues to the database administrator.

### Verification

- Test connectivity from the Application tier.
- Confirm the application can connect to RDS.
- Perform an approved read-only database test.
- Confirm the application functions normally.

---

## 5. Scenario 4: Unintended Public Exposure

**Support Level:** L1 → L2  
**Affected Tier:** Private Application or Database  
**Security Priority:** High

### Reported Issue

A security review identifies that a supposedly private subnet may have an unintended route to the Internet Gateway.

This could expose resources to unintended public network access if other required conditions are also present.

### Possible Causes

- Private subnet is associated with the wrong route table.
- Private route table contains an accidental Internet Gateway route.
- Incorrect subnet-to-route-table association.
- Resource has an unnecessary public IP address.
- Security Group allows overly broad inbound access.

### L1 Investigation

- Identify the affected subnet and resource.
- Confirm whether the subnet is intended to be public or private.
- Record the reported security finding.
- Escalate potential exposure through the security incident process.

### L2 Investigation

**Step 1: Inspect Route Table Associations**

- Open the VPC console.
- Navigate to Route Tables.
- Identify the route table associated with the affected subnet.
- Confirm the actual subnet association.

**Step 2: Inspect Default Routes**

An unintended public route looks like:

| Destination | Target |
|---|---|
| `0.0.0.0/0` | `igw-id` |

Expected private Application route:

| Destination | Target |
|---|---|
| `10.20.0.0/16` | `local` |

If outbound internet access is required, the intended default route should normally point to a NAT Gateway:

| Destination | Target |
|---|---|
| `0.0.0.0/0` | `nat-id` |

**Step 3: Review Resource Exposure**

- Check for public IPv4 addresses or Elastic IPs.
- Review inbound Security Group rules.
- Inspect NACL rules.
- Confirm the resource is not unintentionally accessible from the internet.

### Corrective Action

- Remove an accidental Internet Gateway route after authorization.
- Associate the correct private route table.
- Remove unnecessary public addressing through an approved change.
- Restrict overly broad Security Group rules.
- Notify the security team if unintended exposure is confirmed.

### Verification

- Confirm the private subnet uses the correct route table.
- Confirm no unintended Internet Gateway default route remains.
- Review public addressing and Security Group configuration.
- Document the security review result.

**Important:** An Internet Gateway route alone does not automatically make an instance publicly reachable. Public addressing and appropriate network/security configuration also matter.

---

## 6. Scenario 5: SSH Timeout vs. Connection Refused

**Support Level:** L1 → L2  
**Affected Service:** SSH  
**Expected Port:** TCP 22

### Reported Issue

An administrator cannot connect to an EC2 Linux instance using SSH.

The connection fails with either:

- Connection timed out
- Connection refused

These errors can indicate different problems.

### A. SSH Connection Timed Out

**Possible Causes**

- Security Group blocks inbound TCP 22.
- NACL blocks inbound SSH or return traffic.
- Incorrect route table.
- Missing public connectivity path.
- Incorrect public IP address.
- Instance is unavailable.

**L1 Investigation**

- Confirm the target IP address.
- Check whether the instance is running.
- Determine whether other administrators are affected.
- Record the exact error and timestamp.
- Test TCP 22 connectivity.

Example:

```powershell
Test-NetConnection server-ip -Port 22
```

**L2 Investigation**

1. Verify the instance has the expected public connectivity path.
2. Check the route table and Internet Gateway.
3. Inspect the Security Group inbound rule.
4. Verify TCP 22 is allowed only from the approved administrator IP, if SSH access is required.
5. Inspect NACL inbound and outbound rules.
6. Confirm the instance is reachable through the intended network path.

**Corrective Action**

- Correct the route or connectivity path.
- Allow SSH only from an approved administrator IP if required.
- Correct NACL rules if they block the connection.

### B. SSH Connection Refused

**Possible Causes**

- SSH service is stopped.
- SSH daemon is not listening.
- SSH is listening on a different port.
- Local host firewall blocks the connection.
- The destination actively rejects connections to that port.

**L1 Investigation**

- Record the exact error.
- Confirm the target IP and port.
- Test TCP 22 connectivity.
- Determine whether the issue affects other administrators.

**L2 Investigation**

If authorized access is available through AWS Systems Manager Session Manager or another approved administrative method, inspect the instance.

Check SSH service status:

```bash
sudo systemctl status ssh
```

Some Linux distributions use:

```bash
sudo systemctl status sshd
```

Check whether SSH is listening:

```bash
sudo ss -tlnp | grep ':22'
```

Review local firewall rules if necessary.

**Corrective Action**

- Restore the SSH service if confirmed stopped.
- Correct the SSH listening configuration.
- Review local firewall rules.
- Verify the service is listening on the expected port.

### Key Difference

| Error | Possible Meaning |
|---|---|
| Connection timed out | Connection did not complete; investigate routing, firewall rules, and host reachability |
| Connection refused | Destination or intermediate device actively rejected the connection; investigate whether the service is listening |

**Important:** These are troubleshooting clues, not definitive diagnoses. A timeout does not always mean a Security Group block, and a refusal does not always mean the SSH service is down.

Never open SSH to `0.0.0.0/0` as a quick workaround.

### Verification

- Retry SSH from the approved source.
- Confirm TCP 22 is reachable.
- Confirm an authorized administrator can log in.
- Document the actual root cause.

---

## 7. Troubleshooting Quick Reference

| Scenario | First Things to Check |
|---|---|
| Unreachable Web Server | IGW attachment → Public route table → SG TCP 443 → Public IP/EIP |
| Private Instance Fails OS Update | NAT Gateway → Private App route table → Public NAT route |
| Application Fails to Reach RDS | `sg-db` inbound source → `sg-app` → Correct database port |
| Unintended Public Exposure | Route table associations → Accidental IGW routes → Public addressing |
| SSH Timeout vs. Refused | Network path and firewall → SSH service and local port |

---

## 8. L1 vs. L2 Responsibilities

| L1 Service Desk | L2 Network / Cloud Support |
|---|---|
| Gather user symptoms | Perform network-path analysis |
| Confirm business impact | Inspect route tables |
| Record error messages | Investigate IGW and NAT Gateway |
| Run basic DNS and port tests | Analyze Security Groups and NACLs |
| Record timestamps | Review EC2 and RDS connectivity |
| Follow approved procedures | Identify root cause |
| Escalate with evidence | Implement approved infrastructure changes |

L1 should not independently modify production route tables, Security Groups, or NACLs without authorization.

---

## 9. Escalation Evidence Checklist

Before escalating an AWS networking incident:

- [ ] Affected user or system identified.
- [ ] Business impact documented.
- [ ] Affected application or server identified.
- [ ] Date and time recorded.
- [ ] Exact error message captured.
- [ ] Source and destination IP recorded where appropriate.
- [ ] Port connectivity test performed.
- [ ] Relevant route table findings recorded.
- [ ] Security Group findings recorded.
- [ ] NACL findings recorded.
- [ ] Troubleshooting steps documented.
- [ ] Root cause or suspected cause explained.
- [ ] Corrective action and verification recorded.

---

## 10. Personal Reflection

My Service Desk experience has taught me to investigate incidents systematically, gather evidence, and avoid assuming the cause before testing.

This AWS troubleshooting exercise extends those skills into cloud infrastructure.

I am learning how routing, public and private addressing, NAT Gateways, Security Groups, and NACLs affect connectivity.

The main lesson is that troubleshooting requires understanding the entire network path, not just checking one firewall rule.

---

## 11. Project Status

**Status:** Troubleshooting documentation completed.

**Deployment status:** Not deployed or tested against live AWS infrastructure.

This runbook represents documented troubleshooting procedures and diagnostic examples, not proof of a live AWS environment.
