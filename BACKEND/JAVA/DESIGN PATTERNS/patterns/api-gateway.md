---
tags: [pattern, gateway, microservices]
title: API Gateway
updated: 2025-10-30
---

# API Gateway

Overview
- Single entry point for client traffic; handles routing, auth, rate limit, aggregation.

When To Use
- Many backend services, need consistent cross-cutting concerns and versioning.

Java/Spring Notes
- Spring Cloud Gateway; external platforms like Apigee for enterprise policies.

Trade-offs
- Pros: Simplified clients; Cons: Gateway becomes a critical dependency.

Related
- [[Microservices Patterns]] • [[circuit-breaker]] • [[saga-pattern]] • [[Java Design Patterns Hub]]

