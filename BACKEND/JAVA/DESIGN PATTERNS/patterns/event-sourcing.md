---
tags: [pattern, event-sourcing, microservices]
title: Event Sourcing
updated: 2025-10-30
---

# Event Sourcing

Overview
- Persist state changes as an append-only stream of events; rebuild state by replaying events.

When To Use
- Audit/history, temporal queries, or complex domain invariants.

Java/Spring Notes
- Use Kafka or an event store; add snapshots; version events and handlers.

Trade-offs
- Pros: Auditability, decoupled reads; Cons: evolution and operational complexity.

Related
- [[cqrs]] • [[saga-pattern]] • [[Microservices Patterns]] • [[Java Design Patterns Hub]] • [[Tech Stack Profile]]

