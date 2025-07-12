# ⚙️ commands.md

This file contains all the important shell commands used throughout the AWS infrastructure project. These commands are primarily for SSH setup, Bastion access, transferring key files, and hosting the simple web application on private EC2 instances.

---

## 🔐 SSH Key Setup

```bash
# Set proper permission for your key file
chmod 400 key.pem

# Create .ssh directory if it doesn't exist
mkdir -p ~/.ssh
```

---

## 🚁 Transfer Key to Bastion Host

```bash
# Copy the key file to Bastion Host (replace with actual IP)
scp -i key.pem key.pem ubuntu@<bastion-ip>:/home/ubuntu/
```

---

## 🔌 SSH into Bastion Host

```bash
ssh -i key.pem ubuntu@<bastion-ip>
```

---

## 🤑 SSH into Private EC2 from Bastion Host

```bash
# From inside the Bastion Host
ssh -i key.pem ubuntu@<private-ec2-ip>
```

---

## 🖊️ Create and Host HTML Page in Private Instance

```bash
# Create a basic HTML page
vim index.html
```

Example HTML content:

```html
<h1>Hello from the Private EC2 Instance!</h1>
```

```bash
# Start a simple Python HTTP server on port 8000
python3 -m http.server 8000
```

---

## ✅ Test App Access

* From your local browser:

  * Go to the **EC2 Console → Load Balancers → Description tab**
  * **Copy the DNS name** of the ALB and paste it in your browser:

```text
http://<alb-dns-name>
```

* You should see the HTML page if ALB is correctly routing to the private instance.

---

## 📃 Notes

* Ensure security groups are correctly configured to allow:

  * SSH from your IP to Bastion
  * Port 8000 and 22 from Bastion to private EC2
  * Port 80 (HTTP) from anywhere to the Load Balancer

* If you get permission errors, re-check file permissions or SG rules.

---