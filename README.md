# AWS VPC Peering (Stage - Dev)

This repository documents the setup of a **VPC peering connection between two AWS accounts** Stage and Dev enabling secure and private communication between EC2 instances across environments in the **same region**.

---

## Project Summary

- **Accounts Involved**: Stage Account and Dev Account
- **Region**: Same AWS Region (e.g., `eu-west-1`)
- **VPC CIDRs**:
  - Stage: `10.0.0.0/16`
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
  - (Dev SG): allow `10.0.0.0/16`
  - (Stage SG): allow ICMP `0.0.0.0/0`
  - (Dev SG): allow ICMP `0.0.0.0/0`

---

### 5. Test Connectivity
From Stage:
```bash
ping (private IP of othe other EC2 instance)

