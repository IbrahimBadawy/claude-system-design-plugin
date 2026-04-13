# /api - REST API Design

Design RESTful API endpoints for a resource with full specifications.

## Usage
```
/api <resource_name>
```

## Behavior

### Output Specification

```markdown
# API: [Resource Name]
**Version**: v1
**Base URL**: /api/v1

## Authentication
- Type: Bearer JWT
- Header: `Authorization: Bearer <token>`

## Rate Limiting
- Authenticated: 1000 req/min
- Unauthenticated: 100 req/min
- Headers: X-RateLimit-Limit, X-RateLimit-Remaining, X-RateLimit-Reset

## Pagination
- Cursor-based (recommended): `?cursor=<encoded>&limit=20`
- Offset-based (simple): `?page=1&limit=20`

## Endpoints

### Create [Resource]
POST /api/v1/[resources]
Auth: Required
Idempotency-Key: Required

Request:
{
  "field1": "value",
  "field2": "value"
}

Response 201:
{
  "id": "uuid",
  "field1": "value",
  "created_at": "2026-01-01T00:00:00Z"
}

### Get [Resource]
GET /api/v1/[resources]/:id
Auth: Required

Response 200:
{
  "id": "uuid",
  "field1": "value",
  ...
}

### List [Resources]
GET /api/v1/[resources]?cursor=xxx&limit=20&sort=created_at&order=desc&filter[status]=active
Auth: Required

Response 200:
{
  "data": [...],
  "pagination": {
    "next_cursor": "encoded-cursor",
    "has_more": true,
    "total": 150
  }
}

### Update [Resource]
PUT /api/v1/[resources]/:id (full update)
PATCH /api/v1/[resources]/:id (partial update)
Auth: Required

### Delete [Resource]
DELETE /api/v1/[resources]/:id
Auth: Required
Response 204 No Content

## Error Format
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human readable message",
    "details": [{"field": "email", "issue": "Invalid format"}],
    "request_id": "req_uuid"
  }
}

## Status Codes
| Code | Meaning |
|------|---------|
| 200 | Success |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Validation Error |
| 429 | Too Many Requests |
| 500 | Internal Server Error |
```

### Design Principles
- RESTful resource naming (plural nouns)
- Version from day 1 (/api/v1/)
- Cursor pagination for large datasets (better than offset)
- Idempotency keys for all write operations
- Consistent error format across all endpoints
- HATEOAS links where beneficial
- ETag/If-None-Match for caching
- Request validation with clear error messages

## Examples
```
/api users
/api payments
/api courses
```
