# 🗒️ note.md

These are raw step-by-step notes outlining the manual setup of a secure and scalable AWS infrastructure using VPC, Auto Scaling Group, Load Balancer, and Bastion Host. This complements the main README documentation.

---

## Step 1: Log in to AWS

* Go to the AWS Management Console

## Step 2: Create a VPC

* Navigate to **VPC → VPC and more**
* Disable IPv6 CIDR block
* Create:

  * 2 **Public Subnets** (in different AZs)
  * 2 **Private Subnets** (in different AZs)
  * 1 **NAT Gateway per AZ** (for private subnet outbound access)
  * (Optional) Add VPC endpoint nodes

## Step 3: Create Launch Template

* Go to **EC2 → Launch Templates → Create Template**
* Choose **Ubuntu AMI**
* Instance Type: `t2.micro`
* Key Pair: Choose existing
* Security Group:

  * VPC: Select the VPC created
  * Inbound rules:

    * SSH (port 22)
    * Custom TCP (port 8000)

## Step 4: Create Auto Scaling Group

* Use the launch template created
* Select the custom VPC
* Do **not attach a load balancer yet**
* Set group size:

  * Desired Capacity: 2
  * Minimum: 1
  * Maximum: 4

## Step 5: Launch Bastion Host

* Go to **EC2 → Launch Instance**
* Use Ubuntu AMI
* Enable auto-assign public IP
* Network: Select the VPC
* Subnet: Choose one of the public subnets
* Security Group:

  * Allow SSH (port 22) from your IP

## Step 6: Transfer Key & SSH Access

* Ensure local key permissions: `chmod 400 key.pem`
* On Windows, copy key to `~/.ssh`, create dir if needed using `mkdir -p ~/.ssh`
* Use `scp` to copy the key to the Bastion host
* SSH into Bastion host
* From Bastion, SSH into one of the private EC2 instances

## Step 7: Deploy App on Private EC2

* Create a basic HTML file (`index.html`)
* Host it using Python HTTP server:

  ```bash
  python3 -m http.server 8000
  ```

## Step 8: Create Application Load Balancer

* Go to **EC2 → Load Balancer → Application Load Balancer**
* Select VPC and **public subnets**
* Attach existing Security Group (must allow HTTP on port 80)
* Listener: HTTP (port 80)
* Create a Target Group:

  * Register private EC2 instances
  * Set health check to port 8000

## Step 9: Test the Application

* Copy DNS name from the ALB Description tab
* Open it in a browser: `http://<alb-dns-name>`
* If it fails:

  * Go to Security Groups
  * Edit ALB SG Inbound Rules → Allow TCP on port 80 from `0.0.0.0/0`

---
