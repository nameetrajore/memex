---
title: "Caching"
type: concept
tags: [system-design, caching, redis, performance]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, caching-deep-dive]
---

## Summary

Store frequently accessed data in fast memory to skip the database. Redis ~1ms vs DB 20–50ms. Cache-aside with LRU + TTL is the default. The hardest problems are invalidation, stampede, and hot keys.

## Where to Cache

| Layer | Use For | Notes |
|-------|---------|-------|
| **External (Redis/Memcached)** | Application data | Default answer. Shared across servers. |
| **CDN** | Static media, cacheable API responses | Edge servers close to users. |
| **In-process** | Config, feature flags, hot keys | No network call. Not shared across instances. Optimization layer only. |
| **Client-side** | Browser cache, mobile local storage | Limited server control. |

## Cache Architectures

| Pattern | How | Trade-off | When |
|---------|-----|-----------|------|
| **Cache-aside** | App checks cache → miss → query DB → store | Default. Lean cache. Cache miss = extra latency. | **Always start here.** |
| **Write-through** | Write to cache → cache writes to DB synchronously | Fresh reads. Slower writes. Dual-write risk. | Reads must always be fresh. |
| **Write-behind** | Write to cache → async batch flush to DB | Fastest writes. **Data loss risk.** | Analytics, metrics pipelines. |
| **Read-through** | Cache fetches from DB on miss (proxy) | Centralized logic. CDNs do this. | CDN. Rarely for app-level. |

## Eviction Policies
- **LRU** — least recently used. Default.
- **LFU** — least frequently used. For consistently popular keys.
- **TTL** — expiration timer. Combine with LRU. Must-have for freshness.

## Common Problems

### Cache Stampede
Popular key expires → hundreds of concurrent DB queries. Fix: **request coalescing** (only one rebuilds, others wait). Also: proactive cache warming.

### Cache Consistency
Stale data after DB writes. Fix: invalidate cache on writes + short TTLs. Accept eventual consistency for feeds/analytics.

### Hot Keys
One key overloads one cache node (Taylor Swift's profile). Fix: replicate across nodes, in-process fallback, rate limiting.

## Interview Approach
1. Identify bottleneck (read-heavy? expensive queries? high DB CPU? latency requirement?)
2. Decide what to cache (read-heavy + rarely changing + expensive)
3. Architecture: cache-aside (default)
4. Eviction: LRU + TTL
5. Address 1–2 problems (invalidation, stampede, hot keys)

> [!warning] Common Mistake
> Caching everything. Only cache data that's read frequently and changes rarely. Don't jump to caching without establishing why it's necessary.

## Related

- [[Caching Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[Database Indexing]]
- [[Networking Essentials]] (CDN)
