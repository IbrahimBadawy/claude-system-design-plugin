# /improve - Generate Improvement Recommendations

Analyze a system and generate a prioritized improvement roadmap.

## Usage
```
/improve <system_name_or_description>
```

## Behavior

### Phase 1: Current State Assessment
1. Understand the current architecture and its constraints
2. Identify the system's current scale and growth trajectory
3. Map all pain points and known issues
4. Understand the team's expertise and capacity

### Phase 2: Improvement Categories

Analyze improvements across these dimensions:

#### Performance Improvements
- Query optimization (indexes, query patterns, N+1 elimination)
- Caching strategy (add/improve cache layers, invalidation)
- Connection pooling and resource management
- Async processing for non-critical paths
- CDN for static content
- Database read replicas for read-heavy workloads

#### Scalability Improvements
- Identify the component that breaks first at 10x
- Horizontal scaling readiness (stateless services)
- Database scaling (read replicas -> sharding -> NewSQL)
- Message queue for decoupling
- CQRS for read/write optimization
- Microservice extraction for independently scaling components

#### Reliability Improvements
- Eliminate single points of failure
- Add circuit breakers on external calls
- Implement retry with exponential backoff + jitter
- Add health checks and readiness probes
- Design graceful degradation paths
- Implement bulkhead pattern for isolation

#### Security Improvements
- OWASP Top 10 remediation
- Add/improve authentication and authorization
- Encrypt data at rest and in transit
- Secrets management (vault, not in code)
- Input validation at all boundaries
- Audit logging for sensitive operations

#### Operational Improvements
- Monitoring: RED metrics (Rate, Errors, Duration)
- Structured logging with correlation IDs
- Distributed tracing (OpenTelemetry)
- Alerting on SLI/SLO violations
- Runbooks for common incidents
- Automated deployment pipeline

#### Code Quality Improvements
- Apply refactoring catalog (Fowler) for code smells
- Apply Clean Code principles
- Apply SOLID principles
- Improve test coverage
- Apply appropriate design patterns (GoF)
- Architecture boundary enforcement (Clean Architecture)

### Phase 3: Improvement Roadmap

Organize improvements into phases:

| Phase | Timeline | Focus | Key Deliverables |
|-------|----------|-------|-----------------|
| Quick Wins | 1-2 weeks | Low effort, high impact | Caching, indexes, monitoring |
| Foundation | 1-2 months | Core improvements | Error handling, security, tests |
| Scale | 2-4 months | Scaling readiness | Async, sharding, CQRS |
| Excellence | Ongoing | Continuous improvement | Optimization, automation |

### Phase 4: Detailed Recommendations

For each improvement:
1. **What**: Clear description of the change
2. **Why**: Problem it solves, impact on system quality
3. **How**: Implementation approach with specific steps
4. **Effort**: T-shirt sizing (S/M/L/XL)
5. **Impact**: High/Medium/Low
6. **Dependencies**: What must be done first
7. **Risks**: What could go wrong

### Phase 5: Before/After Comparison

Show the expected state after improvements:
```
BEFORE                          AFTER
Single DB, no cache      ->     Read replicas + Redis cache
No monitoring            ->     Prometheus + Grafana + alerts
Manual deployment        ->     CI/CD with canary releases
Monolith                 ->     Modular monolith with clear boundaries
No error handling        ->     Circuit breakers + retry + graceful degradation
```

## Examples
```
/improve our-api-service
/improve the-database-layer
/improve our-entire-backend
```
