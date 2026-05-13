---
title: "Networking Essentials"
type: concept
tags: [system-design, networking, protocols, load-balancing]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, networking-essentials-deep-dive]
---

## Summary

Foundational networking for system design: transport protocols (TCP default, UDP for real-time), application protocols (HTTP/REST default, SSE/WebSockets for push, gRPC for internal perf), load balancing (L7 default, L4 for WebSockets), and strategies for latency and failure handling.

## Transport Layer

| | TCP | UDP |
|---|---|---|
| Connection | Connection-oriented (3-way handshake) | Connectionless |
| Reliability | Guaranteed, ordered delivery | Best-effort |
| Use cases | Everything else | Streaming, gaming, VoIP, DNS |
| Browser support | Full | WebRTC only |

**QUIC**: modernized TCP over UDP. Treat as "better TCP" for interviews.

## Application Protocols

- **HTTP/REST** — stateless request-response. Default for external APIs.
- **GraphQL** — single endpoint, client specifies data shape. For diverse clients / flexible data needs.
- **gRPC** — binary (protobuf) over HTTP/2, ~10x faster. Internal service-to-service only.
- **SSE** — server pushes messages as HTTP response chunks. Unidirectional. Auto-reconnect.
- **WebSockets** — persistent bidirectional via HTTP upgrade. Only when you genuinely need bidirectional real-time.
- **WebRTC** — peer-to-peer via STUN/TURN. Audio/video calling only.

## Load Balancing

- **Client-side**: client queries registry, picks server. Fast. Redis Cluster, DNS, gRPC built-in.
- **L4**: routes by IP/port, no content inspection. **Use for WebSockets.**
- **L7**: inspects HTTP content, routes by URL/headers/cookies. **Default for HTTP.**
- **Algorithms**: Round Robin (default), Least Connections (for persistent connections), IP Hash (session affinity).
- **Health checks**: TCP or HTTP-level probes for automatic failover.

## Latency & Regionalization

- NY→London ≥ 56ms (speed of light through fiber).
- **CDN**: cache at edge for static/cacheable content.
- **Regional partitioning**: co-locate data + compute per geography (Uber by city).

## Failure Handling

- **Retries with exponential backoff + jitter**: the magic phrase interviewers want.
- **Idempotency**: GET/PUT/DELETE naturally idempotent. Use idempotency keys for writes (payments).
- **Circuit breakers**: monitor failures → trip → fail fast → test recovery. Prevents cascading failures.

> [!tip] Interview Tip
> Don't propose WebSockets when SSE or polling suffices. Default to TCP, HTTP, REST, L7 load balancer. Reach for alternatives only with specific justification.

## Related

- [[Networking Essentials Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[API Design]]
- [[Caching]] (CDN)
