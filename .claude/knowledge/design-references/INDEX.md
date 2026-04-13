# Design Reference Templates

Pre-built architecture references for common system designs.
Based on Alex Xu's System Design Interview books and real-world architectures.

When designing a system that resembles one of these, use the reference as a starting point
and customize for the specific requirements.

## Available References

### Core Infrastructure
| # | System | File | Key Patterns |
|---|--------|------|-------------|
| 01 | [Rate Limiter](./01-rate-limiter.md) | Token Bucket, Sliding Window, Redis | Rate limiting, distributed counters |
| 02 | [URL Shortener](./02-url-shortener.md) | Base62, hash collision, redirect | ID generation, caching, analytics |
| 03 | [Unique ID Generator](./03-unique-id-generator.md) | Snowflake, UUID, DB sequence | Distributed ID, time-sorting |
| 04 | [Key-Value Store](./04-key-value-store.md) | Consistent hashing, quorum, Dynamo | Partitioning, replication, consistency |

### Communication & Social
| # | System | File | Key Patterns |
|---|--------|------|-------------|
| 05 | [Chat System](./05-chat-system.md) | WebSocket, presence, sync queue | Real-time, message ordering, fanout |
| 06 | [News Feed](./06-news-feed.md) | Fan-out on write/read, hybrid | Timeline, celebrity problem, cache layers |
| 07 | [Notification System](./07-notification-system.md) | Multi-channel, priority queue, templates | Push/SMS/Email, rate limiting, dedup |

### Search & Discovery
| # | System | File | Key Patterns |
|---|--------|------|-------------|
| 08 | [Search Autocomplete](./08-search-autocomplete.md) | Trie, top-K, prefix caching | Data structures, real-time suggestions |
| 09 | [Web Crawler](./09-web-crawler.md) | BFS, URL frontier, Bloom filter | Distributed crawling, dedup, politeness |
| 10 | [Nearby/Proximity Service](./10-proximity-service.md) | Geohash, Quadtree, spatial index | Location-based, geospatial queries |

### Media & Storage
| # | System | File | Key Patterns |
|---|--------|------|-------------|
| 11 | [Video Platform (YouTube)](./11-video-platform.md) | DAG transcoding, adaptive bitrate, CDN | Upload pipeline, streaming, cost |
| 12 | [File Storage (Google Drive)](./12-file-storage.md) | Block sync, delta, conflict resolution | Chunked upload, versioning, notifications |

### Commerce & Finance
| # | System | File | Key Patterns |
|---|--------|------|-------------|
| 13 | [Payment System](./13-payment-system.md) | Idempotency, double-entry, reconciliation | Exactly-once, PSP, state machine |
| 14 | [Leaderboard](./14-leaderboard.md) | Redis Sorted Sets, real-time ranking | ZADD, ZREVRANGE, sharding by game |

### Enterprise
| # | System | File | Key Patterns |
|---|--------|------|-------------|
| 15 | [Metrics Monitoring](./15-metrics-monitoring.md) | Time-series DB, Kafka, alerting | Prometheus, Grafana, push vs pull |
| 16 | [Stock Exchange](./16-stock-exchange.md) | Matching engine, sequencer, shared memory | Ultra-low latency, single-threaded |

## How to Use
When `/design <system>` is invoked and the system resembles a reference:
1. Load the relevant reference template
2. Use it as the starting architecture
3. Customize based on specific requirements
4. Call out differences from the reference
