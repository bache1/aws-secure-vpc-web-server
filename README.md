# AWS Secure VPC & EC2 Ubuntu Web Server Deployment
> A hands-on cloud infrastructure project implementing a secure, isolated custom VPC architecture on AWS with a public subnet, strict Security Group firewall rules, and an automated Nginx web server deployment on an Ubuntu EC2 instance (Free Tier eligible).

---

## Project Overview
In cloud computing, deploying resources into a default VPC without proper network segmentation poses a security risk. This project demonstrates foundational Cloud Engineering and security principles by designing a **Custom Virtual Private Cloud (VPC)** from scratch, establishing public-facing networking components, and securely deploying an Ubuntu web server on an **Amazon EC2** instance while adhering strictly to AWS Free Tier limits.

---

## Tech Stack & Services (100% Free Tier)
* **Cloud Provider:** Amazon Web Services (AWS)
* **Networking:** Amazon VPC (Custom VPC `10.1.0.0/16`, Public Subnet, Internet Gateway, Route Tables)
* **Compute:** Amazon EC2 (Ubuntu Server 26.04 LTS, `t3.micro`)
* **Security:** AWS Security Groups (`web-server-sg`: Restricted SSH & Public HTTP Firewall)
* **Web Server:** Nginx with Custom HTML Landing Page
* **Version Control:** Git & GitHub

---

## Architecture Design & Data Flow
1. **Custom VPC Creation:** Allocated a private IP address space (`10.1.0.0/16`) to isolate cloud resources.
2. **Internet Gateway (IGW):** Attached to the VPC to enable bidirectional communication with the public internet.
3. **Public Subnet & Route Table:** Configured public subnets (`my-secure-subnet-public1` and `my-secure-subnet-public2`) with a default route (`0.0.0.0/0`) pointing directly to the Internet Gateway.
4. **Security Group (Firewall):** Enforces least-privilege access via `web-server-sg`, restricting management traffic (Port 22 / SSH) strictly to the administrator's IP address while allowing public web traffic (Port 80 / HTTP) from anywhere (`0.0.0.0/0`).
5. **EC2 Ubuntu Instance:** Deployed inside the public subnet with an auto-assigned public IP, running an Nginx web server to serve a custom HTML page.

---

## Repository Structure
```text
aws-secure-vpc-web-server/
├── docs/               # Architecture screenshots and verification proofs
├── scripts/            # Setup and configuration notes
└── README.md           # Complete project documentation & tutorial guide
```

## Step-by-Step Implementation Tutorial
> This tutorial serves as a personal documentation guide and technical reference for setting up a secure AWS cloud networking environment.

### Step 1: Create a Custom VPC
1. Navigate to the AWS Management Console -> VPC Dashboard.
2. Click Create VPC and select VPC and more.
3. Configure your settings:
* Name tag auto-generation:```text my-secure-vpc```
* IPv4 CIDR block: 10.1.0.0/16
* Number of Availability Zones (AZs): ```text 2```
* Number of public subnets: ```text 2```
* Number of private subnets: ```text 2```
* NAT gateways:```text```  None (Ensures zero unexpected charges / Free Tier compliant)
* DNS options: Enable both DNS hostnames and DNS resolution.
4. Click Create VPC.

### Step 2: Configure Security Groups
1. Go to Security Groups in the VPC Dashboard, then click Create security group.
2. Configure the details:
* Security group name:```text web-server-sg```
* Description:```text Allow HTTP and restricted SSH access```
* VPC: Select```text my-secure-vpc```.
3. Configure Inbound Rules:
* Type:```text SSH``` (Port 22) | Source:```text My IP``` (Restricts management access to authorized networks only).
* Type: HTTP (Port 80) | Source: Anywhere-IPv4 (0.0.0.0/0) (Allows public web viewing).
4. Configure Outbound Rules:
* Type:```text All traffic``` | Destination:```text 0.0.0.0/0``` (Allows server outbound connectivity for package updates).
5. Click Create security group.

### Step 3: Launch an EC2 Ubuntu Instance in the Custom VPC
1. Open the EC2 Dashboard and click Launch instance.
2. Configure the instance specifications:
* Name:```text secure-ubuntu-webserver```
* AMI: Select Ubuntu Server 26.04 LTS (Free tier eligible).
* Instance Type:```text t3.micro``` (Free tier eligible).
* Key Pair: Select or create an SSH key pair (e.g.,```text my-aws-key```).
3. Network Settings (Click Edit):
* VPC: Select```text my-secure-vpc```.
* Subnet: Choose one of the public subnets (e.g.,```text my-secure-subnet-public1```).
* Auto-assign public IP: Set to Enable.
* Firewall (Security Groups): Choose Select existing security group and pick```text web-server-sg```.
* Click Launch instance.

### Step 4: Install and Configure Nginx Web Server
1. Connect to your instance via SSH using your private key:
Bash
```text
chmod 400 my-aws-key.pem
ssh -i "my-aws-key.pem" ubuntu@<YOUR_EC2_PUBLIC_IP>
```
2. Force IPv4 and update package lists, then install Nginx:
Bash
```text sudo apt -o Acquire::ForceIPv4=true update -y
sudo apt -o Acquire::ForceIPv4=true install nginx -y
```

3. Start and enable the Nginx service:
```text
Bash
sudo systemctl start nginx
sudo systemctl enable nginx
```
4. Deploy a custom HTML landing page:
Bash
```text
echo "<h1>Hello from AWS Secure VPC & EC2 Ubuntu! Deployed by Salsabila Bachtiar</h1>" | sudo tee /var/www/html/index.html
```
### Step 5: Verification & Testing
1. Copy your EC2 instance's Public IPv4 address.
2. Open your web browser and navigate to```text  http://<YOUR_EC2_PUBLIC_IP>``` (ensure to use HTTP, as SSL is not configured yet).
3. Verify that the custom HTML landing page loads successfully.

## Key Takeaways & Learnings
1. Network Isolation: Designing custom VPCs provides full granular control over IP address distribution ```text (10.1.0.0/16)``` and traffic routing.
2. Security Hardening: Implementing strict firewall segmentation prevents unauthorized administrative intrusion while securely exposing application entry points.
3. Cost Management: Architecting strictly within Free Tier limitations prevents unexpected cloud billing.

**Salsabila Bachtiar**
- Informatics Student | Aspiring DevOps & Cloud Engineer  
[LinkedIn](https://www.linkedin.com/in/salsabila-bachtiar-30161724a) | [GitHub](https://github.com/bache1)
