---
title: "Data Modeling Deep Dive"
type: source
tags: [system-design, databases, data-modeling, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [data-modeling-deep-dive]
---

## Summary

Deep dive on data modeling for system design interviews from [[Hello Interview]]. Covers database model selection (relational, document, key-value, wide-column, graph), schema design fundamentals, and how to tie schema choices back to requirements. Default to PostgreSQL unless requirements clearly signal otherwise.

## Database Model Options

### Relational (SQL) — Default Choice
- Tables with fixed schemas, rows and columns. Enforce relationships via foreign keys.
- ACID transactions for consistency. Arbitrary joins, flexible indexing.
- Scale with read replicas, [[Sharding]], connection pooling, [[Caching]].
- Facebook and Airbnb run on relational foundations at massive scale.
- **Recommended**: PostgreSQL.

### Document Databases
- JSON-like documents with flexible schemas. Embed related data (posts inside user doc).
- Eliminates joins but makes updates harder (modify entire document).
- Use when: schema changes frequently, deeply nested data, records with vastly different structures.
- Interview tip: scoped requirements rarely need "evolving schemas" — prefer SQL.
- **Technologies**: MongoDB, Firestore, CouchDB.

### Key-Value Stores
- Simple key → value lookups. Extremely fast, limited query capabilities.
- Often used alongside SQL: SQL as source of truth, key-value (Redis) as cache.
- Heavily denormalized; duplicate data across keys for different access patterns.
- **Technologies**: Redis, DynamoDB, Memcached.

### Wide-Column Databases
- Column families, rows with different column sets. Optimized for massive write-heavy workloads.
- Data keyed by (partition_key, sort_key). Same partition stored contiguously.
- Use for: telemetry, event logging, IoT, time-series, analytics.
- **Technologies**: Cassandra, HBase.

### Graph Databases
- Nodes and edges for relationship traversal.
- **Almost never the right choice in interviews.** Even Facebook/LinkedIn/Twitter use SQL for core relationship data.
- **Technologies**: Neo4j, Amazon Neptune.

> [!warning] Common Mistake
> Choosing graph databases in interviews. They add unnecessary complexity. SQL with proper indexing handles relationship queries well enough for interview purposes.

## Schema Design Fundamentals

Three factors drive all schema decisions:

1. **Data volume** — determines physical storage and partitioning needs.
2. **Access patterns** — most important. Ask "what queries does each API endpoint need?"
3. **Consistency requirements** — ACID for financial data; eventual consistency for feeds/likes.

### Entities, Keys & Relationships
- Core entities from the delivery framework map 1:1 to tables/collections.
- Use system-generated IDs (UUID, ULID) not business data (email) as primary keys.
- Foreign keys enforce relationships in SQL.

### Schema in the Interview
- Sketch during High-Level Design, next to your database box.
- Include only key fields, relationships, and index/partition notes.
- Tie schema choices to requirements: "Since likes can be eventually consistent, I'll denormalize like counts into the posts table."

> [!note] Open Question
> Source clip was truncated — normalization vs denormalization details and indexing sections were cut off. See [[Core Concepts for System Design Interviews]] for overview-level coverage.

## Related

- [[Data Modeling]]
- [[Database Indexing]]
- [[Sharding]]
- [[Core Concepts for System Design Interviews]]
- [[Hello Interview]]
