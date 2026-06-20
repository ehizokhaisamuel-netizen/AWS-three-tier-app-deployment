## 🚀 Deployment Guide

### Prerequisites
- AWS Account with permissions to create VPC, EC2, ALB resources
- SSH client (Windows: PuTTY, Linux/Mac: native SSH)
- Basic knowledge of networking and Linux

### Step-by-Step Deployment

#### 1. Create VPC & Networking
```bash
# VPC CIDR: 10.0.0.0/16
# Public Subnets: 10.0.1.0/24, 10.0.2.0/24 (different AZs)
# Private Subnets: 10.0.10.0/24, 10.0.20.0/24 (different AZs)

# Create Internet Gateway and attach to VPC
# Create NAT Gateway in public subnet for private subnet egress
```

#### 2. Configure Security Groups

**External ALB Security Group:**
```
Inbound: HTTP (80) from 0.0.0.0/0
Inbound: HTTPS (443) from 0.0.0.0/0 [future]
```

**Frontend EC2 Security Group:**
```
Inbound: Port 80 from External ALB SG
Inbound: Port 443 from External ALB SG [future]
```

**Internal ALB Security Group:**
```
Inbound: Port 80 from Frontend EC2 SG
```

**Backend EC2 Security Group:**
```
Inbound: Port 3001 from Internal ALB SG
Inbound: Port 22 from Admin IP [SSH access]
```

**RDS/Database Security Group:**
```
Inbound: Port 3306 (MySQL) from Backend EC2 SG only
```

#### 3. Launch EC2 Instances
```bash
# Frontend EC2 (t2.micro / t3.micro)
# - Amazon Linux 2023
# - Public IP enabled
# - Attach Frontend Security Group

# Backend EC2 (t2.micro / t3.micro)
# - Amazon Linux 2023
# - Private IP only
# - Attach Backend Security Group

# Database (manual MySQL or RDS)
# - Private subnet only
# - Attach RDS Security Group
```

#### 4. Configure Frontend EC2
```bash
# 1. Install Node.js & npm
sudo yum update -y
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs

# 2. Install Nginx
sudo yum install -y nginx

# 3. Clone & setup Next.js app
cd /home/ec2-user
git clone <your-repo>
cd frontend
npm install
npm run build

# 4. Create .env file
cat > .env << EOF
NEXT_PUBLIC_API_URL=/api
PORT=3000
NODE_ENV=production
EOF

# 5. Start with PM2
npm install -g pm2
pm2 start npm --name "frontend" -- start
pm2 startup
pm2 save
```

#### 5. Configure Nginx Reverse Proxy
```bash
sudo nano /etc/nginx/sites-available/bookapp
```

**Nginx Configuration:**
```nginx
server {
    listen 80;
    server_name _;

    # Static assets & frontend
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # Backend API routing
    location /api/ {
        proxy_pass http://internal-alb-xxxx.ap-south-1.elb.amazonaws.com;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

```bash
# Enable & start Nginx
sudo ln -s /etc/nginx/sites-available/bookapp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl start nginx
sudo systemctl enable nginx
```

#### 6. Configure Backend EC2
```bash
# 1. Install Node.js
sudo yum update -y
curl -fsSL https://rpm.nodesource.com/setup_18.x | sudo bash -
sudo yum install -y nodejs

# 2. Clone & setup backend
cd /home/ec2-user
git clone <your-repo>
cd backend
npm install

# 3. Create .env file
cat > .env << EOF
DB_HOST=your-rds-endpoint.ap-south-1.rds.amazonaws.com
DB_NAME=Epicreads
DB_USER=epicuser
DB_PASS=YourStrongPassword123!
DB_DIALECT=mysql
DB_PORT=3306
PORT=3001
NODE_ENV=production
JWT_SECRET=YourSecureJWTSecret!
ALLOWED_ORIGINS=http://external-alb-xxxx.ap-south-1.elb.amazonaws.com,http://localhost:3000
EOF

# 4. Initialize database
mysql -h your-rds-endpoint -u epicuser -p Epicreads < database/dump.sql

# 5. Start with PM2
npm install -g pm2
pm2 start server.js --name "backend"
pm2 startup
pm2 save
```

#### 7. Configure Load Balancers

**External ALB:**
- Create in public subnets
- Target Group → Frontend EC2 (Port 80)
- Health Check: `/` (HTTP)

**Internal ALB:**
- Create in private subnets
- Target Group → Backend EC2 (Port 3001)
- Health Check: `/api/health` (HTTP)

---

