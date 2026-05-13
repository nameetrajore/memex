---
title: "System Design Key Technologies"
type: source
tags: [system-design, interviews, technologies, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-key-technologies]
---

## Summary

A catalog of the key technology categories needed for 90% of system design problems, from [[Hello Interview]]. You don't need to know every option — just have at least one in each category. Focus on breadth before depth; probe depth scales with interview level.

## Technology Categories

### Core Database
- **Relational (Postgres, MySQL)**: Tables, SQL joins, indexes, ACID transactions. Default for product design interviews.
- **NoSQL (DynamoDB, Cassandra, MongoDB)**: Flexible schemas, horizontal scaling, various data models (key-value, document, column-family, graph). Default for infrastructure interviews.
- **Avoid** the SQL-vs-NoSQL comparison trap. Say what you know about your chosen DB and how it solves the problem.

### [[Blob Storage]]
- For large unstructured data (images, videos, files). Use S3/GCS, not your database.
- Store metadata (pointers/URLs) in your core DB; blobs in S3.
- **Presigned URLs** for direct client upload/download. CDN for global distribution.
- Chunking/multipart upload for large files. Infinitely scalable, cheap ($0.023/GB/month S3).

### Search Optimized Database
- Elasticsearch (built on Lucene). Uses **inverted indexes** to map words → documents.
- Supports tokenization, stemming, fuzzy search.
- Alternative: Postgres GIN indexes for simpler full-text search needs.

### API Gateway
- Routes requests to backend services. Handles auth, rate limiting, logging.
- Include in most product design interviews as first point of contact.
- Options: AWS API Gateway, Kong, Apigee, nginx.

### Load Balancer
- Distributes traffic across horizontally scaled machines.
- **L7**: routes by HTTP content (flexible). **L4**: routes by TCP (needed for WebSockets).
- Options: AWS ELB, nginx, HAProxy.
- In interviews: can often just mention "horizontally scaled" instead of drawing a LB box.

### [[Message Queues]]
- Buffer bursty traffic; decouple producers and consumers for independent scaling.
- FIFO ordering, retries, dead letter queues, partitioning by key.
- **Backpressure**: queue without capacity growth just obscures the problem.
- Avoid for synchronous workloads with strong latency requirements.
- Options: Kafka, SQS.

### [[Event Sourcing|Streams / Event Sourcing]]
- Retain events for replay, audit trails, multiple consumers.
- Event sourcing: store state changes as events, replay to reconstruct state.
- Partitioning, consumer groups, replication, windowing (time-based aggregation).
- Options: Kafka, Flink, Kinesis.

### [[Distributed Locks]]
- Lock a resource across systems for a duration (e.g., hold a ticket for 10 minutes during checkout).
- Implemented via Redis (Redlock) or ZooKeeper. Support TTL-based expiry.
- Use cases: e-commerce checkout, ride-sharing matchmaking, distributed cron, auctions.
- Watch for deadlocks when acquiring multiple locks.

### Distributed Cache
- Store expensive-to-compute data in memory. See [[Caching]] for concepts.
- Be explicit about **what data** and **which data structure** (sorted set, hash, etc.).
- Eviction policies: LRU, FIFO, LFU.
- Write strategies: write-through, write-around, write-back.
- Options: Redis (rich data structures), Memcached (simple key-value).

### CDN
- Cache content on edge servers close to users. Not just for static assets — can cache API responses.
- TTL-based eviction + cache invalidation.
- Options: Cloudflare, Akamai, CloudFront.

## Related

- [[Core Concepts for System Design Interviews]]
- [[System Design Delivery Framework]]
- [[System Design Interview Patterns]]
- [[Hello Interview]]
