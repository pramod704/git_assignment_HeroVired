# Travel Memory Application Deployment Guide

## Overview
This guide details the deployment of the Travel Memory MERN stack application on Amazon EC2 with load balancing and custom domain setup via Cloudflare.

## Architecture Diagram

```mermaid
graph TD
    A[User] --> B[Cloudflare DNS]
    B --> C[Application Load Balancer]
    C --> D[EC2 Instance 1 - Frontend]
    C --> E[EC2 Instance 2 - Frontend]
    C --> F[EC2 Instance 3 - Backend]
    C --> G[EC2 Instance 4 - Backend]
    D --> H[Nginx - Serve React App]
    E --> I[Nginx - Serve React App]
    F --> J[Node.js Backend - Port 3000]
    G --> K[Node.js Backend - Port 3000]
    J --> L[MongoDB Atlas]
    K --> L
```

## Prerequisites
- AWS Account
- Custom Domain
- Cloudflare Account
- MongoDB Atlas Account (or EC2 MongoDB)

## Step 1: Backend Configuration

### 1.1 Clone Repository
```bash
git clone https://github.com/UnpredictablePrashant/TravelMemory
cd TravelMemory/backend
```

### 1.2 Update Environment Variables
Create `.env` file in backend directory:
```
PORT=3000
MONGO_URI=mongodb+srv://username:password@cluster.mongodb.net/travelmemory?retryWrites=true&w=majority
```

Replace with your actual MongoDB connection string.

### 1.3 Install Dependencies
```bash
npm install
```

### 1.4 Set up Nginx Reverse Proxy
Install nginx on EC2:
```bash
sudo apt update
sudo apt install nginx
```

Create nginx config for backend:
```nginx
server {
    listen 80;
    server_name your-backend-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable and start nginx:
```bash
sudo systemctl enable nginx
sudo systemctl start nginx
```

### 1.5 Start Backend with PM2
Install PM2:
```bash
sudo npm install -g pm2
```

Start the backend:
```bash
pm2 start index.js --name "travel-backend"
pm2 startup
pm2 save
```

## Step 2: Frontend Configuration

### 2.1 Navigate to Frontend
```bash
cd ../frontend
```

### 2.2 Update Backend URL
The `src/url.js` is already updated to use `http://localhost:3000` for local development. For production, set environment variable:
```bash
export REACT_APP_BACKEND_URL=http://your-backend-load-balancer-url
```

### 2.3 Build Frontend
```bash
npm install
npm run build
```

### 2.4 Serve with Nginx
Create nginx config for frontend:
```nginx
server {
    listen 80;
    server_name your-frontend-domain.com;
    root /path/to/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

Copy build files to nginx root and restart nginx.

## Step 3: Scaling with Multiple Instances

### 3.1 Launch Multiple EC2 Instances
- Launch 2-3 EC2 instances for frontend
- Launch 2-3 EC2 instances for backend
- Install required software on each (Node.js, nginx, etc.)

### 3.2 Create Application Load Balancer
1. Go to EC2 > Load Balancers
2. Create Application Load Balancer
3. Configure listeners (HTTP/HTTPS)
4. Add target groups for frontend and backend instances
5. Register instances to target groups

### 3.3 Update Frontend URL
Set REACT_APP_BACKEND_URL to the backend load balancer DNS.

## Step 4: Domain Setup with Cloudflare

### 4.1 Add Domain to Cloudflare
1. Sign in to Cloudflare
2. Add your custom domain
3. Update nameservers

### 4.2 Create DNS Records
- A Record: yourdomain.com -> Frontend EC2 IP
- CNAME Record: api.yourdomain.com -> Backend Load Balancer DNS

### 4.3 SSL/TLS
Enable Always Use HTTPS in Cloudflare.

## Step 5: Testing and Monitoring

### 5.1 Test Application
- Access frontend via domain
- Test backend API endpoints
- Verify load balancing

### 5.2 Monitoring
- Use CloudWatch for EC2 metrics
- Set up health checks on load balancer
- Monitor PM2 processes

## Security Best Practices
- Use security groups to restrict traffic
- Enable HTTPS
- Use IAM roles for EC2
- Regularly update packages
- Use environment variables for secrets

## Troubleshooting
- Check nginx error logs: `sudo tail -f /var/log/nginx/error.log`
- Check PM2 logs: `pm2 logs`
- Verify MongoDB connection
- Ensure ports are open in security groups

## Cost Optimization
- Use spot instances for non-critical workloads
- Set up auto-scaling
- Monitor and adjust instance types