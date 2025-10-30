---
tags: [reference, scalability]
title: Scalability Checklist
updated: 2025-10-30
---

# Scalability Checklist

Quick Checks
- Hot paths and read/write split (see [[cqrs]]).
- Caching layers (CDN, Redis), idempotency, backpressure.
- Async/event-driven where possible; bounded queues.
- Health, timeouts, retries, [[circuit-breaker]].
- Observability: metrics, tracing, log hygiene.

Related
- [[Java Design Patterns Hub]] • [[Tech Stack Profile]]

