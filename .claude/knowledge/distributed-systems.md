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
- All use epoch numbering (Raft: term, Paxos: ballot, Viewstamped: view)
- Two voting rounds: (1) elect leader, (2) vote on proposal. Quorums MUST overlap.
- Consensus requires majority votes; 2PC requires unanimity

## Linearizability vs Serializability (DDIA Ch.9)
- **Serializability**: Transaction isolation (multiple objects, some serial order OK)
- **Linearizability**: Recency guarantee on individual objects (real-time ordering)
- **Strict serializability** = both combined (SSI is NOT linearizable)
- **Causal consistency** is strongest model that doesn't require coordination (CAP doesn't apply)

## Total Order Broadcast (DDIA Ch.9)
- Reliable delivery + totally ordered delivery
- Equivalent to repeated rounds of consensus
- State machine replication: same writes in same order = consistent replicas
- Proven equivalent to linearizable storage (can implement one from the other)

## 2PC Deep Mechanics (DDIA Ch.9)
- Commit point = coordinator writes decision to disk BEFORE notifying participants
- "In doubt" transactions hold locks and block other transactions
- 3PC exists but requires bounded delays (impractical in async networks)
- Heuristic decisions: emergency escape allowing participants to unilaterally decide (breaks atomicity)

## ZooKeeper Internals (DDIA Ch.9)
- Ephemeral nodes: locks auto-released on session timeout
- Watches: reactive notifications, avoid polling
- Reads are NOT linearizable by default (need sync() or quorum read)
- Data must fit in memory; not for application runtime state

## Data Structures for Scale
- **Bloom Filter**: Probabilistic set membership (false positives OK, no false negatives)
- **HyperLogLog**: Approximate cardinality counting
- **Merkle Tree**: Efficient data synchronization between replicas
- **LSM-Tree / SSTable**: Write-optimized storage engine
- **B-Tree**: Read-optimized storage engine

## Batch Processing (DDIA Ch.10)
- MapReduce shuffle: map partitions by reducer key -> sort -> reducer merges sorted files
- Three join types: sort-merge (reduce-side), broadcast hash (map-side), partitioned hash
- Hot key handling: Pig samples to identify, sends hot keys to random reducers
- Dataflow engines (Spark/Flink/Tez): flexible DAGs, skip unnecessary sorting, pipeline execution
- Spark fault tolerance: RDD lineage recomputation. Flink: checkpointing.
- Pregel model for graphs: BSP, vertices send messages along edges per iteration

## Stream Processing (DDIA Ch.11)
- Log-based brokers (Kafka): append-only, consumer tracks offset, replay possible
- Traditional brokers (AMQP): per-message ack, destructive consumption, no replay
- **CDC (Change Data Capture)**: Parse replication log, make one DB the leader for all derived systems
  - Tools: Debezium (MySQL), Bottled Water (PostgreSQL), Maxwell, GoldenGate
- **Event Sourcing vs CDC**: CDC is transparent (app unaware); event sourcing is explicit (app writes events)
  - Log compaction works for CDC; NOT for event sourcing (need full history)
- **Stream-Table Duality**: State = integral of events; Change stream = derivative of state
- Three stream joins: stream-stream (windowed), stream-table (enrichment), table-table (materialized view)
- Windowing: tumbling (fixed, non-overlap), hopping (fixed, overlap), sliding (interval), session (inactivity gap)
- Exactly-once: microbatching (Spark), checkpointing (Flink), idempotent writes with metadata

## Timeliness vs Integrity (DDIA Ch.12)
- **Timeliness**: Users see up-to-date state (temporary violation = eventual consistency)
- **Integrity**: No corruption, correct derivations (violation = permanent inconsistency)
- Integrity is FAR more important; can be preserved WITHOUT distributed transactions
- End-to-end operation identifiers (UUID) prevent duplicate submissions across all systems
- "Apology workflows" / compensating transactions for loosely interpreted constraints

## Key Numbers
```
L1 cache:           1 ns
L2 cache:          10 ns
RAM:              100 ns
SSD random read:  100 us
HDD seek:          10 ms
Network RTT:        1 ms (same DC), 100 ms (cross-region)
```
