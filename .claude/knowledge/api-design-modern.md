# Modern API Design (2024-2026)

> Synthesized from Zalando API Guidelines, Microsoft REST Guidelines, Google API
> Design Guide, Stripe patterns, OpenAPI 3.1, and RFC 9457 (Problem Details).

---

## REST Baseline (plugin default)

Synthesis of 3 authoritative guides (they agree on principles):

- Plural nouns, kebab-case URLs: `/api/v1/user-profiles/{id}`
- JSON: **camelCase** (JS/TS interop); snake_case only if backend language strongly prefers it (Python, Ruby)
- HTTP verbs strictly:
  - `GET` — safe + idempotent
  - `POST` — create
  - `PUT` — full replace, idempotent
  - `PATCH` — partial update
  - `DELETE` — idempotent
- Status codes:
  - `200 OK` — success with body
  - `201 Created` + `Location` header — new resource
  - `204 No Content` — success, no body
  - `400 Bad Request` — malformed request
  - `401 Unauthorized` — no/invalid auth
  - `403 Forbidden` — authenticated but no permission
  - `404 Not Found`
  - `409 Conflict` — state conflict
  - `422 Unprocessable Entity` — validation errors (NOT `400`)
  - `429 Too Many Requests`
  - `500 Internal Server Error`

---

## OpenAPI 3.1 (stable in 2024-2026 tooling)

Major wins over 3.0:
- Full **JSON Schema 2020-12** alignment
- `webhooks` as first-class top-level construct
- Nullable via `type: [string, "null"]`
- SPDX license identifiers

### Best Practices

1. **Contract-first** — write spec BEFORE code. Generate types/clients/servers from it.
   - TS: `openapi-typescript`, `orval`, `kubb`
   - Polyglot: `openapi-generator`
2. **Single source of truth** — spec in repo, CI fails if implementation drifts
   - Use `schemathesis` or `dredd`
3. **$ref aggressively** for shared schemas (`components/schemas/Problem.yaml`)
4. **Mock server from spec** in dev (Prism, Mockoon)

---

## Idempotency (Stripe Pattern)

```http
POST /api/v1/payments
Idempotency-Key: 550e8400-e29b-41d4-a716-446655440000
Content-Type: application/json

{"amount": 5000, "currency": "USD"}
```

Rules:
- Server stores `(key, request_hash, response)` for 24h min
- Same key + same body → return cached response (200/201 again)
- Same key + different body → `422 Unprocessable Entity` (key reuse mismatch)
- Required on all non-idempotent methods: POST and state-changing PATCH
- Storage: Redis with TTL, or dedicated table with cleanup job

---

## Pagination, Filtering, Sorting

**Cursor pagination is the default.** Offset breaks at scale.

```http
GET /api/v1/orders?limit=50&cursor=eyJpZCI6MTIzfQ==
```

Response:
```json
{
  "data": [...],
  "pagination": {
    "nextCursor": "eyJpZCI6MTczfQ==",
    "hasMore": true
  }
}
```

Rules:
- **Cursor** = opaque base64-encoded `{id, sortKey}` — never expose internal ordering
- **limit** — default 20, max 100
- **Filtering:** `?status=active&createdAfter=2026-01-01` (structured query params, not `?filter=...`)
- **Sorting:** `?sort=-createdAt,name` (`-` prefix for desc)
- **Field selection:** `?fields=id,name,email` (GraphQL-lite; saves bandwidth)

---

## Error Format — RFC 9457 (was 7807) Problem Details

```http
HTTP/1.1 422 Unprocessable Entity
Content-Type: application/problem+json

{
  "type": "https://api.example.com/errors/validation-failed",
  "title": "Validation failed",
  "status": 422,
  "detail": "email must be a valid email address",
  "instance": "/api/v1/users/create",
  "traceId": "abc123",
  "errors": [
    {"field": "email", "code": "INVALID_FORMAT"},
    {"field": "password", "code": "TOO_SHORT", "minLength": 8}
  ]
}
```

RFC 9457 (2023) superseded RFC 7807 — same shape, minor clarifications.

---

## Versioning Strategies

| Strategy | When | Format |
|----------|------|--------|
| **URL path** (default) | Most discoverable, cacheable, CDN-friendly | `/v1/users`, `/v2/users` |
| **Header** | Internal APIs where clients are controlled | `Accept-Version: 2` |
| **Content-Type** | GitHub/Stripe style; powerful but tooling-unfriendly | `application/vnd.api.v2+json` |

**Rule:** URL path by default. Never bump major without a migration plan and 6+ months of parallel support.

---

## REST vs GraphQL vs tRPC vs gRPC

| Criterion | REST | GraphQL | tRPC | gRPC |
|-----------|------|---------|------|------|
| **Public API** | Best | OK | No | No |
| **Internal microservices** | OK | OK | No | Best |
| **Monorepo TS full-stack** | OK | OK | Best | OK |
| **Mobile/bandwidth** | OK | Best | OK | Best |
| **Tooling maturity** | Mature | Mature | Growing | Mature |
| **Learning curve** | Low | Medium | Low (TS only) | High |
| **Caching** | HTTP-native | Manual/Apollo | Manual | Manual |
| **Streaming** | SSE/WS | Subscriptions | Subscriptions | Native bidi |
| **Schema** | OpenAPI | Schema-native | Inferred TS | Protobuf |

### Decision Rules (plugin defaults)

- Public / third-party API → **REST + OpenAPI**
- TS monorepo, same team owns client+server → **tRPC**
- Polyglot microservices, low-latency internal → **gRPC**
- Mobile / complex nested UI / multiple clients → **GraphQL**

---

## Webhooks

- **Signing:** HMAC-SHA256 over `timestamp.body`, signature in `X-Signature-256` header, reject if timestamp > 5 min old
- **Retries:** Exponential backoff — 1m, 5m, 30m, 2h, 5h, 10h, 24h (7 attempts over ~24h), then dead-letter
- **Idempotency:** Include `event_id` in payload so consumers can dedupe
- **Delivery status endpoint:** `/webhooks/deliveries` for customer debugging
- **Test endpoint:** let users send test events from dashboard

```http
POST /webhooks/yours
X-Signature-256: sha256=abcd1234...
X-Timestamp: 1712345678

{"event_id": "evt_xyz", "type": "order.created", "data": {...}}
```

---

## API Security

- **OAuth 2.1** (draft but stable) — consolidates best practices
  - PKCE **required** for all flows
  - No implicit flow
  - No password grant
  - Refresh token rotation
- **OIDC** for identity on top of OAuth 2.1 (`openid` scope → ID token)
- **JWT rules:**
  - `RS256` / `EdDSA` (not `HS256` across services)
  - `exp` ≤ 15 min for access tokens
  - Rotate signing keys (JWKS)
  - **Never** store in localStorage (httpOnly cookie for web, secure storage for mobile)
- **API keys** (server-to-server only):
  - Prefix with identifier: `sk_live_...`
  - Hash at rest (SHA-256)
  - Support rotation with overlap window
- **mTLS** for high-value internal APIs

---

## Rate Limiting

| Pattern | Use Case |
|---------|----------|
| **Token bucket** (default) | Bursty traffic allowed |
| **Sliding window log** | Strict enforcement, higher memory |
| **Fixed window** | Simple, but boundary bursts possible |

Response headers (IETF draft `ratelimit-headers`):
```http
RateLimit-Limit: 100
RateLimit-Remaining: 97
RateLimit-Reset: 60
Retry-After: 60
```

Tiers: per-tenant + per-user + per-IP. Return `429` with `Retry-After` on breach.

---

## API Checklist — top 10

- [ ] OpenAPI 3.1 spec in repo, CI-validated against implementation
- [ ] URL-path versioning `/api/v1/`
- [ ] RFC 9457 problem+json for all errors
- [ ] Cursor pagination, opaque base64 cursors
- [ ] `Idempotency-Key` on all POSTs with 24h replay store
- [ ] Rate limiting with IETF draft headers
- [ ] OAuth 2.1 + PKCE; JWT RS256 with short TTL and JWKS rotation
- [ ] Signed webhooks (HMAC-SHA256 + timestamp + event_id)
- [ ] HTTPS only, HSTS, strict CORS
- [ ] OpenAPI mock server available in dev

Sources:
- https://opensource.zalando.com/restful-api-guidelines/
- https://learn.microsoft.com/en-us/azure/architecture/best-practices/api-design
- https://cloud.google.com/apis/design
- https://spec.openapis.org/oas/v3.1.0
- https://datatracker.ietf.org/doc/rfc9457/
- https://stripe.com/docs/api/idempotent_requests
- https://datatracker.ietf.org/doc/draft-ietf-oauth-v2-1/
