---
title: "API Design"
type: concept
tags: [system-design, api, rest, graphql, grpc]
created: 2026-05-13
updated: 2026-05-13
sources: [core-concepts-for-system-design-interviews, api-design-deep-dive]
---

## Summary

Design how clients interact with your system. Default to REST (resources + HTTP verbs). Spend max 5 minutes on APIs in interviews — sketch 4–5 endpoints and move on. Know pagination, auth, and rate limiting patterns but don't go deep unless asked.

## Protocol Selection

| Protocol | When | Not For |
|----------|------|---------|
| **REST** | Default. 90% of use cases. | Ultra-high perf internal APIs |
| **GraphQL** | Diverse clients, flexible data needs, avoid over/under-fetching | Fixed requirements (most interviews) |
| **gRPC** | Internal service-to-service, perf-critical | External/browser-facing APIs |

## REST Design

- **Resources** = core entities. Plural nouns: `/events`, `/bookings`.
- Think **things**, not actions. Not `bookTicket()` but `POST /events/{id}/bookings`.
- **Nested** for required parent-child: `/events/{id}/tickets`. **Query params** for optional filters: `/tickets?event_id=123&section=VIP`.

### Data Passing
- **Path params**: required identifiers (`/events/123`).
- **Query params**: optional modifiers (`?page=2&limit=20`).
- **Request body**: create/update payload (JSON).

### Idempotency
- GET, PUT, DELETE: idempotent. POST, PATCH: not guaranteed.
- Matters for retry safety. Use idempotency keys for non-idempotent writes.

## Pagination
- **Offset-based**: `/events?offset=20&limit=10`. Simple, fine for most cases. Unstable with real-time inserts.
- **Cursor-based**: `/events?cursor=<id>&limit=10`. Stable, but can't jump to page N. Use for real-time feeds.

## Security
- **JWT**: user sessions. Encode user_id, role, expiry. Stateless verification. Default for user-facing.
- **API keys**: server-to-server, 3rd party devs. Long-lived, no user context.
- **RBAC**: roles (customer, admin) with permissions. Check auth + authorization per endpoint.
- **Rate limiting**: per-user/IP/endpoint. Return 429. Implement at gateway.

> [!tip] Interview Tip
> If you're still designing APIs 10 minutes in, you're going too deep. Most interviewers care more about architecture than perfect API specs.

## Related

- [[API Design Deep Dive]]
- [[Core Concepts for System Design Interviews]]
- [[Networking Essentials]]
- [[System Design Delivery Framework]]
