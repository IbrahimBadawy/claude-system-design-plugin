# /checklist - System Design Review Checklist

Output the full system design review checklist for the current system.

## Usage
```
/checklist
/checklist <system_name>
```

## Output

```markdown
## System Design Checklist: [System Name]

### Requirements
- [ ] Functional requirements documented
- [ ] Non-functional requirements defined (latency, throughput, availability)
- [ ] Scale estimated (DAU, QPS, storage)
- [ ] Constraints and edge cases identified
- [ ] Out of scope items documented
- [ ] Assumptions listed

### API Design
- [ ] RESTful conventions followed
- [ ] API versioning (v1)
- [ ] Consistent error response format
- [ ] Pagination for list endpoints (cursor-based preferred)
- [ ] Rate limiting configured
- [ ] Idempotency keys for writes
- [ ] Authentication on all endpoints
- [ ] Request/response validation
- [ ] CORS configured
- [ ] API documentation (OpenAPI/Swagger)

### Data Model
- [ ] Schema designed for access patterns
- [ ] Indexes match query patterns
- [ ] Relationships defined with proper constraints
- [ ] Migration strategy planned
- [ ] Soft delete vs hard delete decided
- [ ] Audit fields (created_at, updated_at, created_by)
- [ ] Data types are appropriate and constrained
- [ ] Sensitive data encryption planned

### Architecture
- [ ] No single points of failure
- [ ] Stateless services where possible
- [ ] Caching layer designed with invalidation strategy
- [ ] Async processing for heavy tasks
- [ ] CDN for static content
- [ ] Load balancer configured
- [ ] Service boundaries clearly defined
- [ ] Data flow documented

### Scalability
- [ ] Horizontal scaling plan
- [ ] Database scaling strategy (replicas, sharding)
- [ ] Read replicas for read-heavy workloads
- [ ] Connection pooling
- [ ] 10x growth plan documented
- [ ] 100x growth plan identified
- [ ] Bottleneck identified and addressed

### Reliability
- [ ] Retry logic with exponential backoff + jitter
- [ ] Circuit breaker on external calls
- [ ] Timeout on ALL external calls
- [ ] Dead letter queue for failed messages
- [ ] Graceful degradation strategy
- [ ] Health check endpoints (liveness + readiness)
- [ ] Failover strategy for critical components

### Security
- [ ] HTTPS everywhere
- [ ] Authentication (JWT/OAuth)
- [ ] Authorization (RBAC/ABAC)
- [ ] Input validation and sanitization
- [ ] SQL injection protection (parameterized queries)
- [ ] XSS protection
- [ ] Secrets in vault (not in code)
- [ ] Audit logging
- [ ] CORS configured restrictively
- [ ] Encryption at rest for sensitive data

### Operations
- [ ] Monitoring (metrics, dashboards)
- [ ] Structured logging with correlation IDs
- [ ] Distributed tracing
- [ ] Alerting rules defined (P1-P4)
- [ ] SLI/SLO defined
- [ ] Deployment strategy (blue-green/canary)
- [ ] Rollback plan
- [ ] Backup and disaster recovery
- [ ] Runbook for common incidents

### Code Quality
- [ ] Clean Architecture / proper layering
- [ ] SOLID principles applied
- [ ] Error handling comprehensive
- [ ] No code smells (check Fowler's catalog)
- [ ] Tests: unit + integration + e2e
- [ ] No hardcoded values (use configuration)
- [ ] Logging at appropriate levels

### Domain Modeling (DDD)
- [ ] Bounded contexts identified
- [ ] Aggregates and aggregate roots defined
- [ ] Domain events mapped
- [ ] Context map drawn (relationships between contexts)
- [ ] Ubiquitous language documented per context

### Data Privacy & Compliance
- [ ] Personal data inventory complete
- [ ] User consent management designed
- [ ] Right to erasure (deletion cascade) implemented
- [ ] Data export (portability) endpoint
- [ ] Data retention policies defined
- [ ] Encryption at rest for sensitive fields
- [ ] Audit logging for personal data access
- [ ] GDPR/CCPA compliance verified
- [ ] Data Processing Agreements with vendors

### Multi-Tenancy (if SaaS)
- [ ] Tenancy model chosen (silo/pool/bridge) with justification
- [ ] Tenant resolution strategy (subdomain/header/JWT)
- [ ] Data isolation verified (RLS or schema separation)
- [ ] Per-tenant configuration and feature flags
- [ ] Resource quotas per tenant
- [ ] Tenant onboarding/offboarding automated

### Performance
- [ ] Performance budgets defined (API p99, LCP, bundle size)
- [ ] N+1 queries eliminated
- [ ] Database queries optimized (EXPLAIN ANALYZE)
- [ ] Caching implemented for hot paths
- [ ] Frontend: code splitting, image optimization, lazy loading
- [ ] Load testing performed (k6/Gatling)

### Accessibility (WCAG 2.2 AA)
- [ ] Keyboard navigation works for all interactions
- [ ] Screen reader compatible (semantic HTML, ARIA)
- [ ] Color contrast meets 4.5:1 minimum
- [ ] Focus indicators visible
- [ ] RTL support (if serving Arabic/Hebrew users)
- [ ] axe-core integrated in CI

### CI/CD & DevOps
- [ ] CI pipeline: lint -> test -> build -> scan -> deploy
- [ ] Deployment strategy chosen (blue-green/canary)
- [ ] Automated rollback on error spike
- [ ] Infrastructure as Code (Docker, Terraform)
- [ ] Secret management (not in code)
- [ ] Environment promotion (dev -> staging -> prod)

### Feature Flags
- [ ] Feature flag system in place for safe rollouts
- [ ] Kill switches for new features
- [ ] Stale flags cleaned up regularly

### Technical Debt
- [ ] Debt backlog maintained
- [ ] 20% sprint time allocated for debt reduction
- [ ] Code quality gates in CI (coverage, complexity)

### Trade-offs Documented
- [ ] Consistency vs Availability
- [ ] Latency vs Throughput
- [ ] Cost vs Performance
- [ ] Database choice justified
- [ ] Caching strategy justified
- [ ] Sync vs Async decisions documented
```
