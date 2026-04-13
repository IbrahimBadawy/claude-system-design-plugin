# Design Reference: Chat System

## Requirements
- 1-on-1 and group messaging
- Real-time delivery (< 500ms)
- Online/offline presence
- Message history and multi-device sync
- Push notifications for offline users

## Architecture
```
Client (WebSocket) -> Load Balancer -> Chat Server (stateful)
                                           |
                      +--------------------+--------------------+
                      |                    |                    |
              Message Sync Queue    Service Discovery     Push Notification
              (per-user queue)      (Zookeeper)           (APNs/FCM)
                      |
              Key-Value Store (message history)
              (Cassandra / HBase)
```

## Key Decisions

### Communication Protocol: WebSocket
- Bidirectional, persistent connection
- Starts as HTTP, upgrades to WS
- Used for BOTH send and receive
- Why not long polling: sender/receiver may hit different servers, can't detect disconnect well

### Storage: Cassandra or HBase
- Write-heavy workload (billions of messages)
- Time-series access pattern (recent messages read most)
- Wide-column store handles this well
- Key design: partition by channel_id, sort by message_id (time-based)

### Message ID: Local Sequence (not global Snowflake)
- Only needs to be unique and ordered within a channel
- Auto-increment per channel is sufficient and simpler

## Data Models
```
-- 1-on-1 messages
message_id: bigint (PK, time-ordered)
from_user_id: uuid
to_user_id: uuid
content: text
created_at: timestamp

-- Group messages
channel_id: uuid (partition key)
message_id: bigint (sort key within channel)
from_user_id: uuid
content: text
created_at: timestamp
```

## Message Flow

### 1-on-1
1. User A sends message via WebSocket to Chat Server A
2. Chat Server A generates message_id, stores in KV store
3. Chat Server A pushes to User B's message sync queue
4. If User B is online: Chat Server B delivers via WebSocket
5. If User B is offline: Push notification sent

### Group (small groups < 500)
1. User sends message to channel
2. Message copied to each member's sync queue
3. Each member's chat server delivers

### Multi-device Sync
- Each device tracks `cur_max_message_id`
- On reconnect: fetch all messages with `id > cur_max_message_id`

## Presence Service
- KV store: `{user_id: {status: online, last_active: timestamp}}`
- **Heartbeat**: Client sends every 5 seconds
- **Offline**: No heartbeat for 30 seconds = mark offline
- **Fan-out**: Use pub/sub per friend pair. For large groups: fetch on-demand only

## Scaling
- Chat servers are stateful (WS connections) -> use service discovery (Zookeeper) to route
- Shard message storage by channel_id
- Separate read path (history) from write path (real-time)
- Redis for recent messages cache, Cassandra for full history
