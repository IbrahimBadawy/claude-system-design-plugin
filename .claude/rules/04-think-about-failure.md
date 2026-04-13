---
description: Design for failure - every component can and will fail
globs: "*"
---

# Rule: Think About Failure

For every component, answer:

1. **What happens when it fails?** (blast radius)
2. **How do we detect the failure?** (monitoring, health checks)
3. **How do we recover?** (auto-failover, manual steps)
4. **What's the blast radius?** (which other components are affected)

Required resilience patterns:
- **Timeout** on ALL external calls (HTTP, DB, cache, queue)
- **Retry with exponential backoff + jitter** for transient failures
- **Circuit breaker** for repeated failures to external services
- **Graceful degradation** (serve partial results when components fail)
- **Health checks** (liveness + readiness probes)
- **Dead letter queue** for failed message processing
