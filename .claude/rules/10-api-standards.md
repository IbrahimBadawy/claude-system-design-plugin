---
description: API design standards and conventions
globs: "*"
---

# Rule: API Design Standards

1. **Version from day 1**: `/api/v1/resource`
2. **RESTful conventions**: Plural nouns for resources, proper HTTP methods
3. **Consistent error format**: Same error schema across all endpoints
4. **Pagination**: Cursor-based for large datasets (better than offset)
5. **Rate limiting**: On all public endpoints with proper headers
6. **Idempotency keys**: Required for all write operations (POST, PUT)
7. **Input validation**: Validate at the API boundary, return 422 with details
8. **Authentication**: Required on every endpoint (explicit public exceptions only)
9. **HATEOAS/Links**: Include navigation links where beneficial
10. **Documentation**: OpenAPI/Swagger spec for every API

## HTTP Methods
- GET: Read (cacheable, idempotent)
- POST: Create (not idempotent without idempotency key)
- PUT: Full update (idempotent)
- PATCH: Partial update
- DELETE: Remove (idempotent)

## Status Codes
- 2xx: Success (200, 201, 204)
- 4xx: Client error (400, 401, 403, 404, 409, 422, 429)
- 5xx: Server error (500, 502, 503)
