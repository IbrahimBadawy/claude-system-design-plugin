# /tenancy - Multi-Tenancy Architecture Design

Design the multi-tenancy strategy for SaaS applications.

## Usage
```
/tenancy <system>                   # Full multi-tenancy design
/tenancy compare                    # Compare isolation models
```

## Behavior

### Phase 1: Tenancy Model Selection

Ask the user about:
1. How many tenants? (10, 100, 1000+)
2. Size variation? (all similar vs mix of small/large)
3. Compliance requirements? (data isolation, residency)
4. Customization needs? (per-tenant features, branding)
5. Budget constraints?

### Phase 2: Isolation Models

| Model | Database | Pros | Cons | Best For |
|-------|----------|------|------|----------|
| **Silo** | Tenant-per-DB | Max isolation, easy compliance, independent scaling | High cost, complex management | Enterprise, regulated |
| **Pool** | Shared DB + tenant_id | Low cost, simple operations | Noisy neighbor, complex queries | SMB, high tenant count |
| **Bridge** | Shared for small + dedicated for large | Flexible, cost-optimized | Complex routing logic | Mixed tenant sizes |

### Phase 3: Architecture Design

**Tenant Resolution (how to identify the tenant):**
```
Request -> Extract tenant from:
  1. Subdomain: {tenant}.app.com
  2. Custom domain: tenant-domain.com (DNS CNAME)
  3. Header: X-Tenant-ID
  4. JWT claim: tenant_id in token payload
  5. URL path: /api/v1/{tenant}/resource (less common)
```

**Database Strategy (Pool Model):**
```sql
-- Row-Level Security (PostgreSQL)
ALTER TABLE courses ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON courses
    USING (tenant_id = current_setting('app.current_tenant')::UUID);

-- Set tenant context per request (middleware)
SET app.current_tenant = '<tenant-uuid>';
```

**Per-Tenant Configuration:**
```json
{
  "tenant_id": "uuid",
  "name": "University of Cairo",
  "subdomain": "cairo",
  "features": {
    "max_students": 50000,
    "modules_enabled": ["courses", "grades", "attendance"],
    "custom_branding": true,
    "api_rate_limit": 1000
  },
  "settings": {
    "timezone": "Africa/Cairo",
    "locale": "ar-EG",
    "academic_year_start": "September"
  }
}
```

**Key Architectural Components:**
- **Tenant Router**: Middleware that resolves tenant from request and sets context
- **Tenant Config Store**: Redis-cached tenant settings
- **Feature Gate**: Per-tenant feature flags
- **Resource Quotas**: Prevent one tenant from consuming all resources
- **Data Export/Import**: Per-tenant data migration tools
- **Tenant Onboarding**: Automated setup (create DB/schema, seed data, configure)
- **Tenant Offboarding**: Data export, cleanup, archival

### Phase 4: Cross-Cutting Concerns

**Auth**: JWT includes tenant_id claim. Users belong to exactly one tenant.
**Caching**: Prefix all cache keys with tenant_id: `tenant:{id}:users:{userId}`
**Queues**: Tenant-aware job processing with fair scheduling (no starvation)
**Search**: Tenant-filtered indexes (Elasticsearch alias per tenant or filter)
**File Storage**: Prefix S3 paths: `s3://bucket/{tenant_id}/uploads/`
**Logging**: Include tenant_id in every log entry for debugging
**Monitoring**: Per-tenant metrics (request rate, error rate, storage usage)
**Billing**: Track resource usage per tenant for billing

### Output
```markdown
# Multi-Tenancy Design: [System Name]

## Tenancy Model
**Selected**: [Silo/Pool/Bridge]
**Justification**: [why]

## Tenant Resolution
[Strategy: subdomain, header, JWT, etc.]

## Database Isolation
[RLS, schema-per-tenant, DB-per-tenant, etc.]

## Tenant Configuration
[Configuration model and storage]

## Feature Management
[Per-tenant feature flags]

## Resource Quotas
| Resource | Default Limit | Enterprise Limit |
|----------|-------------|-----------------|
| API calls/min | 100 | 1000 |
| Storage | 10 GB | 100 GB |
| Users | 1000 | 50000 |

## Onboarding Flow
[Steps to add a new tenant]

## Migration Between Models
[How to move a tenant from pool to silo if needed]
```

## Examples
```
/tenancy university-saas
/tenancy compare
```
