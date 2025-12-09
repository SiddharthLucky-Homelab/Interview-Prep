---
tags: [design, patterns, interview, checklist]
title: Design Patterns Lightning Review
updated: 2025-11-01
---

# Design Patterns Lightning Review

Quick one-pager consolidating interview checklists for core microservices patterns.

## API Gateway
- Role: edge routing, authN/Z, rate limiting, CORS, TLS.
- BFF vs. Gateway vs. Ingress; avoid SPOF, canary rule changes.
- Spring Cloud Gateway routes + custom filters; timeouts/retries/CBs.
- Observability (traceId, p95/5xx), security headers, caching.

## Circuit Breaker
- States: Closed → Open → Half-Open; thresholds and cool-downs.
- Pair with timeouts, retries (jitter), bulkheads; protect hot paths.
- Resilience4j config + annotations; expose metrics and health.
- Pitfalls: over-retrying, long timeouts, swallowing errors.

## CQRS
- Separate command/write model from query/read models.
- Propagate via events + outbox; eventual consistency; idempotent projections.
- Rebuild projections from history/backfills; version events.
- Use when read/write asymmetry or complex aggregates; avoid for simple CRUD.

## Event Sourcing
- Store immutable domain events; rebuild state by replay; snapshot for speed.
- Storage layout, per-aggregate ordering; outbox/log for integrations.
- Schema evolution: versioning, up-casters, migrations.
- PII strategies: tokenization/envelope encryption/crypto-erasure.

## Saga Pattern
- Long-lived transactions using local TX + compensations.
- Orchestration (central workflow) vs. choreography (event-driven).
- Idempotency, timeouts, retries, DLQs, and saga state observability.
- Tooling: Temporal/Camunda; design compensations carefully.

## Microservices Patterns (Hub)
- Decompose by business capability; DB-per-service; integrate via events.
- Resilience (CBs, bulkheads, timeouts), sagas for transactions.
- Observability; CI/CD; zero-downtime migrations (expand/migrate/contract).
- Anti-patterns: shared DB, chatty sync calls, too-tiny services.

## Service Discovery
- Client-side (Eureka/Consul) vs. server-side (K8s Service/ALB).
- Registration, health/eviction, zone-aware routing; DNS naming in K8s.
- Spring `@LoadBalanced` RestTemplate/WebClient; mesh adds policies+mTLS.
- Pitfalls: stale registry, cross-zone chatter, polyglot complexity.

