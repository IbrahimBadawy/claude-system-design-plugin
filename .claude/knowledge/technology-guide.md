# Technology Selection Guide (2026)

## Databases

### Relational (SQL)
| Technology | Best For | Notes |
|-----------|---------|-------|
| **PostgreSQL** | Default choice 2026. ACID, JSON (JSONB), full-text search, pgvector for AI | Most versatile, massive ecosystem |
| MySQL | Web apps, read-heavy workloads | Simpler, widely deployed |

### Document (NoSQL)
| Technology | Best For | Notes |
|-----------|---------|-------|
| **MongoDB** | Flexible schema, rapid iteration, nested documents | Schema-on-read, good for prototyping |
| DynamoDB | Key-value + document, serverless, AWS-native | Single-digit ms latency, pay-per-request |

### Key-Value
| Technology | Best For | Notes |
|-----------|---------|-------|
| **Redis** | Caching, sessions, leaderboards, pub/sub, rate limiting | Sub-ms latency, rich data structures. Note: Valkey (fork) gaining traction |
| Memcached | Simple caching, multi-threaded | Simpler than Redis, no persistence |

### Wide-Column
| Technology | Best For | Notes |
|-----------|---------|-------|
| Cassandra | High write throughput, time-series, geo-distributed | AP system, eventual consistency |
| ScyllaDB | Cassandra-compatible, higher performance | C++ rewrite of Cassandra |

### NewSQL
| Technology | Best For | Notes |
|-----------|---------|-------|
| CockroachDB | Distributed ACID, global scale | PostgreSQL-compatible wire protocol |
| TiDB | MySQL-compatible distributed SQL | Good for MySQL migrations |
| YugabyteDB | PostgreSQL-compatible distributed | Strong consistency + horizontal scale |

### Graph
| Technology | Best For | Notes |
|-----------|---------|-------|
| Neo4j | Social networks, recommendation engines, knowledge graphs | Cypher query language |

### Search
| Technology | Best For | Notes |
|-----------|---------|-------|
| Elasticsearch | Full-text search, log analytics, faceted search | Powerful but resource-heavy |
| Meilisearch | Simple search, typo-tolerant, fast setup | Lighter alternative |

### Time-Series
| Technology | Best For | Notes |
|-----------|---------|-------|
| TimescaleDB | Time-series on PostgreSQL | Familiar SQL interface |
| InfluxDB | Metrics, IoT data | Purpose-built, high ingest rate |

### Vector (AI/ML)
| Technology | Best For | Notes |
|-----------|---------|-------|
| pgvector | Vector search on PostgreSQL | Great if already using PG |
| Pinecone | Managed vector DB, RAG pipelines | Serverless, easy to start |
| Milvus | Self-hosted, high performance | Open source, GPU support |

## Message Queues

| Technology | Best For | Key Trait |
|-----------|---------|-----------|
| **Kafka** | Event streaming, log aggregation, high throughput | Distributed log, replayable, KRaft mode |
| RabbitMQ | Task routing, RPC, smaller systems | Flexible routing, degrades under backpressure |
| Pulsar | Multi-tenant, geo-distributed | Separate compute/storage |
| BullMQ | Background jobs on Redis | Simple, Node.js ecosystem |

## Caching

| Strategy | How It Works | Best For |
|----------|-------------|----------|
| Cache-Aside | App checks cache, loads from DB on miss | Read-heavy, tolerates stale |
| Write-Through | Write to cache + DB simultaneously | Strong consistency |
| Write-Behind | Write to cache, async to DB | Write-heavy, eventual consistency |
| Read-Through | Cache auto-loads from DB on miss | Simplify app code |

## Load Balancing
- **L4 (Transport)**: TCP-level, faster, less flexible. HAProxy, NLB.
- **L7 (Application)**: HTTP-level, content-based routing. Nginx, ALB.
- **Algorithms**: Round Robin, Least Connections, Weighted, Consistent Hashing.

## Container Orchestration
- **Kubernetes**: Dominant, complex. Best for large-scale production.
- **Docker Compose**: Development and small deployments.
- **Cloud Run / Fargate**: Serverless containers, auto-scaling.
- **Nomad**: Kubernetes alternative, simpler.

## Observability Stack (2026)
- **Collection**: OpenTelemetry (GA for all signals)
- **Metrics**: Prometheus (67% production adoption)
- **Visualization**: Grafana
- **Tracing**: Jaeger or Grafana Tempo
- **Logging**: Grafana Loki or ELK Stack
- **Alerting**: Alertmanager + PagerDuty/OpsGenie

## API Styles

| Style | Best For | Notes |
|-------|---------|-------|
| REST | Standard CRUD, public APIs | Simple, cacheable, well-understood |
| gRPC | Internal service-to-service, low latency | Binary protocol, code generation, streaming |
| GraphQL | Flexible client queries, mobile apps | Reduces over-fetching, complex caching |
| WebSocket | Real-time bidirectional | Chat, live updates, collaborative editing |
| SSE | Server-to-client streaming | Simpler than WebSocket for one-way |

## Authentication
- **JWT**: Stateless, short-lived access + long-lived refresh tokens
- **OAuth 2.0**: Third-party authorization
- **OpenID Connect**: Authentication on top of OAuth 2.0
- **API Keys + HMAC**: Server-to-server authentication
- **mTLS**: Service mesh authentication
