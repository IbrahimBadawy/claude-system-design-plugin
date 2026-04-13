---
description: Every project must have full documentation generated automatically. Documentation updates when code changes significantly.
globs: "*"
---

# Rule: Auto Documentation

## Documentation is NOT optional - it's DEFAULT

Every project gets full documentation automatically during implementation.

### During Implementation (/implement)
When writing code, ALSO generate documentation:

1. **After creating API endpoints** -> Update or create `docs/API.md` with:
   - Endpoint URL, method, auth requirements
   - Request/response schema with examples
   - Error responses
   - curl examples

2. **After creating DB schema** -> Update or create `docs/DATABASE.md` with:
   - Table definitions, columns, types, constraints
   - Indexes and their purpose
   - Relationships between tables

3. **After creating frontend components** -> Update `docs/frontend/COMPONENTS.md`

4. **After configuring Docker/deployment** -> Update `docs/DEPLOYMENT.md`

5. **After adding environment variables** -> Update `docs/README.md` env table

### Change Detection
Track documentation freshness. When you notice:
- 3+ new API endpoints without docs -> Warn user
- 2+ new DB tables without docs -> Warn user
- 5+ modified files since last docs update -> Suggest `/docs update`
- 10+ undocumented items -> Auto-run documentation update

### Documentation Quality Standards
- Every public API endpoint MUST be documented
- Every database table MUST be documented
- Every environment variable MUST be documented
- Installation steps MUST work (test them)
- README must have Quick Start that gets a new developer running in < 5 minutes

### What NOT to Document
- Internal implementation details that change frequently
- Auto-generated code (unless usage is non-obvious)
- Obvious code (self-documenting clean code doesn't need comments)
