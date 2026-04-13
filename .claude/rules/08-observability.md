---
description: Every system must have monitoring, logging, and tracing from day 1
globs: "*"
---

# Rule: Observability from Day 1

Every service MUST have:

## Metrics (RED Method)
- Request **R**ate (requests per second)
- **E**rror rate (failed requests per second)  
- **D**uration (latency p50, p95, p99)

## Logging
- Structured JSON format
- Correlation/trace IDs in every log entry
- Appropriate log levels (ERROR, WARN, INFO, DEBUG)
- Never log sensitive data (PII, passwords, tokens)

## Health Checks
- `/health/live` - Is the process running?
- `/health/ready` - Can it serve traffic? (DB connected, dependencies up)

## Alerting
- Error rate > threshold -> P1 alert
- Latency p99 > SLO -> P2 alert
- Resource saturation > 80% -> P2 alert
- Service down -> P1 alert

## Recommended Stack
- OpenTelemetry for collection
- Prometheus + Grafana for metrics
- Structured logging (JSON) to aggregator
- Distributed tracing for multi-service systems
