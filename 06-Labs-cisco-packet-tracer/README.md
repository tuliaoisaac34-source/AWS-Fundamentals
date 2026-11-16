# Week 1 – AWS VPC Network Design and Cisco Packet Tracer Simulation

## 1. AWS VPC Architecture

This diagram presents the planned AWS VPC architecture, including the public web subnets, private application subnets, private database subnets, Internet Gateway, and route table design.

![Week 1 AWS VPC Architecture](https://github.com/user-attachments/assets/60c8d071-1b5d-4536-9b82-1c45daf106da)

## 2. Cisco Packet Tracer Network Topology

The following topology represents the simulated network environment used to test subnet connectivity, routing, and traffic filtering.

![Network Topology](https://github.com/user-attachments/assets/5272c3e6-f705-4d83-a5f8-2c26ceb95ccf)

## 3. Network Device and VLAN Configuration

### 3.1 PC and External Test Server IP Configuration

![PC IP Configuration](https://github.com/user-attachments/assets/2e47ad43-58c5-412d-ac08-e6169778bf4c)

![Internet Test Server IP Configuration](https://github.com/user-attachments/assets/355c04a0-c640-4b58-b806-c39dea3aea2e)

### 3.2 VLAN Configuration

![VLAN Configuration](https://github.com/user-attachments/assets/81518e13-ba56-428e-b630-c71382fd780d)

### 3.3 Switch Trunk Verification

![Show Interfaces Trunk](https://github.com/user-attachments/assets/57bd3fe0-e4c8-4892-a569-6030f7381a8b)

## 4. Network Connectivity Testing

These tests validate communication between the simulated network tiers and the external test network.

### 4.1 Successful WEB-A to APP-A Connectivity

![Successful WEB-A to APP-A Packet Flow](https://github.com/user-attachments/assets/809c4a7d-38a5-430e-817e-9026e207185f)

### 4.2 Successful APP-A to DB-A Connectivity

![Successful APP-A to DB-A Ping](https://github.com/user-attachments/assets/e0e376cb-1f75-4b3e-9d4b-fc69718331f7)

### 4.3 Successful WEB-A External Network Connectivity

![Successful WEB-A External Ping](https://github.com/user-attachments/assets/bbd7c2b3-46cf-436a-b3db-f9a26170fa1d)

### 4.4 Successful HTTP Browser Test from WEB-A

![Successful HTTP Browser Test from WEB-A](https://github.com/user-attachments/assets/c0d11be7-caaa-422c-a91c-f040c64cf7c2)

### 4.5 Successful Database and Application Connectivity Test

![DB-A and APP-A Test Success](https://github.com/user-attachments/assets/0f926123-0b3b-4570-b1d5-50b1f0b34f53)

### 4.6 Successful Traffic Simulation

![Successful Traffic Simulation](https://github.com/user-attachments/assets/710f1475-5c81-41d6-8f4f-521a5cec5a0a)

## 5. Security and Traffic Filtering Tests

The following tests demonstrate the simulated security restrictions applied to private application and database subnets.

### 5.1 Blocked APP-A External Network Ping

![Blocked APP-A External Ping](https://github.com/user-attachments/assets/0e65a94b-3ac0-41e6-b840-0f460b316c4f)

### 5.2 Blocked HTTP Browser Test from APP-A

![Failed HTTP Browser Test from APP-A](https://github.com/user-attachments/assets/b994a589-5b59-471c-8312-94c8191dffb2)

### 5.3 Blocked Traffic Simulation

![Blocked Traffic Simulation](https://github.com/user-attachments/assets/a9711230-96cb-416a-866e-46a612a3713d)

## 6. Router and Switch Verification

### 6.1 Interface Status Verification

![Show IP Interface Brief](https://github.com/user-attachments/assets/66e14490-c5ba-4dd2-bf3c-bb7e8374e732)

### 6.2 IP Routing Table Verification

![Show IP Route](https://github.com/user-attachments/assets/1ee154a7-ca05-471e-863b-4974aa7ee658)

### 6.3 Access Control List Verification

![Show Access Lists](https://github.com/user-attachments/assets/3c2a4f43-b17b-4b18-86e9-f21650167ddc)

### 6.4 Running Configuration Verification

![Show Running Configuration](https://github.com/user-attachments/assets/31bba739-c4aa-475b-8538-d350a93caeb1)

### 6.5 Router and Switch Verification Summary

![Router and Switch Verification](https://github.com/user-attachments/assets/e1a9b7f8-13c7-4aaa-8f0c-bbef7c4626b2)

---

## 7. Project Summary

This project demonstrates the design of a multi-tier AWS VPC network and a Cisco Packet Tracer simulation used to validate fundamental networking concepts.

**Key concepts practiced:**
- VPC and subnet architecture planning
- Public and private subnet segmentation
- IPv4 addressing and subnetting
- VLAN configuration and trunking
- Router-on-a-stick inter-VLAN routing
- Static routing
- Access Control Lists (ACLs)
- Internal and external connectivity testing
- Traffic filtering and troubleshooting
- Router and switch configuration verification

**Important implementation note:** The AWS architecture was designed as a reference architecture. Cisco Packet Tracer was used to simulate selected networking and security behaviors. This project does not represent an actual AWS deployment, and Cisco ACLs are not exact equivalents of AWS Security Groups or Network ACLs.
