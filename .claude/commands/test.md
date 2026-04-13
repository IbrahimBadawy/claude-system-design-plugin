# /test - Testing Strategy & Commands

Design and manage the testing strategy for the current project.

## Usage
```
/test strategy <project>              # Design test pyramid split
/test contract <service-a> <service-b> # Contract testing between modules
/test data <entity>                   # Generate test fixtures & factories
/test chaos <scenario>                # Chaos engineering scenarios
/test coverage                        # Analyze test coverage gaps
```

## Test Strategy Design

### Test Pyramid (Target Ratios)
- **Unit Tests (70%)**: Domain logic, use cases, pure functions. Fast, isolated, no external deps.
- **Integration Tests (20%)**: API endpoints, DB queries, external services. Testcontainers.
- **E2E Tests (10%)**: Critical user journeys. Playwright/Cypress.

### /test strategy <project>
1. Analyze the system architecture
2. Define test pyramid split
3. Identify critical paths needing e2e tests
4. Define contract test boundaries
5. Set coverage targets per layer
6. Generate test plan document

### /test contract <service-a> <service-b>
Define how two services verify their communication:
- Provider publishes contract (OpenAPI spec or Pact file)
- Consumer verifies against contract
- Break in contract = CI failure
- Tools: Pact, Spring Cloud Contract, OpenAPI validation

### /test data <entity>
Generate:
- Factory functions: `createUser()`, `createOrder(overrides)`
- Fixtures for common scenarios (happy path, edge cases)
- Seed data for development databases
- Edge case data: empty, max length, special chars, unicode, SQL injection attempts

### /test chaos <scenario>
Design chaos experiments:
- **DB failover**: Kill primary, verify replica promotion + zero data loss
- **Cache failure**: Disable Redis, verify graceful degradation to DB
- **Network partition**: Isolate service, verify circuit breaker activates
- **High load**: 10x traffic, verify autoscaling + no 5xx errors
- **Dependency timeout**: 30s latency on external API, verify timeout + fallback
- **Disk full**: Fill disk, verify alerts fire + graceful handling

### /test coverage
- Critical business logic: 90%+ required
- API endpoints: 80%+ required
- UI components: 60%+ (focus on interaction)
- Overall target: 80%
- Identify untested critical paths

## Examples
```
/test strategy university-system
/test contract user-service auth-service
/test data Student
/test chaos database-failover
/test coverage
```
