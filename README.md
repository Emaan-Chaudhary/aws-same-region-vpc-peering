# Same Region VPC Peering (AWS Networking Project)

This project demonstrates how to create a **VPC Peering connection within the same AWS region** and enable **private communication between EC2 instances across different VPCs**.

![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/infra.png?raw=true.png)

---

## 🧠 Project Overview

- Two VPCs in the same AWS region (`us-east-1`)
- Separate CIDR blocks
- EC2 instances deployed in private subnets
- VPC Peering connection established
- Route tables updated to allow inter-VPC traffic

---

## 🛠️ Technologies Used

- Amazon VPC
- EC2 (Amazon Linux 2023)
- VPC Peering
- Route Tables
- Security Groups

---

## 📌 Architecture Summary

| Component | VPC-1A | VPC-1B |
|---------|--------|--------|
| CIDR | 10.75.0.0/16 | 192.168.0.0/16 |
| AZ | us-east-1a | us-east-1b |
| Public Subnet | 10.75.1.0/24 | 192.168.1.0/24 |
| Private Subnet | 10.75.2.0/24 | 192.168.2.0/24 |

---

## 🚀 Step-by-Step Implementation

### 1️⃣ Create VPC-1A

- Region: `us-east-1`
- CIDR: `10.75.0.0/16`
- 1 Availability Zone (`us-east-1a`)
- 1 Public + 1 Private subnet
- NAT Gateway: Enabled (Zonal)

Inbound Security Group Rules:
- SSH (22) — *restricted in real environments*
- ICMP — for testing connectivity
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/vpc1a.png?raw=true.png)
---

### 2️⃣ Create VPC-1B

- Region: `us-east-1`
- CIDR: `192.168.0.0/16`
- 1 Availability Zone (`us-east-1b`)
- 1 Public + 1 Private subnet
- NAT Gateway: Enabled (Zonal)

Inbound Security Group Rules:
- SSH (22)
- ICMP
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/vpc1b.png?raw=true.png)
---

### 3️⃣ Launch EC2 Instances in VPC-1A

**Jump Server**
- Subnet: Public
- Public IP: Enabled
- Purpose: Bastion / testing access
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/jum-ec2.png?raw=true.png)
**Private EC2**
- Subnet: Private
- Public IP: Disabled
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/private-ec2-1A.png?raw=true.png)
---

### 4️⃣ Launch EC2 Instance in VPC-1B

- Subnet: Private
- Public IP: Disabled
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/private-ec2-1B.png?raw=true.png)
---

### 5️⃣ Connectivity Test (Before Peering)

From `Jump-Server-1a`:
- Ping `Private-EC2-1a` ✅
- Ping `Private-EC2-1b` ❌ (Expected – no routing)
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/packet-loss.png?raw=true.png)
---

### 6️⃣ Create VPC Peering Connection

- Requester VPC: `VPC-1A`
- Accepter VPC: `VPC-1B`
- Region: Same (`us-east-1`)
- Accept the peering request
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/vpc-peering.png?raw=true.png)
---

### 7️⃣ Update Route Tables

**VPC-1A Private Route Table**
**VPC-1A Private Route Table**
Destination: 192.168.0.0/16
Target: VPC Peering Connection
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/rt.png?raw=true.png)

**VPC-1B Private Route Table**
Destination: 10.75.0.0/16
Target: VPC Peering Connection


---

### 8️⃣ Connectivity Test (After Peering)

- Ping from `Private-EC2-1a` → `Private-EC2-1b` ✅
- Ping successful confirms **VPC Peering is working**
![Architecture Diagram](https://github.com/Emaan-Chaudhary/aws-same-region-vpc-peering/blob/main/images/result.png?raw=true.png)
---

## ✅ Result

The two VPCs can now communicate **privately** over AWS’s internal network without using the internet.

---

## ⚠️ Important Notes

- VPC Peering **does not support transitive routing**
- CIDR blocks **must not overlap**
- Security groups and NACLs must allow traffic
- DNS resolution between VPCs requires additional configuration

---

## 📚 Learning Outcome

- Understand VPC Peering architecture
- Configure routing between isolated networks
- Debug network connectivity issues in AWS

---
