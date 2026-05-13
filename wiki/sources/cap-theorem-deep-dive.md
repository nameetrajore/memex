---
title: "CAP Theorem Deep Dive"
type: source
tags: [system-design, distributed-systems, consistency, availability, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [cap-theorem-deep-dive]
---

## Summary

Deep dive on the CAP theorem from [[Hello Interview]]. Explains the theorem through practical examples, when to choose consistency vs availability, how to mix both within one system, the consistency spectrum (strong → causal → read-your-own-writes → eventual), and design implications.

## The Core Choice

Network partitions are unavoidable. CAP boils down to: **when a partition occurs, do you prioritize consistency (reject requests that can't guarantee freshness) or availability (serve potentially stale data)?**

Simple test: "Would it be catastrophic if users briefly saw inconsistent data?" Yes → consistency. No → availability.

## When to Choose Consistency

- **Ticket booking**: prevent double-booking the same seat.
- **E-commerce inventory**: prevent overselling.
- **Financial systems**: accurate order books, account balances.

Design implications: distributed transactions (2PC), single-node solutions for simplicity, technologies like PostgreSQL, Spanner, DynamoDB (strong consistency mode).

## When to Choose Availability

- **Social media**: profile picture update can be stale for minutes.
- **Content platforms**: movie descriptions can lag.
- **Review sites**: slightly outdated business hours are acceptable.

Design implications: multiple read replicas with async replication, CDC for eventual propagation, technologies like Cassandra, DynamoDB (multi-AZ), Redis clusters.

## Mixed Consistency (Senior+ Level)

Real systems need both. Ticketmaster: strong consistency for booking, availability for browsing events. Tinder: consistency for matching, availability for viewing profiles.

> [!tip] Interview Phrase
> "For this system, I'll prioritize consistency for [critical operation] but optimize for availability when users are [browsing/viewing]."

## Consistency Spectrum

| Level | Guarantee | Example |
|-------|-----------|---------|
| **Strong** | All reads reflect most recent write. | Bank balances, inventory. |
| **Causal** | Related events appear in same order. | Comments appear after the post. |
| **Read-your-own-writes** | You see your own updates immediately. | Social media profile edits. |
| **Eventual** | System converges given time. No guarantees on when. | DNS, social feeds, analytics. |

> [!note] Note
> CAP "consistency" ≠ ACID "consistency". Different concepts despite same word.

## Related

- [[CAP Theorem]]
- [[Sharding]]
- [[Data Modeling]]
- [[Hello Interview]]
