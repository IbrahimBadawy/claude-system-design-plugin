# /perf - Performance Optimization

Audit and optimize system performance. Set performance budgets.

## Usage
```
/perf <system>                      # Full performance audit
/perf frontend                      # Frontend performance (Core Web Vitals)
/perf backend                       # Backend performance (API latency, DB)
/perf budgets                       # Define performance budgets
```

## Behavior

### Frontend Performance (Core Web Vitals 2026)
| Metric | Good | Needs Work | Poor |
|--------|------|-----------|------|
| LCP (Largest Contentful Paint) | < 2.5s | 2.5-4s | > 4s |
| INP (Interaction to Next Paint) | < 200ms | 200-500ms | > 500ms |
| CLS (Cumulative Layout Shift) | < 0.1 | 0.1-0.25 | > 0.25 |

**Optimization Checklist:**
- [ ] Code splitting and lazy loading (route-based)
- [ ] Image optimization (WebP/AVIF, responsive, lazy load)
- [ ] Bundle size budget (< 200KB initial JS)
- [ ] CDN for static assets
- [ ] Preload critical resources
- [ ] Service worker for caching
- [ ] Tree shaking unused code
- [ ] Font optimization (swap, preload, subset)

### Backend Performance
- [ ] N+1 query detection and fix (use eager loading)
- [ ] Database EXPLAIN ANALYZE on slow queries
- [ ] Connection pooling configured
- [ ] Response compression (gzip/brotli)
- [ ] Pagination on all list endpoints
- [ ] Caching hot paths (Redis, CDN)
- [ ] Async for non-critical operations
- [ ] Load testing with k6/Gatling

### Performance Budgets
```markdown
| Component | Metric | Budget | Measured | Status |
|-----------|--------|--------|----------|--------|
| API p99 | Response time | < 200ms | | |
| Homepage | LCP | < 2.5s | | |
| JS Bundle | Initial size | < 200KB | | |
| DB Query | Max per request | < 5 | | |
| API | Max response size | < 500KB | | |
```

**Tools:** Lighthouse, k6, EXPLAIN ANALYZE, OpenTelemetry, pprof/pyspy

## Examples
```
/perf university-system
/perf frontend
/perf budgets
```
