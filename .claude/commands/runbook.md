# /runbook - Operations & Incident Management

Design operational runbooks, on-call procedures, and SLA definitions.

## Usage
```
/runbook <scenario>            # Create operational runbook
/runbook oncall                # Design on-call rotation & escalation
/runbook incident <type>       # Pre-written incident response playbook
/runbook sla <service>         # Define SLA/SLO/SLI
```

## /runbook <scenario>
```markdown
# Runbook: [Scenario]
**Severity**: P1/P2/P3
**On-Call**: [team]
**Last Updated**: [date]

## Detection
- Alert: [what fires]
- Dashboard: [what to check]

## Diagnosis Steps
1. [ ] Check [metric/log/dashboard]
2. [ ] Verify [component status]
3. [ ] Check [recent deployments]

## Resolution Steps
1. [ ] [Immediate action]
2. [ ] [Fix action]
3. [ ] [Verify fix]

## Rollback (if fix fails)
1. [ ] [Rollback step]

## Post-Resolution
- [ ] Notify stakeholders
- [ ] Update status page
- [ ] Schedule postmortem if P1/P2
```

## /runbook oncall
Design on-call rotation:
- Primary + secondary on-call
- Rotation schedule (weekly)
- Escalation path: alert -> primary (5min) -> secondary (15min) -> engineering lead (30min)
- Tools: PagerDuty/OpsGenie
- Handoff checklist

## /runbook sla <service>
```markdown
## SLA: [Service]

| SLI | SLO | Measurement |
|-----|-----|-------------|
| Availability | 99.9% (8.77h downtime/year) | Successful requests / total |
| Latency | p99 < 500ms | histogram_quantile(0.99) |
| Error Rate | < 0.1% | 5xx responses / total |

### Error Budget
- Monthly budget: 43.8 minutes downtime
- Current burn rate: [X min used]
- Remaining: [Y min]
- If budget exhausted: freeze deploys, focus on reliability
```

## Examples
```
/runbook database-primary-down
/runbook oncall
/runbook incident security-breach
/runbook sla payment-service
```
