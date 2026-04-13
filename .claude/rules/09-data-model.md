---
description: Data model design is critical - spend extra time getting it right
globs: "*"
---

# Rule: Data Model is King

1. **Access patterns FIRST, schema SECOND**: Design the schema based on
   how data will be queried, not just how it's structured logically.

2. **Index strategy must match query patterns**: Every WHERE clause,
   JOIN condition, and ORDER BY should have a supporting index.

3. **Choose the right database for the job**:
   - Complex queries + ACID -> SQL (PostgreSQL)
   - Simple key-value lookups -> Redis/DynamoDB
   - Flexible schema + documents -> MongoDB
   - High write throughput + time-series -> Cassandra/TimescaleDB
   - Full-text search -> Elasticsearch
   - Graph relationships -> Neo4j
   - AI embeddings -> pgvector/Pinecone

4. **Plan for data migration from the start**:
   - Schema versioning
   - Backward-compatible migrations
   - Rollback scripts
   - Data backfill strategies

5. **Consider data growth**: Estimate storage for 1 year, 3 years, 5 years.
   Plan partitioning/archival strategy early.

6. **Idempotency for writes**: Every write operation must answer:
   "What happens if this request is sent twice?"
