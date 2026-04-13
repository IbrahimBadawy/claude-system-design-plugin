# /env - Environment Strategy

Design the environment pipeline, configuration management, and feature flags.

## Usage
```
/env design                    # Design Dev -> Staging -> Prod pipeline
/env config <module>           # Environment-specific configuration
/env feature-flags <feature>   # Feature flag strategy
```

## /env design
```markdown
## Environments

| Environment | Purpose | Deploy Trigger | Data |
|------------|---------|---------------|------|
| Local | Development | Manual | Seed data |
| Dev | Integration | Push to dev branch | Seed data |
| Staging | Pre-production | Merge to main | Anonymized prod copy |
| Production | Live | Manual approval after staging E2E | Real data |

### Promotion Rules
- Local -> Dev: Automatic on push
- Dev -> Staging: Automatic on merge to main (after CI passes)
- Staging -> Prod: Manual approval after E2E tests pass
- Rollback: Automatic if error rate > 5% within 10 minutes
```

## /env config <module>
12-Factor App configuration:
- Environment variables for all config (never hardcode)
- .env.example committed (with descriptions, no secrets)
- Secrets in vault (AWS Secrets Manager / HashiCorp Vault)
- Config validation at startup (fail fast if missing)
- Per-tenant overrides stored in database

## /env feature-flags <feature>
```markdown
## Feature Flag: [feature-name]
**Type**: Release / Experiment / Ops / Permission
**Lifecycle**: Created -> Active -> Fully Rolled Out -> Removed

### Rollout Plan
| Stage | Target | Duration | Success Criteria |
|-------|--------|----------|-----------------|
| 1 | Internal team | 3 days | No errors |
| 2 | 5% of users | 1 week | Error rate < 0.1% |
| 3 | 25% of users | 1 week | Latency unchanged |
| 4 | 100% | Permanent | Remove flag after 2 weeks |

### Kill Switch
If issues detected: disable flag immediately (reverts to old behavior)
```

## Examples
```
/env design
/env config payment-module
/env feature-flags new-enrollment-flow
```
