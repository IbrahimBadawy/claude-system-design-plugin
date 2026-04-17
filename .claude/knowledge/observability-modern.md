# Modern Observability (OpenTelemetry Era, 2024-2026)

> Canonical sources: OpenTelemetry, Google SRE Book, Brendan Gregg (USE),
> Grafana, Honeycomb, Prometheus.

---

## OpenTelemetry (OTel) — the CNCF Standard

By 2026, OTel is the default in all major languages. Skipping it locks you into one vendor.

### Three signals, one SDK

- **Traces** (distributed tracing)
- **Metrics** (Prometheus-compatible)
- **Logs** (logs bridge stable since 2023)

### OTLP Protocol

Over gRPC or HTTP. All major backends accept OTLP:
- Datadog, Honeycomb, Grafana, New Relic, Jaeger, Tempo

### Auto-instrumentation

For popular libs: Express, NestJS, FastAPI, Django, Spring Boot, .NET.

### Semantic Conventions

Standard attribute names: `http.request.method`, `db.system`, `service.name`, `user_agent.original`.

### Setup Pattern

```
app → OTel SDK → OTel Collector (sidecar or gateway) → backend(s)
```

The Collector decouples apps from backends — swap Datadog for Grafana without code changes.

Sources: https://opentelemetry.io/docs

---

## USE vs RED vs Golden Signals

| Method | Scope | Metrics | Who |
|--------|-------|---------|-----|
| **USE** | Resources (CPU, disk, network, memory) | Utilization, Saturation, Errors | Brendan Gregg |
| **RED** | Request-driven services | Rate, Errors, Duration | Weaveworks |
| **Golden Signals** | User-facing services | Latency, Traffic, Errors, Saturation | Google SRE |

### When to Use Each

- **USE** for infra dashboards — kernel, VM, container, disk
- **RED** for application/service dashboards — per-endpoint breakdown
- **Golden Signals** for customer-facing products (superset of RED + saturation)

Practical rule: every service dashboard has RED per endpoint; every host/container
dashboard has USE; top-level product dashboards use Golden Signals.

---

## SLO / SLI / Error Budget

- **SLI**: a measurable signal (e.g., `successful_requests / total_requests` over 30 days)
- **SLO**: target for that signal (e.g., 99.9%)
- **Error budget**: `1 - SLO` (e.g., 0.1% = ~43 min/month)

### Rules

- Start with **99.9%** (43 min/month). Resist 99.99% without strong business case; each 9 costs 10× more
- **Burn-rate alerts** (Google SRE book):
  - Fast burn: 2% budget in 1h → page
  - Slow burn: 10% in 6h → ticket
- **Error budget frozen** → halt risky changes, focus on reliability
- **Publish SLOs externally** only when you can defend them

---

## Distributed Tracing

### What to Span

- Every outbound HTTP/DB/cache/queue call
- Every business-meaningful operation (`processPayment`, `validateOrder`)

### Trace Context Propagation

W3C `traceparent` header — OTel does this automatically.

### Sampling Strategies

- **Head-based** — decide at trace start (simple, cheap, misses rare errors)
- **Tail-based** — decide after trace completes in collector (keeps errors, slow traces, + % of happy paths; better signal, more infra)
- **Adaptive sampling from rate** (Honeycomb-style) — default 1% healthy, 100% errors

### Span Attributes

Include `tenant_id`, `user_id` (hashed if PII), `request_id`. Avoid high-cardinality values
in metrics (OK in traces).

---

## Structured Logging

```json
{
  "timestamp": "2026-04-16T10:23:45.123Z",
  "level": "ERROR",
  "service": "payment-service",
  "traceId": "abc123",
  "spanId": "def456",
  "tenantId": "t_789",
  "userId": "u_hashed_abc",
  "message": "payment declined",
  "errorCode": "INSUFFICIENT_FUNDS",
  "amount": 5000,
  "currency": "USD"
}
```

### Rules

- JSON always, ISO 8601 UTC
- `traceId` / `spanId` from OTel context — automatic correlation with traces
- **NEVER log tokens, passwords, PII** (full names, emails, SSN, credit cards)
- One log event per line

---

## Alert Design

- **Symptom-based alerts** ("checkout success rate < 99%") → **page** a human
- **Cause-based alerts** ("DB CPU > 90%") → **ticket**, not page
- **Pager hygiene** — < 2 pages/week per on-call rotation. More = alert fatigue, fix root cause
- Every alert has a **runbook link** in the description
- Alerts without auto-recovery include the remediation command

---

## Observability Stack Comparison

| Stack | Best For | Cost Signal |
|-------|----------|-------------|
| **Prometheus + Grafana + Loki + Tempo** | Self-hosted, polyglot, control-focused | Low infra $, higher ops time |
| **Grafana Cloud** | Same but managed | $/active series |
| **Datadog** | Enterprise, deep integrations, APM | High; cardinality pricing |
| **Honeycomb** | Event-driven debugging, wide events, observability 2.0 | $/events |
| **New Relic** | Full-stack APM with free tier | Generous free tier, then usage-based |

### Plugin Default

**OTel SDK → OTel Collector → {Prometheus + Grafana + Tempo + Loki}** self-hosted or
Grafana Cloud. Swap to Datadog/Honeycomb by changing collector config only.

---

## Cardinality Trap

Prometheus cost scales with unique label combinations. Avoid high-cardinality labels in metrics:

- **Never label metrics with:** `user_id`, `email`, `order_id`, full URL with IDs, `request_id`
- **OK labels:** endpoint template (`/users/:id`), status code, method, region, tenant_tier
- Need per-user data → use **traces** or **structured logs**, not metrics

---

## Observability Checklist — top 8

- [ ] OpenTelemetry SDK in every service; OTLP export
- [ ] OTel Collector in cluster (traces + metrics + logs)
- [ ] RED metrics per endpoint; USE metrics per host
- [ ] SLO defined with error budget; burn-rate alerts
- [ ] Tail-based sampling at collector (keep 100% errors + slow)
- [ ] Structured JSON logs with traceId/spanId correlation
- [ ] Symptom-based pager alerts only; every alert has runbook
- [ ] No high-cardinality labels in metrics

Sources:
- https://opentelemetry.io/docs
- https://sre.google/workbook/implementing-slos
- https://brendangregg.com/usemethod.html
- https://grafana.com/blog
- https://honeycomb.io/blog
