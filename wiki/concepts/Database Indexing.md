---
title: "Database Indexing"
type: concept
tags: [system-design, databases, indexing, b-tree, performance]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, database-indexing-deep-dive]
---

## Summary

Indexes are separate data structures that provide fast lookup paths, avoiding full table scans. Default to B-tree. Know when to reach for LSM trees (write-heavy), geospatial indexes (proximity), or inverted indexes (full-text search). Composite indexes are the most common optimization.

## Index Types

| Type | Strengths | Limitations | Use When |
|------|-----------|-------------|----------|
| **B-Tree** | Exact match + range + sorting. Self-balancing. Default. | Write overhead. | Default choice. When in doubt. |
| **LSM Tree** | Blazing fast writes (sequential I/O). | Slower reads (check multiple SSTables). | Write-heavy: metrics, logs, IoT, time-series. |
| **Hash** | O(1) exact match. | No range queries, no sorting. | In-memory (Redis). Rarely used on disk. |
| **Geospatial** | 2D proximity queries. | Specialized. | Location search (Uber, Yelp). |
| **Inverted** | Full-text search. | Storage overhead, update cost. | Search features (Elasticsearch). |

## B-Tree Details
- Nodes sized to one disk page (~8KB). ~2–3 page reads for any record.
- PostgreSQL auto-creates B-trees for primary keys and unique constraints.

## LSM Tree Details
- Write path: memtable (RAM) → WAL (sequential disk) → SSTable flush → compaction.
- Read mitigations: Bloom filters, sparse indexes, compaction strategies.
- Used by: Cassandra, RocksDB, DynamoDB.

## Geospatial Approaches
- **Geohash**: 2D → 1D string preserving proximity. Use B-tree on strings. Redis `GEOSEARCH`.
- **R-tree**: overlapping bounding rectangles. Handles points + shapes. PostGIS default.

## Composite Indexes
- Single index on multiple columns: `(user_id, created_at)`.
- **Column order matters** — only helps for prefix queries.
- Eliminates index intersection and sort operations.

## Covering Indexes
- Include all query-needed columns in index. Avoids table lookups.
- Niche optimization; modern optimizers rarely need it. Justify before using.

> [!tip] Interview Tip
> Think about query patterns → propose indexes on frequently-queried fields. When asked what type, default to B-tree. Mention geospatial or inverted only for location/search features.

## Related

- [[Database Indexing Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[Data Modeling]]
- [[Caching]]
