## 🔧 Challenges & Solutions

### Challenge 1: Backend Target Group Unhealthy
**Error:** External ALB unable to reach backend  
**Root Cause:** Incorrect health check path and port mismatch

**Resolution:**
```bash
# Backend listening on port 3001
netstat -tlnp | grep node

# Updated health check path to /api/health
# Created health endpoint in Node.js:
app.get('/api/health', (req, res) => {
  res.status(200).json({ status: 'OK' });
});
```

### Challenge 2: 504 Gateway Timeout
**Error:** User requests timeout when calling API  
**Root Cause:** Frontend calling backend via external ALB instead of internal ALB

**Resolution:**
```bash
# Updated Nginx to route /api to internal ALB
# Removed trailing slash from proxy_pass directive
# Changed from: proxy_pass http://internal-alb.com/;
# Changed to:  proxy_pass http://internal-alb.com;
```

### Challenge 3: API Endpoint Doubling (`/api/api/books`)
**Error:** API calls hitting `/api/api/` instead of `/api/`  
**Root Cause:** Frontend `.env` had incorrect base URL

**Resolution:**
```env
# Changed from:
NEXT_PUBLIC_API_URL=http://external-alb.../book

# Changed to:
NEXT_PUBLIC_API_URL=/api
```

### Challenge 4: CORS Errors
**Error:** Browser blocking API responses  
**Root Cause:** Backend CORS allowed origins misconfigured

**Resolution:**
```javascript
// Backend CORS configuration
const cors = require('cors');
const allowedOrigins = process.env.ALLOWED_ORIGINS.split(',');

app.use(cors({
  origin: (origin, callback) => {
    if (!origin || allowedOrigins.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('CORS not allowed'));
    }
  },
  credentials: true
}));
```

### Challenge 5: Database Connection Refused
**Error:** Backend unable to connect to MySQL  
**Root Cause:** Security group rules blocking access

**Resolution:**
```bash
# Updated RDS security group to allow:
# Inbound: MySQL (3306) from Backend EC2 SG

# Tested connection:
mysql -h your-rds.ap-south-1.rds.amazonaws.com -u epicuser -p
```

---
