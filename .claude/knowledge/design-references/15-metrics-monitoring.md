# Design Reference: Metrics Monitoring System

## Requirements
- Collect metrics from all services (counters, gauges, histograms)
- Store time-series data efficiently
- Visualize with dashboards
- Alert on anomalies

## Architecture
```
Services -> Metrics SDK -> Kafka (buffer) -> Consumer -> Time-Series DB
                                                             |
                                                     Query Service -> Grafana
                                                             |
                                                     Alert Service -> PagerDuty/Slack
```

## Pull vs Push
- **Pull** (Prometheus): Scrapes /metrics endpoint. Uses service discovery (Kubernetes). Simpler for Kubernetes environments.
- **Push** (StatsD/OTLP): Services push to collector. Better for short-lived jobs, serverless.
- **Recommendation 2026**: OpenTelemetry Collector (accepts both) -> Prometheus (storage) -> Grafana (viz)

## Time-Series DB Choice
| DB | Best For |
|----|----------|
| Prometheus | Kubernetes metrics, up to ~10M series |
| InfluxDB | IoT, high cardinality, SQL-like queries |
| TimescaleDB | PostgreSQL-based, complex queries |
| VictoriaMetrics | Long-term storage, Prometheus-compatible |

## Key Metrics (RED + USE)
- **RED** for services: Rate, Errors, Duration
- **USE** for resources: Utilization, Saturation, Errors

## Alerting Rules
```yaml
- alert: HighErrorRate
  expr: rate(http_errors_total[5m]) / rate(http_requests_total[5m]) > 0.05
  for: 5m
  severity: critical

- alert: HighLatency
  expr: histogram_quantile(0.99, http_duration_seconds) > 2
  for: 5m
  severity: warning
```
