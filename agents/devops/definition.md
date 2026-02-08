---
type: agent
role: devops
framework: multi-agent-sdlc
version: 1.0
platforms:
  - claude-code
  - gemini-cli
  - cursor
tags:
  - agent
  - devops
  - infrastructure
  - deployment
  - sdlc
created: 2026-02-08
modified: 2026-02-08
---

# DevOps Agent

## Purpose
The DevOps agent is responsible for infrastructure provisioning, deployment automation, monitoring setup, and operational excellence. This agent translates architectural and implementation decisions into production-ready infrastructure and CI/CD pipelines.

## Capabilities
- Infrastructure as Code (Terraform, Pulumi)
- CI/CD pipeline configuration (GitHub Actions, GitLab CI, Cloud Build)
- Container orchestration (Docker, Kubernetes, Cloud Run)
- Cloud platform deployment (GCP, AWS, Azure)
- Monitoring and observability setup (Prometheus, Grafana, Cloud Monitoring)
- Secret management (Secret Manager, Vault)
- Database migrations and management
- Load balancing and auto-scaling configuration
- Backup and disaster recovery planning
- Cost optimization and resource management
- Performance tuning and capacity planning

## System Prompt

```markdown
You are a DevOps agent in a Multi-Agent SDLC Framework. Your role is to create reliable, scalable, and cost-effective infrastructure and deployment pipelines.

CORE RESPONSIBILITIES:
1. Design and implement infrastructure as code
2. Create automated deployment pipelines
3. Set up monitoring, logging, and alerting
4. Ensure security best practices in infrastructure
5. Optimize for cost and performance
6. Plan for disaster recovery and backups
7. Document operational procedures

DEVOPS PRINCIPLES:
- Automate everything possible
- Infrastructure as code, never manual changes
- Fail fast with clear error messages
- Design for observability
- Plan for failure and recovery
- Optimize for cost without sacrificing reliability
- Document all operational procedures
- Use managed services when appropriate

OUTPUT FORMAT:
Always produce Infrastructure Records in markdown with:
- Infrastructure Overview: What is being deployed
- Infrastructure Code: Terraform/scripts with comments
- Deployment Pipeline: CI/CD configuration
- Monitoring Setup: Dashboards and alerts
- Operational Runbook: How to operate and troubleshoot
- Cost Analysis: Expected monthly costs
- Handoff Notes: Issues for Security and testing

CLOUD PLATFORMS:
- GCP: Cloud Run, Cloud Functions, GKE, Cloud SQL, Secret Manager
- AWS: ECS, Lambda, RDS, Secrets Manager
- Azure: Container Apps, Functions, AKS, SQL Database
- Multi-cloud: Design portable where possible

APPROVAL GATES:
Your infrastructure configuration requires review before provisioning.
Mark approval points clearly in your output.
```

## Input Artifacts
- Architecture Decision Record (ADR) from Solution Architect
- Implementation Record from Senior Engineer
- Application requirements (dependencies, environment variables)
- Security requirements from Security Advisor

## Output Artifacts

### Infrastructure Record
```markdown
# Infrastructure Record: {Project Name}

**Date:** YYYY-MM-DD
**Cloud Provider:** {GCP | AWS | Azure}
**ADR Reference:** {ADR-XXX}
**Implementation Reference:** {IMPL-XXX}
**Status:** Planned | Provisioned | Live

## Infrastructure Overview

### Architecture Diagram
```
[ASCII or mermaid diagram showing infrastructure components]

Production Environment:
┌─────────────────────────────────────────────────┐
│              Cloud Load Balancer                 │
│              (global, HTTPS)                     │
└────────────┬────────────────────────────────────┘
             │
    ┌────────┴────────┐
    │                 │
┌───▼────┐      ┌────▼────┐
│ Region │      │ Region  │
│  US     │      │  EU     │
└───┬────┘      └────┬────┘
    │                │
    │  Cloud Run     │  Cloud Run
    │  (API)         │  (API)
    │                │
    ├────────────────┤
    │                │
┌───▼────┐      ┌────▼────┐
│Cloud   │      │ Cloud   │
│SQL     │◄─────┤ SQL     │
│(Primary│      │(Replica)│
└────────┘      └─────────┘
    │
┌───▼────────┐
│ Firestore  │
│ (Sessions) │
└────────────┘
```

### Components

**Compute:**
- Cloud Run: API service (auto-scale 0-10 instances)
- Cloud Functions: Background tasks (event-driven)

**Database:**
- Cloud SQL (PostgreSQL 15): Primary data store
- Firestore: Real-time chat history
- Redis (Memorystore): Session cache

**Storage:**
- Cloud Storage: Static assets, backups

**Networking:**
- Cloud Load Balancer: Global traffic distribution
- Cloud CDN: Static asset caching
- VPC: Private networking

**Security:**
- Secret Manager: API keys and credentials
- Cloud Armor: DDoS protection
- IAM: Access control

**Monitoring:**
- Cloud Logging: Centralized logs
- Cloud Monitoring: Metrics and alerts
- Cloud Trace: Distributed tracing

## Infrastructure Code

### Terraform Configuration

**File:** `terraform/main.tf`
```hcl
# Generic Cloud Infrastructure Template
# Adaptable to: GCP, AWS, Azure
# Pattern: Containerized API + Managed Database + Cache

terraform {
  required_version = ">= 1.5"
  
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = "~> 5.0"
    }
  }
  
  backend "gcs" {
    bucket = "PROJECT-terraform-state"
    prefix = "app/production"
  }
}

provider "google" {
  project = var.project_id
  region  = var.primary_region
}

# Variables
variable "project_id" {
  description = "Cloud project ID"
  type        = string
}

variable "primary_region" {
  description = "Primary deployment region"
  type        = string
  default     = "us-central1"
}

variable "app_name" {
  description = "Application name"
  type        = string
}

variable "environment" {
  description = "Environment (dev, staging, production)"
  type        = string
  default     = "production"
}

# Serverless Container Platform (Cloud Run / App Runner / Container Apps)
resource "google_cloud_run_service" "api" {
  name     = "${var.app_name}-api"
  location = var.primary_region
  
  template {
    spec {
      containers {
        image = "gcr.io/${var.project_id}/${var.app_name}:latest"
        
        # Environment variables from secrets
        env {
          name = "DATABASE_URL"
          value_from {
            secret_key_ref {
              name = google_secret_manager_secret.db_url.secret_id
              key  = "latest"
            }
          }
        }
        
        resources {
          limits = {
            cpu    = "1000m"
            memory = "512Mi"
          }
        }
        
        ports {
          container_port = 8000
        }
      }
      
      # Auto-scaling configuration
      container_concurrency = 80
      timeout_seconds       = 300
      service_account_name  = google_service_account.api.email
    }
    
    metadata {
      annotations = {
        "autoscaling.knative.dev/minScale" = var.min_instances
        "autoscaling.knative.dev/maxScale" = var.max_instances
      }
    }
  }
  
  traffic {
    percent         = 100
    latest_revision = true
  }
}

# Managed Relational Database (Cloud SQL / RDS / Azure SQL)
resource "google_sql_database_instance" "primary" {
  name             = "${var.app_name}-db"
  database_version = "POSTGRES_15"
  region           = var.primary_region
  
  settings {
    tier              = var.db_tier
    availability_type = var.high_availability ? "REGIONAL" : "ZONAL"
    disk_size         = var.db_disk_size_gb
    disk_type         = "PD_SSD"
    
    backup_configuration {
      enabled                        = true
      start_time                     = "03:00"
      point_in_time_recovery_enabled = true
      transaction_log_retention_days = 7
    }
    
    ip_configuration {
      ipv4_enabled    = false
      private_network = google_compute_network.vpc.id
      require_ssl     = true
    }
    
    maintenance_window {
      day  = 7  # Sunday
      hour = 3  # 3 AM
    }
  }
  
  deletion_protection = var.environment == "production"
}

resource "google_sql_database" "app" {
  name     = var.app_name
  instance = google_sql_database_instance.primary.name
}

# NoSQL Database (Firestore / DynamoDB / CosmosDB) - Optional
resource "google_firestore_database" "realtime" {
  count       = var.enable_nosql ? 1 : 0
  project     = var.project_id
  name        = "(default)"
  location_id = var.primary_region
  type        = "FIRESTORE_NATIVE"
  
  concurrency_mode = "OPTIMISTIC"
}

# Cache Layer (Redis / Memcached / ElastiCache)
resource "google_redis_instance" "cache" {
  count          = var.enable_cache ? 1 : 0
  name           = "${var.app_name}-cache"
  tier           = var.cache_tier
  memory_size_gb = var.cache_memory_gb
  region         = var.primary_region
  
  redis_version      = "REDIS_7_0"
  authorized_network = google_compute_network.vpc.id
  connect_mode       = "PRIVATE_SERVICE_ACCESS"
}

# Secret Management
resource "google_secret_manager_secret" "api_keys" {
  for_each = toset(var.secret_names)
  
  secret_id = each.key
  
  replication {
    automatic = true
  }
}

resource "google_secret_manager_secret" "db_url" {
  secret_id = "database-url"
  
  replication {
    automatic = true
  }
}

# Service Account (IAM Role)
resource "google_service_account" "api" {
  account_id   = "${var.app_name}-api-sa"
  display_name = "${var.app_name} API Service Account"
}

# IAM: Secret Access
resource "google_secret_manager_secret_iam_member" "api_secrets" {
  for_each = google_secret_manager_secret.api_keys
  
  secret_id = each.value.id
  role      = "roles/secretmanager.secretAccessor"
  member    = "serviceAccount:${google_service_account.api.email}"
}

# Public Access (adjust for production authentication)
resource "google_cloud_run_service_iam_member" "public" {
  service  = google_cloud_run_service.api.name
  location = google_cloud_run_service.api.location
  role     = "roles/run.invoker"
  member   = var.public_access ? "allUsers" : "serviceAccount:${google_service_account.api.email}"
}

# Storage Bucket for Assets/Backups
resource "google_storage_bucket" "assets" {
  name          = "${var.project_id}-${var.app_name}-assets"
  location      = var.primary_region
  storage_class = "STANDARD"
  
  versioning {
    enabled = true
  }
  
  lifecycle_rule {
    condition {
      age = var.asset_retention_days
    }
    action {
      type = "Delete"
    }
  }
}

# VPC Network
resource "google_compute_network" "vpc" {
  name                    = "${var.app_name}-vpc"
  auto_create_subnetworks = false
}

resource "google_compute_subnetwork" "private" {
  name          = "${var.app_name}-private-subnet"
  ip_cidr_range = "10.0.0.0/24"
  region        = var.primary_region
  network       = google_compute_network.vpc.id
  
  private_ip_google_access = true
}

# Outputs
output "api_url" {
  description = "API service URL"
  value       = google_cloud_run_service.api.status[0].url
}

output "database_connection" {
  description = "Database connection name"
  value       = google_sql_database_instance.primary.connection_name
  sensitive   = true
}

output "service_account_email" {
  description = "Service account email"
  value       = google_service_account.api.email
}
```

**File:** `terraform/variables.tf`
```hcl
# Application Configuration
variable "min_instances" {
  description = "Minimum container instances"
  type        = number
  default     = 0
}

variable "max_instances" {
  description = "Maximum container instances"
  type        = number
  default     = 10
}

# Database Configuration
variable "db_tier" {
  description = "Database instance tier"
  type        = string
  default     = "db-f1-micro"  # Adjust for production
}

variable "db_disk_size_gb" {
  description = "Database disk size in GB"
  type        = number
  default     = 10
}

variable "high_availability" {
  description = "Enable HA for database"
  type        = bool
  default     = false
}

# Cache Configuration
variable "enable_cache" {
  description = "Enable Redis cache"
  type        = bool
  default     = true
}

variable "cache_tier" {
  description = "Cache tier (BASIC or STANDARD_HA)"
  type        = string
  default     = "BASIC"
}

variable "cache_memory_gb" {
  description = "Cache memory in GB"
  type        = number
  default     = 1
}

# NoSQL Configuration
variable "enable_nosql" {
  description = "Enable NoSQL database"
  type        = bool
  default     = false
}

# Secrets
variable "secret_names" {
  description = "List of secret names to create"
  type        = list(string)
  default     = []
}

# Access Control
variable "public_access" {
  description = "Allow public access to API"
  type        = bool
  default     = false
}

# Storage
variable "asset_retention_days" {
  description = "Days to retain assets"
  type        = number
  default     = 30
}
```

**File:** `terraform/monitoring.tf`
```hcl
# Monitoring and Alerting Configuration

# Uptime check for API health
resource "google_monitoring_uptime_check_config" "api" {
  display_name = "${var.app_name} API Health Check"
  timeout      = "10s"
  period       = "60s"
  
  http_check {
    path         = var.health_check_path
    port         = "443"
    use_ssl      = true
    validate_ssl = true
  }
  
  monitored_resource {
    type = "uptime_url"
    labels = {
      project_id = var.project_id
      host       = replace(google_cloud_run_service.api.status[0].url, "https://", "")
    }
  }
}

# Alert policy for API downtime
resource "google_monitoring_alert_policy" "api_down" {
  display_name = "${var.app_name} API Down"
  combiner     = "OR"
  
  conditions {
    display_name = "Uptime check failed"
    
    condition_threshold {
      filter          = "metric.type=\"monitoring.googleapis.com/uptime_check/check_passed\" AND resource.type=\"uptime_url\""
      duration        = "300s"
      comparison      = "COMPARISON_LT"
      threshold_value = 1
      
      aggregations {
        alignment_period   = "60s"
        per_series_aligner = "ALIGN_FRACTION_TRUE"
      }
    }
  }
  
  notification_channels = [google_monitoring_notification_channel.email.id]
  
  alert_strategy {
    auto_close = "1800s"
  }
}

# Notification channel
resource "google_monitoring_notification_channel" "email" {
  display_name = "DevOps Team Email"
  type         = "email"
  
  labels = {
    email_address = var.alert_email
  }
}

# Log-based metric for errors
resource "google_logging_metric" "api_errors" {
  name   = "${var.app_name}_error_count"
  filter = "resource.type=\"cloud_run_revision\" AND severity>=ERROR"
  
  metric_descriptor {
    metric_kind = "DELTA"
    value_type  = "INT64"
  }
}

# Alert for high error rate
resource "google_monitoring_alert_policy" "high_error_rate" {
  display_name = "${var.app_name} High Error Rate"
  combiner     = "OR"
  
  conditions {
    display_name = "Error rate exceeds threshold"
    
    condition_threshold {
      filter          = "metric.type=\"logging.googleapis.com/user/${var.app_name}_error_count\""
      duration        = "300s"
      comparison      = "COMPARISON_GT"
      threshold_value = var.error_threshold
      
      aggregations {
        alignment_period   = "60s"
        per_series_aligner = "ALIGN_RATE"
      }
    }
  }
  
  notification_channels = [google_monitoring_notification_channel.email.id]
}

# Additional variables for monitoring
variable "health_check_path" {
  description = "Health check endpoint path"
  type        = string
  default     = "/health"
}

variable "alert_email" {
  description = "Email for alerts"
  type        = string
}

variable "error_threshold" {
  description = "Error count threshold for alerts"
  type        = number
  default     = 10
}
```

## Deployment Pipeline

### GitHub Actions CI/CD

**File:** `.github/workflows/deploy.yml`
```yaml
name: Deploy to Production

on:
  push:
    branches: [main]
  workflow_dispatch:

env:
  PROJECT_ID: ${{ secrets.GCP_PROJECT_ID }}
  REGION: ${{ secrets.GCP_REGION || 'us-central1' }}
  SERVICE_NAME: ${{ secrets.SERVICE_NAME }}
  REGISTRY: ${{ secrets.REGISTRY || 'gcr.io' }}

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      # Backend tests (Python example)
      - name: Set up Python
        if: hashFiles('backend/requirements.txt') != ''
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      
      - name: Install backend dependencies
        if: hashFiles('backend/requirements.txt') != ''
        run: |
          cd backend
          pip install uv
          uv pip install -r requirements.txt
          uv pip install -r requirements-dev.txt
      
      - name: Run backend linters
        if: hashFiles('backend/requirements.txt') != ''
        run: |
          cd backend
          ruff check .
          mypy .
      
      - name: Run backend tests
        if: hashFiles('backend/requirements.txt') != ''
        run: |
          cd backend
          pytest --cov=. --cov-report=xml
      
      # Frontend tests (Node.js example)
      - name: Set up Node.js
        if: hashFiles('frontend/package.json') != ''
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      
      - name: Install frontend dependencies
        if: hashFiles('frontend/package.json') != ''
        run: |
          cd frontend
          npm install -g pnpm
          pnpm install
      
      - name: Run frontend linters
        if: hashFiles('frontend/package.json') != ''
        run: |
          cd frontend
          pnpm lint
      
      - name: Run frontend tests
        if: hashFiles('frontend/package.json') != ''
        run: |
          cd frontend
          pnpm test
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Authenticate to Google Cloud
        uses: google-github-actions/auth@v2
        with:
          credentials_json: ${{ secrets.GCP_SA_KEY }}
      
      - name: Set up Cloud SDK
        uses: google-github-actions/setup-gcloud@v2
      
      - name: Configure Docker
        run: gcloud auth configure-docker
      
      - name: Build Docker image
        run: |
          docker build -t $REGISTRY/$PROJECT_ID/$SERVICE_NAME:${{ github.sha }} .
          docker tag $REGISTRY/$PROJECT_ID/$SERVICE_NAME:${{ github.sha }} \
                     $REGISTRY/$PROJECT_ID/$SERVICE_NAME:latest
      
      - name: Push to Container Registry
        run: |
          docker push $REGISTRY/$PROJECT_ID/$SERVICE_NAME:${{ github.sha }}
          docker push $REGISTRY/$PROJECT_ID/$SERVICE_NAME:latest

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Authenticate to Google Cloud
        uses: google-github-actions/auth@v2
        with:
          credentials_json: ${{ secrets.GCP_SA_KEY }}
      
      - name: Deploy to Cloud Run
        run: |
          gcloud run deploy $SERVICE_NAME \
            --image $REGISTRY/$PROJECT_ID/$SERVICE_NAME:${{ github.sha }} \
            --region $REGION \
            --platform managed \
            --allow-unauthenticated \
            --memory ${{ secrets.MEMORY || '512Mi' }} \
            --cpu ${{ secrets.CPU || '1' }} \
            --min-instances ${{ secrets.MIN_INSTANCES || '0' }} \
            --max-instances ${{ secrets.MAX_INSTANCES || '10' }} \
            --set-env-vars "ENVIRONMENT=production"
      
      - name: Run database migrations
        if: hashFiles('backend/migrations/**') != ''
        run: |
          SERVICE_URL=$(gcloud run services describe $SERVICE_NAME \
            --region $REGION --format 'value(status.url)')
          
          curl -X POST "$SERVICE_URL/admin/migrate" \
            -H "Authorization: Bearer $(gcloud auth print-identity-token)"
      
      - name: Verify deployment
        run: |
          SERVICE_URL=$(gcloud run services describe $SERVICE_NAME \
            --region $REGION --format 'value(status.url)')
          
          curl -f "$SERVICE_URL/health" || exit 1
      
      - name: Notify deployment success
        if: success()
        run: |
          echo "✅ Deployment successful to $SERVICE_URL"
          # Add Slack/Discord/email notification here

  rollback:
    if: failure()
    needs: deploy
    runs-on: ubuntu-latest
    steps:
      - name: Rollback to previous revision
        run: |
          gcloud run services update-traffic $SERVICE_NAME \
            --region $REGION \
            --to-revisions LATEST=0,@previous-revision=100
      
      - name: Notify rollback
        run: |
          echo "⚠️ Deployment failed - rolled back to previous version"
```

**File:** `Dockerfile`
```dockerfile
# Multi-stage build for containerized application

# Stage 1: Dependencies (Python example - adapt for your stack)
FROM python:3.11-slim as dependencies

WORKDIR /app

# Install package manager (uv for Python, adapt as needed)
RUN pip install uv

# Copy dependency files
COPY requirements.txt .

# Install dependencies
RUN uv pip install --system -r requirements.txt

# Stage 2: Application
FROM python:3.11-slim

WORKDIR /app

# Copy installed dependencies from stage 1
COPY --from=dependencies /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages

# Copy application code
COPY . .

# Create non-root user for security
RUN useradd -m -u 1000 appuser && chown -R appuser:appuser /app
USER appuser

# Expose application port
EXPOSE 8000

# Health check endpoint
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8000/health || exit 1

# Run application (adjust command for your stack)
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]

# For Node.js applications:
# FROM node:20-slim
# COPY package*.json ./
# RUN npm ci --only=production
# COPY . .
# CMD ["node", "server.js"]

# For Go applications:
# FROM golang:1.21 as builder
# COPY . .
# RUN CGO_ENABLED=0 go build -o app
# FROM alpine:latest
# COPY --from=builder /app/app .
# CMD ["./app"]
```

## Monitoring Setup

### Dashboards

**Cloud Monitoring Dashboard:**
- API Request Rate (requests/second)
- API Latency (p50, p95, p99)
- Error Rate (errors/total requests)
- Database Connection Pool Usage
- Cache Hit Rate
- Cloud Run Instance Count
- Cost per Request

**Logs to Monitor:**
- Application errors (ERROR level)
- Database slow queries (>1s)
- Failed LLM API calls
- Authentication failures
- Rate limit hits

### Alerts

**Critical Alerts** (page on-call):
1. API downtime > 5 minutes
2. Error rate > 10% for 5 minutes
3. Database connection failures
4. p99 latency > 10 seconds

**Warning Alerts** (email):
1. Error rate > 5% for 10 minutes
2. p95 latency > 5 seconds
3. Database disk usage > 80%
4. Daily cost > $50

## Operational Runbook

### Deployment Process

**Standard Deployment:**
1. Merge PR to `main` branch
2. GitHub Actions runs tests
3. Builds Docker image
4. Pushes to GCR
5. Deploys to Cloud Run
6. Runs database migrations
7. Verifies health check
8. Notifies team

**Emergency Rollback:**
```bash
# Rollback to previous revision
gcloud run services update-traffic omni-chat-api \
  --region us-central1 \
  --to-revisions @previous-revision=100

# Or rollback to specific revision
gcloud run services update-traffic omni-chat-api \
  --region us-central1 \
  --to-revisions omni-chat-api-00042-abc=100
```

### Common Operations

**View Logs:**
```bash
# Real-time logs from container service
gcloud run services logs tail ${SERVICE_NAME} --region ${REGION}

# Filter for errors
gcloud logging read "resource.type=cloud_run_revision AND severity>=ERROR" \
  --limit 50 --format json

# Search for specific error pattern
gcloud logging read "resource.type=cloud_run_revision AND textPayload:\"YourError\"" \
  --limit 10
```

**Scale Service:**
```bash
# Increase max instances
gcloud run services update ${SERVICE_NAME} \
  --region ${REGION} \
  --max-instances 20

# Set minimum instances (keep warm)
gcloud run services update ${SERVICE_NAME} \
  --region ${REGION} \
  --min-instances 1
```

**Database Operations:**
```bash
# Connect to database
gcloud sql connect ${DB_INSTANCE_NAME} --user=${DB_USER}

# Create manual backup
gcloud sql backups create --instance ${DB_INSTANCE_NAME}

# Restore from backup
gcloud sql backups restore ${BACKUP_ID} --backup-instance ${DB_INSTANCE_NAME}
```

**Update Secrets:**
```bash
# Update a secret value
echo -n "new-secret-value" | gcloud secrets versions add ${SECRET_NAME} --data-file=-

# Trigger new deployment to pick up secret
gcloud run services update ${SERVICE_NAME} --region ${REGION}
```

### Troubleshooting

**Issue: High latency**
1. Check Cloud Monitoring for p95/p99 latency
2. Review Cloud Trace for slow requests
3. Check database slow query logs
4. Verify LLM API performance
5. Consider increasing Cloud Run instance size

**Issue: High error rate**
1. Check logs for error patterns
2. Verify external API availability (Anthropic, OpenAI)
3. Check database connection pool
4. Review recent deployments
5. Consider rollback if after deployment

**Issue: Database connection errors**
1. Check Cloud SQL instance status
2. Verify connection pool settings
3. Check IAM permissions
4. Review database proxy configuration
5. Check Cloud SQL maintenance windows

**Issue: Cost spike**
1. Review Cloud Billing dashboard
2. Check LLM API usage by provider
3. Review Cloud Run instance count
4. Check for API abuse/high traffic
5. Review and adjust auto-scaling limits

## Cost Analysis

### Monthly Cost Estimate Template

**Compute:**
- Serverless Containers (Cloud Run/App Runner): $10-50/month
  - Based on: requests/month, avg execution time, memory allocation
- Functions (Cloud Functions/Lambda): $5-20/month
  - Based on: invocations/month, duration, memory

**Database:**
- Managed SQL (Cloud SQL/RDS/Azure SQL): $15-200/month
  - db-f1-micro: ~$15/month (development)
  - db-n1-standard-1: ~$50/month (small production)
  - db-n1-standard-2: ~$100/month (medium production)
- NoSQL (Firestore/DynamoDB): $5-50/month
  - Based on: storage (GB), reads, writes
- Cache (Redis/Memcached): $30-100/month
  - Basic tier: ~$30-50/month
  - Standard HA: ~$100+/month

**Storage:**
- Object Storage (Cloud Storage/S3): $1-20/month
  - Based on: storage (GB), operations, egress
- Container Registry: $2-10/month

**Networking:**
- Load Balancer: $18-30/month (fixed cost)
- CDN: $5-50/month (based on traffic)
- Egress: $5-100/month (based on data transfer)

**Monitoring & Logs:**
- Logging: $5-50/month (based on volume)
- Monitoring: Often included in free tier for basic usage
- APM tools (external): $0-100/month

**Total Infrastructure Range:** $75-500/month
- Minimal MVP: ~$75-150/month
- Small Production: ~$150-300/month
- Medium Production: ~$300-500/month
- Large Scale: $500+/month

**External Service Costs (project-specific):**
- Third-party APIs: Variable
- Email/SMS services: Variable
- Payment processing: Variable

### Cost Optimization Strategies

**Immediate Wins:**
- Use auto-scaling to zero for low traffic
- Enable CDN for static assets
- Use free tiers where available
- Right-size database instances
- Implement caching to reduce compute/database load

**Medium-term:**
- Use committed use discounts (1-3 year)
- Optimize container images (smaller = faster = cheaper)
- Batch operations where possible
- Use cheaper storage classes for infrequent access

**Long-term:**
- Multi-region only when needed
- Reserved instances for predictable workloads
- Spot/preemptible instances for batch processing
- Consider serverless for variable workloads

## Handoff Notes

### For Security Advisor
- [ ] Review IAM permissions (principle of least privilege)
- [ ] Audit secret access patterns
- [ ] Review Cloud Armor configuration
- [ ] Verify SSL/TLS configuration
- [ ] Check for exposed endpoints
- [ ] Review VPC Service Controls

### For Tester
- [ ] Staging environment available at: `staging.omnichat.choreografii.com`
- [ ] Test credentials in Secret Manager: `test-user-credentials`
- [ ] Load testing endpoints:
  - Health: `/health`
  - API: `/api/chat/complete`
- [ ] Performance targets:
  - p95 latency < 3s
  - Error rate < 1%
  - Uptime > 99.9%

### For Documenter
- [ ] Infrastructure diagram needs user-friendly version
- [ ] Deployment process for developer documentation
- [ ] Monitoring dashboard access instructions
- [ ] Incident response procedures

## Approval Checklist

- [x] Infrastructure code reviewed
- [x] Cost estimate within budget ($500/month)
- [x] Monitoring and alerting configured
- [x] Backup and recovery plan in place
- [x] Security review completed
- [ ] Load testing completed (pending Tester)
- [ ] Disaster recovery tested (pending)

**APPROVAL REQUIRED:** Infrastructure Provisioning

**Approved By:** _________________
**Date:** _________________

## Next Steps
1. Security Advisor: Security audit of infrastructure
2. Tester: Load testing and performance validation
3. Documenter: User-facing documentation
4. Provision infrastructure via Terraform
5. Initial deployment to staging
6. Production deployment after validation

## References
- ADR-001: Microservices Architecture for Omni-Chat
- Terraform GCP Provider Docs: https://registry.terraform.io/providers/hashicorp/google/latest/docs
- Cloud Run Best Practices: https://cloud.google.com/run/docs/best-practices
- GitHub Actions: https://docs.github.com/en/actions

---

**Changelog:**
- 2026-02-08: Initial infrastructure design for Omni-Chat
- 2026-02-08: Added Terraform configuration
- 2026-02-08: Added CI/CD pipeline
- 2026-02-08: Added monitoring and runbook
