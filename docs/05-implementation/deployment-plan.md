# Deployment Plan – Kế hoạch Triển khai Hệ thống

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  

---

## 1. Environment Strategy

### 1.1 Ba môi trường

| Môi trường | Mục đích | Cập nhật | Data |
|---|---|---|---|
| **Development (dev)** | Phát triển và test cá nhân | Mỗi commit | Dữ liệu giả (seeds) |
| **Staging** | Integration test, UAT, demo | Mỗi PR merge vào develop | Copy anonymized từ production |
| **Production** | Vận hành thực tế | Manual deploy với approval | Dữ liệu thực |

### 1.2 Infrastructure per Environment

| Resource | Development | Staging | Production |
|---|---|---|---|
| Application | Docker Compose local | 1 instance (2 CPU, 4GB) | 2+ instances (4 CPU, 8GB) |
| PostgreSQL | Local Docker | 1 instance (2 CPU, 4GB) | Primary + 1 Replica |
| Redis | Local Docker | 1 instance | 3-node cluster |
| CDN | No | Cloudflare (staging zone) | Cloudflare (production) |
| Load Balancer | No | Nginx | Cloud LB |

---

## 2. Docker Configuration

### 2.1 Dockerfile (Multi-stage)

```dockerfile
# Backend Dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production=false
COPY . .
RUN npm run build

FROM node:20-alpine AS production
WORKDIR /app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package*.json ./
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/main.js"]
```

### 2.2 Docker Compose (Development)

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://postgres:postgres@postgres:5432/bandienthoai
      - REDIS_URL=redis://redis:6379
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    volumes:
      - ./src:/app/src  # Hot reload in development

  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: bandienthoai
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    ports:
      - "6379:6379"

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    ports:
      - "3001:3001"
    environment:
      - NEXT_PUBLIC_API_URL=http://app:3000/api/v1
    depends_on:
      - app
    volumes:
      - ./frontend/src:/app/src

volumes:
  postgres_data:
  redis_data:
```

---

## 3. Kubernetes Configuration (Production)

### 3.1 Application Deployment

```yaml
# k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bandienthoai-api
  namespace: production
spec:
  replicas: 2
  selector:
    matchLabels:
      app: bandienthoai-api
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0    # Zero-downtime
  template:
    metadata:
      labels:
        app: bandienthoai-api
    spec:
      containers:
      - name: api
        image: ghcr.io/org/bandienthoai-api:VERSION
        ports:
        - containerPort: 3000
        env:
        - name: NODE_ENV
          value: "production"
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: database-url
        - name: REDIS_URL
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: redis-url
        resources:
          requests:
            cpu: "500m"
            memory: "512Mi"
          limits:
            cpu: "2000m"
            memory: "2Gi"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 10
          periodSeconds: 5
      terminationGracePeriodSeconds: 30
```

### 3.2 Horizontal Pod Autoscaler

```yaml
# k8s/hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: bandienthoai-api-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: bandienthoai-api
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

---

## 4. Infrastructure as Code (IaC)

### 4.1 Terraform Structure

```
terraform/
├── main.tf
├── variables.tf
├── outputs.tf
├── modules/
│   ├── networking/      VPC, subnets, security groups
│   ├── database/        RDS PostgreSQL
│   ├── cache/           ElastiCache Redis
│   ├── compute/         EKS cluster
│   ├── cdn/             CloudFront distribution
│   └── storage/         S3 buckets
└── environments/
    ├── staging/
    └── production/
```

### 4.2 Key Terraform Resources

```hcl
# Production PostgreSQL (AWS RDS)
resource "aws_db_instance" "postgres" {
  identifier           = "bandienthoai-prod"
  engine               = "postgres"
  engine_version       = "15.4"
  instance_class       = "db.t3.medium"
  allocated_storage    = 100
  storage_encrypted    = true
  
  backup_retention_period = 30
  backup_window          = "02:00-03:00"
  maintenance_window     = "sun:03:00-sun:04:00"
  
  multi_az               = true   # High availability
  deletion_protection    = true
  
  vpc_security_group_ids = [aws_security_group.db.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name
}
```

---

## 5. Deployment Procedures

### 5.1 Standard Deployment (Staging)

```bash
#!/bin/bash
# deploy-staging.sh

set -e

IMAGE_TAG=$1
NAMESPACE="staging"

echo "Deploying ${IMAGE_TAG} to staging..."

# Update deployment image
kubectl set image deployment/bandienthoai-api \
  api=ghcr.io/org/bandienthoai-api:${IMAGE_TAG} \
  -n ${NAMESPACE}

# Wait for rollout
kubectl rollout status deployment/bandienthoai-api -n ${NAMESPACE} --timeout=300s

# Run smoke tests
./scripts/smoke-tests.sh staging

echo "Staging deployment complete!"
```

### 5.2 Production Deployment (Blue-Green)

```mermaid
flowchart LR
  LB[Load Balancer] --> BLUE[Blue\nv1.0.0\nProduction]
  BLUE -.-> GREEN[Green\nv1.1.0\nDeploy new version]
  GREEN --> SMOKE[Run Smoke Tests]
  SMOKE -->|Pass| SWITCH[Switch LB to Green]
  SWITCH --> MONITOR[Monitor 15 phút]
  MONITOR -->|OK| DECOMMISSION[Decommission Blue]
  MONITOR -->|Issues| ROLLBACK[Rollback to Blue]
  SMOKE -->|Fail| ROLLBACK
```

**Deployment Steps:**

```bash
#!/bin/bash
# deploy-production.sh

VERSION=$1
OLD_VERSION=$(kubectl get deployment bandienthoai-api -n production -o jsonpath='{.spec.template.spec.containers[0].image}' | cut -d: -f2)

echo "=== Production Deployment ==="
echo "New version: ${VERSION}"
echo "Current version: ${OLD_VERSION}"
echo ""
echo "⚠️  This will deploy to PRODUCTION. Continue? (yes/no)"
read CONFIRM
if [ "$CONFIRM" != "yes" ]; then
  echo "Deployment cancelled"
  exit 0
fi

# 1. Apply DB migrations first (backward compatible)
echo "Applying database migrations..."
kubectl run db-migrate --image=ghcr.io/org/bandienthoai-api:${VERSION} \
  --restart=Never -n production \
  --env="DATABASE_URL=$(kubectl get secret app-secrets -n production -o jsonpath='{.data.database-url}' | base64 -d)" \
  -- npm run db:migrate
kubectl wait --for=condition=complete job/db-migrate -n production --timeout=120s

# 2. Deploy new version (rolling update)
echo "Deploying new version..."
kubectl set image deployment/bandienthoai-api api=ghcr.io/org/bandienthoai-api:${VERSION} -n production
kubectl rollout status deployment/bandienthoai-api -n production --timeout=300s

# 3. Run smoke tests
echo "Running smoke tests..."
./scripts/smoke-tests.sh production
SMOKE_EXIT_CODE=$?

if [ $SMOKE_EXIT_CODE -ne 0 ]; then
  echo "❌ Smoke tests FAILED! Rolling back..."
  kubectl rollout undo deployment/bandienthoai-api -n production
  kubectl rollout status deployment/bandienthoai-api -n production
  exit 1
fi

echo "✅ Deployment successful!"
echo "Monitor at: https://grafana.bandienthoai.vn/d/overview"
```

### 5.3 Database Migration Safety

```bash
# migrations/verify-migration.sh
# Chạy trước khi apply migration lên production

# 1. Backup trước khi migrate
echo "Creating backup before migration..."
pg_dump $PROD_DATABASE_URL > backup/pre-migration-$(date +%Y%m%d%H%M%S).sql

# 2. Verify migration is backward compatible
# (cột mới phải nullable hoặc có DEFAULT)
npx prisma migrate diff \
  --from-url $PROD_DATABASE_URL \
  --to-schema-datamodel prisma/schema.prisma

# 3. Apply to staging first and verify
npx prisma migrate deploy --schema prisma/schema.prisma
```

---

## 6. Rollback Procedures

### 6.1 Application Rollback

```bash
# Rollback application deployment
kubectl rollout undo deployment/bandienthoai-api -n production

# Rollback to specific version
kubectl rollout undo deployment/bandienthoai-api -n production --to-revision=3

# Check rollback status
kubectl rollout status deployment/bandienthoai-api -n production
```

### 6.2 Database Rollback

Database migrations là forward-only. Khi có vấn đề:

1. **Phương án 1:** Restore từ backup (nếu < 1 giờ sau migration)
   ```bash
   psql $PROD_DATABASE_URL < backup/pre-migration-TIMESTAMP.sql
   ```

2. **Phương án 2:** Compensating migration (thêm migration để undo thay đổi)
   ```sql
   -- Ví dụ: nếu migration thêm cột NOT NULL gây lỗi
   ALTER TABLE order ALTER COLUMN new_column DROP NOT NULL;
   ALTER TABLE order ALTER COLUMN new_column SET DEFAULT NULL;
   ```

### 6.3 Rollback Decision Matrix

| Tình huống | Hành động | Thời gian |
|---|---|---|
| Smoke test fail sau deploy | Auto rollback application | 5 phút |
| Error rate > 5% sau deploy | Manual rollback application | 15 phút |
| Data corruption phát hiện | Restore database từ backup | 30–60 phút |
| Payment integration broken | Rollback + disable payment method | 15 phút |

---

## 7. Smoke Test Suite

```bash
#!/bin/bash
# scripts/smoke-tests.sh
ENV=$1
BASE_URL="https://api.bandienthoai.vn"
if [ "$ENV" = "staging" ]; then
  BASE_URL="https://api-staging.bandienthoai.vn"
fi

FAILED=0

check() {
  local desc=$1
  local url=$2
  local expected=$3
  
  status=$(curl -s -o /dev/null -w "%{http_code}" "$url")
  if [ "$status" = "$expected" ]; then
    echo "✅ $desc"
  else
    echo "❌ $desc (expected $expected, got $status)"
    FAILED=$((FAILED+1))
  fi
}

echo "Running smoke tests on $BASE_URL..."
check "Health check" "${BASE_URL}/health" "200"
check "API products" "${BASE_URL}/api/v1/products" "200"
check "API categories" "${BASE_URL}/api/v1/categories" "200"
check "Admin protected (should 401)" "${BASE_URL}/api/admin/v1/orders" "401"
check "Webhook endpoint exists" "${BASE_URL}/webhooks/payment/vnpay" "400"  # 400 = endpoint exists but invalid payload

if [ $FAILED -gt 0 ]; then
  echo "❌ $FAILED smoke test(s) failed!"
  exit 1
fi
echo "✅ All smoke tests passed!"
```
