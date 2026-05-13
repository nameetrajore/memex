---
title: "Distributed Locks"
type: concept
tags: [system-design, concurrency, locks, redis, infrastructure]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-key-technologies]
---

## Summary

Lock a resource across distributed systems for a bounded duration. Unlike database transaction locks (short-lived, automatic), distributed locks handle longer-term coordination like holding a ticket during checkout for 10 minutes. Implemented via Redis (Redlock) or ZooKeeper with TTL-based expiry.

## Key Points

- **Mechanism**: set a key in Redis atomically. If already set, lock acquisition fails. TTL ensures auto-release if holder crashes.
- **Redlock**: uses multiple Redis instances for safe, consistent lock acquisition.
- **Lock expiry**: prevents stuck locks from crashed processes.
- **Granularity**: lock a single resource or a group (one ticket vs a section of seats).
- **Deadlocks**: occur when two processes each hold a lock the other needs. Avoid by acquiring locks in consistent order and keeping lock scopes narrow.

## Common Use Cases
- E-commerce checkout (hold item during payment)
- Ride-sharing matchmaking (lock driver during assignment)
- Distributed cron jobs (ensure single execution)
- Online auctions (serialize final-second bids)

## Related

- [[System Design Key Technologies]]
- [[Caching]] (Redis)
- [[System Design Interview Patterns]] (Dealing with Contention)
