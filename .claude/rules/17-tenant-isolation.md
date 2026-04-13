---
description: Every query must be tenant-scoped. No cross-tenant data leakage. Mandatory for multi-tenant SaaS systems.
globs: "*.{ts,js,py,java,go,rs,tsx,jsx,sql}"
---

# Rule: Tenant Isolation

For ANY multi-tenant system (SaaS, platform serving multiple clients):

1. **Every database query MUST be tenant-scoped** - no query without WHERE tenant_id = X
2. **No cross-tenant data leakage** - user from tenant A must NEVER see tenant B's data
3. **Tenant-specific configuration** over hardcoded values - features, limits, branding
4. **Test with multiple tenants** - never test with just one tenant
5. **Cache keys include tenant_id** - `tenant:{id}:users:{userId}` not `users:{userId}`
6. **File storage paths include tenant_id** - `s3://bucket/{tenant_id}/uploads/`
7. **Logs include tenant_id** - for debugging and audit
8. **Rate limits per tenant** - prevent noisy neighbor problem

## Implementation Patterns
- PostgreSQL Row-Level Security (RLS): `CREATE POLICY tenant_isolation USING (tenant_id = current_setting('app.tenant'))`
- Middleware sets tenant context on every request (from JWT, subdomain, or header)
- Repository layer automatically adds tenant filter

## Testing Requirements
- Create test suite with 2+ tenants
- Verify tenant A cannot access tenant B's data via API
- Verify tenant A cannot see tenant B's data in search results
- Test tenant isolation under concurrent requests
