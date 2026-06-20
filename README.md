# AWS Three-Tier Book Review Application 🚀

[![AWS](https://img.shields.io/badge/AWS-FF9900?style=flat-square&logo=amazon-aws)](https://aws.amazon.com)
[![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat-square&logo=node.js)](https://nodejs.org)
[![Next.js](https://img.shields.io/badge/Next.js-000000?style=flat-square&logo=next.js)](https://nextjs.org)
[![MySQL](https://img.shields.io/badge/MySQL-005C87?style=flat-square&logo=mysql)](https://www.mysql.com)
[![Nginx](https://img.shields.io/badge/Nginx-009639?style=flat-square&logo=nginx)](https://nginx.org)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)

> A production-grade three-tier web application deployed on AWS demonstrating enterprise-level architecture, networking, and DevOps practices.

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Key Features](#key-features)
- [Deployment Guide](#deployment-guide)
- [Challenges & Solutions](#challenges--solutions)
- [Lessons Learned](#lessons-learned)
- [Future Improvements](#future-improvements)
- [Security](#security)
- [Author](#author)

---

## 📖 Project Overview

This project demonstrates the **design, deployment, and troubleshooting** of a production-style three-tier web application architecture on AWS. The application is a book review platform where users can browse books, read reviews, and leave their own feedback.

**Built to gain hands-on experience with:**
- AWS infrastructure and networking
- Linux administration and server management
- Load balancing and reverse proxy configuration
- Real-world deployment troubleshooting
- Security best practices and network isolation
- Full-stack application deployment

---

## 🏗️ Architecture

### System Design

```
┌─────────────────────────────────────────────────────────────────┐
│                        Public Internet                          │
└────────────────────┬────────────────────────────────────────────┘
                     │
         ┌───────────▼──────────────┐
         │   External ALB (Port 80) │
         │   (Internet-Facing)      │
         └───────────┬──────────────┘
                     │
       ┌─────────────▼──────────────┐
       │  Frontend EC2 (Public)     │
       │  • Next.js (Port 3000)     │
       │  • Nginx Reverse Proxy     │
       └─────────────┬──────────────┘
                     │
         ┌───────────▼──────────────┐
         │   Internal ALB           │
         │   (Private Only)         │
         └───────────┬──────────────┘
                     │
       ┌─────────────▼──────────────┐
       │  Backend EC2 (Private)     │
       │  • Node.js/Express         │
       │  • Port 3001               │
       └─────────────┬──────────────┘
                     │
       ┌─────────────▼──────────────┐
       │  RDS MySQL (Private)       │
       │  • Port 3306               │
       │  • Zero Internet Access    │
       └────────────────────────────┘
```

### Network Layers

| Layer | Components | Purpose |
|-------|-----------|---------|
| **Public Tier** | External ALB, NAT Gateway, IGW | Handle internet traffic, manage outbound connectivity |
| **Application Tier** | Frontend EC2 + Nginx | Serve UI, route requests, reverse proxy |
| **Backend Tier** | Backend EC2 (Node.js) | Business logic, API processing |
| **Data Tier** | MySQL Database | Data persistence, no internet access |

### Traffic Flow

```
User Browser
    ↓
External ALB (0.0.0.0:80)
    ↓
Frontend EC2 (Nginx - Port 80)
    ├─→ / (static assets)         → localhost:3000 (Next.js)
    └─→ /api/* (API requests)     → Internal ALB → Backend
    ↓
Internal ALB (Private)
    ↓
Backend EC2 (Node.js - Port 3001)
    ↓
MySQL Database (Port 3306)
```

---

## 💡 Technology Stack

### Infrastructure (AWS)
- **VPC** - Custom virtual network with public & private subnets
- **EC2 Instances** - Application servers (Amazon Linux 2023)
- **Application Load Balancers** - External (internet-facing) & Internal (private)
- **Target Groups** - Route traffic to backend services
- **Security Groups** - Firewall rules for network isolation
- **Internet Gateway** - Public subnet internet access
- **NAT Gateway** - Private subnet outbound connectivity
- **Route Tables** - Network routing rules

### Frontend
- **Next.js 14** - React framework, SSR/SSG
- **Nginx** - Reverse proxy, static asset serving
- **Axios** - HTTP client for API calls

### Backend
- **Node.js 18** - JavaScript runtime
- **Express.js** - Web framework
- **Sequelize** - ORM for database operations
- **PM2** - Process manager for background service

### Database
- **MySQL 8.0** - Relational database
- **Private Subnet Deployment** - No public internet access

---

## ✨ Key Features

✅ **Production-Grade Architecture**
- Three-tier separation of concerns
- Load balancing for high availability
- Private database with zero internet exposure

✅ **Security-First Design**
- VPC isolation and security groups
- Internal communication via private ALB
- Principle of least privilege networking

✅ **Reverse Proxy Configuration**
- Nginx handles static files and API routing
- Reduces backend exposure
- Single entry point for all traffic

✅ **Real-World Troubleshooting**
- Documented 5+ production issues and resolutions
- Practical debugging techniques
- Health check configuration

✅ **Scalability Ready**
- Load balancer targets multiple instances
- Auto Scaling group ready architecture
- Horizontal scaling capability

---

## 📚 Lessons Learned

### AWS & Infrastructure
- VPC segmentation and subnet design principles
- Difference between internet-facing and internal load balancers
- Security group rule precedence and implications
- NAT Gateway for private subnet internet access
- Health checks and target group configuration

### Networking & Proxying
- Nginx reverse proxy fundamentals
- Trailing slash behavior in `proxy_pass` directives
- Request/response header manipulation
- Load balancer path-based routing rules
- CORS configuration and troubleshooting

### DevOps & Operations
- Linux server administration (Amazon Linux 2023)
- PM2 process management and auto-restart
- Application logging and debugging
- Environment variable management
- Server health checks and monitoring

### Application Development
- Frontend-backend separation of concerns
- API endpoint design and RESTful principles
- Authentication and token management
- Database connection pooling
- Error handling and logging best practices

### Troubleshooting Methodology
- Systematic error diagnosis from browser console
- Network traffic inspection using browser DevTools
- Log file analysis (`pm2 logs`, `/var/log/nginx/`)
- Health check validation
- Incremental testing and isolation

---

## 🔮 Future Improvements

- [ ] **Auto Scaling Groups** - Dynamically scale frontend and backend tiers
- [ ] **Amazon RDS** - Replace EC2 MySQL with managed RDS (multi-AZ)
- [ ] **SSL/HTTPS** - AWS Certificate Manager for encrypted traffic
- [ ] **CloudWatch** - Centralized monitoring and alerting
- [ ] **VPC Peering** - Connect to other VPCs if needed
- [ ] **Route 53** - DNS management and failover
- [ ] **WAF** - Web Application Firewall for DDoS protection
- [ ] **S3 & CloudFront** - Static asset delivery with CDN
- [ ] **Terraform/CloudFormation** - Infrastructure as Code
- [ ] **CI/CD Pipeline** - Automated deployment (GitHub Actions / CodePipeline)

---

## 🔐 Security

### By Design
✅ **Database Isolation**
- Zero internet access (private subnet only)
- Access restricted to backend EC2 via security group rules
- No SSH access to database server

✅ **Backend Isolation**
- Only reachable via internal ALB (not directly from internet)
- Private subnet deployment
- Security group restricts to ALB traffic only

✅ **Frontend Gateway**
- Single entry point via external ALB
- Nginx reverse proxy (no direct backend exposure)
- Static assets served efficiently

✅ **Network Segmentation**
- VPC isolation from other infrastructure
- Public/private subnet separation
- NAT Gateway for private subnet egress control

### Best Practices Implemented
- Principle of least privilege (security groups)
- Defense in depth (multiple security layers)
- No credentials in code or config files
- Environment-based configuration management
- Regular health checks for availability

---

## 📁 Repository Structure

```
aws-three-tier-book-review-app/
│
├── README.md                    # This file
├── LICENSE                      # MIT License
│
├── frontend/                    # Next.js application
│   ├── package.json
│   ├── .env.example
│   ├── public/
│   ├── pages/                   # Next.js pages
│   ├── components/              # React components
│   ├── src/
│   │   └── api.js              # Axios API client
│   └── next.config.js
│
├── backend/                     # Node.js/Express API
│   ├── package.json
│   ├── .env.example
│   ├── server.js               # Entry point
│   ├── routes/                 # API endpoints
│   ├── controllers/            # Business logic
│   ├── models/                 # Database models
│   ├── middleware/             # Auth, logging, etc
│   └── database/
│       └── dump.sql            # Initial schema
│
├── architecture/               # Diagrams & documentation
│   ├── three-tier-diagram.png
│   ├── vpc-design.png
│   └── network-flow.md
│
└── documentation/              # Additional guides
    ├── deployment-guide.md
    ├── troubleshooting-guide.md
    └── security-checklist.md
```

---

## 🛠️ Local Development

### Prerequisites
- Node.js 18+
- MySQL 8.0+
- Git

### Setup Frontend
```bash
cd frontend
cp .env.example .env
npm install
npm run dev    # Development server on localhost:3000
```

### Setup Backend
```bash
cd backend
cp .env.example .env
npm install
npm run dev    # Development server on localhost:3001
```

### Setup Database
```bash
mysql -u root -p < database/dump.sql
# Or use MySQL Workbench GUI
```

---

## 📞 Support & Contact

**Issues & Questions:**
- Check the [Troubleshooting Guide](documentation/troubleshooting-guide.md)
- Review [Security Checklist](documentation/security-checklist.md)
- Open an issue on GitHub

**Author:**  
**Samuel Ehizokhai** | DevOps/Cloud Engineer  
📧 Email: [your-email@example.com]  
🔗 LinkedIn: [linkedin.com/in/samuel-ehizokhai](https://www.linkedin.com/in/samuel-ehizokhai)  
💻 GitHub: [@username](https://github.com/username)  

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**[⬆ Back to Top](#aws-three-tier-book-review-application-)**

Built with ❤️ on AWS | DevOps & Cloud Engineering

</div>
