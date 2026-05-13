---
title: "Numbers to Know Deep Dive"
type: source
tags: [system-design, capacity, hardware, scaling, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [numbers-to-know-deep-dive]
---

## Summary

2026 hardware capabilities reference from [[Hello Interview]]. Modern hardware is far more powerful than most candidates realize — using outdated numbers leads to over-engineered systems. Do capacity math in context when making decisions, not as upfront ritual.

## 2026 Hardware Capabilities

### Servers
- CPU: 8–128 vCPUs. Memory: 64GB–24TB RAM. Storage: 60TB SSD, 336TB HDD.
- Network: 25 Gbps standard, 50–100 Gbps high-perf. Intra-AZ < 1ms, cross-AZ 1–2ms, cross-region 50–150ms.

### Caching (Redis/ElastiCache)
- Memory: up to 1TB.
- Latency: < 1ms reads, < 1ms writes (same-AZ).
- Throughput: 100K–200K+ ops/sec per instance.
- **Scale triggers**: hit rate < 80%, latency > 1ms, memory > 80%.

### Databases (Postgres/Aurora)
- Storage: up to 64 TiB (Aurora: 256 TiB).
- Latency: 1–5ms reads (cached), 5–30ms (disk), 5–15ms writes.
- Throughput: up to 50K read TPS, 10–20K write TPS (single node).
- Connections: 5–20K concurrent.
- **Scale triggers**: dataset > 50 TiB, writes > 10K TPS, geo distribution needs.

### Application Servers
- Connections: 100K+ concurrent.
- CPU: 8–64 cores. Memory: 64–512GB (up to 2TB).
- Startup: 30–60s containerized.
- **Scale triggers**: CPU > 70%, response latency > SLA, memory > 80%.

### Message Queues (Kafka)
- Throughput: up to 1M msgs/sec per broker.
- Latency: 1–5ms end-to-end.
- Storage: up to 50TB per broker. Retention: weeks to months.
- **Scale triggers**: throughput near 800K msgs/sec, partition count ~200K, growing consumer lag.

## Common Interview Mistakes

### 1. Premature Sharding
- Yelp: 10M businesses × 1KB = 10GB + 10× for reviews = 100GB. Why shard?
- LeetCode leaderboard: 100K competitions × 100K users × 40B = 400GB. Fits in one large cache.

### 2. Overestimating Latency
- Indexed SSD lookups are sub-millisecond to a few milliseconds. Don't add caching for simple row lookups that are already fast.

### 3. Over-engineering Write Throughput
- Postgres handles 20K+ simple writes/sec. Don't add message queues at 5K WPS unless you need guaranteed delivery, event sourcing, or true spike absorption.
- Before reaching for a queue: try batch writes, schema optimization, connection pooling, async commits.

## Key Insight

Many applications that once required distributed systems can now run on a single well-tuned machine. **Know where the real limits are** to avoid premature optimization. Costs: don't memorize AWS pricing — just avoid obviously wasteful designs (100 machines when 1 suffices).

## Related

- [[Core Concepts for System Design Interviews]]
- [[Caching]]
- [[Sharding]]
- [[Database Indexing]]
- [[Hello Interview]]
