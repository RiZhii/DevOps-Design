# DevOps Design – UI + API + Database on AWS

## 📌 Overview

This repository contains a complete design-only DevOps architecture for deploying, securing, scaling, and maintaining:

- A *UI (frontend)* application  
- An *API (backend)* service  
- A *relational database*  
- A *CI/CD pipeline* using GitHub Actions  

This submission includes:

- **System Architecture Diagram (draw.io exported as PDF)**
- **CI/CD Pipeline Diagram (draw.io exported as PDF)**
- Infrastructure explanation
- Scaling strategies for UI + API
- Database HA + DR strategy
- CI/CD workflow description


---

## 🏗 1. Architecture Explanation

### 1.1 UI (Frontend)  
The frontend is designed as a **static SPA** (React/Angular/Vue) deployed using AWS serverless and globally distributed services.

#### Hosting
- Hosted in **S3** as static files (HTML, JS, CSS)
- Distributed via **CloudFront CDN**

#### Security
- S3 bucket is private; CloudFront uses **Origin Access Control**  
- **AWS WAF** (optional) protects against common attacks

#### Traffic Flow
User → Route 53 → CloudFront → S3  

#### Horizontal Scaling
- CloudFront scales globally
- S3 auto-scales
- Stateless SPA → infinite horizontal scalability

---

### 1.2 API (Backend)

#### Runtime
- Dockerized app
- ECS Fargate = serverless containers

#### Traffic Flow
Route 53 → ALB → ECS Tasks  

#### Security
- ECS tasks in **private subnets**
- Only ALB is public-facing
- Secrets in **AWS Secrets Manager**
- IAM Task Roles used to fetch secrets

#### Horizontal Scaling
- ECS Auto Scaling:
  - CPU > 60%
  - Memory > 70%
  - Requests-per-target threshold
- Multi-AZ tasks
- Rolling updates

---

### 1.3 Database (RDS PostgreSQL)

#### High Availability
- **Multi-AZ failover**  
- Primary + Standby

#### Scalability
- Vertical scaling  
- Read replicas  

#### Backups & PITR
- Automated backups  
- PITR available  
- Manual snapshots before migrations  

#### Schema Migrations
- Flyway/Liquibase/Prisma/Django migrations  
- Dev → Stage → Snapshot → Prod pipeline  

---

## 🔄 2. CI/CD Workflow Explanation

### Triggers
- **PR:** build + tests  
- **Merge to main:** deploy to DEV  
- **Tag/approval:** promote to STAGE → PROD  

### Build Stage
- Build frontend  
- Build backend container  
- Push to ECR  

### Test Stage
- Unit tests  
- Linting  
- Security scans  

### Deployment
#### Frontend:
- Upload to S3  
- Invalidate CloudFront  

#### Backend:
- Update ECS task definition  
- ECS rolling deploy  
- ALB health checks  

### Promotion
DEV → STAGE → PROD  
With manual approvals.

### Rollback
- ECS rollback to previous task definition  
- Redeploy previous frontend artifact  

---

## ⚖ 3. How the System Handles Load

### Frontend
- CloudFront edge caching  
- S3 autoscaling  
- Browser caching  

### API
- ALB load balancing  
- ECS auto-scaling  
- Multi-AZ distribution  

### Database
- Read replicas for heavy reads  
- Multi-AZ failover  
- Vertical scaling when needed  

---

## 🛡 4. High Availability & Disaster Recovery

| Layer | HA Mechanism |
|-------|--------------|
| UI | CloudFront global CDN + S3 multi-AZ |
| API | ECS tasks across multiple AZs |
| Database | RDS Multi-AZ failover + replicas |
| DNS | Route 53 global DNS |

Backups:
- Automated backups  
- PITR  
- Snapshots  

---
