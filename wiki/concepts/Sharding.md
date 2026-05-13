---
title: "Sharding"
type: concept
tags: [system-design, databases, scaling, sharding]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, sharding-deep-dive]
---

## Summary

Split data across multiple independent database servers when a single instance can't handle storage, write throughput, or read throughput. The shard key is the most critical decision. Default to hash-based with [[Consistent Hashing]]. Don't shard prematurely — do the capacity math first.

## Shard Key Selection

A good shard key has: **high cardinality**, **even distribution**, **query alignment**.

| Key | Quality | Why |
|-----|---------|-----|
| user_id (user-centric app) | Good | Millions of values, most queries scoped to one user |
| order_id (e-commerce) | Good | High cardinality, queries scoped to single order |
| is_premium (boolean) | Bad | Only 2 possible shards |
| created_at | Bad | All new writes hit the latest shard (hot spot) |

## Distribution Strategies

| Strategy | Pros | Cons | When |
|----------|------|------|------|
| **Hash-based** | Even distribution | Changing N moves data (use consistent hashing) | **Default** |
| **Range-based** | Simple, supports range scans | Hot spots on recent ranges | Multi-tenant SaaS |
| **Directory-based** | Maximum flexibility | SPOF, latency on every request | Rarely in interviews |

## Challenges

### Hot Spots (Celebrity Problem)
Some keys are inherently hotter. Solutions: isolate to dedicated shards, compound keys `hash(user_id + date)`, dynamic shard splitting.

### Cross-Shard Operations
Queries spanning all shards are expensive. Solutions: cache results, denormalize, accept cost for rare queries. If common queries need all shards, reconsider your shard key.

### Consistency
No single-DB transactions across shards. Avoid 2PC (fragile). Solutions:
- Design to avoid cross-shard transactions (keep user's data on one shard)
- **Saga pattern**: sequence of steps with compensating actions
- Accept eventual consistency where appropriate

## Modern Database Support
Cassandra, DynamoDB, MongoDB, Vitess, Citus, Aurora, Spanner all handle sharding. In interviews: "We'll use DynamoDB with user_id as partition key" is sufficient.

## Interview Approach
1. Identify bottleneck (storage > few TB, writes > 50K TPS)
2. Explain why single DB won't scale
3. Propose shard key based on access patterns
4. Hash-based + consistent hashing (default)
5. Call out trade-offs (which queries become expensive)

> [!warning] #1 Mistake
> Introducing sharding before proving it's necessary. A well-tuned single DB with read replicas handles far more than most candidates assume.

## Related

- [[Sharding Deep Dive]]
- [[Consistent Hashing]]
- [[Data Modeling]]
- [[CAP Theorem]]
- [[Core Concepts for System Design Interviews]]
