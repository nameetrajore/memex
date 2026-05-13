---
title: "Message Queues"
type: concept
tags: [system-design, queues, kafka, async, infrastructure]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-key-technologies]
---

## Summary

Buffers for bursty traffic and work distribution. A producer sends messages and forgets; workers process at their own pace. Queues decouple producers and consumers, enabling independent scaling. But they add latency — don't use them for synchronous workloads with tight latency requirements.

## Key Points

- **FIFO ordering**: messages processed in arrival order (usually). Kafka supports ordering within partitions.
- **Retries**: built-in redeliver mechanisms with configurable delay and max attempts.
- **Dead letter queues (DLQ)**: store unprocessable messages for debugging/audit.
- **Partitioning**: scale by distributing across servers. Partition key keeps related messages together.
- **Backpressure**: if consumption rate < production rate permanently, the queue just hides the problem. Must reject or slow producers when overwhelmed.

> [!warning] Common Mistake
> Pushing short-running jobs behind a queue when a synchronous response would be simpler, faster, and provide better UX and back-pressure.

## Technologies
- **Kafka** — distributed streaming platform, also usable as a queue. Excellent for high throughput.
- **SQS** — fully managed AWS queue service. Simpler operational model.

## Related

- [[System Design Key Technologies]]
- [[Event Sourcing]]
- [[System Design Interview Patterns]] (Long-Running Tasks, Scaling Writes)
