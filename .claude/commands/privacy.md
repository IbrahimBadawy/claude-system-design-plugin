# /privacy - Data Privacy & GDPR Compliance

Design data privacy architecture and ensure compliance with GDPR, CCPA, and other regulations.

## Usage
```
/privacy <system>                   # Full privacy audit and design
/privacy audit                      # Audit current system for compliance gaps
/privacy data-map                   # Map all personal data flows
```

## Behavior

### Phase 1: Data Inventory
Map ALL personal data in the system:

```markdown
## Personal Data Inventory

| Data | Category | Storage | Purpose | Retention | Legal Basis |
|------|----------|---------|---------|-----------|-------------|
| Email | Contact | users.email (PostgreSQL) | Authentication | Account lifetime | Contract |
| Name | Identity | users.name | Display | Account lifetime | Contract |
| IP Address | Technical | access_logs | Security | 90 days | Legitimate interest |
| Location | Sensitive | check_ins | Service delivery | 30 days | Consent |
```

### Phase 2: User Rights Architecture

Design implementation for each right:

**Right to Access (Data Portability)**
- API endpoint: GET /api/v1/users/me/data-export
- Format: JSON + CSV download
- Include all data across all services
- Automated, available within 30 days

**Right to Erasure (Right to be Forgotten)**
- API endpoint: DELETE /api/v1/users/me
- Cascade delete/anonymize across:
  - Primary database (soft delete -> hard delete after grace period)
  - Read replicas (propagation delay)
  - Caches (invalidate immediately)
  - Search indexes (remove within 24h)
  - Backups (mark for exclusion on next rotation)
  - Third-party services (trigger deletion via API)
  - Logs (anonymize, don't delete for audit)
- What to keep: anonymized aggregated analytics, legal obligations

**Right to Rectification**
- Standard update endpoints with audit trail
- Propagate corrections to all data stores

**Consent Management**
```sql
CREATE TABLE user_consents (
    id UUID PRIMARY KEY,
    user_id UUID REFERENCES users(id),
    purpose VARCHAR(100) NOT NULL,     -- 'marketing', 'analytics', 'third-party'
    granted BOOLEAN NOT NULL,
    granted_at TIMESTAMP,
    revoked_at TIMESTAMP,
    ip_address INET,
    consent_version VARCHAR(20)        -- track which T&C version
);
```

### Phase 3: Architecture Patterns

**Data Minimization**
- Collect only what's needed for the stated purpose
- Auto-delete after retention period (scheduled job)
- Anonymize data for analytics (hash PII)

**Encryption**
- At rest: AES-256 for sensitive fields (database column-level encryption)
- In transit: TLS 1.3 everywhere
- Key management: AWS KMS / HashiCorp Vault (never in code)

**Audit Logging**
- Log all access to personal data (who, what, when, why)
- Immutable audit log (append-only)
- Separate from application logs

**Data Residency**
- Store data in the region of the user (EU data in EU servers)
- Multi-region deployment with data locality awareness
- Document all cross-border data transfers

**Third-Party Data Processing**
- DPA (Data Processing Agreement) with every vendor
- Minimize data shared with third parties
- Regular audit of third-party compliance

### Phase 4: Breach Response Plan
1. Detection: automated monitoring for unusual data access patterns
2. Assessment: classify severity within 24 hours
3. Notification: regulators within 72 hours, users without undue delay
4. Remediation: contain, fix, prevent recurrence
5. Documentation: full incident record for compliance

### Output
```markdown
# Privacy Design: [System Name]

## Data Inventory
[complete personal data map]

## User Rights Implementation
[technical design for each right]

## Consent Architecture
[consent management design]

## Retention Policies
| Data Type | Retention | Auto-Delete | Justification |
|-----------|-----------|-------------|---------------|

## Encryption Strategy
[what's encrypted, how, key management]

## Third-Party Data Sharing
| Vendor | Data Shared | DPA Signed | Purpose |
|--------|-------------|------------|---------|

## Compliance Checklist
- [ ] GDPR Article 5 principles met
- [ ] Privacy by design implemented
- [ ] Data Protection Impact Assessment done
- [ ] DPO appointed (if required)
- [ ] Breach notification process in place
```

## Examples
```
/privacy university-system
/privacy audit
/privacy data-map
```
