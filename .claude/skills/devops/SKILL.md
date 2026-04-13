---
name: devops
description: DevOps and infrastructure skill - triggered when setting up CI/CD pipelines, Docker, Kubernetes, deployment, infrastructure as code, or cloud services. Generates production-ready configs and follows security best practices.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# DevOps & Infrastructure Skill

You are setting up infrastructure, CI/CD, or deployment. Generate production-ready configs.

## When This Skill Activates
- User runs `/cicd`, `/implement --infra`
- User asks about Docker, Kubernetes, deployment, CI/CD
- During implementation when creating Dockerfiles, docker-compose, CI configs
- When discussing cloud architecture (AWS, GCP, Azure)

## Docker Best Practices

### Multi-Stage Build (Node.js)
```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:20-alpine
WORKDIR /app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/main.js"]
```

### Docker Security
- Use specific version tags (not `latest`)
- Run as non-root user
- Use `.dockerignore` (exclude node_modules, .env, .git)
- Scan images: `docker scout cves`
- Minimal base images (alpine, distroless)

### Docker Compose (Development)
```yaml
services:
  app:
    build: .
    ports: ["3000:3000"]
    env_file: .env
    depends_on:
      db: { condition: service_healthy }
      redis: { condition: service_healthy }

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes: ["pgdata:/var/lib/postgresql/data"]
    ports: ["5432:5432"]
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s

  redis:
    image: redis:7-alpine
    ports: ["6379:6379"]
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]

volumes:
  pgdata:
```

## CI/CD Pipeline (GitHub Actions)

### Standard Pipeline
```yaml
name: CI/CD
on:
  push: { branches: [main] }
  pull_request: { branches: [main] }

jobs:
  lint-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: 20, cache: npm }
      - run: npm ci
      - run: npm run lint
      - run: npm test -- --coverage
      - run: npm run build

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm audit --audit-level=high
      - uses: github/codeql-action/analyze@v3

  deploy-staging:
    needs: [lint-test, security]
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - # Deploy to staging
      - # Run E2E tests against staging

  deploy-production:
    needs: deploy-staging
    if: github.ref == 'refs/heads/main'
    environment: production
    runs-on: ubuntu-latest
    steps:
      - # Deploy to production
      - # Smoke tests
      - # Monitor for 5 minutes
```

## Deployment Strategies
| Strategy | How | When |
|----------|-----|------|
| **Blue-Green** | Two identical environments, switch traffic | Zero downtime, instant rollback |
| **Canary** | Roll out to 1% -> 5% -> 25% -> 100% | Gradual, monitor at each step |
| **Rolling** | Replace instances one by one | Default for Kubernetes |
| **Feature Flags** | Deploy dark, enable gradually | Most flexible |

## Cloud Patterns

### AWS Typical Stack
```
Route 53 (DNS) -> CloudFront (CDN) -> ALB (Load Balancer)
  -> ECS Fargate (containers) or Lambda (serverless)
  -> RDS (PostgreSQL) + ElastiCache (Redis)
  -> S3 (storage) + SQS (queue) + SNS (notifications)
  -> CloudWatch (monitoring)
```

### Infrastructure as Code
- **Terraform**: Multi-cloud, declarative, most popular
- **Pulumi**: Use real programming languages (TypeScript, Python)
- **AWS CDK**: AWS-specific, TypeScript/Python

## Security
- Secrets: Never in code. Use Vault, AWS Secrets Manager, or GitHub Secrets
- HTTPS everywhere (Let's Encrypt for free certs)
- Security groups: minimal ports open
- Container scanning in CI
- Dependency scanning (Snyk, npm audit)

## ALWAYS Research Before Config
WebSearch the latest syntax for:
- Docker Compose v2 format
- GitHub Actions runner versions and action versions
- Cloud provider CLI commands
- Terraform provider versions
