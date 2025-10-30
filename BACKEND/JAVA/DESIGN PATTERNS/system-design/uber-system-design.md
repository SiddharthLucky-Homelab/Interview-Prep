---
tags: [system-design, uber, microservices, java]
title: Uber System Design (Java)
updated: 2025-10-30
---

# Uber System Design (Java)

Problem
- Design a ride-hailing platform: matching, pricing, real-time tracking, payments, reliability.

Core Services
- Dispatch/Matching, Driver, Rider, Trip, Pricing, Maps/Geo, Payments, Notifications.

Data & Scale
- Geospatial index (grid/quadkeys), hot cache for active drivers, append-only trip events.

Patterns Applied
- [[saga-pattern]] for trip lifecycle; [[circuit-breaker]] for downstream resilience; [[cqrs]] + [[event-sourcing]] for trip state; [[api-gateway]] for client access; see [[microservices-patterns]].

Tech Notes (Java)
- Spring Boot services, Kafka streams for events, Redis for proximity cache, Postgres/Cloud SQL for OLTP, object storage for receipts.

Related
- [[Java Design Patterns Hub]] • [[Tech Stack Profile]] • [[scalability-checklist]] • [[cap-theorem]] • [[java-implementation-notes]]

