# Example: /evaluate monolith-api

This is an example of what the plugin produces when evaluating an existing system.

## Input
```
I have a Node.js Express API:
- Single server, no load balancer
- MongoDB with no indexes
- console.log everywhere
- No auth middleware
- No input validation
- No error handling (app crashes on errors)
- Manual deployment via SSH

/evaluate this system
```

## Expected Output Structure

### Score Card
```
Reliability:    [==--------] 2/10  -- SPOF, no error handling, crashes
Scalability:    [==---------] 2/10 -- Single server, no cache, no scaling plan
Performance:    [===-------] 3/10  -- No indexes, no caching, no optimization
Security:       [=---------] 1/10  -- No auth, no validation, no encryption
Operations:     [==--------] 2/10  -- console.log, manual deploy, no monitoring
Code Quality:   [====------] 4/10  -- Express works but no structure
Data Model:     [===-------] 3/10  -- MongoDB no indexes, no schema validation
API Design:     [====------] 4/10  -- Endpoints exist but no versioning/pagination
Overall:        [===-------] 2.6/10
```

### Findings (Prioritized)

**CRITICAL**
1. No error handling - app crashes propagate to users
2. No authentication - all endpoints publicly accessible
3. Single point of failure - one server goes down = total outage
4. No input validation - injection attacks possible

**HIGH**
5. No database indexes - queries degrade with data growth
6. Manual SSH deployment - error-prone, no rollback
7. console.log - no structured logging, no trace IDs, no log levels
8. No caching - every request hits database

**MEDIUM**
9. No API versioning - breaking changes break all clients
10. No pagination - list endpoints will timeout at scale
11. No rate limiting - vulnerable to abuse

### Improvement Roadmap

| Phase | Timeline | Actions |
|-------|----------|---------|
| Emergency (Week 1) | Now | Add error handler, add helmet, add basic auth |
| Foundation (Month 1) | 2-4 weeks | Add indexes, structured logging, input validation, CI/CD |
| Scale (Month 2-3) | 4-12 weeks | Load balancer, Redis cache, read replicas, monitoring |

## What Makes This Good
1. Scored against 10 dimensions (from evaluate-system skill)
2. Referenced Clean Code (structured logging), DDIA (indexes, replication)
3. Findings prioritized by severity with clear impact
4. Actionable roadmap with timeline
5. Didn't just list problems - provided solutions
