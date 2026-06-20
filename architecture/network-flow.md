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
