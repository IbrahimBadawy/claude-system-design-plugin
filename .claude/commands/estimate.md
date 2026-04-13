# /estimate - Back-of-Envelope Calculation

Perform capacity estimation calculations for a system or component.

## Usage
```
/estimate <description>
```

## Behavior

1. Parse scale parameters from description (DAU, requests, data size)
2. If parameters are missing, ask clarifying questions
3. Calculate ALL of the following, showing math step-by-step:

### Calculations

**Traffic:**
```
QPS_avg = DAU * requests_per_user / 86,400
QPS_peak = QPS_avg * peak_factor (typically 3x, up to 10x for flash sales)
Writes_per_second = QPS * write_ratio
Reads_per_second = QPS * read_ratio
```

**Storage:**
```
Storage_per_record = sum of all field sizes
Storage_daily = writes_per_day * storage_per_record
Storage_monthly = Storage_daily * 30
Storage_yearly = Storage_daily * 365
Storage_total = Storage_yearly * retention_years * replication_factor
```

**Bandwidth:**
```
Incoming_bandwidth = QPS_avg * avg_request_size
Outgoing_bandwidth = QPS_avg * avg_response_size
```

**Cache:**
```
Cache_size = daily_active_records * avg_record_size * cache_ratio (20%)
Cache_hit_ratio_target = 80-95% (depends on access pattern)
```

**Server Count:**
```
Servers = QPS_peak / QPS_per_server
Web_servers: ~500-1000 QPS each
DB_connections: ~100-500 per instance
```

4. Present results in a clear table
5. Provide recommendations based on the numbers:
   - Do we need sharding? (if storage > single server capacity)
   - Do we need caching? (if read QPS > DB capacity)
   - Do we need CDN? (if serving static content globally)
   - Do we need message queue? (if write QPS > sustainable rate)

### Quick Reference Numbers
```
Seconds in a day: 86,400 ~ 100,000 (for quick math)
Seconds in a year: ~31.5 million ~ 30 million
1M requests/day ~ 12 QPS
1 char = 1-4 bytes (ASCII=1, UTF-8=1-4)
UUID = 16 bytes, Timestamp = 8 bytes, Integer = 4-8 bytes
1 image (compressed) ~ 200KB - 2MB
1 minute video (compressed) ~ 50-100MB
PostgreSQL: ~10K-50K QPS (depends on query complexity)
Redis: ~100K-200K ops/sec
Kafka: ~100K-1M messages/sec per partition
```

## Output Format
```
## Capacity Estimation: [System Name]

### Input Parameters
| Parameter | Value |
|-----------|-------|
| DAU | X |
| Requests/user/day | Y |
| Avg request size | Z |
| ... | ... |

### Traffic Estimates
| Metric | Calculation | Result |
|--------|------------|--------|
| Avg QPS | ... | ... |
| Peak QPS | ... | ... |

### Storage Estimates
| Metric | Calculation | Result |
|--------|------------|--------|
| Per record | ... | ... |
| Daily | ... | ... |
| Yearly | ... | ... |
| 5-year total | ... | ... |

### Bandwidth Estimates
...

### Cache Estimates
...

### Server Estimates
...

### Recommendations
- [recommendation based on numbers]
```

## Examples
```
/estimate 10M DAU, 5 requests/user/day, 2KB per request
/estimate chat system with 50M users, 40 messages/day each
/estimate video platform with 1M daily uploads, avg 100MB each
```
