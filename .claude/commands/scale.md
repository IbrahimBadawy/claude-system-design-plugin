# /scale - Scaling Strategy

Design a scaling strategy for a specific component or the entire system.

## Usage
```
/scale <component>
/scale database
/scale api-layer
/scale the-whole-system
```

## Behavior

### Phase 1: Current State Analysis
1. Understand current architecture and bottleneck
2. Identify the limiting factor (CPU, memory, disk I/O, network, connections)
3. Measure or estimate current capacity
4. Identify the "breaking point"

### Phase 2: Scaling Phases

| Phase | Scale | Strategy | When |
|-------|-------|----------|------|
| **Phase 0** | Current | Optimize what you have | Now |
| **Phase 1** | 10x | Quick wins + read scaling | Growth detected |
| **Phase 2** | 100x | Architectural changes | Limits hit |
| **Phase 3** | 1000x | Major redesign | Global scale |

### Phase 0: Optimize (Before scaling out)
- Add indexes for slow queries
- Optimize N+1 queries
- Add connection pooling
- Enable compression
- Optimize payload sizes
- Profile and fix hot paths

### Phase 1: 10x Growth
**Application Layer:**
- Add load balancer (Nginx/HAProxy/ALB)
- Run multiple stateless app server instances
- Autoscaling based on CPU/memory/QPS
- Session data to Redis

**Database:**
- Add read replicas for read-heavy workloads
- Implement caching layer (Redis)
- Connection pooling (PgBouncer)
- Query optimization and better indexes

**Infrastructure:**
- CDN for static assets
- Message queue for async processing
- Object storage for files/media

### Phase 2: 100x Growth
**Application Layer:**
- Microservice extraction for independently scaling components
- API Gateway for routing, rate limiting, auth
- Service mesh for service-to-service communication

**Database:**
- Horizontal sharding (choose shard key carefully)
- CQRS: separate read and write models
- Different databases for different access patterns (polyglot persistence)
- Event sourcing for audit trail and replay

**Infrastructure:**
- Multi-region deployment with geoDNS
- Edge computing for latency-sensitive operations
- Kafka for event streaming and decoupling
- Distributed caching (Redis Cluster)

### Phase 3: 1000x Growth
**Application Layer:**
- Cell-based architecture (isolated failure domains)
- Edge-native processing

**Database:**
- Global distributed database (CockroachDB, Spanner)
- Data lake for analytics
- Real-time stream processing (Flink)

**Infrastructure:**
- Multi-cloud or hybrid cloud
- Custom CDN / own edge network
- Hardware optimization (custom servers, FPGA)

### Component-Specific Strategies

**Database Scaling:**
```
Vertical -> Read Replicas -> Caching -> Sharding -> NewSQL/Distributed
```

**API Scaling:**
```
Single Server -> Load Balancer + Multiple -> Autoscale -> Microservices -> Cell Architecture
```

**Cache Scaling:**
```
Local Cache -> Single Redis -> Redis Cluster -> Multi-tier Cache (L1 local + L2 Redis)
```

**Queue Scaling:**
```
Single Queue -> Partitioned Queue -> Kafka Cluster -> Multi-region Kafka
```

### For Each Phase, Document:
1. What changes are needed
2. Expected capacity after change
3. Trade-offs introduced
4. Estimated effort (person-weeks)
5. Prerequisites and dependencies
6. Rollback plan

## Examples
```
/scale database
/scale our payment processing pipeline
/scale the notification service to handle 1M/minute
```
