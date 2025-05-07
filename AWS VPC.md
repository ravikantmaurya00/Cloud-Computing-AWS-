# AWS VPC Setup with Public and Private Subnets

This guide walks you through creating a custom VPC with public and private subnets, including internet access configuration using an Internet Gateway and NAT Gateway.

## Prerequisites

- AWS account with appropriate permissions
- Basic understanding of networking concepts
- AWS CLI installed (optional)

## Step-by-Step Implementation

### 1. Create a Custom VPC

1. **Navigate to VPC Dashboard**
   - Sign in to AWS Management Console
   - Search for "VPC" and select "VPC" under Services

2. **Create Your VPC**
   - Click "Create VPC"
   - Select "VPC only" option
   - Configure settings:
     - Name tag: `my-custom-vpc`
     - IPv4 CIDR block: `10.0.0.0/16` (recommended for new VPCs)
     - Leave IPv6 disabled (unless needed)
     - Tenancy: Default
   - Click "Create VPC"

### 2. Create Subnets

#### Public Subnet
3. **Create Public Subnet**
   - In left sidebar, click "Subnets"
   - Click "Create subnet"
   - Configure:
     - VPC ID: Select your newly created VPC
     - Subnet name: `public-subnet-1a`
     - Availability Zone: Select any AZ (e.g., `us-east-1a`)
     - IPv4 CIDR block: `10.0.1.0/24`
   - Click "Create subnet"

#### Private Subnet
4. **Create Private Subnet**
   - Click "Create subnet" again
   - Configure:
     - VPC ID: Select your VPC
     - Subnet name: `private-subnet-1a`
     - Availability Zone: Same AZ as public subnet
     - IPv4 CIDR block: `10.0.2.0/24`
   - Click "Create subnet"

### 3. Create and Attach Internet Gateway

5. **Create Internet Gateway**
   - In left sidebar, click "Internet Gateways"
   - Click "Create internet gateway"
   - Name tag: `my-igw`
   - Click "Create internet gateway"

6. **Attach to VPC**
   - Select the new IGW
   - Click "Actions" > "Attach to VPC"
   - Select your VPC
   - Click "Attach internet gateway"

### 4. Configure Route Tables

#### Public Route Table
7. **Edit Public Route Table**
   - In left sidebar, click "Route Tables"
   - Locate the main route table for your VPC
   - Click "Actions" > "Edit routes"
   - Add route:
     - Destination: `0.0.0.0/0`
     - Target: Select your Internet Gateway
   - Click "Save changes"

8. **Associate Public Subnet**
   - Select your route table
   - Click "Subnet associations" tab
   - Click "Edit subnet associations"
   - Select your public subnet
   - Click "Save associations"

#### Private Route Table
9. **Create Private Route Table**
   - Click "Create route table"
   - Name tag: `private-route-table`
   - VPC: Select your VPC
   - Click "Create"

10. **Associate Private Subnet**
    - Select the new route table
    - Click "Subnet associations" tab
    - Click "Edit subnet associations"
    - Select your private subnet
    - Click "Save associations"

### 5. Create NAT Gateway

11. **Allocate Elastic IP**
    - In left sidebar, click "Elastic IPs"
    - Click "Allocate Elastic IP address"
    - Click "Allocate"

12. **Create NAT Gateway**
    - In left sidebar, click "NAT Gateways"
    - Click "Create NAT Gateway"
    - Configure:
      - Name: `my-nat-gateway`
      - Subnet: Select your public subnet
      - Elastic IP allocation ID: Select the EIP you just created
    - Click "Create NAT Gateway"
    - Wait for status to become "Available" (may take several minutes)

13. **Update Private Route Table**
    - Go back to "Route Tables"
    - Select your private route table
    - Click "Edit routes"
    - Add route:
      - Destination: `0.0.0.0/0`
      - Target: Select your NAT Gateway
    - Click "Save changes"

### 6. Launch EC2 Instances

#### Public Subnet Instance
14. **Launch Public Instance**
    - Navigate to EC2 Dashboard
    - Click "Launch Instance"
    - Configure:
      - Name: `public-instance`
      - AMI: Amazon Linux 2
      - Instance type: t2.micro
      - VPC: Select your VPC
      - Subnet: Select your public subnet
      - Auto-assign Public IP: Enable
      - Security group: Create new allowing SSH (port 22) from your IP
      - Key pair: Select existing or create new
    - Click "Launch Instance"

#### Private Subnet Instance
15. **Launch Private Instance**
    - Click "Launch Instance"
    - Configure:
      - Name: `private-instance`
      - AMI: Amazon Linux 2
      - Instance type: t2.micro
      - VPC: Select your VPC
      - Subnet: Select your private subnet
      - Auto-assign Public IP: Disable
      - Security group: Create new allowing SSH (port 22) from VPC CIDR (10.0.0.0/16)
      - Key pair: Use same key as public instance
    - Click "Launch Instance"

## Verification

1. **Test Public Instance Internet Access**
   - SSH into public instance
   - Run: `ping google.com` (should work)

2. **Test Private Instance Internet Access**
   - SSH into private instance via public instance (bastion host)
   - Run: `ping google.com` (should work via NAT Gateway)
   - Run: `curl ifconfig.me` (should show NAT Gateway IP)

## Clean Up

To avoid unnecessary charges:

1. Terminate both EC2 instances
2. Delete NAT Gateway
3. Release Elastic IP
4. Delete subnets
5. Detach and delete Internet Gateway
6. Delete VPC

## Architecture Diagram
