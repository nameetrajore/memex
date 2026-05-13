---
title: "Sharding Deep Dive"
type: source
tags: [system-design, databases, sharding, scaling, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [sharding-deep-dive]
---

## Summary

Comprehensive sharding guide from [[Hello Interview]]. Covers partitioning vs sharding, shard key selection criteria, distribution strategies (range, hash, directory), challenges (hot spots, cross-shard operations, consistency), modern database support, and a systematic interview approach.

## Partitioning vs Sharding

- **Partitioning**: split a large table into smaller pieces within a single DB instance. Horizontal (split rows) or vertical (split columns).
- **Sharding**: horizontal partitioning across multiple machines. Each shard is an independent database with its own CPU/memory/storage.

## Shard Key Selection

A good shard key has:
1. **High cardinality** — many unique values (not booleans).
2. **Even distribution** — values spread uniformly (not 90% US if sharding by country).
3. **Query alignment** — most common queries hit one shard.

**Good keys**: user_id (user-centric apps), order_id (e-commerce).
**Bad keys**: is_premium (only 2 values), created_at (all writes go to latest shard).

## Distribution Strategies

### Range-Based
Assign value ranges to shards. Simple, supports range scans. **Risk**: hot spots on recent ranges. Best for multi-tenant systems.

### Hash-Based (Default)
`shard = hash(key) % N`. Even distribution. Problem: changing N moves ~all data. Solution: [[Consistent Hashing]]. **Default strategy in interviews.**

### Directory-Based
Lookup table maps keys to shards. Maximum flexibility. **Problem**: every request needs a lookup, directory is single point of failure. Rarely the answer in interviews.

## Challenges

### Hot Spots (Celebrity Problem)
Even with good shard keys, some keys are inherently hotter (Taylor Swift's shard). Solutions: isolate hot keys to dedicated shards, compound shard keys (`hash(user_id + date)`), dynamic shard splitting.

### Cross-Shard Operations
Queries spanning multiple shards are expensive (fan-out to all shards + aggregate). Solutions: cache results, denormalize to co-locate related data, accept the cost for rare queries. If a common query needs all shards, reconsider your shard key.

### Consistency
Single-DB transactions break across shards. Avoid 2PC (slow, fragile). Solutions:
- **Design to avoid cross-shard transactions** — keep all user data on one shard.
- **Saga pattern** — sequence of independent steps with compensating actions.
- **Accept eventual consistency** — fine for follower counts, like counts.

## Modern Database Support

| Database | Mechanism |
|----------|-----------|
| Cassandra | Consistent hashing with virtual nodes |
| DynamoDB | Hash partition key, auto split/merge |
| MongoDB | Range-based chunks, background balancer |
| Vitess/Citus | Sharding layer for PostgreSQL/MySQL |
| Aurora/Spanner | Built-in distributed SQL |

In interviews: "We'll use DynamoDB with user_id as partition key" is sufficient.

## Interview Approach

1. Identify the bottleneck (storage > few TB, writes > 50K TPS, reads beyond replicas).
2. Explain why single DB won't scale.
3. Propose shard key based on access patterns.
4. Choose distribution (hash-based + consistent hashing, default).
5. Call out trade-offs (which queries become expensive).
6. Address growth (start with enough shards, consistent hashing for expansion).

> [!warning] #1 Mistake
> Introducing sharding before proving it's necessary. Do the capacity math first.

## Related

- [[Sharding]]
- [[Consistent Hashing]]
- [[Data Modeling]]
- [[Core Concepts for System Design Interviews]]
- [[Hello Interview]]
