# Distributed Systems Concepts

Core concepts from Designing Data-Intensive Applications (DDIA) and system design interviews.

## CAP Theorem
- **Consistency**: All nodes see the same data at the same time
- **Availability**: Every request gets a response
- **Partition Tolerance**: System works despite network failures
- Network partitions are INEVITABLE -> choose CP or AP
- Real-world: it's a spectrum, not binary

## ACID vs BASE
- **ACID** (SQL): Atomicity, Consistency, Isolation, Durability
- **BASE** (NoSQL): Basically Available, Soft state, Eventually consistent

## Consistency Models (ordered by strength)
1. **Strong/Linearizable**: Latest write always visible (expensive)
2. **Sequential**: Operations appear in some total order
3. **Causal**: Causally related operations are ordered
4. **Eventual**: All replicas converge eventually (cheapest)

## Replication Strategies
- **Leader-based (Master-Slave)**: One leader for writes, followers for reads
  - Sync: follower guaranteed up-to-date, any failure blocks
  - Async: no blocking, risk of data loss on leader failure
  - Semi-sync: one sync follower + async others (common)
- **Multi-leader**: Multiple write points, conflict resolution needed
- **Leaderless (Dynamo)**: Quorum reads/writes, eventual consistency

## Replication Lag Solutions
- **Read-after-write**: Read own writes from leader
- **Monotonic reads**: Same user always reads from same replica
- **Consistent prefix reads**: Causal ordering preserved

## Partitioning (Sharding)
- **By key range**: Good for range queries, risk of hotspots
- **By hash**: Even distribution, destroys ordering
- **Compound key**: Hash first part (partition), sort second part (within)
- Rebalancing: Fixed partitions, dynamic splitting, or proportional to nodes

## Transactions
- **Read Committed**: No dirty reads/writes
- **Snapshot Isolation**: Consistent point-in-time snapshot (MVCC)
- **Serializable**: Three approaches:
  1. Actual serial execution (single-threaded)
  2. Two-Phase Locking (2PL) - pessimistic
  3. Serializable Snapshot Isolation (SSI) - optimistic

## Distributed System Challenges
- **Unreliable networks**: Messages lost, delayed, duplicated, reordered
- **Unreliable clocks**: Time-of-day can jump (NTP), use monotonic for durations
- **Process pauses**: GC, context switches create uncertainty
- **Partial failures**: Some nodes fail while others work
- Solution: Quorums (majority votes), fencing tokens

## Failure Detection
- **Gossip Protocol**: Decentralized heartbeat propagation
- **Phi Accrual Detector**: Probabilistic failure detection
- Timeouts: no perfect value (too short = false positives, too long = slow detection)

## Consensus Algorithms
- **Raft**: Leader election, log replication (etcd, CockroachDB)
- **Paxos**: Classic consensus (Google Spanner)
- Used for: leader election, distributed locks, atomic broadcasts

## Data Structures for Scale
- **Bloom Filter**: Probabilistic set membership (false positives OK, no false negatives)
- **HyperLogLog**: Approximate cardinality counting
- **Merkle Tree**: Efficient data synchronization between replicas
- **LSM-Tree / SSTable**: Write-optimized storage engine
- **B-Tree**: Read-optimized storage engine

## Batch vs Stream Processing
- **Batch** (MapReduce/Spark): High throughput, high latency, bounded data
- **Stream** (Kafka/Flink): Low latency, unbounded data, event-driven
- **Lambda Architecture**: Batch + stream layers (being replaced by)
- **Kappa Architecture**: Unified stream processing (Kafka-centric)

## Key Numbers
```
L1 cache:           1 ns
L2 cache:          10 ns
RAM:              100 ns
SSD random read:  100 us
HDD seek:          10 ms
Network RTT:        1 ms (same DC), 100 ms (cross-region)
```
