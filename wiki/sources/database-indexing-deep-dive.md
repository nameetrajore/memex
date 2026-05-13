---
title: "Database Indexing Deep Dive"
type: source
tags: [system-design, databases, indexing, b-tree, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [database-indexing-deep-dive]
---

## Summary

Comprehensive guide to database indexing from [[Hello Interview]]. Covers how indexes work at the storage level, then examines each index type: B-trees (default), LSM trees (write-optimized), hash indexes (exact match), geospatial (geohash, quadtree, R-tree), and inverted indexes (full-text search). Also covers optimization patterns: composite and covering indexes.

## How Indexes Work

- Data stored as heap files (rows in no order). Without indexes: full sequential scan.
- Indexes are separate data structures that provide a structured path to the data, minimizing page reads.
- **Cost**: extra disk space, write overhead (every insert/update touches all indexes). Don't over-index.
- For small tables (few hundred rows), sequential scan may be faster than index traversal.

## Index Types

### B-Tree (Default)
- Self-balancing tree with sorted keys. Nodes sized to fit one disk page (~8KB). 2–3 page reads to find any record.
- Supports exact match AND range queries AND sorting. Handles random inserts/deletes while staying balanced.
- PostgreSQL uses B-trees for primary keys, unique constraints, and most indexes by default.
- **When in doubt, use B-tree.**

### LSM Tree (Log-Structured Merge Tree)
- Write-optimized: batch writes in memory (memtable) → flush to immutable SSTables on disk → background compaction.
- Converts random writes to sequential writes. Much faster writes than B-trees.
- **Read cost**: must check memtable + all SSTables. Mitigated by Bloom filters, sparse indexes, compaction.
- Trade-off: faster writes, slower reads. Use for write-heavy workloads (metrics, logging, IoT, time-series).
- Used by: Cassandra, RocksDB, DynamoDB.

### Hash Index
- Persistent hashmap: O(1) exact-match lookups. No range queries, no sorting.
- Rare in practice — B-trees are nearly as fast for exact match while being more flexible.
- Shines in-memory: Redis uses hash tables as primary structure.
- Don't overemphasize in interviews.

### Geospatial Indexes
- Regular B-trees on lat/lon fail for proximity (2D problem with 1D indexes).
- **Geohash**: convert 2D coordinates to 1D string preserving proximity. Use B-tree on geohash strings. Used by Redis (`GEOSEARCH`).
- **Quadtree**: recursive spatial subdivision. Adaptive resolution. Less common in modern databases.
- **R-tree**: flexible, overlapping bounding rectangles. Handles points AND shapes. Default in PostGIS, MySQL. Production standard.
- Interview approach: explain why regular indexes fail, then contrast hash-based (geohash) vs tree-based (R-tree).

### Inverted Index
- Maps words → documents (reverse of normal). Enables full-text search.
- Pipeline: tokenization → lowercase → stop words → stemming. "Databases" matches "database".
- Additional features: term frequency, relevance scoring, fuzzy matching, phrase queries.
- Used by Elasticsearch/Lucene, PostgreSQL GIN indexes.

## Optimization Patterns

### Composite Indexes
- Single index on multiple columns in specific order: `(user_id, created_at)`.
- **Column order matters**: index only helps for prefix queries. `(user_id, created_at)` helps filter by user_id but NOT by created_at alone.
- Eliminates index intersection and sort operations. Common patterns: `(customer_id, order_date)`, `(status, priority, created_at)`.

### Covering Indexes
- Include all columns needed by query in the index itself. Avoids table lookups ("index-only scan").
- `CREATE INDEX ... ON posts(user_id, created_at) INCLUDE (likes)`.
- Niche optimization in 2026 — modern query optimizers are smart enough that this is rarely needed. Use only for specific, justified hot paths.

## Related

- [[Database Indexing]]
- [[Data Modeling]]
- [[Core Concepts for System Design Interviews]]
- [[Hello Interview]]
