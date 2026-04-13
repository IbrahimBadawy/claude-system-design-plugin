# Design Reference: News Feed / Timeline

## Requirements
- Users see posts from people they follow, ordered by time/relevance
- Fast feed loading (< 500ms)
- Handle celebrities (millions of followers)
- Support rich content (text, images, video)

## Architecture
```
Publishing:
  User posts -> Post Service -> DB + Cache -> Fanout Service -> Followers' Feed Caches

Reading:
  User opens feed -> Feed Service -> Feed Cache (pre-computed post IDs)
                                         -> Hydrate with full post data from Post Cache
                                         -> Return to client
```

## Fanout Models

### Fan-Out on Write (Push) - for normal users
- When user posts: immediately write post_id to ALL followers' feed caches
- **Pro**: Feed read is instant (pre-computed)
- **Con**: Slow write for users with millions of followers, wasted compute for inactive users

### Fan-Out on Read (Pull) - for celebrities
- When user opens feed: fetch latest posts from all followed users in real-time
- **Pro**: No wasted writes
- **Con**: Slow read (merge & sort at read time)

### Hybrid (Instagram/Twitter) - RECOMMENDED
- **Push** for users with < 5K followers
- **Pull** for celebrities with > 5K followers
- Merge at read time

## Fanout Service Flow
1. User posts -> Post Service stores in DB + Post Cache
2. Fanout Service fetches friend list from Graph DB
3. Filter by user settings (muted, blocked)
4. For each follower: write `<post_id, user_id>` to their Feed Cache
5. Notification Service sends push notification

## Feed Cache Structure (Redis)
```
Key: feed:{user_id}
Value: Sorted Set of post_ids (scored by timestamp)
Max size: 800 entries (older entries evicted)

ZADD feed:user123 1672531200 post_456
ZREVRANGE feed:user123 0 19  -- Get latest 20 posts
```

## Cache Architecture (5 Layers)
| Cache | Contents | Why |
|-------|----------|-----|
| News Feed | List of post_ids per user | Fast feed loading |
| Content | Full post data (text, media URLs) | Avoid DB reads |
| Social Graph | Follower/following relationships | Fast fanout |
| Action | Likes, comments, shares per post | Engagement data |
| Counters | Like count, follower count | Display metrics |

## Data Model
```sql
-- Posts
posts: id, user_id, content, media_urls[], created_at

-- Feed (denormalized, or in Redis)
feed: user_id, post_id, created_at

-- Social Graph (Graph DB or adjacency list)
follows: follower_id, followee_id, created_at
```

## Scaling
- Shard feed cache by user_id
- Separate read and write paths (CQRS)
- Async fanout via message queue (Kafka)
- Celebrity detection: check follower count before fanout decision
