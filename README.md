This project demonstrates how to securely access the resources of a private server (EC2 instance) through a public server (jump server/bastion) or a load balancer. The setup involves using AWS VPC, public and private subnets, a NAT gateway for internet access, and configuring a load balancer for traffic distribution.

## Table of Contents
- [Purpose](#purpose)
- [Technologies Used](#technologies-used)
- [Prerequisites](#prerequisites)
- [Architecture Overview](#architecture-overview)
- [Setup and Configuration](#setup-and-configuration)
- [Steps for Access](#steps-for-access)
- [Testing Load Balancer Access](#testing-load-balancer-access)
- [Troubleshooting](#troubleshooting)
- [Conclusion](#conclusion)

## Purpose

The goal of this project is to demonstrate how to access the resources of a private EC2 instance hosted in a private subnet. The two primary methods demonstrated are:

1. **Using a public server (jump server or bastion host)** to securely SSH into the private server.
2. **Using a load balancer** to access the private server's resources via HTTP (e.g., accessing Nginx running on the private server).

## Technologies Used
- **AWS EC2**
- **AWS VPC**
- **NAT Gateway**
- **Elastic Load Balancer (ELB)**
- **Linux (Ubuntu)**
- **PowerShell (for SCP)**
- **Nginx (Web Server)**

## Prerequisites
Before starting this demonstration, you will need:
- An AWS account with sufficient permissions to create VPCs, EC2 instances, and load balancers.
- A basic understanding of VPC, subnets, EC2 instances, and SSH.
- An SSH key pair to access EC2 instances.

## Architecture Overview

The architecture consists of:
- A VPC with three subnets: 
  - Two public subnets (`subnet1` and `subnet3`), used for the jump server and load balancer.
  - One private subnet (`subnet2`), used for hosting the private server.
- A NAT gateway in the public subnet for the private server to access the internet.
- A load balancer configured with public subnets to distribute traffic to the private server.
- A jump server (bastion) in the public subnet to securely access the private server via SSH.

## Setup and Configuration

### 1. VPC and Subnets
- Create a VPC with three subnets: two public subnets (`subnet1` and `subnet3`) and one private subnet (`subnet2`).
- Set up route tables: 
  - One public route table for `subnet1` and `subnet3` associated with an Internet Gateway.
  - One private route table for `subnet2` associated with a NAT gateway.

### 2. NAT Gateway
- Set up a NAT Gateway in one of the public subnets (`subnet1`) to allow the private subnet instances to access the internet.
- Associate the NAT Gateway with the private subnet's route table.

### 3. EC2 Instances
- Create two EC2 instances:
  - A **jump server (bastion)** in the public subnet for SSH access.
  - A **private server** in the private subnet for hosting resources (e.g., Nginx).
- Transfer the private key of the private server to the jump server using `scp` via PowerShell or terminal.

## Steps for Access

### Method 1: Accessing the Private Server via Jump Server (Bastion)
1. **SSH into the jump server** from your local machine using the public IP of the jump server.
   ```bash
   ssh -i jump-server-key.pem ec2-user@<jump-server-public-ip>
   ```
2. Once inside the jump server, **SSH into the private server** using the private key that was transferred earlier.
   ```bash
   ssh -i private-server-key.pem ec2-user@<private-server-private-ip>
   ```
3. On the private server, you can now update and install software. For example, installing Nginx:
   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```

### Method 2: Accessing the Private Server via Load Balancer
1. Set up a **Load Balancer** targeting the private server instance running in `subnet2`.
2. Ensure that the security groups for both the load balancer and the private server allow HTTP traffic (port 80).
3. Copy the DNS name of the load balancer and access it through a browser:
   ```text
   http://<load-balancer-dns>
   ```
   This will route the traffic to the Nginx web server running on the private server.

## Testing Load Balancer Access
- After setting up the load balancer and adding the private server as a target, access the load balancer’s DNS through a browser. You should see the default Nginx welcome page served by the private server.
- This access should remain available even if the jump server is terminated, as the load balancer provides direct routing to the private server.

## Troubleshooting

- **Nginx not loading through the Load Balancer**:
  - Check security group settings for both the load balancer and the private server to ensure HTTP (port 80) is allowed.
  - Ensure the private server instance passes health checks configured in the load balancer's target group.

- **Unable to SSH into the private server from the jump server**:
  - Verify the security group for the private server allows SSH (port 22) from the jump server's IP address.
  - Double-check the private key permissions and ensure it was transferred correctly using `scp`.

## Conclusion

This project demonstrates two methods for securely accessing a private server hosted in AWS. You can either use a jump server (bastion) for direct SSH access, or configure a load balancer to serve the private server's resources (e.g., Nginx) to the public. These techniques help maintain secure and efficient access to private resources in a cloud environment.
```

---

### Summary of Changes:
- Focused the README around the **main purpose**: demonstrating how to access a private server through a public jump server or load balancer.
- Clearly explained both access methods: SSH through a jump server and HTTP access via a load balancer.
- Included specific steps for setting up instances, load balancer, and NAT gateway, with troubleshooting advice.

