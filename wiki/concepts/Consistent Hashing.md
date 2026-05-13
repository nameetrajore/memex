---
title: "Consistent Hashing"
type: concept
tags: [system-design, distributed-systems, hashing, scaling]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, consistent-hashing-deep-dive]
---

## Summary

A technique for distributing data across servers such that adding or removing a node only requires moving a small fraction of keys, unlike simple modulo hashing which redistributes nearly everything. Keys and servers are placed on a virtual ring; each key belongs to the next clockwise server.

## The Problem with Modulo Hashing

`shard = hash(key) % N`. Changing N (adding capacity or handling failure) remaps almost every key to a different server — catastrophic data redistribution.

## Hash Ring

1. Create a ring (hash space 0 to 2^32 - 1).
2. Place server nodes on the ring via hashing their identifiers.
3. Each key hashes to a ring position → assigned to the next clockwise server.
4. **Adding a server**: only keys between the new server and its predecessor move (~1/N of data).
5. **Removing a server**: only its keys relocate to the next clockwise neighbor.

## Virtual Nodes

Without virtual nodes, removing a server dumps all its load onto one neighbor. With virtual nodes, each server occupies multiple ring positions (e.g., hash "DB1-vn1", "DB1-vn2", etc.).

- **Failure**: load distributes across multiple neighbors evenly instead of one.
- **Addition**: new node absorbs small chunks from many existing nodes.
- More virtual nodes = more even distribution.

**Key distinction**: virtual nodes prevent *structural imbalance*; replication and salting prevent *workload imbalance*.

## Hot Spot Mitigation

Consistent hashing distributes **keys** evenly, not **traffic**. Hot spots arise from popular keys (e.g., Taylor Swift concert).

| Strategy | How It Works |
|----------|-------------|
| **Read replicas** | Replicate popular keys, load-balance reads across copies. |
| **Key-space salting** | Append random suffix (e.g., `taylor-swift-{0..9}`) to scatter across nodes. |
| **Adaptive rebalancing** | Monitor traffic, move key ranges off overloaded nodes (DynamoDB does this). |

## Data Movement in Practice

Most distributed databases replicate alongside consistent hashing. When a node fails, a replica is promoted via Raft consensus — no data movement needed. Data movement only happens during planned membership changes (adding capacity, restoring replication factor).

## Real-World Usage

- **Cassandra**: consistent hashing with virtual nodes.
- **DynamoDB**: consistent hashing for partition placement.
- **CDNs**: consistent hashing for edge server content assignment.
- **Redis Cluster**: uses fixed hash slots (16,384 slots via `CRC16(key) % 16384`), NOT consistent hashing. Simpler but requires more coordination when rebalancing.

> [!note] Loose Terminology
> "Consistent hashing" is used loosely. Some systems claiming it actually use hash-based partitioning with fixed slot ranges. The principle of minimizing data movement is what matters.

## Interview Guidance

- Most interviews: mention that DynamoDB/Cassandra use consistent hashing under the hood.
- Deep infrastructure interviews (design distributed cache/DB/broker): explain the ring, virtual nodes, and hot spot strategies.

## Related

- [[Consistent Hashing Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[Sharding]]
- [[Caching]]
- [[Numbers to Know]]
