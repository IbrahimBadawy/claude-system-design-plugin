# /tradeoff - Trade-off Analysis

Structured comparison of two or more design options.

## Usage
```
/tradeoff <option_a> vs <option_b>
/tradeoff <option_a> vs <option_b> vs <option_c>
```

## Behavior

1. Present context for the trade-off
2. Analyze each option across multiple dimensions
3. Provide scenario-based recommendations

### Comparison Dimensions

| Dimension | Description |
|-----------|-------------|
| Performance | Latency, throughput, resource efficiency |
| Scalability | Horizontal/vertical scaling capability |
| Consistency | Data consistency guarantees |
| Availability | Uptime, fault tolerance |
| Complexity | Implementation and operational complexity |
| Cost | Infrastructure and development cost |
| Team Expertise | Learning curve, available talent |
| Ecosystem | Libraries, tools, community support |
| Maintainability | Long-term maintenance burden |
| Maturity | Production readiness, battle-tested |

### Output Format
```
## Trade-off Analysis: [Option A] vs [Option B]

### Context
[Why this decision matters, what problem we're solving]

### Comparison

| Dimension | Option A | Option B |
|-----------|----------|----------|
| Performance | ... | ... |
| Scalability | ... | ... |
| ... | ... | ... |

### Detailed Analysis

#### Option A: [Name]
- Strengths: ...
- Weaknesses: ...
- Best suited for: ...

#### Option B: [Name]
- Strengths: ...
- Weaknesses: ...
- Best suited for: ...

### Recommendation

**Choose [Option A] when:**
- [scenario 1]
- [scenario 2]

**Choose [Option B] when:**
- [scenario 1]
- [scenario 2]

**Final recommendation for YOUR case:** [Option X] because [specific reasons]
```

### Common Trade-offs Reference

- **SQL vs NoSQL**: ACID + joins vs flexibility + scale
- **Monolith vs Microservices**: Simplicity vs independent scaling
- **Push vs Pull**: Low latency vs consumer-paced processing
- **Sync vs Async**: Immediate response vs throughput + decoupling
- **Strong vs Eventual Consistency**: Correctness vs availability
- **Cache-Aside vs Write-Through**: Simplicity vs consistency
- **REST vs gRPC vs GraphQL**: Simplicity vs performance vs flexibility
- **Kafka vs RabbitMQ**: Event streaming vs task routing
- **PostgreSQL vs MongoDB**: Relations + ACID vs flexible schema
- **Redis vs Memcached**: Data structures + persistence vs simplicity
- **Replication vs Sharding**: Read scaling vs write scaling
- **Optimistic vs Pessimistic Locking**: Throughput vs correctness
- **Orchestration vs Choreography**: Control vs decoupling
- **Blue-Green vs Canary**: Safety vs cost

## Examples
```
/tradeoff SQL vs NoSQL for user profiles
/tradeoff Kafka vs RabbitMQ for our event system
/tradeoff monolith vs microservices for a startup
/tradeoff REST vs gRPC for internal service communication
```
