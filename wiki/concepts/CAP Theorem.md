---
title: "CAP Theorem"
type: concept
tags: [system-design, distributed-systems, consistency, availability]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, cap-theorem-deep-dive]
---

## Summary

In a distributed system, you can only guarantee two of three properties: Consistency, Availability, and Partition tolerance. Since network partitions are unavoidable, the real choice is between consistency and availability. Most systems should default to availability (eventual consistency) unless stale data costs money.

## The Core Choice

Network partitions are unavoidable. CAP boils down to: **when a partition occurs, do you prioritize consistency (reject requests that can't guarantee freshness) or availability (serve potentially stale data)?**

Simple test: "Would it be catastrophic if users briefly saw inconsistent data?" Yes → consistency. No → availability.

## When to Choose Consistency

- **Ticket booking**: prevent double-booking the same seat.
- **E-commerce inventory**: prevent overselling.
- **Financial systems**: accurate order books, account balances.

Design implications: distributed transactions (2PC), single-node solutions for simplicity. Technologies: PostgreSQL, Spanner, DynamoDB (strong consistency mode).

## When to Choose Availability

- **Social media**: profile picture updates can be stale for minutes.
- **Content platforms**: movie descriptions can lag.
- **Review sites**: slightly outdated business hours are acceptable.

Design implications: multiple read replicas with async replication, CDC for eventual propagation. Technologies: Cassandra, DynamoDB (multi-AZ), Redis clusters.

## Mixed Consistency (Senior+ Level)

Real systems need both. Examples:
- **Ticketmaster**: strong consistency for booking, availability for browsing events.
- **Tinder**: consistency for matching, availability for viewing profiles.

> [!tip] Interview Phrase
> "For this system, I'll prioritize consistency for [critical operation] but optimize for availability when users are [browsing/viewing]."

## Consistency Spectrum

| Level | Guarantee | Example |
|-------|-----------|---------|
| **Strong** | All reads reflect most recent write. | Bank balances, inventory. |
| **Causal** | Related events appear in same order. | Comments appear after the post. |
| **Read-your-own-writes** | You see your own updates immediately. | Social media profile edits. |
| **Eventual** | System converges given time. No guarantees on when. | DNS, social feeds, analytics. |

## Important Distinctions

- **CAP "consistency" ≠ ACID "consistency"**. Different concepts despite the same word.
- **PACELC extension**: during Partition → A or C; Else → Latency or Consistency. Strong consistency always adds latency due to node coordination.

## Related

- [[CAP Theorem Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[Sharding]]
- [[Data Modeling]]
- [[Consistent Hashing]]
