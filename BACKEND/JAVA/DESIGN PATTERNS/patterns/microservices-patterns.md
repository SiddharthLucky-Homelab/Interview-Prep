---
tags: [pattern, microservices, java]
title: Microservices Patterns
updated: 2025-10-30
---

# Microservices Patterns

Overview
- Collection of tactics for reliable, scalable services. See also [[saga-pattern]], [[cqrs]], [[event-sourcing]], [[circuit-breaker]], [[api-gateway]].

When To Use
- Independent deployability, team ownership per service, mixed tech stacks, or scaling hotspots.

Java/Spring Notes
- Spring Boot + Spring Cloud (Config, Gateway, Sleuth/Observability). Use Idempotency keys and retry/backoff.

Trade-offs
- Network complexity, eventual consistency, higher operational overhead.

Related
- Hub: [[Java Design Patterns Hub]]
- Context: [[Tech Stack Profile]]

