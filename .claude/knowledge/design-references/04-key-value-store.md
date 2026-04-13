# Design Reference: Distributed Key-Value Store

## Requirements
- Simple API: put(key, value), get(key)
- High availability, partition tolerant
- Tunable consistency (strong to eventual)
- Automatic scaling (add/remove nodes)

## Architecture (Dynamo-style)
```
Client -> Any Node (coordinator) -> Replicate to N nodes
          |
    Consistent Hash Ring (with virtual nodes)
```

## Core Components

### 1. Data Partition: Consistent Hashing
- Hash ring (SHA-1: 0 to 2^160)
- Each server has ~200 virtual nodes (even distribution)
- Key maps to first server clockwise on ring
- Adding/removing server affects only adjacent keys

### 2. Replication: N copies
- Replicate to next N unique physical servers clockwise
- Place replicas in different data centers for disaster recovery

### 3. Consistency: Quorum (W + R > N)
| Config | Behavior |
|--------|----------|
| W=1, R=N | Fast write, slow read |
| W=N, R=1 | Slow write, fast read |
| W=R=N/2+1 | Balanced (typical: N=3, W=R=2) |
| W+R > N | Strong consistency guaranteed |
| W+R <= N | Eventual consistency (faster) |

### 4. Conflict Resolution: Vector Clocks
- Each node maintains [server_id, counter] pairs
- Detect ancestor (no conflict) vs sibling (conflict)
- Client resolves conflicts (last-write-wins or merge)

### 5. Failure Detection: Gossip Protocol
- Each node maintains heartbeat counters for all nodes
- Periodically sends heartbeat list to random nodes
- No heartbeat for X seconds = suspected failure
- More efficient than all-to-all heartbeating

### 6. Temporary Failure: Sloppy Quorum + Hinted Handoff
- If a node is down, write to next healthy node (hint stored)
- When original node recovers, hinted data is transferred back

### 7. Permanent Failure: Merkle Trees
- Hash tree for each key range
- Compare root hashes to quickly find inconsistencies
- Only sync the differing ranges (efficient)

## Write Path (Cassandra-like)
```
Request -> Commit Log (append-only, for durability)
       -> Memtable (in-memory sorted structure)
       -> When Memtable full -> Flush to SSTable on disk
       -> Background compaction merges SSTables
```

## Read Path
```
Request -> Check Memtable (memory)
       -> If not found: Bloom Filter check (which SSTables might have it)
       -> Read from SSTables
       -> Return most recent value
```

## CAP Trade-off
- Network partitions are inevitable -> choose CP or AP
- CP (strong consistency): Block writes during partition
- AP (high availability): Accept writes, resolve conflicts later
- Real-world: tunable per-request via W and R values
