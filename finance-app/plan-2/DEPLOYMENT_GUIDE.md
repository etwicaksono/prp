# WalletFlow - Deployment Guide

**Version:** 1.0.0
**Last Updated:** November 2025

---

## Table of Contents

1. [Overview](#overview)
2. [Docker Setup](#docker-setup)
3. [Environment Variables](#environment-variables)
4. [CI/CD Pipeline](#cicd-pipeline)
5. [Frontend Deployment](#frontend-deployment)
6. [Backend Deployment](#backend-deployment)
7. [Database Deployment](#database-deployment)
8. [Monitoring & Logging](#monitoring--logging)
9. [SSL/HTTPS Configuration](#sslhttps-configuration)
10. [Backup Strategies](#backup-strategies)

---

## 1. Overview

### 1.1 Deployment Architecture

```
┌────────────────────────────────────────────────────────────┐
│                     PRODUCTION SETUP                       │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  CDN (Cloudflare)                                         │
│         │                                                  │
│         ▼                                                  │
│  ┌─────────────┐                                          │
│  │   Vercel    │  Frontend (Next.js)                      │
│  │  (Global)   │  - Auto-scaling                          │
│  └──────┬──────┘  - Edge functions                        │
│         │         - Image optimization                     │
│         │                                                  │
│         │ HTTPS Requests                                   │
│         │                                                  │
│         ▼                                                  │
│  ┌─────────────┐                                          │
│  │  Railway    │  Backend API                             │
│  │ (US-West)   │  - Auto-scaling                          │
│  └──────┬──────┘  - Health checks                         │
│         │         - Zero-downtime deploys                  │
│         │                                                  │
│         │ PostgreSQL Protocol                              │
│         │                                                  │
│         ▼                                                  │
│  ┌─────────────┐                                          │
│  │  Supabase   │  PostgreSQL Database                     │
│  │ (US-West)   │  - Auto-backups                          │
│  └─────────────┘  - Connection pooling                    │
│                   - Point-in-time recovery                 │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### 1.2 Deployment Options Summary

| Component | Option 1 (Recommended) | Option 2 | Option 3 |
|-----------|----------------------|----------|----------|
| **Frontend** | Vercel | Netlify | AWS Amplify |
| **Backend** | Railway | Render | AWS ECS |
| **Database** | Supabase | Neon | AWS RDS |
| **Redis** | Upstash | Railway | AWS ElastiCache |
| **File Storage** | Vercel Blob | AWS S3 | Cloudinary |
| **Monitoring** | Sentry | Datadog | New Relic |

---

## 2. Docker Setup

### 2.1 Frontend Dockerfile

```dockerfile
# frontend/Dockerfile

# Stage 1: Dependencies
FROM node:20-alpine AS deps
WORKDIR /app

# Copy package files
COPY package.json package-lock.json ./

# Install dependencies
RUN npm ci --only=production

# Stage 2: Builder
FROM node:20-alpine AS builder
WORKDIR /app

# Copy dependencies from deps stage
COPY --from=deps /app/node_modules ./node_modules

# Copy source code
COPY . .

# Set environment variables
ENV NEXT_TELEMETRY_DISABLED 1
ENV NODE_ENV production

# Build application
RUN npm run build

# Stage 3: Runner
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV production
ENV NEXT_TELEMETRY_DISABLED 1

# Create non-root user
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

# Copy built application
COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT 3000

CMD ["node", "server.js"]
```

### 2.2 Backend Dockerfile (Node.js)

```dockerfile
# backend-node/Dockerfile

# Stage 1: Dependencies
FROM node:20-alpine AS deps
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --only=production

# Stage 2: Builder
FROM node:20-alpine AS builder
WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Install dev dependencies for build
RUN npm ci

# Build TypeScript
RUN npm run build

# Stage 3: Runner
FROM node:20-alpine AS runner
WORKDIR /app

ENV NODE_ENV production

# Copy dependencies and built files
COPY --from=deps /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/package.json ./package.json

# Create non-root user
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 apiuser

USER apiuser

EXPOSE 3001

CMD ["node", "dist/server.js"]
```

### 2.3 Docker Compose (Development)

```yaml
# docker-compose.yml

version: '3.8'

services:
  # Frontend
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
      target: runner
    ports:
      - "3000:3000"
    environment:
      - NEXT_PUBLIC_API_URL=http://localhost:3001/api/v1
    depends_on:
      - backend
    networks:
      - walletflow-network
    restart: unless-stopped

  # Backend
  backend:
    build:
      context: ./backend-node
      dockerfile: Dockerfile
      target: runner
    ports:
      - "3001:3001"
    environment:
      - NODE_ENV=production
      - PORT=3001
      - DATABASE_URL=postgresql://walletflow:password@postgres:5432/walletflow
      - REDIS_URL=redis://redis:6379
      - JWT_ACCESS_SECRET=${JWT_ACCESS_SECRET}
      - JWT_REFRESH_SECRET=${JWT_REFRESH_SECRET}
      - FRONTEND_URL=http://localhost:3000
    depends_on:
      - postgres
      - redis
    networks:
      - walletflow-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3001/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  # PostgreSQL
  postgres:
    image: postgres:15-alpine
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_USER=walletflow
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=walletflow
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./backend-node/database/init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - walletflow-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U walletflow"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis
  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - walletflow-network
    restart: unless-stopped
    command: redis-server --appendonly yes
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  # Nginx (Reverse Proxy)
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
      - ./nginx/ssl:/etc/nginx/ssl
    depends_on:
      - frontend
      - backend
    networks:
      - walletflow-network
    restart: unless-stopped

networks:
  walletflow-network:
    driver: bridge

volumes:
  postgres_data:
    driver: local
  redis_data:
    driver: local
```

### 2.4 Nginx Configuration

```nginx
# nginx/nginx.conf

events {
    worker_connections 1024;
}

http {
    upstream frontend {
        server frontend:3000;
    }

    upstream backend {
        server backend:3001;
    }

    # HTTP Server (redirect to HTTPS)
    server {
        listen 80;
        server_name walletflow.com www.walletflow.com;

        location /.well-known/acme-challenge/ {
            root /var/www/certbot;
        }

        location / {
            return 301 https://$host$request_uri;
        }
    }

    # HTTPS Server
    server {
        listen 443 ssl http2;
        server_name walletflow.com www.walletflow.com;

        # SSL Configuration
        ssl_certificate /etc/nginx/ssl/fullchain.pem;
        ssl_certificate_key /etc/nginx/ssl/privkey.pem;
        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_ciphers HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers on;

        # Security Headers
        add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
        add_header X-Frame-Options "SAMEORIGIN" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header X-XSS-Protection "1; mode=block" always;

        # Frontend (Next.js)
        location / {
            proxy_pass http://frontend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;
        }

        # Backend API
        location /api/ {
            proxy_pass http://backend;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_cache_bypass $http_upgrade;

            # CORS Headers
            add_header Access-Control-Allow-Origin "https://walletflow.com" always;
            add_header Access-Control-Allow-Methods "GET, POST, PUT, PATCH, DELETE, OPTIONS" always;
            add_header Access-Control-Allow-Headers "Authorization, Content-Type" always;
            add_header Access-Control-Allow-Credentials "true" always;

            # Handle preflight requests
            if ($request_method = 'OPTIONS') {
                return 204;
            }
        }

        # Rate Limiting
        limit_req_zone $binary_remote_addr zone=api_limit:10m rate=10r/s;
        limit_req zone=api_limit burst=20 nodelay;
    }
}
```

### 2.5 Docker Commands

```bash
# Build all services
docker-compose build

# Start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Remove volumes (careful!)
docker-compose down -v

# Restart specific service
docker-compose restart backend

# Execute command in container
docker-compose exec backend npm run migrate

# View running containers
docker-compose ps

# Scale service
docker-compose up -d --scale backend=3
```

---

## 3. Environment Variables

### 3.1 Frontend (.env.production)

```bash
# API Configuration
NEXT_PUBLIC_API_URL=https://api.walletflow.com/api/v1

# Analytics
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
NEXT_PUBLIC_SENTRY_DSN=https://xxxxx@sentry.io/xxxxx

# Feature Flags
NEXT_PUBLIC_ENABLE_2FA=true
NEXT_PUBLIC_ENABLE_OAUTH=true

# Build Configuration
NEXT_TELEMETRY_DISABLED=1
```

### 3.2 Backend (.env.production)

```bash
# Server
NODE_ENV=production
PORT=3001

# Database
DATABASE_URL=postgresql://user:password@db.supabase.com:5432/walletflow?schema=public&sslmode=require

# Redis
REDIS_URL=redis://:password@redis.upstash.io:6379

# JWT
JWT_ACCESS_SECRET=your-production-access-secret-min-256-bits
JWT_REFRESH_SECRET=your-production-refresh-secret-min-256-bits

# OAuth
GOOGLE_CLIENT_ID=123456789.apps.googleusercontent.com
GOOGLE_CLIENT_SECRET=GOCSPX-xxxxxxxxxxxxx
GOOGLE_REDIRECT_URI=https://api.walletflow.com/api/v1/auth/oauth/google/callback

FACEBOOK_APP_ID=123456789012345
FACEBOOK_APP_SECRET=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
FACEBOOK_REDIRECT_URI=https://api.walletflow.com/api/v1/auth/oauth/facebook/callback

# Email
EMAIL_SERVICE=resend
EMAIL_API_KEY=re_xxxxxxxxxxxxxxxxxxxxx
EMAIL_FROM=noreply@walletflow.com

# File Storage
AWS_ACCESS_KEY_ID=AKIAXXXXXXXXXXXXXXXXX
AWS_SECRET_ACCESS_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
AWS_S3_BUCKET=walletflow-uploads
AWS_REGION=us-west-2

# Monitoring
SENTRY_DSN=https://xxxxx@sentry.io/xxxxx
DATADOG_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# Frontend URL
FRONTEND_URL=https://walletflow.com

# Rate Limiting
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_MAX_REQUESTS=100

# CORS
CORS_ORIGIN=https://walletflow.com
```

---

## 4. CI/CD Pipeline

### 4.1 GitHub Actions Workflow

```yaml
# .github/workflows/deploy.yml

name: Deploy to Production

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

env:
  NODE_VERSION: '20'

jobs:
  # Frontend Tests
  test-frontend:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
          cache-dependency-path: frontend/package-lock.json

      - name: Install dependencies
        working-directory: ./frontend
        run: npm ci

      - name: Run linter
        working-directory: ./frontend
        run: npm run lint

      - name: Run type check
        working-directory: ./frontend
        run: npm run type-check

      - name: Run tests
        working-directory: ./frontend
        run: npm run test:ci

      - name: Build application
        working-directory: ./frontend
        run: npm run build
        env:
          NEXT_PUBLIC_API_URL: ${{ secrets.NEXT_PUBLIC_API_URL }}

  # Backend Tests
  test-backend:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: test_db
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

      redis:
        image: redis:7-alpine
        options: >-
          --health-cmd "redis-cli ping"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 6379:6379

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
          cache-dependency-path: backend-node/package-lock.json

      - name: Install dependencies
        working-directory: ./backend-node
        run: npm ci

      - name: Run linter
        working-directory: ./backend-node
        run: npm run lint

      - name: Run type check
        working-directory: ./backend-node
        run: npm run type-check

      - name: Run database migrations
        working-directory: ./backend-node
        run: npm run migrate:test
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/test_db

      - name: Run tests
        working-directory: ./backend-node
        run: npm run test:ci
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/test_db
          REDIS_URL: redis://localhost:6379
          JWT_ACCESS_SECRET: test-secret
          JWT_REFRESH_SECRET: test-secret

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./backend-node/coverage/coverage-final.json
          flags: backend

  # Deploy Frontend to Vercel
  deploy-frontend:
    needs: [test-frontend, test-backend]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          working-directory: ./frontend
          vercel-args: '--prod'

  # Deploy Backend to Railway
  deploy-backend:
    needs: [test-frontend, test-backend]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install Railway CLI
        run: npm install -g @railway/cli

      - name: Deploy to Railway
        working-directory: ./backend-node
        run: railway up --service backend
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

  # Run Database Migrations
  migrate-database:
    needs: [deploy-backend]
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}

      - name: Install dependencies
        working-directory: ./backend-node
        run: npm ci

      - name: Run migrations
        working-directory: ./backend-node
        run: npm run migrate:deploy
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}

  # Notify on Slack
  notify:
    needs: [deploy-frontend, deploy-backend, migrate-database]
    runs-on: ubuntu-latest
    if: always()
    steps:
      - name: Send Slack notification
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: |
            Deployment ${{ job.status }}
            Frontend: https://walletflow.com
            Backend: https://api.walletflow.com
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
        if: always()
```

### 4.2 GitHub Secrets Required

```bash
# Vercel
VERCEL_TOKEN
VERCEL_ORG_ID
VERCEL_PROJECT_ID

# Railway
RAILWAY_TOKEN

# Database
DATABASE_URL

# Slack
SLACK_WEBHOOK

# Frontend Env
NEXT_PUBLIC_API_URL

# Backend Env
JWT_ACCESS_SECRET
JWT_REFRESH_SECRET
GOOGLE_CLIENT_ID
GOOGLE_CLIENT_SECRET
FACEBOOK_APP_ID
FACEBOOK_APP_SECRET
```

---

## 5. Frontend Deployment

### 5.1 Vercel Deployment

**Install Vercel CLI:**
```bash
npm install -g vercel
```

**Login:**
```bash
vercel login
```

**Deploy:**
```bash
cd frontend
vercel --prod
```

**Configuration (vercel.json):**
```json
{
  "version": 2,
  "framework": "nextjs",
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "env": {
    "NEXT_PUBLIC_API_URL": "@api-url"
  },
  "regions": ["iad1"],
  "functions": {
    "app/api/**": {
      "memory": 1024,
      "maxDuration": 10
    }
  },
  "headers": [
    {
      "source": "/(.*)",
      "headers": [
        {
          "key": "X-Content-Type-Options",
          "value": "nosniff"
        },
        {
          "key": "X-Frame-Options",
          "value": "SAMEORIGIN"
        },
        {
          "key": "X-XSS-Protection",
          "value": "1; mode=block"
        }
      ]
    }
  ]
}
```

### 5.2 Netlify Deployment

**Install Netlify CLI:**
```bash
npm install -g netlify-cli
```

**Configuration (netlify.toml):**
```toml
[build]
  command = "npm run build"
  publish = ".next"

[build.environment]
  NODE_VERSION = "20"
  NEXT_PUBLIC_API_URL = "https://api.walletflow.com/api/v1"

[[plugins]]
  package = "@netlify/plugin-nextjs"

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "SAMEORIGIN"
    X-Content-Type-Options = "nosniff"
    X-XSS-Protection = "1; mode=block"

[[redirects]]
  from = "/api/*"
  to = "https://api.walletflow.com/api/:splat"
  status = 200
  force = true
```

**Deploy:**
```bash
cd frontend
netlify deploy --prod
```

---

## 6. Backend Deployment

### 6.1 Railway Deployment

**Install Railway CLI:**
```bash
npm install -g @railway/cli
```

**Login:**
```bash
railway login
```

**Initialize Project:**
```bash
cd backend-node
railway init
```

**Deploy:**
```bash
railway up
```

**Configuration (railway.json):**
```json
{
  "$schema": "https://railway.app/railway.schema.json",
  "build": {
    "builder": "NIXPACKS",
    "buildCommand": "npm run build"
  },
  "deploy": {
    "startCommand": "node dist/server.js",
    "healthcheckPath": "/health",
    "healthcheckTimeout": 100,
    "restartPolicyType": "ON_FAILURE",
    "restartPolicyMaxRetries": 10
  }
}
```

**Environment Variables:**
Set via Railway dashboard or CLI:
```bash
railway variables set DATABASE_URL=postgresql://...
railway variables set JWT_ACCESS_SECRET=...
railway variables set FRONTEND_URL=https://walletflow.com
```

### 6.2 Render Deployment

**Configuration (render.yaml):**
```yaml
services:
  - type: web
    name: walletflow-api
    env: node
    region: oregon
    plan: starter
    buildCommand: npm install && npm run build
    startCommand: node dist/server.js
    healthCheckPath: /health
    autoDeploy: true
    envVars:
      - key: NODE_ENV
        value: production
      - key: DATABASE_URL
        fromDatabase:
          name: walletflow-db
          property: connectionString
      - key: REDIS_URL
        fromService:
          name: walletflow-redis
          type: redis
          property: connectionString
      - key: JWT_ACCESS_SECRET
        sync: false
      - key: JWT_REFRESH_SECRET
        sync: false

databases:
  - name: walletflow-db
    databaseName: walletflow
    plan: starter
    region: oregon

redis:
  - name: walletflow-redis
    plan: starter
    region: oregon
    maxmemoryPolicy: allkeys-lru
```

**Deploy:**
```bash
# Connect GitHub repo in Render dashboard
# Or use Render CLI
render deploy
```

### 6.3 AWS ECS Deployment

**Task Definition (task-definition.json):**
```json
{
  "family": "walletflow-backend",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "512",
  "memory": "1024",
  "containerDefinitions": [
    {
      "name": "backend",
      "image": "123456789012.dkr.ecr.us-west-2.amazonaws.com/walletflow-backend:latest",
      "portMappings": [
        {
          "containerPort": 3001,
          "protocol": "tcp"
        }
      ],
      "environment": [
        {
          "name": "NODE_ENV",
          "value": "production"
        },
        {
          "name": "PORT",
          "value": "3001"
        }
      ],
      "secrets": [
        {
          "name": "DATABASE_URL",
          "valueFrom": "arn:aws:secretsmanager:us-west-2:123456789012:secret:DATABASE_URL"
        },
        {
          "name": "JWT_ACCESS_SECRET",
          "valueFrom": "arn:aws:secretsmanager:us-west-2:123456789012:secret:JWT_ACCESS_SECRET"
        }
      ],
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/ecs/walletflow-backend",
          "awslogs-region": "us-west-2",
          "awslogs-stream-prefix": "ecs"
        }
      },
      "healthCheck": {
        "command": ["CMD-SHELL", "curl -f http://localhost:3001/health || exit 1"],
        "interval": 30,
        "timeout": 5,
        "retries": 3,
        "startPeriod": 60
      }
    }
  ]
}
```

**Deploy Script:**
```bash
#!/bin/bash

# Build and push Docker image
docker build -t walletflow-backend .
docker tag walletflow-backend:latest 123456789012.dkr.ecr.us-west-2.amazonaws.com/walletflow-backend:latest
docker push 123456789012.dkr.ecr.us-west-2.amazonaws.com/walletflow-backend:latest

# Update ECS service
aws ecs update-service \
  --cluster walletflow-cluster \
  --service walletflow-backend \
  --force-new-deployment
```

---

## 7. Database Deployment

### 7.1 Supabase (Recommended)

**Setup:**
1. Create project at https://supabase.com
2. Copy connection string
3. Run migrations

```bash
# Install Supabase CLI
npm install -g supabase

# Login
supabase login

# Link project
supabase link --project-ref your-project-ref

# Run migrations
supabase db push
```

**Connection String:**
```
postgresql://postgres:[YOUR-PASSWORD]@db.your-project.supabase.co:5432/postgres
```

### 7.2 Neon

**Setup:**
1. Create project at https://neon.tech
2. Copy connection string
3. Enable connection pooling

**Connection String:**
```
postgresql://user:password@ep-xxx.us-west-2.aws.neon.tech/walletflow?sslmode=require
```

**Pooled Connection (Recommended):**
```
postgresql://user:password@ep-xxx-pooler.us-west-2.aws.neon.tech/walletflow?sslmode=require
```

### 7.3 AWS RDS

**Create RDS Instance:**
```bash
aws rds create-db-instance \
  --db-instance-identifier walletflow-db \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version 15.3 \
  --master-username walletflow \
  --master-user-password YourPassword123 \
  --allocated-storage 20 \
  --storage-type gp3 \
  --vpc-security-group-ids sg-xxxxx \
  --db-subnet-group-name walletflow-subnet-group \
  --backup-retention-period 7 \
  --preferred-backup-window "03:00-04:00" \
  --preferred-maintenance-window "mon:04:00-mon:05:00" \
  --storage-encrypted \
  --enable-cloudwatch-logs-exports postgresql
```

**Connection String:**
```
postgresql://walletflow:YourPassword123@walletflow-db.xxxxxx.us-west-2.rds.amazonaws.com:5432/walletflow
```

### 7.4 Database Migrations

**Prisma Migrations:**
```bash
# Generate migration
npx prisma migrate dev --name add_new_table

# Deploy to production
npx prisma migrate deploy

# Reset database (development only!)
npx prisma migrate reset
```

**Manual SQL Migration:**
```sql
-- migrations/001_add_notifications_table.sql
CREATE TABLE IF NOT EXISTS notifications (
  id VARCHAR(36) PRIMARY KEY,
  user_id VARCHAR(36) NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  type VARCHAR(32) NOT NULL,
  title VARCHAR(128) NOT NULL,
  message TEXT NOT NULL,
  read BOOLEAN DEFAULT FALSE,
  action_url VARCHAR(255),
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_notifications_user_id ON notifications(user_id);
CREATE INDEX idx_notifications_read ON notifications(read);
```

---

## 8. Monitoring & Logging

### 8.1 Sentry Setup

**Frontend:**
```typescript
// frontend/src/app/layout.tsx
import * as Sentry from '@sentry/nextjs';

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1,
  replaysSessionSampleRate: 0.1,
  replaysOnErrorSampleRate: 1.0,
});
```

**Backend:**
```typescript
// backend/src/server.ts
import * as Sentry from '@sentry/node';

Sentry.init({
  dsn: process.env.SENTRY_DSN,
  environment: process.env.NODE_ENV,
  tracesSampleRate: 0.1,
});

// Error handler
app.use(Sentry.Handlers.errorHandler());
```

### 8.2 Application Logs

**Winston Logger (Node.js):**
```typescript
// backend/src/lib/logger.ts
import winston from 'winston';

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.Console(),
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' }),
  ],
});

export default logger;
```

**Usage:**
```typescript
import logger from './lib/logger';

logger.info('User logged in', { userId: user.id });
logger.error('Database connection failed', { error: error.message });
```

### 8.3 Performance Monitoring

**Frontend (Vercel Analytics):**
```typescript
// frontend/src/app/layout.tsx
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

**Backend (Datadog APM):**
```typescript
// backend/src/server.ts
import tracer from 'dd-trace';

tracer.init({
  env: process.env.NODE_ENV,
  service: 'walletflow-api',
  version: '1.0.0',
  logInjection: true,
});

export default tracer;
```

### 8.4 Health Checks

**Backend Health Endpoint:**
```typescript
// backend/src/routes/health.routes.ts
import { Router } from 'express';
import { prisma } from '../lib/prisma';
import { redis } from '../lib/redis';

const router = Router();

router.get('/health', async (req, res) => {
  try {
    // Check database
    await prisma.$queryRaw`SELECT 1`;

    // Check Redis
    await redis.ping();

    res.status(200).json({
      status: 'healthy',
      timestamp: new Date().toISOString(),
      uptime: process.uptime(),
      services: {
        database: 'up',
        redis: 'up'
      }
    });
  } catch (error) {
    res.status(503).json({
      status: 'unhealthy',
      error: error.message
    });
  }
});

export default router;
```

---

## 9. SSL/HTTPS Configuration

### 9.1 Let's Encrypt (Certbot)

**Install Certbot:**
```bash
sudo apt-get update
sudo apt-get install certbot python3-certbot-nginx
```

**Generate Certificate:**
```bash
sudo certbot --nginx -d walletflow.com -d www.walletflow.com
```

**Auto-renewal:**
```bash
# Test renewal
sudo certbot renew --dry-run

# Add to crontab
0 0 * * * /usr/bin/certbot renew --quiet
```

### 9.2 Cloudflare SSL

1. Add domain to Cloudflare
2. Update nameservers
3. Enable SSL/TLS (Full Strict mode)
4. Configure Page Rules:
   - Always Use HTTPS
   - Automatic HTTPS Rewrites

### 9.3 AWS Certificate Manager

```bash
# Request certificate
aws acm request-certificate \
  --domain-name walletflow.com \
  --subject-alternative-names www.walletflow.com \
  --validation-method DNS \
  --region us-west-2

# Validate via DNS (add CNAME records)
# Certificate will be auto-renewed by AWS
```

---

## 10. Backup Strategies

### 10.1 Database Backups

**Automated Backups (Supabase):**
- Daily automatic backups
- Point-in-time recovery (PITR)
- 7-day retention (free tier)
- 30-day retention (paid tiers)

**Manual Backup:**
```bash
# Backup database
pg_dump -h db.your-project.supabase.co \
  -U postgres \
  -d postgres \
  -F c \
  -f backup_$(date +%Y%m%d).dump

# Restore database
pg_restore -h db.your-project.supabase.co \
  -U postgres \
  -d postgres \
  backup_20251118.dump
```

**AWS RDS Automated Backups:**
```bash
# Create manual snapshot
aws rds create-db-snapshot \
  --db-instance-identifier walletflow-db \
  --db-snapshot-identifier walletflow-backup-20251118

# Restore from snapshot
aws rds restore-db-instance-from-db-snapshot \
  --db-instance-identifier walletflow-db-restored \
  --db-snapshot-identifier walletflow-backup-20251118
```

### 10.2 File Storage Backups

**S3 Versioning:**
```bash
# Enable versioning
aws s3api put-bucket-versioning \
  --bucket walletflow-uploads \
  --versioning-configuration Status=Enabled

# Lifecycle policy for old versions
aws s3api put-bucket-lifecycle-configuration \
  --bucket walletflow-uploads \
  --lifecycle-configuration file://lifecycle.json
```

**lifecycle.json:**
```json
{
  "Rules": [
    {
      "Id": "DeleteOldVersions",
      "Status": "Enabled",
      "NoncurrentVersionExpiration": {
        "NoncurrentDays": 90
      }
    }
  ]
}
```

### 10.3 Backup Verification

**Script to Verify Backups:**
```bash
#!/bin/bash
# backup-verify.sh

BACKUP_FILE="backup_$(date +%Y%m%d).dump"

# Create test database
createdb test_restore

# Restore backup to test database
pg_restore -d test_restore $BACKUP_FILE

# Run verification queries
psql test_restore -c "SELECT COUNT(*) FROM users;"
psql test_restore -c "SELECT COUNT(*) FROM transactions;"

# Cleanup
dropdb test_restore

echo "Backup verification completed successfully"
```

### 10.4 Disaster Recovery Plan

**Recovery Time Objective (RTO):** 1 hour
**Recovery Point Objective (RPO):** 1 hour

**Steps:**
1. Identify failure (monitoring alerts)
2. Assess damage scope
3. Notify team via Slack/PagerDuty
4. Restore from latest backup
5. Verify data integrity
6. Switch DNS to backup region (if needed)
7. Post-mortem analysis

---

## Appendix

### A. Deployment Checklist

**Pre-Deployment:**
- [ ] Run all tests locally
- [ ] Update environment variables
- [ ] Review database migrations
- [ ] Check for security vulnerabilities
- [ ] Update documentation
- [ ] Notify team of deployment

**During Deployment:**
- [ ] Monitor CI/CD pipeline
- [ ] Watch application logs
- [ ] Check health endpoints
- [ ] Verify SSL certificates
- [ ] Test critical user flows

**Post-Deployment:**
- [ ] Monitor error rates (Sentry)
- [ ] Check performance metrics
- [ ] Verify database backups
- [ ] Update status page
- [ ] Create rollback plan

### B. Troubleshooting

**Common Issues:**

1. **Database Connection Timeout**
   ```bash
   # Check connection pooling
   # Increase max connections
   # Verify network security groups
   ```

2. **High Memory Usage**
   ```bash
   # Check for memory leaks
   # Increase container memory
   # Enable Redis caching
   ```

3. **Slow API Response**
   ```bash
   # Review database indexes
   # Enable query caching
   # Optimize N+1 queries
   ```

### C. Cost Estimation

**Monthly Costs (Startup Plan):**

| Service | Provider | Cost |
|---------|----------|------|
| Frontend | Vercel Pro | $20 |
| Backend | Railway Starter | $5 |
| Database | Supabase Pro | $25 |
| Redis | Upstash | $10 |
| Storage | Vercel Blob | $5 |
| Monitoring | Sentry | $26 |
| **Total** | | **$91/month** |

**Monthly Costs (Production Plan):**

| Service | Provider | Cost |
|---------|----------|------|
| Frontend | Vercel Enterprise | $150 |
| Backend | Railway Pro | $20 |
| Database | Supabase Pro | $25 |
| Redis | Upstash Pro | $30 |
| Storage | AWS S3 | $10 |
| CDN | Cloudflare Pro | $20 |
| Monitoring | Datadog | $31 |
| **Total** | | **$286/month** |

---

**Document Status:** ✅ Complete
**Last Updated:** November 2025
**Next Review:** January 2026
