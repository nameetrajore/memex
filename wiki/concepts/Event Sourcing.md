---
title: "Event Sourcing"
type: concept
tags: [system-design, streaming, events, kafka, infrastructure]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-key-technologies]
---

## Summary

Store state changes as a sequence of immutable events rather than overwriting current state. Events can be replayed to reconstruct state at any point in time. Streams retain data for configurable periods, supporting multiple consumers, audit trails, and complex processing scenarios.

## Key Points

- **Replay**: reconstruct application state by replaying events from any point in time.
- **Multiple consumer groups**: different consumers process the same stream independently (e.g., one for dashboards, one for historical storage).
- **Partitioning**: scale by distributing events across partitions. Partition key keeps related events together.
- **Replication**: fault tolerance via data replication across servers.
- **Windowing**: group events by time or count for aggregation (e.g., hourly delivery time averages).
- **Pub/Sub pattern**: publish events to a stream; multiple subscribers receive them simultaneously (chat rooms, notifications).

## When to Use
- Real-time processing of high-volume data
- Audit trails and transaction reversal (banking)
- Multiple consumers needing the same data stream
- Event-driven architectures

## Technologies
- **Kafka** — distributed streaming + event sourcing
- **Flink** — stream processing engine
- **Kinesis** — AWS managed streaming service

## Related

- [[Message Queues]]
- [[System Design Key Technologies]]
- [[System Design Interview Patterns]] (Realtime Updates, Multi-Step Processes)
