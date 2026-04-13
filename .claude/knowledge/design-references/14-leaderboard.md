# Design Reference: Real-Time Leaderboard

## Requirements
- Real-time ranking of millions of users
- Get top-K, get user's rank, update score
- O(log N) operations

## Solution: Redis Sorted Sets

### Operations
```redis
ZADD leaderboard <score> <user_id>       -- Add/update score
ZINCRBY leaderboard <increment> <user_id> -- Increment score
ZREVRANGE leaderboard 0 9                -- Top 10 (descending)
ZREVRANK leaderboard <user_id>           -- User's rank (0-indexed)
ZSCORE leaderboard <user_id>             -- User's score
ZCOUNT leaderboard <min> <max>           -- Count in score range
```

All operations are O(log N) - handles millions of entries efficiently.

## Scaling
- Single Redis handles millions of entries
- Shard by game/category/league (NOT by user)
- Separate sorted set per time period: `leaderboard:daily:2026-04-13`, `leaderboard:weekly:2026-W15`
- Archive old leaderboards to DB

## Architecture
```
Game Event -> Score Service -> Redis (real-time leaderboard)
                            -> DB (persistent history)
```

## Historical Leaderboards
- Snapshot daily/weekly/monthly leaderboards to DB
- Use DB for historical queries, Redis for current
