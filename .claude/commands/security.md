# /security - Security Review

Perform a security review and suggest hardening measures.

## Usage
```
/security
/security <specific-component>
```

## Behavior

### Security Review Checklist (OWASP + System Design)

#### 1. Authentication
- [ ] Strong password policy (min length, complexity, bcrypt/argon2 hashing with salt)
- [ ] Multi-factor authentication (MFA/2FA)
- [ ] JWT with short expiry + refresh token rotation
- [ ] SSO integration where applicable
- [ ] Account lockout after failed attempts
- [ ] Session management (secure, httpOnly, sameSite cookies)
- [ ] OAuth 2.0 / OpenID Connect for third-party auth

#### 2. Authorization
- [ ] Role-Based Access Control (RBAC) or Attribute-Based (ABAC)
- [ ] Principle of least privilege
- [ ] Resource-level authorization (not just endpoint-level)
- [ ] API key management and rotation
- [ ] Service-to-service authentication (mTLS)

#### 3. Input Validation (OWASP A03)
- [ ] Validate ALL user input on server side
- [ ] Parameterized queries (prevent SQL injection)
- [ ] HTML encoding output (prevent XSS)
- [ ] Content-Type validation
- [ ] File upload validation (type, size, content scanning)
- [ ] Request size limits

#### 4. Data Protection
- [ ] Encryption in transit (TLS 1.3, HTTPS everywhere)
- [ ] Encryption at rest (AES-256 for sensitive data)
- [ ] PII handling and data classification
- [ ] Data masking in logs (no passwords, tokens, PII in logs)
- [ ] Secure key management (AWS KMS, HashiCorp Vault)
- [ ] Database field-level encryption for sensitive data

#### 5. API Security
- [ ] Rate limiting (per user, per IP, per endpoint)
- [ ] CORS configuration (restrictive, not wildcard)
- [ ] API versioning
- [ ] Request/response validation (JSON Schema)
- [ ] Idempotency keys for write operations
- [ ] HMAC signature for webhooks
- [ ] API authentication on all endpoints

#### 6. Infrastructure Security
- [ ] Network segmentation (VPC, private subnets)
- [ ] Firewall rules (security groups, minimal ports)
- [ ] Secret management (not in code, not in env vars)
- [ ] Container security (non-root, minimal base images)
- [ ] Dependency scanning (Snyk, Dependabot)
- [ ] Regular patching and updates

#### 7. Logging & Audit
- [ ] Audit trail for sensitive operations
- [ ] Failed authentication logging
- [ ] Access logs with request details
- [ ] Log integrity protection
- [ ] Compliance logging (GDPR, HIPAA, SOC2)

#### 8. Availability & DoS Protection
- [ ] DDoS protection (CloudFlare, AWS Shield)
- [ ] Rate limiting at multiple levels
- [ ] Circuit breakers on external dependencies
- [ ] Graceful degradation under load
- [ ] Captcha for public endpoints

### Output Format
```
## Security Review: [System Name]

### Findings

| # | Severity | Category | Finding | Recommendation |
|---|----------|----------|---------|----------------|
| 1 | CRITICAL | Auth | No password hashing | Use bcrypt with salt |
| 2 | HIGH | Input | SQL injection risk | Use parameterized queries |
| 3 | MEDIUM | Data | PII in logs | Implement log masking |

### Action Items (Prioritized)
1. [CRITICAL] ...
2. [HIGH] ...
3. [MEDIUM] ...
```

## Examples
```
/security
/security the API endpoints
/security our authentication flow
```
