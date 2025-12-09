---
tags: [architecture, domain-driven-design, backend, java]
title: Architecture And Domain Design
aliases: ["Architecture And Domain Design"]
updated: 2025-11-05
---

# Architecture And Domain Design

Design services around the domain so that teams can ship independently while evolving the system safely.

## Senior POV Highlights
- Treat architecture as a product roadmap: align service boundaries with business capabilities, KPIs, and team topology.
- Combine Domain-Driven Design (DDD) with evolutionary architecture: plan for change via contracts, fitness functions, and architecture decision records (ADRs).
- Balance microservices vs. modular monoliths. Choose the smallest deployable unit that unlocks autonomy without exploding operational overhead.

## Practice Checklist
- DDD discovery: event storming, context mapping, ubiquitous language.
- Boundaries: bounded contexts, aggregates, anti-corruption layers, strangler patterns for legacy carve-outs.
- Data ownership: single-service write authority, contract testing for upstream/downstream consumers.
- Architecture styles: layered, hexagonal/clean, CQRS/event sourcing, serverless vs. containerized.
- Governance: ADRs, architecture runway, platform/product guardrails.

## Evolution Strategy
- Start modular: enforce package boundaries, hide persistence, use domain services + interfaces.
- Extract services when teams or scaling pressure demand it. Provide API façade to preserve callers.
- Use incremental refactoring: branch by abstraction, feature toggles, consumer-driven contracts before cutting over.

## Design Conversations To Practice
- Walk through decomposing a monolith: identify seams, data migration, coexistence strategy, rollout safety.
- Explain how bounded contexts inform API design and data models.
- Compare orchestration vs. choreography for cross-context workflows.
- Discuss domain multi-tenancy: per-tenant schema vs. shared schema with tenant scoping.
- Map architecture principles to a concrete platform like [[Concept Application Overview]] to show end-to-end thinking.

## Interview Readiness
- Be ready with recent architecture diagrams highlighting domain boundaries, data flows, and evolution decisions.
- Show trade-off thinking: latency vs. consistency, autonomy vs. platform standardization.
- Have a story about reversing a bad decomposition or consolidating services.
- Reference supporting notes: [[Distributed Data And Transactions]], [[Messaging And Streaming]], [[Delivery And Operational Excellence]].
