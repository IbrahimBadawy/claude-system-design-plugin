# Design Reference: Stock Exchange (Ultra-Low Latency)

## Requirements
- Match buy/sell orders in microseconds
- Deterministic event ordering
- Zero data loss
- Handle millions of orders/day

## Architecture (UNIQUE - everything on single server)
```
Client Gateway -> Sequencer -> Order Manager -> Matching Engine
                                                     |
                                              Market Data Publisher
```

**Why single server?** Network hops = latency. Shared memory = fastest IPC.

## Key Design Principles
1. **Single server**: All critical-path components on ONE machine
2. **Shared memory as event bus**: No disk, no network for critical path
3. **Single-threaded**: No locks, no context switches, CPU-pinned
4. **Sequential processing**: Sequencer assigns deterministic order
5. **No containers**: Bare metal for maximum performance

## Matching Engine
- Order book: sorted buy orders (descending) + sorted sell orders (ascending)
- Match when best buy >= best sell
- O(1) for best bid/ask, O(log N) for insert
- Data structure: price-level map with FIFO queue per level

## Reliability
- Sequencer logs every event (write-ahead log)
- Hot standby on separate machine
- Replay log to recover state after failure
- Checkpoint state periodically

## This design is the EXCEPTION to normal rules
- NOT microservices (single process)
- NOT stateless (stateful by design)
- NOT cloud-native (bare metal)
- NOT horizontally scaled (vertically optimized)
