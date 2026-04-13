---
description: Define and enforce performance budgets. Measure p50/p95/p99.
globs: "*"
---

# Rule: Performance Budgets

Every system MUST have defined performance budgets:

## API Performance
- p50 response time < 100ms
- p95 response time < 300ms
- p99 response time < 500ms
- Max DB queries per request < 5
- Max response payload < 500KB

## Frontend Performance (Core Web Vitals)
- LCP (Largest Contentful Paint) < 2.5s
- INP (Interaction to Next Paint) < 200ms
- CLS (Cumulative Layout Shift) < 0.1
- Initial JS bundle < 200KB
- Total page weight < 1MB

## Enforcement
- Measure in CI (Lighthouse for frontend, load tests for API)
- Alert when budgets are exceeded in production
- Block deploy if critical budgets are violated
- Review budgets quarterly as system grows

## Optimization Order
1. Measure first (don't optimize blindly)
2. Fix the biggest bottleneck first
3. Cache before you scale
4. Async before you parallelize
5. Simple before clever
