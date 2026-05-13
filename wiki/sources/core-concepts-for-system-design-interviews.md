---
title: "Core Concepts for System Design Interviews"
type: source
tags: [system-design, interviews, core-concepts, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews]
---

## Summary

A survey of the 8 fundamental building blocks for system design interviews, from [[Hello Interview]]. These are technology-agnostic concepts that appear across nearly every design problem. Each section provides enough context to be functional, with pointers to deeper articles.

## Core Concepts Covered

### [[Networking Essentials]]
- Default to HTTP/TCP for most communication.
- **WebSockets** for bidirectional real-time (chat, collaboration); **SSE** for unidirectional server-push (live scores, notifications).
- **gRPC** for internal service-to-service when performance matters (binary serialization, HTTP/2). REST externally, gRPC internally is a common pattern.
- **Load balancing**: Layer 7 (application-level routing by HTTP content) vs Layer 4 (TCP-level, faster but no content inspection). WebSockets need L4.
- Geography matters: NY→London minimum ~80ms. Use CDNs and regional deployments for global low latency.

### [[API Design]]
- Default to REST. Map resources to URLs, use HTTP methods.
- Don't spend more than a couple minutes on API design in an interview — sketch 4–5 endpoints and move on.
- Know: cursor-based vs offset pagination, JWT for user sessions, API keys for service-to-service, rate limiting.

### [[Data Modeling]]
- **Relational** (Postgres): structured data, clear relationships, strong consistency, SQL joins.
- **NoSQL** (DynamoDB, MongoDB): flexible schemas, horizontal scaling, no complex joins.
- **Normalization**: split data to avoid duplication; requires joins.
- **Denormalization**: duplicate data to avoid joins; faster reads, harder updates.
- Safe default: start normalized, denormalize hot paths when needed.
- NoSQL forces you to design around access patterns upfront (partition key + sort key).

### [[Database Indexing]]
- Without indexes: full table scan. With indexes: millisecond lookups.
- **B-tree**: sorted, supports exact match + range queries (default in most RDBMS).
- **Hash index**: faster exact match, no range queries.
- **Specialized**: full-text (Elasticsearch), geospatial (PostGIS).
- External indexes sync via CDC — slight staleness is acceptable for search.

### [[Caching]]
- Cache-aside with Redis is the 90% pattern. ~1ms vs 20–50ms DB query.
- Hardest part: **invalidation** — delete/update on writes, use TTLs, or combine both.
- **Cache stampede**: if Redis goes down, all traffic hits DB. Mitigate with in-process fallback cache or circuit breakers.
- Only cache data that's read frequently and changes rarely.
- CDN caching for static assets; in-process caching for config/feature flags.

### [[Sharding]]
- Split data across multiple DB servers when single instance limits are hit (storage, write throughput, read throughput).
- **Shard key** is the most important decision — determines data distribution and query efficiency.
- **Hash-based**: even distribution, avoids hot spots. **Range-based**: natural partitioning but hot-spot risk. **Directory-based**: flexible but adds latency.
- Don't shard too early — do the capacity math first. A single well-tuned DB + read replicas handles more than most think.
- Sharding creates problems: cross-shard transactions, hot spots, painful resharding.

### [[Consistent Hashing]]
- Solves the "adding/removing servers redistributes everything" problem with simple modulo hashing.
- Keys and servers placed on a virtual ring; only keys between affected servers need to move.
- Adding 1 server to 10: ~10% data movement (vs ~90% with modulo).
- Used in: distributed caches (Redis Cluster, Memcached), databases (Cassandra, DynamoDB), CDNs.
- In interviews: mention it when discussing elastic scaling; rarely need to explain internals unless asked.

### [[CAP Theorem]]
- Consistency, Availability, Partition tolerance — pick 2 (really: pick C or A since P is unavoidable).
- **Availability** (eventual consistency) is the default for most systems — users tolerate slightly stale data.
- **Strong consistency** when stale data costs money: inventory, banking, booking.
- Mix models within a system: eventual consistency for reviews, strong for inventory.
- **PACELC**: during Partition → A or C; Else → Latency or Consistency.

### Numbers to Know
- Memory: nanoseconds. SSD: microseconds. Network (datacenter): 1–10ms. Cross-continent: tens–hundreds of ms.
- Redis: ~100K+ ops/sec, ~1ms latency. DB: ~50K TPS, sub-5ms reads. App server: ~100K concurrent connections.
- Single Postgres: comfortable at a few TB. Sharding needed at tens–hundreds of TB.
- Do capacity math **in context** when making decisions, not upfront as a ritual.

## Related

- [[System Design in a Hurry — Introduction]]
- [[How to Prepare for System Design Interviews]]
- [[Hello Interview]]
