# Modern Security (OWASP 2025)

> Canonical sources: OWASP Top 10, OWASP ASVS, OWASP Cheat Sheet Series,
> NIST SP 800-207 (Zero Trust), SLSA.

---

## OWASP Top 10 (current stable 2021; 2025 revision in data-collection)

1. **A01 Broken Access Control** (most critical — IDOR, missing authz checks)
2. **A02 Cryptographic Failures** (weak algorithms, plain HTTP, unsalted hashes)
3. **A03 Injection** (SQL, NoSQL, command, LDAP)
4. **A04 Insecure Design** (threat modeling missing)
5. **A05 Security Misconfiguration** (default creds, verbose errors, open S3)
6. **A06 Vulnerable Components** (outdated dependencies)
7. **A07 Identification & Auth Failures** (weak passwords, session fixation)
8. **A08 Software & Data Integrity Failures** (supply chain, unsigned updates)
9. **A09 Security Logging & Monitoring Failures**
10. **A10 Server-Side Request Forgery (SSRF)**

---

## OWASP ASVS Levels

| Level | Use For | Example Requirements |
|-------|---------|----------------------|
| **L1 (Opportunistic)** | Public apps, low risk | Basic auth, HTTPS, input validation |
| **L2 (Standard)** | Business apps, PII | All L1 + MFA, key mgmt, structured logging, CSRF tokens |
| **L3 (Advanced)** | Regulated, payments, health | All L2 + FIPS crypto, HSM, code review, pen test, anti-forensic logging |

- **Default for SaaS: L2**
- **Payments/health/PII-heavy: L3**

---

## OWASP Cheat Sheets (critical)

Apply these on every implementation:

### Authentication
- **Argon2id** (preferred) or **bcrypt** (cost ≥ 12) for passwords
- No password truncation
- **MFA** with TOTP or WebAuthn
- **Lockout after 5 failed attempts** with delay (exponential backoff)
- Per-user salt mandatory; pepper optional

### Session Management
- Opaque random tokens (≥ 128 bits)
- **httpOnly + Secure + SameSite=Lax** cookies
- **Regenerate on auth** (session fixation defense)
- **Idle timeout**: 15-30 min
- **Absolute timeout**: 24h

### Input Validation
- **Allowlist** (not blocklist)
- Validate at boundary with schema (zod/pydantic/JSON Schema)
- **Reject**, don't sanitize

### CSRF
- **SameSite=Lax** default
- For cross-origin POST: double-submit cookie or synchronizer token

### XSS
- Output-encode based on context (HTML, JS, URL, CSS)
- Use frameworks' auto-escape (React, Vue)
- **Never** `dangerouslySetInnerHTML` with user content
- **CSP** as defense-in-depth

### SQL Injection
- **Parameterized queries always** (no string concat)
- For identifiers, use allowlist mapping
- ORMs OK if raw queries use bindings

### File Upload
- Validate **MIME + magic bytes + extension**
- Store outside webroot
- Random filename
- Antivirus scan
- Size limit
- Serve via separate domain

### Password Storage
- **Argon2id** (preferred) or bcrypt (cost ≥ 12) or scrypt
- **NEVER** MD5/SHA1/SHA256 alone
- Pepper optional, per-user salt mandatory

---

## Security Headers (every app)

```
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
Content-Security-Policy: default-src 'self'; script-src 'self' 'nonce-{random}'; object-src 'none'; frame-ancestors 'none'
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: geolocation=(), camera=(), microphone=()
Cross-Origin-Opener-Policy: same-origin
Cross-Origin-Resource-Policy: same-site
```

Note: `X-Frame-Options: DENY` deprecated in favor of CSP `frame-ancestors 'none'`.

---

## Supply Chain Security

- **SBOM** (Software Bill of Materials) — CycloneDX or SPDX format, generated in CI
- **Dependency audits:** `npm audit`, `pip-audit`, `cargo audit` in CI — fail on high/critical
- **Tools:**
  - **Socket.dev** (behavioral analysis of packages)
  - **Snyk** (CVEs)
  - **Dependabot / Renovate** (automated PRs)
- **Lockfile discipline** — commit lockfile, CI fails on lockfile drift
- **SLSA Level 3** for signed builds (provenance attestation, in-toto)
- **Sigstore / cosign** for signed container images

---

## Secrets Management

Rules:
- **Never in `.env` committed to git**; `.env.example` only with placeholders
- **Dev:** `.env.local` (gitignored), direnv, or doppler/1Password CLI
- **Prod:** HashiCorp Vault, AWS Secrets Manager, GCP Secret Manager, Azure Key Vault
- **Kubernetes:** Sealed Secrets, External Secrets Operator, CSI Secret Store driver
- **Rotate quarterly minimum**; auto-rotate DB creds if vault supports it
- **Detect leaks:** `gitleaks`, `trufflehog` in CI

---

## Zero Trust (NIST SP 800-207)

Principles:

1. Every request authenticated and authorized (no network-based trust)
2. Least privilege, just-in-time access
3. Continuous verification (not one-time login)
4. Assume breach — segment blast radius
5. Encrypt everywhere (mTLS between services, TLS to users)

Practical: service-to-service **mTLS** via Istio/Linkerd, SPIFFE identities,
short-lived workload credentials.

---

## SOC 2 / ISO 27001 (SaaS basics)

For SaaS serving business customers:

- **SOC 2 Type II** — de facto requirement (US market)
  - Trust Service Criteria: Security (mandatory), Availability, Confidentiality, Processing Integrity, Privacy
- **ISO 27001** — international equivalent
- **Minimum controls:**
  - Access review quarterly
  - Encryption at rest + transit
  - Audit logs retained 1 year
  - Incident response plan tested
  - Vendor risk review
  - Background checks
- **Automation tools:** Vanta, Drata, Secureframe — automate 80% of evidence collection

---

## Security Checklist — top 10

- [ ] Argon2id password hashing; MFA available
- [ ] Parameterized queries everywhere (zero raw SQL)
- [ ] All endpoints auth + authz checked (not just auth)
- [ ] CSP with nonce, HSTS preload, all security headers
- [ ] Input validated with schema at boundary (zod/pydantic)
- [ ] Secrets in vault, never in env files in git
- [ ] SBOM + dep audit in CI, fail on high/critical CVE
- [ ] Structured audit log for sensitive operations, 1yr retention
- [ ] OWASP ASVS L2 minimum checklist in PR template
- [ ] mTLS between internal services

Sources:
- https://owasp.org/www-project-top-ten
- https://owasp.org/www-project-application-security-verification-standard
- https://cheatsheetseries.owasp.org
- https://csrc.nist.gov/pubs/sp/800/207/final
- https://slsa.dev
