AWS VPC Network Design — Subnet Allocation & CIDR Plan
1. Project Overview
This document defines a proposed IPv4 addressing and subnet allocation plan for a fictional AWS-hosted application.
The design uses one Amazon Virtual Private Cloud (VPC) in the Asia Pacific (Singapore) Region, with public web subnets and private application and database subnets across two Availability Zones.
Design status: Planned architecture only. No live AWS resources have been deployed or tested.
2. AWS Region and Availability Zones
AWS Region: Asia Pacific (Singapore)
Region code: ap-southeast-1
Availability Zone A: ap-southeast-1a
Availability Zone B: ap-southeast-1b
Note: Availability Zone letter mappings can differ between AWS accounts. These labels represent the intended design; verify the actual AZ mapping in an AWS account before deployment.
3. VPC Configuration
VPC name: company-production-vpc
IPv4 CIDR block: 10.20.0.0/16
Total IPv4 addresses in the VPC CIDR: 65,536
Purpose: Provide a logically isolated network for the proposed application.
The VPC uses the private IPv4 range 10.20.0.0/16.
4. Subnet Allocation Plan
Availability Zone A — ap-southeast-1a
Subnet Name
CIDR Block
Intended Purpose
Public Web-A
10.20.1.0/24
Public-facing web tier
Private App-A
10.20.11.0/24
Internal application tier
Private DB-A
10.20.21.0/24
Private database tier
Availability Zone B — ap-southeast-1b
Subnet Name
CIDR Block
Intended Purpose
Public Web-B
10.20.2.0/24
Public-facing web tier
Private App-B
10.20.12.0/24
Internal application tier
Private DB-B
10.20.22.0/24
Private database tier
5. CIDR and Addressing Explanation
The VPC uses 10.20.0.0/16, which provides a larger address range from which smaller subnet ranges are allocated.
Each subnet uses a /24 CIDR block.
A /24 contains 256 IPv4 addresses in total. AWS reserves five addresses in each IPv4 subnet, leaving 251 addresses for other uses within the subnet.
The six proposed subnet ranges do not overlap and fit within the overall VPC CIDR block.
6. AWS-Reserved IPv4 Addresses
For a subnet such as 10.20.1.0/24, AWS reserves the following five IPv4 addresses:
Address
AWS Reservation
10.20.1.0
Network address
10.20.1.1
Reserved by AWS for the VPC router
10.20.1.2
Reserved by AWS for DNS
10.20.1.3
Reserved by AWS for future use
10.20.1.255
Last address in the subnet, reserved by AWS
The same reservation pattern applies to the other /24 subnets, using their respective address ranges.
Important: AWS reserves the first four IPv4 addresses and the last IPv4 address in each subnet. The final address is reserved by AWS; do not assume that AWS VPC subnets use traditional IPv4 broadcast behavior.
7. Public and Private Subnet Strategy
Public Web Subnets
The two public web subnets are intended to host resources that need to receive authorized internet-facing traffic.
Their route table is planned to include:
The local VPC route.
A default IPv4 route (0.0.0.0/0) to an Internet Gateway.
Internet reachability also depends on resource addressing, security rules, and other applicable network controls.
Private Application Subnets
The application subnets are intended for internal application components.
They should accept only the required application traffic from authorized sources, such as the web tier.
The initial design does not include a default route to an Internet Gateway.
Private Database Subnets
The database subnets are intended to contain database resources that are not directly accessible from the public internet.
The proposed security design allows database traffic only from the authorized application tier.
The initial route plan does not include a default route to an Internet Gateway.
8. Design Validation Checklist
VPC CIDR is 10.20.0.0/16.
Six /24 subnets are documented.
All subnet CIDR blocks fall within the VPC range.
No subnet CIDR ranges overlap.
Each subnet is assigned to the intended Availability Zone.
Public and private subnet purposes are explained.
AWS-reserved IPv4 addresses are documented correctly.
The design is clearly labelled as not deployed or tested.
9. Future Improvements
Define route tables and subnet associations.
Define security group rules and network ACL requirements.
Draw the network architecture diagram.
Evaluate private outbound access requirements.
Review availability, security, and cost before deployment.
