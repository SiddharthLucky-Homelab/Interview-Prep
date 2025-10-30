---
tags: [pattern, saga, transactions, microservices]
title: Saga Pattern
updated: 2025-10-30
---

# Saga Pattern

Overview
- Coordinates a long-lived business transaction across services using local transactions and compensations.

When To Use
- Multi-step workflows requiring consistency without distributed 2PC (e.g., trip booking: reserve -> charge -> confirm).

Approaches
- Orchestration (central coordinator)
- Choreography (event-driven, no central brain)

Java/Spring Notes
- Orchestration via a workflow engine (e.g., Camunda/Temporal) or a coordinator service. Choreography with Kafka topics and compacted command/event streams.

Trade-offs
- Pros: Scalability, resilience; Cons: Complexity, compensations can be tricky.

Related
- [[Microservices Patterns]] • [[cqrs]] • [[event-sourcing]] • [[api-gateway]]

