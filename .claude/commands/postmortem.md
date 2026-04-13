# /postmortem - Incident Postmortem

Create a blameless incident postmortem document.

## Usage
```
/postmortem <incident_description>
```

## Output Template

```markdown
# Postmortem: [Incident Title]

**Date**: [date]
**Severity**: P1 | P2 | P3 | P4
**Duration**: [X hours Y minutes]
**Author**: [name]
**Reviewers**: [names]

## Summary
[1-2 sentence description of what happened and its impact]

## Impact
- **Users affected**: [number/percentage]
- **Revenue impact**: [estimated if applicable]
- **Data loss**: [yes/no, details]
- **SLA impact**: [error budget consumed]
- **Services affected**: [list]

## Timeline (All times in UTC)

| Time | Event |
|------|-------|
| HH:MM | First anomaly detected |
| HH:MM | Alert fired |
| HH:MM | On-call acknowledged |
| HH:MM | Investigation began |
| HH:MM | Root cause identified |
| HH:MM | Fix deployed |
| HH:MM | Monitoring confirmed recovery |
| HH:MM | All-clear declared |

## Root Cause Analysis

### What happened
[Detailed technical explanation of the chain of events]

### Why it happened
[Use the "5 Whys" technique to get to the root cause]
1. Why? [immediate cause]
2. Why? [deeper cause]
3. Why? [even deeper]
4. Why? [systemic issue]
5. Why? [root cause]

### Contributing Factors
- [Factor 1: e.g., missing monitoring]
- [Factor 2: e.g., inadequate testing]
- [Factor 3: e.g., missing runbook]

## What Went Well
- [Effective action 1]
- [Effective action 2]
- [Quick detection / fast response / good communication]

## What Went Wrong
- [Problem 1]
- [Problem 2]
- [Gap in process / tooling / knowledge]

## Where We Got Lucky
- [Lucky break 1: something that could have made it worse]
- [Lucky break 2: timing that minimized impact]

## Action Items

| # | Priority | Action | Owner | Due Date | Status |
|---|----------|--------|-------|----------|--------|
| 1 | P1 | [Prevent recurrence] | [name] | [date] | Open |
| 2 | P1 | [Improve detection] | [name] | [date] | Open |
| 3 | P2 | [Improve response] | [name] | [date] | Open |
| 4 | P3 | [Process improvement] | [name] | [date] | Open |

## Lessons Learned
1. [Lesson 1]
2. [Lesson 2]
3. [Lesson 3]

## References
- [Related incidents]
- [Relevant documentation]
- [External resources]
```

## Examples
```
/postmortem database outage caused 30min downtime
/postmortem payment processing failure during peak hours
/postmortem cascading failure from third-party API
```
