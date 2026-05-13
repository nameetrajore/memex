---
title: "Consistent Hashing Deep Dive"
type: source
tags: [system-design, distributed-systems, hashing, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [consistent-hashing-deep-dive]
---

## Summary

Deep dive on consistent hashing from [[Hello Interview]]. Builds intuition through a TicketMaster example showing why modulo hashing fails, then explains the hash ring, virtual nodes, hot spot mitigation, data movement in practice, and when to discuss it in interviews.

## The Problem with Modulo Hashing

`shard = hash(key) % N`. Adding or removing a server changes N → almost every key maps to a different server → massive data redistribution. Whether adding capacity or handling a failure, this is catastrophic.

## Hash Ring Solution

1. Create a ring (hash space 0 to 2^32 - 1).
2. Place server nodes on the ring via hashing.
3. Each key hashes to a position on the ring → assigned to the next clockwise server.
4. Adding a server: only keys between the new server and its predecessor need to move (~1/N of data).
5. Removing a server: only its keys relocate to the next clockwise server.

## Virtual Nodes

Without virtual nodes, removing a server dumps all its load onto one neighbor. With virtual nodes, each server is placed at multiple ring positions (hash "DB1-vn1", "DB1-vn2", etc.). When a server fails, its load distributes across multiple neighbors evenly. More virtual nodes = more even distribution.

Virtual nodes also help when adding servers — new node absorbs small chunks from many existing nodes instead of one.

## Hot Spots

Consistent hashing distributes **keys** evenly, not **traffic**. Hot spots arise from popular keys (Taylor Swift's concert).

Solutions:
- **Read replicas**: replicate popular keys, load-balance reads.
- **Key-space salting**: append random suffix to hot key (e.g., `taylor-swift-{0..9}`) to scatter across nodes.
- **Adaptive rebalancing**: monitor traffic, move key ranges off overloaded nodes (DynamoDB does this).

**Key distinction**: virtual nodes prevent structural imbalance; replication and salting prevent workload imbalance.

## Data Movement in Practice

Most distributed databases replicate alongside consistent hashing. When a node fails, a replica is promoted (Raft consensus) — no data movement. Data movement only happens during planned membership changes (adding capacity, restoring replication factor).

## Real-World Usage

- Cassandra: consistent hashing with virtual nodes.
- DynamoDB: consistent hashing for partition placement.
- CDNs: consistent hashing for edge server content assignment.
- **Redis Cluster**: uses fixed hash slots (16,384 slots via `CRC16(key) % 16384`), NOT consistent hashing. Simpler but more coordination when rebalancing.

## Interview Guidance

Most interviews: mention that DynamoDB/Cassandra use consistent hashing under the hood. Deep infrastructure interviews (design distributed cache/DB/broker): explain the ring, virtual nodes, and hot spot strategies.

> [!note] Loose Terminology
> "Consistent hashing" is used loosely. Some systems claiming it actually use hash-based partitioning with fixed slot ranges. The principle of minimizing data movement is what matters.

## Related

- [[Consistent Hashing]]
- [[Sharding]]
- [[Caching]]
- [[Hello Interview]]
