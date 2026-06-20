
This project demonstrates the design, deployment, troubleshooting, and successful implementation of a production-style Three-Tier Web Application Architecture on AWS. The application consists of a Next.js frontend, Node.js/Express backend, and MySQL database deployed across multiple layers using AWS networking and load balancing services.

The project was built to gain hands-on experience with AWS infrastructure, Linux administration, networking, load balancing, reverse proxy configuration, and application deployment.

##Project Objectives
• Build a secure and scalable three-tier architecture on AWS.
• Deploy frontend, backend, and database on separate EC2 instances.
• Implement External and Internal Application Load Balancers.
• Configure Nginx as a reverse proxy.
• Apply networking and security best practices.
• Troubleshoot real-world deployment issues.

##Architecture Overview

Internet
   ↓
External Application Load Balancer
   ↓
Frontend EC2 Instance (Next.js + Nginx)
   ↓
Internal Application Load Balancer
   ↓
Backend EC2 Instance (Node.js + Express)
   ↓
Database EC2 Instance (MySQL)

AWS Services Used

• VPC
• Public and Private Subnets
• Internet Gateway
• NAT Gateway
• Route Tables
• Security Groups
• EC2 Instances
• Application Load Balancers (External & Internal)
• Target Groups
• Amazon Linux 2023

Network Design

Public Layer:
• External Load Balancer
• Internet Gateway
• NAT Gateway

Private Layer:
• Frontend Server
• Backend Server
• Database Server

Traffic Flow:
Users → External ALB → Frontend → Internal ALB → Backend → Database

Frontend Configuration (Next.js)

Frontend responsibilities:
• Render user interface
• Handle user requests
• Communicate with backend APIs

Nginx Configuration:
• Listens on Port 80
• Reverse proxies requests to localhost:3000
• Forwards API requests to Internal Load Balancer

Example:
location / {
    proxy_pass http://localhost:3000;
}

location /api/ {
    proxy_pass http://internal-alb-dns-name/;
}

Backend Configuration (Node.js & Express)

Backend responsibilities:
• Process API requests
• Handle business logic
• Communicate with MySQL database

Runs on:
• Port 3001

Backend environment variables:
• Database Host
• Database Name
• Database User
• Database Password
• Application Configuration

Database Configuration (MySQL)

Database responsibilities:
• Store application data
• Handle user records
• Store reviews and application content

Security:
• Private subnet deployment
• Access restricted through Security Groups
• Backend-only access

Role of Internal Load Balancer

The Internal Load Balancer is never accessed directly by end users.

Its purpose is to:
• Route frontend API requests to backend servers.
• Improve scalability.
• Support multiple backend instances.
• Enable backend failover.

Flow:
Frontend → Internal ALB → Backend

Deployment Steps

1. Create VPC
2. Create Public Subnets
3. Create Private Subnets
4. Configure Internet Gateway
5. Configure NAT Gateway
6. Configure Route Tables
7. Create Security Groups
8. Launch Database EC2
9. Launch Backend EC2
10. Launch Frontend EC2
11. Configure Internal ALB
12. Configure External ALB
13. Install Nginx
14. Configure Reverse Proxy
15. Deploy Application
16. Validate Connectivity

Challenges Encountered and Resolutions

Challenge 1: Backend Target Group Unhealthy
Resolution:
• Corrected Health Check Path
• Verified backend service was listening on Port 3001

Challenge 2: Bad Gateway Error
Resolution:
• Installed Nginx
• Fixed proxy_pass configuration

Challenge 3: 504 Gateway Timeout
Resolution:
• Fixed frontend-to-backend communication
• Updated Nginx routing

Challenge 4: API Endpoint Issue (/api/api/books)
Resolution:
• Corrected frontend environment variable configuration

Challenge 5: Database Connectivity Issues
Resolution:
• Updated MySQL permissions
• Validated Security Group rules

Lessons Learned

• Three-Tier Architecture Design
• AWS Networking Fundamentals
• Internal vs External Load Balancers
• Nginx Reverse Proxy Configuration
• Linux Server Administration
• Application Troubleshooting
• Security Group Management
• Health Check Configuration
• Production Deployment Concepts

Future Improvements

• Infrastructure as Code using Terraform
• Docker Containerization
• CI/CD with GitHub Actions
• Kubernetes Deployment
• SSL/TLS using AWS Certificate Manager
• Auto Scaling Groups
Amazon RDS (Multi-AZ)
• CloudWatch Monitoring
