---
tags: [reference, java, spring]
title: Java Implementation Notes
updated: 2025-10-30
---

# Java Implementation Notes

Spring
- Spring Boot + Spring Cloud for config, discovery (if used), gateway, resilience.

Data
- JPA/Hibernate for OLTP; FlywayDB for migrations; Kafka for events; outbox pattern to avoid dual writes.

Ops
- Docker images, K8s/OpenShift; CI with Jenkins/Tekton; secrets via cloud managers; observability with Micrometer + vendor backends.

Related
- [[Tech Stack Profile]] • [[Java Design Patterns Hub]]

