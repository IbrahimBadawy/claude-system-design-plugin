# Design Reference: Nearby/Proximity Service (Yelp)

## Requirements
- Find nearby businesses within radius
- Fast query (< 100ms)
- Read-heavy (95% reads)

## Approaches
| Method | How | Pros | Cons |
|--------|-----|------|------|
| **Geohash** | Encode lat/lng to string prefix | Simple, DB-friendly | Edge cases at boundaries |
| **Quadtree** | Recursive 4-way space partition | Dynamic, memory-efficient | In-memory only |
| **PostGIS** | PostgreSQL extension | Rich queries, standard SQL | Heavier setup |

## Quadtree (Recommended for in-memory)
- Recursively subdivide world into 4 quadrants
- Leaf node when businesses < threshold (e.g., 100)
- ~1.71 GB memory for global index -> fits single server!
- Use read replicas for scaling (no sharding needed)

## Architecture
```
Client (lat, lng, radius) -> LBS (Location-Based Service) -> Quadtree (in-memory)
                                                                 |
                                                          Business DB (PostgreSQL)
```

## Key Design Points
- Quadtree built at startup, updated periodically (businesses don't move often)
- Business data in PostgreSQL, geo-index in memory
- Cache popular queries (same area searched by many users)
- Read replicas of the LBS service for horizontal scaling
