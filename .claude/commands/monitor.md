# /monitor - Design Observability Stack

Design the monitoring, alerting, and observability strategy for the current system.

## Usage
```
/monitor
/monitor <specific-service>
```

## Behavior

### The Three Pillars of Observability

#### 1. Metrics (Prometheus + Grafana)
Track using RED method for services and USE method for resources:

**RED Method (Request-driven services):**
- **R**ate: requests per second
- **E**rrors: failed requests per second
- **D**uration: latency distribution (p50, p95, p99)

**USE Method (Resources: CPU, memory, disk, network):**
- **U**tilization: percentage of resource busy
- **S**aturation: work queued / waiting
- **E**rrors: error events

**Key Metrics:**
```
# Application
http_requests_total{method, endpoint, status}
http_request_duration_seconds{method, endpoint}
active_connections
queue_depth
cache_hit_ratio

# Database
db_connections_active
db_query_duration_seconds
db_errors_total
replication_lag_seconds

# Infrastructure
cpu_usage_percent
memory_usage_bytes
disk_usage_percent
network_io_bytes
```

#### 2. Logging (Structured JSON)
```json
{
  "timestamp": "2026-01-01T00:00:00.000Z",
  "level": "error",
  "service": "payment-service",
  "trace_id": "abc123",
  "span_id": "def456",
  "message": "Payment processing failed",
  "error": "timeout",
  "user_id": "user_789",
  "duration_ms": 5000,
  "metadata": {}
}
```

**Log Levels:**
- ERROR: Failures requiring attention
- WARN: Unexpected but handled situations
- INFO: Significant business events
- DEBUG: Detailed diagnostic information (not in production)

#### 3. Tracing (OpenTelemetry + Jaeger/Tempo)
- Distributed tracing across all services
- Correlation IDs propagated via headers
- Trace critical paths: user request -> API -> service -> DB -> response
- Sample rate: 100% for errors, 1-10% for normal traffic

### Alerting Strategy

**Severity Levels:**
| Level | Response Time | Example | Channel |
|-------|--------------|---------|---------|
| P1 Critical | 5 min | Service down, data loss | PagerDuty + phone |
| P2 High | 30 min | Degraded performance, error spike | Slack + PagerDuty |
| P3 Medium | 4 hours | Elevated latency, disk warning | Slack |
| P4 Low | Next business day | Non-critical warnings | Email |

**Alert Rules:**
```yaml
# Error rate spike
- alert: HighErrorRate
  expr: rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.05
  for: 5m
  severity: critical

# High latency
- alert: HighLatency
  expr: histogram_quantile(0.99, http_request_duration_seconds) > 2
  for: 5m
  severity: high

# Database connection exhaustion
- alert: DBConnectionsHigh
  expr: db_connections_active / db_connections_max > 0.8
  for: 5m
  severity: high
```

### SLI/SLO Definitions
```
Availability SLO: 99.9% (8.77 hours downtime/year)
  SLI: successful requests / total requests

Latency SLO: p99 < 500ms
  SLI: histogram_quantile(0.99, request_duration)

Error SLO: < 0.1% error rate
  SLI: error requests / total requests
```

### Recommended Stack (2026)
- **Collection**: OpenTelemetry Collector
- **Metrics**: Prometheus
- **Visualization**: Grafana
- **Tracing**: Jaeger or Grafana Tempo
- **Logging**: ELK Stack or Grafana Loki
- **Alerting**: Alertmanager + PagerDuty/OpsGenie

### Dashboard Design
1. **Overview**: Request rate, error rate, latency, uptime
2. **Per-Service**: Detailed metrics for each service
3. **Infrastructure**: CPU, memory, disk, network per host
4. **Database**: Query performance, connections, replication lag
5. **Business**: Key business metrics (signups, orders, revenue)

## Examples
```
/monitor
/monitor payment-service
/monitor the database layer
```
