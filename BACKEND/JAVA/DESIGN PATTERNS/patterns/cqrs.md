---
tags: [pattern, cqrs, microservices]
title: CQRS
updated: 2025-10-30
---

# CQRS

Overview
- Split write (commands) and read (queries) models to scale independently and optimize data access.

When To Use
- High read/write asymmetry, complex aggregates, or need for denormalized read models.

Java/Spring Notes
- Command side with JPA; read side with projections/materialized views. Consider messaging (Kafka/Pub/Sub) to build read models.

Trade-offs
- Pros: Performance and separation; Cons: Eventual consistency and complexity.

Related
- [[Microservices Patterns]] • [[event-sourcing]] • [[saga-pattern]]

