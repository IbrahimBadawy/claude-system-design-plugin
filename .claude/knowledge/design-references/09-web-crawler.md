# Design Reference: Web Crawler

## Requirements
- Crawl billions of pages scalably
- Robust (handle errors, traps, malicious content)
- Polite (respect robots.txt, rate limit per domain)
- Extensible (pluggable modules)

## Architecture
```
Seed URLs -> URL Frontier -> HTML Downloader -> Content Parser
                 ^                                    |
                 |              Content Seen? (hash dedup)
                 |                    |
            URL Filter <-- URL Extractor
                 |
          URL Seen? (Bloom filter)
```

## Key Components
- **URL Frontier**: Prioritized + politeness queues. Per-host queue with delay between requests.
- **Bloom Filter**: Probabilistic URL dedup (false positives OK, no false negatives)
- **Content Hash**: SHA-256 of page content for duplicate detection (~30% of web is duplicated)
- **DNS Cache**: Avoid repeated DNS lookups (major bottleneck at scale)

## Politeness
- One queue per host, one worker per queue
- Delay between requests to same host (respect Crawl-delay in robots.txt)
- Priority based on PageRank, update frequency, domain authority

## Robustness
- Consistent hashing for URL distribution across crawlers
- Save crawl state for recovery
- Max URL length to avoid spider traps
- Timeout on all HTTP requests
