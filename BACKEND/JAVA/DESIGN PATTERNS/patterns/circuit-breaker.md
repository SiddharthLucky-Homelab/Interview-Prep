---
tags: [pattern, resilience, microservices]
title: Circuit Breaker
updated: 2025-10-30
---

# Circuit Breaker

Overview
- Prevents cascading failures by opening after repeated errors and letting a dependency recover.

When To Use
- Downstream instability, timeouts, or partial outages; protect hot paths.

Java/Spring Notes
- Resilience4j for circuit breaker, retries, bulkheads, timeouts; configure SLIs/SLOs and fallback paths.

Trade-offs
- Pros: Stability under failure; Cons: Potentially masks issues if thresholds are wrong.

Related
- [[Microservices Patterns]] • [[saga-pattern]] • [[api-gateway]]

