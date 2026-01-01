AWS VPC Architecture – Learning Project (Deep Dive with Bastion Host)

This repository contains a beginner-friendly AWS VPC architecture learning project where a website is hosted on EC2 instances inside a private subnet, following real-world security best practices.

This project focuses on how private servers are accessed securely using a Bastion Host and how applications are deployed using SSH and SCP.

📌 Project Objective

The objective of this project is to understand:

How to host a website in a private subnet

How to securely access private EC2 instances

Why a Bastion Host is required

How scp and ssh commands are used in real-world AWS deployments

End-to-end deployment flow used by cloud engineers

This approach closely matches real production architectures.

🧱 Architecture Overview

The architecture includes:

Custom Amazon VPC

Public and Private Subnets

Internet Gateway

Bastion Host (Jump Server)

Application Load Balancer

EC2 instances in Private Subnet

NAT Gateway

Security Groups

Multi-AZ High Availability

🌐 Why Website is Hosted in Private Subnet

In real-world AWS environments:

Application servers should not be exposed to the internet

Direct SSH or HTTP access to EC2 is avoided

Only controlled entry points are allowed

Benefits:

Improved security

Reduced attack surface

Better compliance with cloud best practices

In this project:

Website runs on EC2 instances in private subnet

Internet users access the site only via Load Balancer

Admin access is done via Bastion Host

🧑‍🚀 Bastion Host (Jump Server)

A Bastion Host is an EC2 instance placed in a public subnet that acts as a secure entry point to access private EC2 instances.

Why Bastion Host is used:

Private EC2 instances have no public IP

Direct SSH from internet is not allowed

Bastion provides controlled and auditable access

Bastion Host placement:

Public Subnet

Has Public IP

SSH access allowed only from trusted IPs

🔐 Security Group Design (Important)
Bastion Host Security Group:

Inbound:

SSH (22) → Allowed from my IP only

Outbound:

All traffic allowed

Private EC2 Security Group:

Inbound:

SSH (22) → Allowed only from Bastion Host SG

HTTP (80) → Allowed only from ALB SG

Outbound:

Allowed via NAT Gateway

This ensures zero direct internet access to private servers.

🖥 Website Hosting on Private EC2 (Step-by-Step)
Step 1: Connect to Bastion Host (SSH)

First, SSH into the Bastion Host from local machine:

ssh -i mykey.pem ec2-user@<BASTION_PUBLIC_IP>


mykey.pem → EC2 key pair

Bastion is the only server accessible from internet

Step 2: Connect to Private EC2 from Bastion

From inside the Bastion Host:

ssh -i mykey.pem ec2-user@<PRIVATE_EC2_PRIVATE_IP>


Now we are inside the private EC2 instance.

📦 Website Deployment using SCP

Since private EC2 has no direct internet access, files are copied using SCP (Secure Copy Protocol).

Copy website files from local machine to Bastion:
scp -i mykey.pem -r website/ ec2-user@<BASTION_PUBLIC_IP>:/home/ec2-user/

Copy files from Bastion to Private EC2:
scp -i mykey.pem -r website/ ec2-user@<PRIVATE_EC2_PRIVATE_IP>:/home/ec2-user/


This two-step SCP flow is common in secure cloud environments.

🌐 Installing Web Server on Private EC2

Once inside private EC2:

Update system packages:
sudo yum update -y

Install Apache Web Server:
sudo yum install httpd -y

Start and enable Apache:
sudo systemctl start httpd
sudo systemctl enable httpd

📁 Deploy Website Files
sudo cp -r website/* /var/www/html/
sudo chown -R apache:apache /var/www/html/


Now the website is running on private EC2, but not accessible directly from internet.

⚖️ Access via Application Load Balancer

ALB is placed in public subnet

ALB forwards HTTP traffic to private EC2

Users access website via ALB DNS name

Private EC2 remains hidden and secure

🔄 Complete Traffic Flow

User accesses website via browser

Request hits Application Load Balancer

ALB forwards traffic to Private EC2

Apache serves website

Response goes back through ALB

Admin access:

Local → Bastion → Private EC2

🌱 Learning Outcomes

From this project, I learned:

How private subnet hosting works

Real-world use of Bastion Host

Secure SSH access patterns

Usage of SCP for file transfer

How enterprises deploy applications securely on AWS

📌 Project Disclaimer

This is a learning and practice project created to understand AWS networking and secure deployment concepts.

No production workloads are hosted.


