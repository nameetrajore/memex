---
title: "Data Modeling"
type: concept
tags: [system-design, databases, data-modeling, schema]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, data-modeling-deep-dive]
---

## Summary

Define how data is structured, stored, and related. Default to PostgreSQL. Pick your database model, design a schema driven by access patterns, and sketch it during the high-level design phase. Three factors drive decisions: data volume, access patterns (most important), and consistency requirements.

## Database Models

| Model | When to Use | Technologies |
|-------|-------------|-------------|
| **Relational (SQL)** | Default. Structured data, relationships, consistency. | PostgreSQL, MySQL |
| **Document** | Evolving schemas, deeply nested data. Rare in interviews. | MongoDB, Firestore |
| **Key-Value** | Caching, sessions, feature flags. Often alongside SQL. | Redis, DynamoDB, Memcached |
| **Wide-Column** | Massive write-heavy, time-series, telemetry. | Cassandra, HBase |
| **Graph** | Almost never in interviews. Even Facebook uses MySQL. | Neo4j, Neptune |

> [!warning] Common Mistake
> Graph databases in interviews add unnecessary complexity. SQL with proper indexing handles relationship queries well enough.

## Schema Design

- **Core entities** from delivery framework → tables/collections.
- **System-generated IDs** (UUID, ULID) as primary keys, not business data.
- **Foreign keys** enforce relationships in SQL.

### Normalization vs Denormalization
- **Normalized**: split tables, avoid duplication. Consistent but requires joins (expensive at scale).
- **Denormalized**: duplicate data, avoid joins. Fast reads, harder updates.
- **Safe default**: start normalized, denormalize hot read paths when needed.

### NoSQL Access Patterns
- DynamoDB: design partition key + sort key around most common query.
- Other queries may require full table scan — must know patterns upfront.

### In the Interview
- Sketch schema during High-Level Design, next to your database box.
- Include only key fields, relationships, and index/partition notes.
- Tie choices to requirements: "likes can be eventually consistent → denormalize like counts into posts table."

## Related

- [[Data Modeling Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[Database Indexing]]
- [[Sharding]]
