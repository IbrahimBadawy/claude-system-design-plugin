# /failure - Failure Analysis

Analyze what happens when a component fails and design recovery strategies.

## Usage
```
/failure <scenario>
```

## Behavior

### Analysis Framework

For each failure scenario, analyze:

1. **Failure Description**: What exactly fails and how
2. **Blast Radius**: What other components are affected
3. **User Impact**: What users experience (error page, degraded, unaffected)
4. **Detection**: How do we know it failed (monitoring, alerts, user reports)
5. **Automatic Recovery**: What the system does automatically
6. **Manual Recovery**: Steps for on-call engineer
7. **Data Impact**: Any data loss or inconsistency risk
8. **Prevention**: How to prevent recurrence

### Output Format

```markdown
## Failure Analysis: [Scenario]

### Scenario
[Detailed description of the failure]

### Blast Radius
| Affected Component | Impact Level | Description |
|-------------------|-------------|-------------|
| [Component A] | Critical | [what happens] |
| [Component B] | Degraded | [what happens] |
| [Component C] | Unaffected | [why it's ok] |

### Detection
- **Alert**: [what metric triggers]
- **Time to Detect**: [estimated seconds/minutes]
- **Dashboard**: [what to look at]

### Automatic Recovery
1. [Health check detects failure]
2. [Load balancer removes unhealthy node]
3. [Auto-restart / failover triggers]
4. [Recovery verification]

### Manual Recovery Runbook
1. [ ] Verify the failure (check logs, metrics)
2. [ ] Assess impact (users affected, data at risk)
3. [ ] Execute recovery steps:
   - [ ] [Step 1]
   - [ ] [Step 2]
4. [ ] Verify recovery
5. [ ] Document incident

### Prevention
- [ ] [Preventive measure 1]
- [ ] [Preventive measure 2]

### Resilience Patterns Applied
- Circuit Breaker: [how it helps]
- Retry with Backoff: [configuration]
- Bulkhead: [isolation strategy]
- Graceful Degradation: [fallback behavior]
```

### Common Failure Scenarios
- Database primary down
- Cache (Redis) failure
- Message queue failure
- External API timeout
- DNS resolution failure
- Certificate expiration
- Disk full
- Memory leak / OOM
- Network partition between services
- Cascading failure from downstream service
- Thundering herd after recovery
- Split-brain in distributed system

## Examples
```
/failure database-primary-down
/failure redis-cluster-failure
/failure payment-provider-timeout
/failure dns-outage
```
