# Design Reference: Rate Limiter

## Requirements
- Limit requests per user/IP/endpoint within a time window
- Distributed (works across multiple servers)
- Low latency (< 1ms overhead per request)
- Show clear error to user when throttled (429 + headers)

## Algorithms

### Token Bucket (Recommended - used by AWS, Stripe)
- Bucket holds N tokens, refills at rate R tokens/sec
- Each request consumes 1 token. Empty bucket = reject
- **Pros**: Allows bursts up to bucket size, memory efficient
- **Params**: bucket_size (max burst), refill_rate (sustained rate)

### Sliding Window Counter (used by Cloudflare)
- Weighted formula: `current_count + previous_count * overlap_%`
- ~0.003% error rate. Good balance of accuracy and memory.

### Others
- **Fixed Window**: Simple but allows 2x burst at window edge
- **Sliding Window Log**: Exact but memory-heavy (stores timestamps)
- **Leaking Bucket**: Fixed output rate (Shopify). Good for stable throughput

## Architecture
```
Client -> API Gateway/Middleware -> Rate Limiter -> App Server
                                       |
                                   Redis (counters)
```

## Implementation (Redis)
```
-- Token Bucket with Redis
Key: rate_limit:{user_id}:{endpoint}
Operations: INCR + EXPIRE (atomic via Lua script)

-- Lua script for atomicity (prevents race conditions)
local current = redis.call('INCR', KEYS[1])
if current == 1 then
    redis.call('EXPIRE', KEYS[1], ARGV[1])
end
return current
```

## API Response
```
HTTP 429 Too Many Requests
Headers:
  X-RateLimit-Limit: 100
  X-RateLimit-Remaining: 0
  X-RateLimit-Reset: 1672531200 (Unix timestamp)
  Retry-After: 30 (seconds)
```

## Scaling Considerations
- Use centralized Redis (not local counters) for distributed rate limiting
- Race condition: Use Lua scripts, NOT distributed locks (too slow)
- Multi-data center: Use local rate limiters + eventual sync, or edge rate limiting
- Per-user vs per-IP vs per-endpoint: different buckets for different granularity
