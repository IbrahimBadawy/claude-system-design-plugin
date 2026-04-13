# /evaluate - Evaluate an Existing System

Critically review and evaluate an existing system design or codebase architecture.

## Usage
```
/evaluate <system_name_or_description>
```

## Behavior

### Phase 1: Understanding
1. Read and understand the current architecture
2. Identify all components and their interactions
3. Map data flows and dependencies
4. Understand the current scale and requirements

### Phase 2: Evaluation Matrix

Evaluate against these categories, scoring each as:
- PASS - Meets best practices
- WARN - Has issues that should be addressed
- FAIL - Critical issues that must be fixed

| Category | Checks |
|----------|--------|
| **Reliability** | Single points of failure, failover strategy, data durability, redundancy |
| **Scalability** | Bottlenecks, horizontal scaling readiness, stateless services, connection pooling |
| **Performance** | Caching strategy, query optimization, N+1 problems, indexing, latency targets |
| **Security** | Auth/authz, input validation, encryption (transit+rest), secrets management, OWASP Top 10 |
| **Data Model** | Schema design, indexes match queries, normalization, migration strategy, backup |
| **API Design** | Versioning, pagination, error handling, rate limiting, idempotency |
| **Operations** | Monitoring, logging, alerting, deployment strategy, runbooks, disaster recovery |
| **Code Quality** | Error handling, DRY, SOLID principles, clean code, test coverage |
| **Resilience** | Circuit breakers, retries with backoff, timeouts, graceful degradation, bulkheads |
| **Cost** | Resource utilization, over-provisioning, optimization opportunities |

### Phase 3: Findings Report

Output findings organized by severity:

**CRITICAL** - Must fix immediately (data loss risk, security vulnerabilities, SPOF)
**HIGH** - Should fix soon (scalability bottlenecks, missing error handling)
**MEDIUM** - Plan to fix (missing monitoring, suboptimal caching)
**LOW** - Nice to have (code cleanup, documentation gaps)

### Phase 4: Recommendations

For each finding provide:
1. **Problem**: What is wrong and why it matters
2. **Impact**: What could happen if not addressed
3. **Recommendation**: How to fix it
4. **Effort**: Low / Medium / High
5. **Priority**: P0 / P1 / P2 / P3

### Phase 5: Score Card

Provide an overall health score:
```
Reliability:    [====------] 4/10
Scalability:    [=======---] 7/10
Performance:    [======----] 6/10
Security:       [===-------] 3/10
Operations:     [=====-----] 5/10
Code Quality:   [========--] 8/10
Overall:        [======----] 5.5/10
```

## What to Look For (from the books)

### From Clean Architecture
- Does it follow the Dependency Rule? (dependencies point inward)
- Are business rules isolated from frameworks and DB?
- Are there proper boundaries between components?
- Is the system testable without external dependencies?

### From Clean Code
- Are functions small and do one thing?
- Are names meaningful and intention-revealing?
- Is error handling comprehensive (no null returns/passes)?
- Are there code smells? (check the full smells catalog)

### From DDIA
- Is the replication strategy appropriate?
- Are partitioning decisions sound?
- Is the consistency model appropriate for the use case?
- Are encoding/evolution concerns handled?

### From Code Complete
- Is defensive programming in place?
- Are there proper integration strategies?
- Is the construction quality appropriate?

## Examples
```
/evaluate our-payment-system
/evaluate the-current-api-architecture
/evaluate this codebase
```
