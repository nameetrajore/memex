---
title: "Caching Deep Dive"
type: source
tags: [system-design, caching, redis, performance, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [caching-deep-dive]
---

## Summary

Comprehensive caching guide from [[Hello Interview]]. Covers where to cache (external, CDN, client-side, in-process), cache architectures (cache-aside, write-through, write-behind, read-through), eviction policies, common problems (stampede, consistency, hot keys), and a systematic approach for introducing caching in interviews.

## Where to Cache

| Layer | What | When |
|-------|------|------|
| **External (Redis)** | Application data | Default answer. High-traffic systems. Shared across servers. |
| **CDN** | Static media, API responses | Global users, static/cacheable content. |
| **Client-side** | Browser cache, mobile local storage | Reduce network calls. Limited control. |
| **In-process** | Config, feature flags, hot keys | Small, frequently accessed, rarely changing. Mention as optimization layer after external cache. |

## Cache Architectures

### Cache-Aside (Default)
Check cache → miss → query DB → store in cache with TTL → return. Only caches on demand. **Default in interviews.**

### Write-Through
Write to cache, cache synchronously writes to DB. Fresh reads guaranteed. Slower writes. Dual-write consistency risk.

### Write-Behind (Write-Back)
Write to cache, async batch flush to DB. Fastest writes. **Data loss risk** if cache crashes before flush. Use for analytics/metrics.

### Read-Through
Cache acts as proxy; on miss, cache fetches from DB itself. Centralizes caching logic. CDNs are a form of this. Rarely propose in interviews except for CDN.

## Eviction Policies

- **LRU** — evict least recently accessed. Default, adapts well to most workloads.
- **LFU** — evict least frequently accessed. Good for consistently popular keys.
- **FIFO** — evict oldest. Ignores usage patterns. Rarely used.
- **TTL** — expiration timer per key. Not a standalone policy; combine with LRU/LFU.

## Common Problems

### Cache Stampede (Thundering Herd)
Popular key expires → hundreds of concurrent requests rebuild it → DB overwhelmed.
- **Request coalescing (single flight)**: only one request rebuilds; others wait. Most effective.
- **Cache warming**: proactively refresh popular keys before TTL expires.

### Cache Consistency
Cache shows stale data after DB write. No perfect solution.
- Invalidate on writes (delete cache entry after DB update).
- Short TTLs for acceptable staleness.
- Accept eventual consistency for feeds, metrics, analytics.

### Hot Keys
One key gets disproportionate traffic, overloading a single cache node.
- Replicate hot keys across multiple nodes.
- In-process fallback cache for extremely hot values.
- Rate limiting on abusive patterns.

## Interview Approach

1. **Identify the bottleneck** — be specific (query latency? DB CPU? expensive computation?).
2. **Decide what to cache** — read-heavy, rarely changing, expensive to fetch.
3. **Choose architecture** — cache-aside (default), mention CDN for static assets.
4. **Set eviction policy** — LRU + TTL (safe default).
5. **Address downsides** — pick 1–2 relevant problems (invalidation, stampede, hot keys).

## Related

- [[Caching]]
- [[Core Concepts for System Design Interviews]]
- [[System Design Interview Patterns]] (Scaling Reads)
- [[Hello Interview]]
