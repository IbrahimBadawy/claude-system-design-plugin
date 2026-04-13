# /cost - Infrastructure Cost Estimation

Estimate infrastructure costs for the current system design.

## Usage
```
/cost
/cost <specific_design>
```

## Behavior

### Step 1: Identify Components
List all infrastructure components with their specifications:
- Compute (app servers, workers)
- Database (primary, replicas)
- Cache (Redis instances)
- Storage (object storage, disk)
- Networking (load balancer, CDN, bandwidth)
- Message Queue
- Monitoring/Logging

### Step 2: Estimate Usage
For each component, estimate:
- Instance type/size
- Number of instances
- Hours per month (730 for always-on)
- Storage GB
- Bandwidth GB
- Requests/operations

### Step 3: Calculate Costs

```markdown
## Infrastructure Cost Estimation: [System Name]
**Scale**: [current DAU/QPS]
**Cloud Provider**: [AWS/GCP/Azure]

### Monthly Cost Breakdown

| Component | Spec | Qty | Unit Cost | Monthly Cost |
|-----------|------|-----|-----------|-------------|
| App Servers | t3.medium (2 vCPU, 4GB) | 3 | $30/mo | $90 |
| Database | db.r5.large (2 vCPU, 16GB) | 1 primary + 2 read | $250/mo | $750 |
| Redis Cache | cache.r5.large (13GB) | 2 (cluster) | $180/mo | $360 |
| Load Balancer | ALB | 1 | $25/mo + usage | $50 |
| S3 Storage | 500 GB | - | $0.023/GB | $12 |
| CDN | 1 TB transfer | - | $0.085/GB | $85 |
| Kafka | kafka.m5.large | 3 brokers | $200/mo | $600 |
| Monitoring | Prometheus + Grafana | - | $0 (self-hosted) | $0 |
| **Total** | | | | **$1,947/mo** |

### Yearly Projection
- Monthly: $1,947
- Yearly: $23,364
- With Reserved Instances (1yr): ~$16,355 (30% savings)
- With Reserved Instances (3yr): ~$11,683 (50% savings)

### Cost at Scale

| Scale | Monthly Cost | Notes |
|-------|-------------|-------|
| Current (10K DAU) | $1,947 | As estimated above |
| 10x (100K DAU) | ~$6,000 | Add replicas, larger instances |
| 100x (1M DAU) | ~$25,000 | Sharding, multi-AZ, CDN heavy |
| 1000x (10M DAU) | ~$150,000 | Multi-region, dedicated instances |

### Cost Optimization Suggestions
1. [Use reserved instances for predictable workloads]
2. [Use spot instances for batch processing]
3. [Implement auto-scaling to reduce off-peak costs]
4. [Use S3 lifecycle policies for cold storage]
5. [Optimize queries to reduce database instance size]
```

### Managed vs Self-Hosted Comparison
| Service | Managed Cost | Self-Hosted Cost | Recommendation |
|---------|-------------|-----------------|----------------|
| Database | RDS $750/mo | EC2 $300/mo + ops | Managed (worth the premium) |
| Kafka | MSK $600/mo | EC2 $400/mo + ops | Depends on team expertise |
| Redis | ElastiCache $360/mo | EC2 $150/mo + ops | Managed (HA built-in) |

## Examples
```
/cost
/cost our chat system design
/cost AWS deployment for 100K DAU
```
