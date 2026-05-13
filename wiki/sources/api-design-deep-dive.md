---
title: "API Design Deep Dive"
type: source
tags: [system-design, api, rest, graphql, grpc, hello-interview]
created: 2026-05-13
updated: 2026-05-13
sources: [api-design-deep-dive]
---

## Summary

Deep dive on API design for system design interviews from [[Hello Interview]]. Covers REST resource modeling, GraphQL for flexible data fetching, RPC/gRPC for internal services, pagination strategies, versioning, and security (JWT, API keys, RBAC, rate limiting). Key takeaway: don't spend more than 5 minutes on APIs in an interview.

## REST (Default Choice)

### Resource Modeling
- Resources = core entities from delivery framework. Plural nouns: `/events`, `/bookings`.
- Resources are **things**, not actions. Not `updateUser` but `PUT /users/{id}`.
- Nested resources for required parent-child: `/events/{id}/tickets`.
- Query params for optional filters: `/tickets?event_id=123&section=VIP`.

### HTTP Methods & Idempotency
| Method | Purpose | Idempotent? |
|--------|---------|-------------|
| GET | Retrieve | Yes |
| POST | Create | No |
| PUT | Replace entire resource | Yes |
| PATCH | Partial update | Depends |
| DELETE | Remove | Yes |

### Passing Data
- **Path params**: required resource identifiers (`/events/123`).
- **Query params**: optional filters/modifiers (`?notify=true`).
- **Request body**: payload for create/update operations.

### Returning Data
- Status codes: 200 OK, 201 Created, 400 Bad Request, 401 Unauthorized, 404 Not Found, 429 Too Many Requests, 500 Server Error.
- Response body: typically JSON.

## GraphQL

- Single endpoint, client specifies exact data shape.
- Solves over-fetching (returning too much) and under-fetching (needing multiple requests).
- **N+1 problem**: querying events with venues → 1 + N queries. Fix with dataloader/batching.
- Schema defines types with embedded relationships; authorization at field level.
- Use when interviewer mentions diverse clients / flexible data needs. Otherwise REST.

## RPC / gRPC

- Action-oriented: `getEvent(eventId)` instead of `GET /events/{id}`.
- Protocol Buffers: binary serialization, ~10x faster than JSON. Compile-time type safety.
- Use for internal service-to-service. Don't outline internal APIs during the API step — mention gRPC during high-level design.

## Common Patterns

### Pagination
- **Offset-based**: `/events?offset=20&limit=10`. Simple but unstable with real-time data (shifting records).
- **Cursor-based**: `/events?cursor=<encoded_id>&limit=10`. Stable, not affected by new records. Harder to "jump to page 5".
- Default to offset unless dealing with real-time feeds.

### Versioning
- **URL versioning**: `/v1/events` — explicit, easy. Default choice.
- **Header versioning**: `Accept-Version: v2` — cleaner URLs but less obvious.

## Security

### Authentication vs Authorization
- **Authentication**: who are you? (JWT, API keys)
- **Authorization**: are you allowed? (RBAC, resource ownership)

### JWT vs API Keys
- **API keys**: for server-to-server / 3rd party developer access. Long-lived, no user context.
- **JWT**: for user sessions. Encode user_id, role, expiry. Stateless verification. Use in distributed systems.

### Rate Limiting
- Per-user, per-IP, or per-endpoint limits. Return 429 on exceed. Implement at API gateway.
- Mention it briefly; don't design the algorithm unless asked.

## Related

- [[API Design]]
- [[Networking Essentials Deep Dive]]
- [[System Design Delivery Framework]]
- [[Hello Interview]]
