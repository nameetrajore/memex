---
title: "System Design Delivery Framework"
type: source
tags: [system-design, interviews, framework, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-delivery-framework]
---

## Summary

A step-by-step structure for delivering a complete system design in an interview, from [[Hello Interview]]. The most common failure mode is not delivering a working system — this framework prevents that by keeping you focused on the right things in the right order.

## The Framework

### 1. Requirements (~5 minutes)

#### Functional Requirements
- "Users should be able to..." statements.
- Ask targeted questions like a PM conversation. Arrive at a prioritized list of **top 3** core features.
- Long requirement lists hurt more than they help. FAANG interviewers evaluate your ability to focus.

#### Non-functional Requirements
- "The system should be..." statements. **Quantify** where possible.
- Bad: "low latency." Good: "low latency search, < 500ms."
- Checklist to consider: [[CAP Theorem]] choice, environment constraints, scalability (bursty traffic? read/write ratio?), latency targets, durability, security, fault tolerance, compliance.

#### Capacity Estimation
- **Skip upfront estimations** unless they directly influence your design. "It's a lot" is not useful.
- Do math **in context** when making decisions (e.g., "do we need to shard this?").
- Tell the interviewer you'll do math when needed during the design phase.

### 2. Core Entities (~2 minutes)

- List the core nouns/resources your API will exchange and your system will persist.
- Don't build the full data model yet — you'll discover more as you design.
- Choose good names. E.g., for Twitter: User, Tweet, Follow.

### 3. API / System Interface (~5 minutes)

- Define the contract between your system and its users.
- **Default to REST** unless you have a specific reason for GraphQL or gRPC.
- Use plural resource nouns: `/v1/tweets`, not `/v1/tweet`.
- Derive current user from auth token, never from request body.

### 4. [Optional] Data Flow (~5 minutes)

- For data-processing systems, describe the high-level sequence of actions.
- Skip if your system doesn't involve a long pipeline. E.g., web crawler: Fetch → Parse → Extract URLs → Store → Repeat.

### 5. High Level Design (~10–15 minutes)

- Draw boxes and arrows: servers, databases, caches, queues, etc.
- Go endpoint-by-endpoint, building up the design to satisfy each API.
- **Stay focused** — don't add caches/queues prematurely. Note areas for complexity, move on.
- Talk through data flow and state changes with each request.
- Document only non-obvious schema fields next to the database visually.

### 6. Deep Dives (~10 minutes)

- Harden the design: satisfy non-functional requirements, address edge cases, fix bottlenecks.
- Seniority determines proactivity: junior candidates get guided; senior candidates lead.
- **Don't talk over the interviewer** — give them room for specific signals they need.

## Related

- [[System Design in a Hurry — Introduction]]
- [[Core Concepts for System Design Interviews]]
- [[System Design Key Technologies]]
- [[System Design Interview Patterns]]
- [[Hello Interview]]
