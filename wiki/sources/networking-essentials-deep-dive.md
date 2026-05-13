---
title: "Networking Essentials Deep Dive"
type: source
tags: [system-design, networking, protocols, load-balancing, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [networking-essentials-deep-dive]
---

## Summary

Comprehensive networking guide from [[Hello Interview]] covering the OSI model, transport protocols (TCP/UDP/QUIC), application protocols (HTTP, REST, GraphQL, gRPC, SSE, WebSockets, WebRTC), load balancing (client-side, L4, L7), regionalization/latency, and failure handling patterns (retries, idempotency, circuit breakers).

## Transport Layer Protocols

### TCP — Reliable, Ordered
- Connection-oriented via 3-way handshake (SYN → SYN-ACK → ACK).
- Reliable delivery, ordering, flow control, congestion control.
- Stateful connection ("stream"). Default for nearly everything.

### UDP — Fast, Unreliable
- Connectionless, no delivery/ordering guarantees, lower latency.
- Use when speed > reliability: live streaming, gaming, VoIP, DNS, telemetry.
- No native browser support except via WebRTC.

### QUIC
- Modernized TCP over UDP. Better performance but less adoption. Treat as "better TCP" for interviews.

## Application Layer Protocols

### HTTP/HTTPS
- Stateless request-response. Methods: GET, POST, PUT, PATCH, DELETE.
- Status codes: 2xx success, 3xx redirect, 4xx client error, 5xx server error.
- HTTPS encrypts in transit (TLS) — but never trust request body without validation.
- Headers are flexible key-value pairs (content negotiation via Accept-Encoding, etc.).

### REST
- Resources (nouns, plural) + HTTP verbs. Default for interviews.
- Core entities map to resources. `/users/{id}/posts` for nested relationships.
- Not the most performant (JSON overhead) but well-understood and sufficient for most problems.

### GraphQL
- Single endpoint, client specifies exact data shape. Solves over-fetching / under-fetching.
- **N+1 problem**: naive resolvers fire N separate queries. Fix with batching/dataloader.
- Use when interviewer mentions diverse clients or flexible data needs. Otherwise default to REST.

### gRPC
- Binary serialization (Protocol Buffers) over HTTP/2. ~10x throughput vs REST.
- Generated client/server stubs with compile-time type safety.
- Internal service-to-service only (no browser support). Common pattern: REST external, gRPC internal.

### SSE (Server-Sent Events)
- Server pushes multiple messages as chunks in a single HTTP response.
- Automatic reconnection with last-event-ID. Limitations: connection timeouts, misbehaving proxies.
- Good for: auction prices, notifications, one-way push.

### WebSockets
- Persistent bidirectional TCP connection via HTTP upgrade.
- Need infrastructure support (firewalls, proxies, LBs). Don't use unless you genuinely need bidirectional real-time.
- Justify over SSE or polling before proposing.

### WebRTC
- Peer-to-peer via STUN/TURN servers. Only app-level protocol using UDP.
- Use exclusively for video/audio calling. Don't try P2P for other problems.

## Load Balancing

### Client-Side
- Client queries service registry, picks server directly. Fast, no extra hop.
- Examples: Redis Cluster gossip protocol, DNS rotation, gRPC built-in.
- Works for small controlled client sets or tolerant-of-delay scenarios (DNS TTL).

### L4 (Transport Layer)
- Routes by IP/port without inspecting content. Maintains persistent TCP connections.
- Fast, efficient. **Use for WebSocket connections**.

### L7 (Application Layer)
- Inspects HTTP content, routes by URL/headers/cookies. Terminates + re-creates connections.
- More flexible (route APIs vs static content differently). **Default for HTTP traffic**.

### Health Checks & Algorithms
- Health checks: TCP or HTTP-level. Automatic failover.
- Algorithms: Round Robin, Random, Least Connections (good for persistent connections), Least Response Time, IP Hash.

## Regionalization & Latency
- NY→London: ~56ms minimum from physics alone.
- **CDN**: cache at edge locations. Best for static/cacheable content + some API responses.
- **Regional partitioning**: co-locate data and compute per region (e.g., Uber by city).

## Failure Handling

### Retries with Exponential Backoff + Jitter
- Retry transient failures with increasing delays + randomness.
- **Magic phrase interviewers look for**: "retry with exponential backoff."

### Idempotency
- Ensure repeated requests produce same result. GET/PUT/DELETE are naturally idempotent.
- For writes: use **idempotency keys** (e.g., user_id + date for payments).

### Circuit Breakers
- Monitor failure rate → trip to "open" state → fail fast → half-open test → close or stay open.
- Prevents cascading failures / thundering herd. Use at service boundaries, DB connections, external API calls.

## Related

- [[Networking Essentials]]
- [[API Design]]
- [[Core Concepts for System Design Interviews]]
- [[Hello Interview]]
