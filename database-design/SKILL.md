---
name: database-design
description: Database schema design, optimization, and migration patterns for PostgreSQL, MySQL, and NoSQL databases. Use for designing schemas, writing migrations, or optimizing queries.
license: MIT
source: skillcreatorai/Ai-Agent-Skills
---

# Database Design

## Schema Design Principles

### Normalization Guidelines
```sql
-- Users table (normalized)
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Addresses table (separate entity)
CREATE TABLE addresses (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES users(id) ON DELETE CASCADE,
  street VARCHAR(255),
  city VARCHAR(100),
  country VARCHAR(100),
  is_primary BOOLEAN DEFAULT false
);
```

## Index Design

### Common Index Patterns
```sql
-- B-tree (default) for equality and range queries
CREATE INDEX idx_users_email ON users(email);

-- Composite index (order matters!)
CREATE INDEX idx_orders_user_date ON orders(user_id, created_at DESC);

-- Partial index for specific conditions
CREATE INDEX idx_active_users ON users(email) WHERE deleted_at IS NULL;

-- GIN index for array/JSONB columns
CREATE INDEX idx_posts_tags ON posts USING GIN(tags);
```

## Migration Patterns

### Safe Migration Template
```sql
BEGIN;

-- Add column with default (non-blocking in PG 11+)
ALTER TABLE users ADD COLUMN status VARCHAR(20) DEFAULT 'active';

-- Create index concurrently (doesn't lock table)
CREATE INDEX CONCURRENTLY idx_users_status ON users(status);

COMMIT;
```

### Zero-Downtime Migrations
```
1. Add new column (nullable)
2. Deploy code that writes to both columns
3. Backfill old data
4. Deploy code that reads from new column
5. Remove old column
```

## Query Optimization

### EXPLAIN Analysis
```sql
EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
SELECT * FROM orders WHERE user_id = 123 AND status = 'pending';
```

### Common Optimizations
```sql
-- Use EXISTS instead of IN for large sets
SELECT * FROM users u
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.user_id = u.id);

-- Pagination with keyset (cursor) instead of OFFSET
SELECT * FROM posts
WHERE created_at < '2024-01-01'
ORDER BY created_at DESC
LIMIT 20;
```

## Best Practices

- Use UUIDs for public-facing IDs, SERIAL/BIGSERIAL for internal
- Always add `created_at` and `updated_at` timestamps
- Use soft deletes (`deleted_at`) for important data
- Document schema decisions in migration files
