#AWS Lab : Building a VPC and Launching a Web Server

## 1. Overview
This project demonstrates the creation of a custom **Amazon Virtual Private Cloud (VPC)** and the deployment of a web server hosted on an **Amazon EC2 instance**.  
The objective was to design a secure, scalable network environment with both public and private subnets and to configure routing, security, and compute resources in line with AWS best practices.

**Region Used:** N. Virginia (us-east-1)

## 2. Architecture Summary
The infrastructure was built from scratch using the **VPC Wizard** in the AWS Management Console and expanded with additional subnets to support high availability.


### Components
- **VPC:** `lab-vpc` (`10.0.0.0/16`)
- **Public Subnets:**
  - `lab-subnet-public1-us-east-1a` (`10.0.0.0/24`)
  - `lab-subnet-public2` (`10.0.2.0/24`)
- **Private Subnets:**
  - `lab-subnet-private1-us-east-1a` (`10.0.1.0/24`)
  - `lab-subnet-private2` (`10.0.3.0/24`)
- **Internet Gateway:** `lab-igw`
- **NAT Gateway:** `lab-nat-public1-us-east-1a`
- **Route Tables:**
  - `lab-rtb-public` — routes `0.0.0.0/0` to Internet Gateway  
  - `lab-rtb-private1-us-east-1a` — routes `0.0.0.0/0` to NAT Gateway
- **Security Group:** `Web Security Group` (HTTP inbound from `0.0.0.0/0`)
- **EC2 Instance:** `Web Server 1` (Amazon Linux 2023, t2.micro)


## 3. Architecture Diagram



### Step 1: VPC Creation
- Created a new VPC named **lab-vpc** with CIDR block `10.0.0.0/16`.
- Used the “VPC and more” option to automatically provision:
  - One public and one private subnet in `us-east-1a`
  - Internet Gateway and NAT Gateway
  - Public and private route tables
- Enabled **DNS hostnames** and **DNS resolution**.

### Step 2: Additional Subnets
- Added two additional subnets in a second Availability Zone (`us-east-1b`):
  - Public Subnet: `lab-subnet-public2` (`10.0.2.0/24`)
  - Private Subnet: `lab-subnet-private2` (`10.0.3.0/24`)
- Updated route tables:
  - Associated both public subnets with `lab-rtb-public`.
  - Associated both private subnets with `lab-rtb-private1-us-east-1a`.

### Step 3: Security Group
- Created a new **Security Group** named `Web Security Group`.
- Configured inbound rule:
  - Type: HTTP  
  - Source: Anywhere (IPv4)  
  - Description: Permit web requests
- Outbound rules left as default (all traffic allowed).

### Step 4: EC2 Instance Deployment
- Launched an **Amazon Linux 2023 t2.micro** instance named `Web Server 1`.
- Configured to run in:
  - VPC: `lab-vpc`
  - Subnet: `lab-subnet-public2`
  - Auto-assign Public IP: Enabled
  - Security Group: `Web Security Group`
- Associated with existing key pair: `vockey`

 


### Step 5: User Data Script
Added the following bootstrap script under **Advanced details → User data**:

```bash
#!/bin/bash
# Install Apache Web Server and PHP
dnf install -y httpd wget php mariadb105-server
```


# Enable and start Apache web server
chkconfig httpd on
service httpd start






