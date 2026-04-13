# Design Reference: Search Autocomplete

## Requirements
- Return top 10 suggestions as user types (< 100ms)
- Sorted by popularity/relevance
- Handle billions of queries

## Core Data Structure: Trie (Prefix Tree)
- Each node: character + children + cached top-K suggestions
- Optimization: Cache top-10 at every node -> O(1) lookup per prefix
- Limit depth to 10 characters (most queries are short)

## Architecture
```
Query Service:
  User types -> API -> Trie Cache (in-memory) -> Return top-10

Data Pipeline:
  Query Logs -> Aggregator (weekly batch) -> Trie Builder -> Trie DB -> Trie Cache
```

## Optimizations
- Browser caching: `Cache-Control: private, max-age=3600` (don't re-fetch same prefix)
- Data sampling: Only log 1 in N queries (reduce processing)
- AJAX requests: Don't wait for full query, send on each keystroke with debounce (300ms)
- Shard by prefix: 's' gets own shard, 'u'-'z' share one (based on frequency)

## Update Strategy
- Real-time apps (Twitter): Update trie more frequently (hourly)
- Standard apps: Weekly batch rebuild from analytics data
- Filter layer: Block offensive/unwanted suggestions before returning
