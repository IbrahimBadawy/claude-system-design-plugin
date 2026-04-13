---
description: Security must be built into every layer from the start
globs: "*"
---

# Rule: Security by Default

Every system must have:

1. **Authentication** on every endpoint (JWT/OAuth2)
2. **Authorization** with principle of least privilege (RBAC/ABAC)
3. **Input validation** at every boundary (never trust user input)
4. **Parameterized queries** (never concatenate SQL strings)
5. **Output encoding** (prevent XSS)
6. **Encryption** in transit (TLS) and at rest (AES-256 for sensitive data)
7. **Secrets management** (never in code, use vault/KMS)
8. **Audit logging** for sensitive operations
9. **Rate limiting** on all public endpoints
10. **CORS** configured restrictively (no wildcards in production)

Never log sensitive data (passwords, tokens, PII, credit cards).
Never store passwords in plain text - use bcrypt/argon2 with salt.
