---
description: Every system must have a defined testing strategy. No implementation without tests. Test pyramid enforced.
globs: "*.{ts,js,py,java,go,rs,tsx,jsx}"
---

# Rule: Testing is Required

Every implemented system MUST have:

## Test Pyramid (target ratios)
- **Unit Tests (70%)**: Domain logic, use cases, pure functions. Fast, isolated.
- **Integration Tests (20%)**: API endpoints, DB queries, external services. Testcontainers.
- **E2E Tests (10%)**: Critical user journeys. Playwright/Cypress.

## Minimum Requirements
- Critical business logic: 90%+ coverage
- API endpoints: 80%+ coverage
- Every bug fix: add a regression test FIRST, then fix
- Contract tests between services/modules that communicate

## What to Test
- Happy path + error paths + edge cases
- Boundary conditions (0, 1, many, max, empty, null)
- Authentication and authorization (can user A access user B's data?)
- Concurrent operations (race conditions)
- Tenant isolation (in multi-tenant systems)

## What NOT to Test
- Framework internals (don't test React renders a div)
- Trivial getters/setters
- Auto-generated code
- Third-party library behavior

## Testing Rules
- Tests run in CI on every push
- No merge to main with failing tests
- Test names describe behavior: "should_reject_order_when_insufficient_stock"
- Each test is independent (no shared mutable state between tests)
