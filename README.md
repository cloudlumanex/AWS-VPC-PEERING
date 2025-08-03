# AWS VPC Peering (Stage-Dev)
This repository documents the setup of a **VPC Peering connection between two AWS accounts** `Stage` and `Dev`  enabling **secure and private communication** between EC2 instances across isolated environments within the **same AWS region**.
This architecture is part of **CloudNG's internal network design** for environment segmentation, collaboration, and operational security.

CloudNG is a cloud-first organization building scalable and secure infrastructure solutions using modern DevOps practices. This project reflects our commitment to modular, isolated, and auditable cloud environments for every stage of the development lifecycle.

---

## Project Summary

- **Accounts Involved**: Stage Account and Dev Account
- **Region**: Same AWS Region `eu-west-1`
- **VPC CIDRs**:
  - Stage: `30.0.0.0/16`
  - Dev:  `15.0.0.0/16`
- **Goal**: Enable bidirectional private communication between EC2 instances in separate AWS accounts using VPC peering

---

## Steps to Implement VPC Peering

### 1. Create VPC Peering Connection (from Stage)
1. Navigate to **VPC Dashboard > Peering Connections** in the **Stage account**
2. Click **“Create Peering Connection”**
3. Configure:
   - **Peering connection name**: `stage-dev-peering`
   - **Requester VPC**: Select Stage VPC
   - **Account**: Select *Another account*
   - **Account ID**: Enter Dev AWS Account ID
   - **Accepter VPC ID**: Enter Dev's VPC ID
4. Click **“Create Peering Connection”**
5. <img width="1573" height="743" alt="Image" src="https://github.com/user-attachments/assets/91bbb90c-1338-4273-913c-d0e327aac784" />
---

### 2. Accept Peering Request (on Dev)
1. Log in to the **Dev AWS account**
2. Go to **VPC Dashboard > Peering Connections**
3. Select the pending request
4. Click **“Accept Request”**
5. <img width="1919" height="828" alt="Image" src="https://github.com/user-attachments/assets/35b1c823-1ee8-4523-b1f5-be3c5e239635" />

---

### 3. Update Route Tables (Both Accounts)
For each VPC:
1. Go to **Route Tables** > Select the one associated with subnets
2. Click **“Edit routes”**
3. Add:
   - **Destination**: CIDR of peer VPC
   - **Target**: Peering Connection ID (e.g., `pcx-abc12345`)

---

### 4. Update Security Groups
Modify EC2 instance security groups in both accounts:
- Allow traffic from peer VPC’s CIDR block
  - (Stage SG): allow `15.0.0.0/16`
  - (Dev SG): allow `30.0.0.0/16`
  - (Stage SG): allow ICMP `0.0.0.0/0`
  - (Dev SG): allow ICMP `0.0.0.0/0`
---

### 5. Test Connectivity
From Stage:
```bash
ping (private IP of othe other EC2 instance)
````
<img width="648" height="579" alt="Image" src="https://github.com/user-attachments/assets/0ef2ffc6-4adc-4035-9e95-6fd48d03c22a" />

##  Security Considerations
* I applied the principle least privilege rules in security groups.
* Monitor activity with VPC Flow Logs.
* Avoid overlapping CIDRs
  

