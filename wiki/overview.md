---
title: "Overview"
type: overview
tags: [system-design, interviews]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-in-a-hurry-introduction, how-to-prepare-for-system-design-interviews, core-concepts-for-system-design-interviews, consistent-hashing-deep-dive, cap-theorem-deep-dive, numbers-to-know-deep-dive, llm-wiki]
---

# Overview

## Summary

This wiki is a persistent, compounding knowledge base — an instantiation of [[Andrej Karpathy]]'s [[LLM Wiki]] pattern, itself descended from [[Vannevar Bush]]'s 1945 Memex vision. The primary content so far covers system design interview preparation, drawn from [[Hello Interview]]'s "System Design in a Hurry" course.

## Current Themes

### System Design Interview Structure
System design interviews assess your ability to decompose ambiguous problems into infrastructure components. They are practical (not academic), have no single right answer, and become critical at the senior+ level. Interviewers evaluate four competencies: problem navigation, solution design, technical excellence, and communication.

### Core Building Blocks
Eight technology-agnostic concepts form the foundation of every design: [[Networking Essentials]], [[API Design]], [[Data Modeling]], [[Database Indexing]], [[Caching]], [[Sharding]], [[Consistent Hashing]], and [[CAP Theorem]]. Mastery of these — plus knowing modern hardware [[Numbers to Know|numbers]] — lets you make justified decisions rather than applying techniques blindly.

### Preparation Philosophy
Work backwards from the interview. Build a foundation (delivery framework + core concepts), then practice deliberately with graded problems across three difficulty tiers. Speaking your design under time pressure is a different skill from reading about it.

### Delivery Framework
A structured approach to the interview itself: Requirements (5 min) → Core Entities (2 min) → API (5 min) → High Level Design (10–15 min) → Deep Dives (10 min). The framework prevents the #1 failure mode: not delivering a working system. Skip upfront capacity estimation; do math in context when making decisions.

### Key Technologies
Ten technology categories cover 90% of problems: Core DB (Postgres/DynamoDB), [[Blob Storage]] (S3), Search DB (Elasticsearch), API Gateway, Load Balancer, [[Message Queues]] (Kafka/SQS), Streams/[[Event Sourcing]], [[Distributed Locks]] (Redis), Distributed Cache (Redis), CDN. Pick one in each category; focus on breadth before depth.

### Common Patterns
Eight reusable patterns combine concepts and technologies: Realtime Updates, Long-Running Tasks, Dealing with Contention, Scaling Reads, Scaling Writes, Handling Large Blobs, Multi-Step Processes, and Proximity-Based Services. Patterns often compose — a video platform uses 4+ patterns simultaneously.

### Hardware Reality Check
Modern hardware is far more powerful than most candidates assume. A single Postgres node handles 50K read TPS and 10–20K write TPS; Redis caches hold up to 1TB with sub-millisecond latency; a single Kafka broker processes 1M msgs/sec. Many systems that seem to need distributed architectures can run on one well-tuned machine. Knowing the [[Numbers to Know|real numbers]] prevents premature sharding, unnecessary caching layers, and over-engineered queue architectures.

### Meta: The LLM Wiki Pattern
This vault follows [[Andrej Karpathy]]'s [[LLM Wiki]] pattern: raw sources are immutable inputs; the LLM builds and maintains all wiki pages; a schema (CLAUDE.md) governs structure and workflows. The human curates sources and asks questions; the LLM handles summarizing, cross-referencing, and bookkeeping. The key insight: wikis fail because maintenance cost exceeds value — LLMs make maintenance cost near zero, so the knowledge compounds.

## Open Questions

*All 16 source documents have been ingested. No outstanding open questions at this time.*

## Related

- [[index]]
- [[log]]
- [[Hello Interview]]
- [[LLM Wiki]]
- [[Andrej Karpathy]]
- [[Vannevar Bush]]
