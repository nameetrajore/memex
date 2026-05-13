---
title: "System Design Interview Patterns"
type: source
tags: [system-design, interviews, patterns, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-interview-patterns]
---

## Summary

Eight common patterns that combine [[Core Concepts for System Design Interviews|core concepts]] and [[System Design Key Technologies|key technologies]] into reusable solutions, from [[Hello Interview]]. Recognizing which patterns apply to a problem separates senior from junior candidates and saves significant interview time.

## Patterns

### 1. Pushing Realtime Updates
- Protocols: HTTP polling (simplest, start here) → SSE (unidirectional push) → WebSockets (bidirectional).
- Server-side: Pub/Sub for decoupled fanout, stateful servers in consistent hash ring for heavy processing.
- Use cases: chat, notifications, live dashboards.

### 2. Managing Long-Running Tasks
- Pattern: accept request → push job to queue → return job ID immediately → workers process asynchronously.
- Don't over-queue: for short-running jobs, synchronous responses simplify architecture and improve UX.
- Key tech: [[Message Queues]], worker pools, dead letter queues.

### 3. Dealing with Contention
- When multiple users access the same resource (last ticket, auction bid).
- Solutions: pessimistic locking, optimistic concurrency control, [[Distributed Locks]], queue-based serialization.
- Start with single-database solutions before distributing. Splitting data across DBs means solving problems the DB already handles.

### 4. Scaling Reads
- Read traffic grows much faster than writes (typical ratio 10:1 → 100:1+).
- Progression: optimize with indexes + denormalization → read replicas → external [[Caching]] (Redis, CDN).
- Challenges: cache invalidation, replication lag, hot keys.

### 5. Scaling Writes
- When single DB hits write throughput limits.
- Strategies: horizontal [[Sharding]], vertical partitioning, write queues for burst buffering, load shedding, batching.
- Partition key selection is critical: distribute evenly, keep related data together.

### 6. Handling Large Blobs
- Direct client-to-storage transfers via presigned URLs. CDN for downloads.
- Don't route gigabytes through app servers.
- Challenges: metadata/blob state sync, upload failures, file lifecycle. See [[Blob Storage]].

### 7. Multi-Step Processes
- Coordinate complex workflows (order fulfillment, onboarding, payments).
- Simple orchestration → event sourcing → workflow engines (Temporal, AWS Step Functions).
- Goal: declarative workflow definitions with exactly-once execution and audit trails.

### 8. Proximity-Based Services
- Search entities by location (Uber drivers, nearby restaurants).
- Geospatial indexes: PostGIS (Postgres), Redis geospatial type, Elasticsearch geo-queries.
- Only needed for hundreds of thousands+ items; for small datasets, just scan.
- Users usually search locally, not globally.

## Pattern Selection
Patterns often combine. Video platform example: Large Blobs (upload) + Long-Running Tasks (transcoding) + Realtime Updates (progress) + Multi-Step Processes (coordination).

Start simple; add complexity only when requirements demand it. Proactively identifying patterns demonstrates architectural maturity.

## Related

- [[System Design Key Technologies]]
- [[Core Concepts for System Design Interviews]]
- [[System Design Delivery Framework]]
- [[Hello Interview]]
