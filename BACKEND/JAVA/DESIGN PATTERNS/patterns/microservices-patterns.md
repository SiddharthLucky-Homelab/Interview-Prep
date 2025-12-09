---
tags: [pattern, microservices, java]
title: Microservices Patterns
updated: 2025-10-30
---

# Microservices Patterns

Overview
- Collection of tactics for reliable, scalable services. See also [[saga-pattern]], [[cqrs]], [[event-sourcing]], [[circuit-breaker]], [[api-gateway]], [[service-discovery]].

Pattern Categories
- **Decomposition:** Patterns for breaking down a monolithic application into smaller services.
- **Integration:** Patterns for how services communicate with each other.
- **Data Management:** Patterns for managing data in a microservices architecture.
- **Observability:** Patterns for monitoring and troubleshooting microservices.
- **Cross-Cutting Concerns:** Patterns for handling concerns that affect multiple services, suchs as security and configuration.

When To Use
- Independent deployability, team ownership per service, mixed tech stacks, or scaling hotspots.

Java/Spring Notes
- Spring Boot + Spring Cloud (Config, Gateway, Sleuth/Observability). Use Idempotency keys and retry/backoff.

Trade-offs
- Network complexity, eventual consistency, higher operational overhead.

Related
- Hub: [[Java Design Patterns Hub]]
- Context: [[Tech Stack Profile]]

## Core Patterns
- Decomposition: by business capability (DDD bounded contexts), not by technical layers.
- Data: database‑per‑service; integration via events; avoid shared DB schemas.
- Resilience: [[circuit-breaker]], timeouts, bulkheads, retries with jitter, backpressure.
- Transactions: [[saga-pattern]] for cross‑service workflows; avoid 2PC.
- API: contract‑first with OpenAPI; versioning and backward compatibility.
- Gateway: [[api-gateway]] or Ingress for routing, authZ, quotas, and edge policies.

## Architecture Checklist
- Ownership: one team per service, clear SLAs/SLOs, on‑call rotation.
- Observability: traces, logs, metrics; propagate `traceId` across services.
- Configuration: externalized via Spring Cloud Config or environment; immutable images.
- Packaging: containerized; health/readiness probes; graceful shutdown.
- Security: mTLS between services, JWT scopes, secrets manager, least privilege.

## Java/Spring Blueprint
```text
service
 ├─ api (DTOs, controllers)
 ├─ domain (entities, aggregates, use cases)
 ├─ infra (JPA, messaging, clients)
 └─ app (config, metrics, wiring)
```

```yaml
# Key Spring Boot properties
server:
  shutdown: graceful
management:
  endpoints.web.exposure.include: health,info,metrics,prometheus
  health.livenessstate.enabled: true
  health.readinessstate.enabled: true
```

## Delivery & Ops
- CI/CD: trunk‑based or short‑lived branches; canary or blue‑green deploys.
- Testing pyramid: unit → contract (Consumer‑Driven) → integration → e2e smoke.
- Migrations: FlywayDB; zero‑downtime schema changes (expand → migrate → contract).
- Capacity: horizontal scale first; set SLOs (latency, error rate) and autoscale rules.

## Common Anti‑Patterns
- Shared database across services (tight coupling, lock contention).
- Chatty synchronous calls in the hot path; prefer event‑driven or aggregation.
- Over‑granularity: too many tiny services increase overhead; start with modules.

## See Also
- [[cqrs]] • [[event-sourcing]] • [[saga-pattern]] • [[circuit-breaker]] • [[api-gateway]]

## Interview Checklist
- Justify microservices vs. modular monolith; decomposition by business capability.
- Cover data ownership (DB‑per‑service) and integration via events.
- Explain resilience patterns and cross‑service transactions (sagas).
- Mention observability, CI/CD, zero‑downtime migrations, and security basics.
- Call out common anti‑patterns (shared DB, chatty calls, tiny services).

## Practice Questions
- You’re asked to decompose a monolith: outline a migration path that preserves delivery velocity and data integrity. What are the first 2–3 services and why?
- Answer Outline: slice by business capability and team boundaries, pick high‑value low‑dependency domains, use strangler/anti‑corruption layer, migrate data via change data capture, iterate safely.
- How would you design zero‑downtime schema changes for a high‑traffic service? Walk through expand/migrate/contract steps.
- Answer Outline: expand (add nullable/compatible fields), dual‑write/read, backfill, switch writes, verify, then contract (drop old) in separate deploy; feature flags and canaries.
