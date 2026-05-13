---
title: "Numbers to Know"
type: concept
tags: [system-design, capacity, hardware, scaling]
created: 2026-05-13
updated: 2026-05-13
sources: [numbers-to-know-deep-dive]
---

## Summary

2026 hardware capabilities are far more powerful than most candidates realize. Using outdated numbers leads to over-engineered systems. Do capacity math in context when making decisions, not as upfront ritual. Many applications that once required distributed systems can now run on a single well-tuned machine.

## 2026 Hardware Reference

| Component | Key Specs | Scale Triggers |
|-----------|-----------|---------------|
| **Servers** | 8–128 vCPUs, 64GB–24TB RAM, 60TB SSD, 25–100 Gbps network | — |
| **Cache (Redis)** | Up to 1TB memory, <1ms latency, 100K–200K+ ops/sec | Hit rate <80%, latency >1ms, memory >80% |
| **Database (Postgres/Aurora)** | Up to 64 TiB (Aurora: 256 TiB), 50K read TPS, 10–20K write TPS | Dataset >50 TiB, writes >10K TPS, geo distribution |
| **App Servers** | 100K+ concurrent connections, 8–64 cores, 64–512GB RAM | CPU >70%, latency >SLA, memory >80% |
| **Message Queues (Kafka)** | 1M msgs/sec per broker, 1–5ms latency, 50TB storage | Throughput ~800K msgs/sec, partition count ~200K |

## Common Interview Mistakes

### 1. Premature Sharding
- Yelp: 10M businesses × 1KB = 10GB + 10× for reviews = 100GB. Fits on one machine.
- LeetCode leaderboard: 100K competitions × 100K users × 40B = 400GB. Fits in one large cache.

### 2. Overestimating Latency
- Indexed SSD lookups are sub-millisecond to a few milliseconds. Don't add caching for simple row lookups that are already fast.

### 3. Over-engineering Write Throughput
- Postgres handles 20K+ simple writes/sec. Don't add message queues at 5K WPS unless you need guaranteed delivery, event sourcing, or true spike absorption.
- Before reaching for a queue: try batch writes, schema optimization, connection pooling, async commits.

## Key Insight

**Know where the real limits are** to avoid premature optimization. Don't memorize AWS pricing — just avoid obviously wasteful designs (100 machines when 1 suffices).

## Related

- [[Numbers to Know Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[Caching]]
- [[Sharding]]
- [[Database Indexing]]
- [[Message Queues]]
