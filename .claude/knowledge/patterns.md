# Architecture Patterns & Case Studies

Reference patterns from Alex Xu's System Design Interview books and real-world systems.

## 1. Fan-Out Pattern (News Feed / Timeline)
- **Fan-Out on Write (Push)**: Pre-compute feed at write time. Fast reads, slow writes for celebrities.
- **Fan-Out on Read (Pull)**: Generate feed at read time. No wasted writes, slow reads.
- **Hybrid** (Instagram/Twitter): Push for < 5K followers, pull for celebrities.

## 2. URL Shortening
- Base62 encoding (a-z, A-Z, 0-9), 7 chars = 3.5T unique URLs
- 301 (permanent, cached) vs 302 (temporary, analytics tracking)
- Cache hot URLs (80/20 rule)

## 3. Rate Limiting
- **Token Bucket**: Refill at rate R, burst up to bucket size. Used by AWS, Stripe.
- **Sliding Window Counter**: Weighted formula across windows, ~0.003% error.
- Redis INCR + EXPIRE for distributed. Lua scripts for atomicity.

## 4. Consistent Hashing
- Hash ring with virtual nodes (200 vnodes = ~5% std deviation)
- Adding/removing server affects only fraction of keys
- Used by: Dynamo, Cassandra, Discord, Akamai

## 5. Key-Value Store (Dynamo-style)
- Consistent hashing for partition, N replicas across distinct DCs
- Quorum: W+R > N for strong consistency
- Vector clocks for conflict detection
- Gossip protocol for failure detection
- Merkle trees for anti-entropy sync
- Write: commit log -> memcache -> SSTable

## 6. Chat System
- WebSocket for real-time bidirectional
- Zookeeper for service discovery
- Message sync queue per user
- Presence: heartbeat every 5s, offline after 30s silence
- Storage: Cassandra/HBase for message history, Redis for recent

## 7. Search Autocomplete
- Trie (prefix tree) with top-K suggestions cached at each node
- Limit depth (max 10 chars), cache at API gateway
- Shard by prefix characters, update trie offline (weekly batch)

## 8. Notification System
- Multi-channel: Push (APNs/FCM), SMS (Twilio), Email (SendGrid), In-App (WebSocket)
- Priority queue, rate limiting per user, retry with backoff
- Template system, user preferences (opt-in/opt-out)
- Dedup with idempotency keys

## 9. Video Platform (YouTube)
- DAG model for transcoding (preprocessor -> scheduler -> workers)
- Adaptive bitrate streaming (MPEG-DASH, HLS)
- Pre-signed upload URLs, CDN for popular content
- Cost: serve popular from CDN, less popular on-demand

## 10. Payment System
- Exactly-once = at-least-once (retry) + at-most-once (idempotency key)
- Double-entry bookkeeping (every txn has debit + credit)
- State machine: CREATED -> PROCESSING -> COMPLETED/FAILED/REFUNDED
- Daily reconciliation: compare internal ledger with PSP records
- Only retry on network errors, NOT on business errors

## 11. File Sync (Google Drive)
- Block-level sync (4MB blocks), SHA-256 hash per block
- Delta sync: upload only changed blocks
- Long polling for notifications (not WebSocket - infrequent, unidirectional)
- Strong consistency required (relational DB for metadata)

## 12. Web Crawler
- BFS with URL frontier (politeness: per-host queue, priority queue)
- Bloom filter for URL dedup, content hash for page dedup
- Respect robots.txt, rate limit per domain

## 13. Unique ID Generation (Snowflake)
```
| 1 bit sign | 41 bits timestamp | 5 bits DC | 5 bits machine | 12 bits sequence |
```
- 69 years, 32 DCs, 32 machines/DC, 4096 IDs/ms/machine

## 14. Leaderboard (Redis Sorted Sets)
- ZADD, ZREVRANGE (top-K), ZREVRANK (user rank), ZINCRBY
- Shard by game/category, separate sorted set per time period

## 15. Proximity Service (Yelp)
- Geohash or Quadtree for spatial indexing
- Quadtree: recursively subdivide into 4 quadrants
- ~1.71GB memory for global index (fits single server, use read replicas)

## 16. Stock Exchange
- All on single server for micro-second latency
- Shared memory as event bus (no disk)
- Single-threaded, CPU-pinned (no context switch, no locks)
- Sequencer for deterministic event ordering

## Decision Cheat Sheets

### Monolith vs Microservices
| Monolith | Microservices |
|----------|---------------|
| Small team (< 5) | Large team (> 10) |
| Early stage/MVP | Mature product |
| Simple domain | Complex, clear boundaries |
| Need speed | Need independent scaling |

### SQL vs NoSQL Decision Tree
```
Need ACID? -> SQL
Need complex JOINs? -> SQL
Schema changes often? -> Document DB
Simple key-value at scale? -> Key-Value DB
High write throughput? -> Wide-Column DB
Complex relationships? -> Graph DB
Full-text search? -> Search Engine
Time-series? -> Time-Series DB
```

### Message Delivery
- **At-most once**: May lose, no retry (monitoring metrics)
- **At-least once**: No loss, may duplicate (dedup with unique keys)
- **Exactly once**: Retry + idempotency (financial systems)
