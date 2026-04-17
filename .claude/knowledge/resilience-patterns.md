# Resilience Patterns

> Canonical sources: Resilience4j, AWS Builders' Library, Netflix Chaos Engineering,
> Google SRE Book, Temporal.

---

## Core Patterns

### Circuit Breaker

Stop calling a failing dependency to give it recovery time.

**States:**
```
Closed (normal) → Open (fail fast) → Half-Open (test recovery)
```

Rules:
- Trip threshold: 50% errors over 20-request window
- Reset after 30-60s; one test call
- Success → Closed
- Failure → Open again

**Libraries:**
- **Resilience4j** (Java)
- **opossum** / **resilience-js** (Node)
- **Polly** (.NET)
- **tenacity** (Python)

### Retry with Exponential Backoff + Jitter

Only retry **idempotent** operations or those with idempotency keys.

```
delay = base * 2^attempt + random_jitter
```

**Full jitter** recommended (AWS Architecture Blog):
```js
const delay = Math.random() * Math.min(cap, base * 2 ** attempt);
```

Rules:
- **Max attempts:** 3 for user-facing, 5-7 for background jobs
- **Don't retry on 4xx** (except 408, 425, 429)
- **Retry on:** 5xx, network errors, timeouts
- **Budget:** total retry time < request timeout

### Timeout Everywhere

No unbounded calls, ever.

| Client | Default |
|--------|---------|
| HTTP connect | 2s |
| HTTP read | 10s (tune per SLO) |
| DB query | 5s (log slow queries) |
| Queue consumer visibility | expected processing × 2 |

**End-to-end budget:** client timeout > sum of internal timeouts + margin.

### Bulkhead

Isolate resources so one failure doesn't sink the ship.

- Thread/connection pools **per downstream** (not shared)
- Separate queues per priority tier
- Per-tenant rate limits (noisy neighbor)

---

## Health Checks

| Probe | Purpose | Fails → |
|-------|---------|---------|
| **Liveness** | Is the process alive? | Kill + restart container |
| **Readiness** | Can it serve traffic now? | Remove from load balancer (temporary) |
| **Startup** | Is it done initializing? | Delay liveness/readiness checks (K8s ≥ 1.16) |

Rules:
- **Liveness:** cheap, no dependencies — just "am I deadlocked?"
- **Readiness:** check critical deps (DB, cache), but not transitive
- **Startup:** longer timeout for slow init (cold caches, migrations)

```yaml
# k8s example
livenessProbe:
  httpGet: { path: /health/live, port: 8080 }
  periodSeconds: 10
  timeoutSeconds: 3
readinessProbe:
  httpGet: { path: /health/ready, port: 8080 }
  periodSeconds: 5
  failureThreshold: 3
startupProbe:
  httpGet: { path: /health/startup, port: 8080 }
  periodSeconds: 5
  failureThreshold: 30  # allow 150s for startup
```

---

## Graceful Degradation

- Serve **stale cache** on downstream failure
- Return **partial response**: `{"degraded": true, "missing": ["recommendations"]}`
- **Feature flags** to disable non-critical paths (recommendations, analytics)
- **Read-only mode** when write path is down
- **Default values** for missing data (empty recommendation list, not 500)

---

## Chaos Engineering (Netflix Principles)

1. **Hypothesis about steady state** (metrics, not code)
2. **Vary real-world events** (kill pod, inject latency, partition network)
3. **Run in production** (with safeguards) — staging doesn't prove production
4. **Automate continuously**, minimize blast radius (start with 1% traffic)

**Tools:**
- **Chaos Mesh** (Kubernetes-native)
- **Gremlin**
- **LitmusChaos**
- Netflix **Chaos Monkey**

**Progression:** schedule kill-a-pod weekly → graduate to network partitions → finally
latency injection across regions.

---

## Saga Pattern for Distributed Transactions

Two-phase commit is dead for microservices. Use sagas — a sequence of local transactions
with compensating actions.

### Two Styles

- **Choreography** — services react to events (simpler but hard to debug at scale)
- **Orchestration** — central coordinator (Temporal, AWS Step Functions) — **recommended for > 3 steps**

Rules:
- Each step has a **compensating action** (refund vs charge, release-inventory vs reserve)
- **Idempotent steps** + at-least-once delivery
- **Dedupe via `event_id`**

### Tools

- **Temporal** (de facto in 2026)
- AWS Step Functions
- Camunda
- Orkes

---

## Resilience Checklist — top 8

- [ ] Timeout on every outbound call — no exceptions
- [ ] Circuit breaker around every external dependency
- [ ] Retry with exponential backoff + jitter only for idempotent ops
- [ ] Bulkhead connection pools per downstream
- [ ] Three K8s probes defined (liveness, readiness, startup)
- [ ] Graceful degradation documented per feature
- [ ] Saga pattern (Temporal) for cross-service transactions
- [ ] Chaos experiment in CI/staging weekly

Sources:
- https://resilience4j.readme.io
- https://aws.amazon.com/builders-library
- https://sre.google/books
- https://principlesofchaos.org
- https://temporal.io/blog
