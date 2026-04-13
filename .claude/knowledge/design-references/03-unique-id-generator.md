# Design Reference: Distributed Unique ID Generator

## Requirements
- Globally unique, 64-bit numeric, roughly time-sorted, low latency, highly scalable

## Approaches Compared
| Method | Pros | Cons | Best For |
|--------|------|------|----------|
| UUID | Simple, no coordination | 128-bit, not sortable | When sortability not needed |
| DB auto-increment | Simple, sortable | SPOF, not distributed | Small scale |
| Snowflake | Sortable, 64-bit, distributed | Clock sync needed | Most use cases |
| ULID | Sortable, 128-bit, string-friendly | Larger than Snowflake | String-based IDs |

## Snowflake (Recommended)
```
| 1 bit (sign=0) | 41 bits timestamp | 5 bits DC | 5 bits machine | 12 bits sequence |
```
- 41 bits timestamp: ~69 years from epoch
- 5 bits datacenter: 32 DCs
- 5 bits machine: 32 machines per DC
- 12 bits sequence: 4,096 IDs per millisecond per machine
- Total capacity: 4,096 * 1,000 * 1,024 = ~4 billion IDs/sec

## Clock Sync
- NTP sync across all machines
- If clock goes backward: wait or reject until caught up
