# /schema - Database Schema Design

Design a detailed database schema for an entity or system.

## Usage
```
/schema <entity_name>
/schema users
/schema orders
/schema the-whole-system
```

## Behavior

### Step 1: Understand Access Patterns
1. What queries will be run against this data?
2. What is the read/write ratio?
3. What are the relationships between entities?
4. What is the expected data volume?
5. Are there any time-series or analytical queries?

### Step 2: Choose Database Type
Based on access patterns, recommend:
- **PostgreSQL**: Complex queries, ACID, relationships, JSON support
- **MongoDB**: Flexible schema, document-oriented, rapid iteration
- **Redis**: Key-value, caching, sessions, leaderboards
- **Cassandra/ScyllaDB**: High write throughput, time-series
- **Elasticsearch**: Full-text search, analytics
- **Neo4j**: Graph relationships, traversals

Justify the choice with trade-offs.

### Step 3: Design Schema

For each table/collection:

```sql
-- Table: [name]
-- Purpose: [what this table stores]
-- Access pattern: [how it's queried]
CREATE TABLE [name] (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    [field]         [TYPE] [CONSTRAINTS],
    ...
    created_at      TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMP WITH TIME ZONE NOT NULL DEFAULT NOW(),
    
    -- Constraints
    CONSTRAINT [name] CHECK ([condition]),
    CONSTRAINT [name] FOREIGN KEY ([field]) REFERENCES [table]([field])
);

-- Indexes (based on query patterns)
CREATE INDEX idx_[table]_[field] ON [table]([field]);
CREATE INDEX idx_[table]_composite ON [table]([field1], [field2]);
CREATE UNIQUE INDEX idx_[table]_unique ON [table]([field]);

-- Partial indexes for common filters
CREATE INDEX idx_[table]_active ON [table]([field]) WHERE status = 'active';
```

### Step 4: Define Relationships
- Document all relationships (1:1, 1:N, N:M)
- Create junction tables for N:M relationships
- Define ON DELETE behavior (CASCADE, SET NULL, RESTRICT)

### Step 5: Query Examples
Show example queries the schema supports:
```sql
-- Get user with their orders
SELECT u.*, o.*
FROM users u
LEFT JOIN orders o ON o.user_id = u.id
WHERE u.id = $1;

-- Analytics query example
SELECT DATE_TRUNC('day', created_at) as day, COUNT(*)
FROM orders
WHERE created_at >= NOW() - INTERVAL '30 days'
GROUP BY 1 ORDER BY 1;
```

### Step 6: Migration Strategy
```sql
-- Migration: 001_create_[table]
-- UP
[CREATE statements]

-- DOWN
[DROP statements]
```

### Step 7: Considerations
- Soft delete vs hard delete strategy
- Audit trail (created_by, updated_by)
- Multi-tenancy (tenant_id column, row-level security)
- Encryption for sensitive fields
- Partitioning strategy for large tables (time-based, range-based)

## Examples
```
/schema users
/schema e-commerce order system
/schema university course enrollment
```
