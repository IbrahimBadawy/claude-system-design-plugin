# Design Reference: URL Shortener

## Requirements
- Shorten long URLs (write), redirect short URLs (read)
- High read volume (100:1 read/write ratio)
- Short URLs: 7 characters, [0-9, a-z, A-Z] = 62^7 = ~3.5T unique URLs

## Architecture
```
Write: Long URL -> API -> Generate ID -> Base62 encode -> Store mapping -> Return short URL
Read:  Short URL -> API -> Check Cache -> DB lookup -> 301/302 Redirect
```

## Key Decisions
- **301 vs 302**: 301 (permanent, browser caches - less server load) vs 302 (temporary, every hit tracked - better for analytics). Choose 302 if analytics matter.
- **Base62 conversion**: Convert unique numeric ID to base-62 string. Deterministic, no collision.
- **Hash + collision**: Hash long URL (CRC32/MD5), take first 7 chars. On collision, append and retry. Use Bloom filter for fast collision detection.

## Schema
```sql
urls: id (PK), short_code (UNIQUE INDEX), long_url, user_id, created_at, expires_at, click_count
```

## Caching
- 80/20 rule: 20% of URLs get 80% of traffic
- Cache hot URLs in Redis: `short_code -> long_url`
- TTL: 24 hours, LRU eviction

## Scaling
- Read-heavy: multiple read replicas + Redis cache
- Shard by first character of short_code or hash-based
- CDN for redirect (optional, adds complexity)
