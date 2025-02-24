# Creation-of-VPC-and-deployment-of-Private-and-Public-Subnet
## Overview
This repository provides step-by-step instructions for creating a VPC, deploying public and private servers, and enabling internet access for a private server using a NAT Gateway.

## Steps to Create Custom Network

### 1. Create VPC
- **VPC Name**: DemoVPC  
- **IPv4 CIDR**: 10.0.0.0/16

### 2. Create Internet Gateway (IG) and Attach to VPC
- **Internet Gateway Name**: demoIG
- Attach to the created VPC.

### 3. Create Subnets
- **Public Subnet**: Select Availability Zone and enable auto-assign public IP.
- **Private Subnet**: Select Availability Zone, but disable auto-assign public IP.

### 4. Create Route Table
- **Public Route Table Name**: demoVPCRT

### 5. Configure Routing
- **Route IG to Route Table**: Add a route for `0.0.0.0/0` to the Internet Gateway.
- **Associate Route Table with Public Subnet**: Edit subnet associations and select the public subnet.

### 6. Create Public and Private Servers
#### Public Server
- Select the **DemoVPC** and **Public Subnet**.
- Enable **Auto Assign Public IP**.
- Configure inbound rules for **SSH (22)** and **HTTP (80)**.

#### Private Server
- Select the **Private Subnet**.
- Disable **Auto Assign Public IP**.

### 7. Connect to Public Server
- Use **MobaXterm** with the **Public IP** and **Private Key** (e.g., `multicloud.pem`).

### 8. Connect to Private Server via Public Server (Bastion Host)
- Use **SSH Proxy** through the public server.
- Commands:
  ```bash
  chmod 400 multicloud.pem
  ssh -i "multicloud.pem" ec2-user@10.0.1.160
  ```

## Steps to Provide Internet to Private Server Using NAT Gateway

### 1. Create NAT Gateway
- **NAT Gateway Name**: demoNAT
- Attach to the **Public Subnet**.
- Allocate a **Static IP**.

### 2. Create a Route Table for Private Server
- Define a **Private Route Table**.

### 3. Configure Routing for NAT Gateway
- Add a route from the **NAT Gateway to the Private Route Table**.
- Add a route from the **Private Route Table to the Private Subnet**.

### 4. Verify Internet Connectivity
- Ping external websites from the private server:
  ```bash
  ping youtube.com
  ```
- Install Git on the private server:
  ```bash
  sudo yum install git -y
  ```

## Repository Contents
- **README.md** - This guide.
- **Creation of VPC and Connect private server to the internet.docx** - Detailed steps with screenshots.
- **setup_nat_gateway.sh** - Script to configure NAT Gateway and routing.

## NAT Gateway Setup Script
Save the following as `setup_nat_gateway.sh`:
```bash
#!/bin/bash
aws ec2 create-nat-gateway --subnet-id subnet-xyz --allocation-id eip-abc
aws ec2 create-route --route-table-id rtb-xyz --destination-cidr-block 0.0.0.0/0 --nat-gateway-id nat-abc
```

## License
MIT License.
