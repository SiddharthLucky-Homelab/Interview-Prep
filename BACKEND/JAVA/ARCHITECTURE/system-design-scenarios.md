---
tags: [system-design, backend, architecture]
title: System Design Scenarios
aliases: ["System Design Scenarios"]
updated: 2025-11-05
---

# System Design Scenarios

Use these prompts and frameworks to practice senior-level system design interviews. Tie each solution back to domain goals, SLAs, and operational readiness.

## Payments Platform
- **Scope:** idempotent charge capture, fraud signals, ledgering, reconciliation.
- **Architecture Notes:** multi-tenant compliance boundaries, double-entry ledger, saga for payment lifecycle, webhook + streaming integration with PSPs, PCI segmentation.
- **Data Strategy:** strongly consistent ledger store, append-only events, reconciliation warehouse, retry queue for PSP failures.
- **Risks & Mitigations:** idempotency keys, replay protection, currency conversion accuracy, regional failover, regulatory reporting.

## Real-Time Notifications
- **Scope:** multi-channel delivery (push, email, SMS, in-app), preference management, retries.
- **Architecture Notes:** event router -> fan-out services, channel adaptors, rate limiting, compliance (quiet hours, GDPR consent), template management.
- **Data Strategy:** outbox, per-channel queues, delivery receipts, user preference store (cached).
- **Risks & Mitigations:** redrive dead letters, exponential backoff, provider degradation detection, personalization latency.

## Search & Discovery
- **Scope:** indexed catalog, facets/filters, relevance tuning, autocomplete.
- **Architecture Notes:** ingestion pipelines, de-normalization, search cluster (Elasticsearch/OpenSearch), synonyms, personalization signals.
- **Data Strategy:** change capture from primary DB, near real-time indexing, cache invalidation, analytics feedback loop.
- **Risks & Mitigations:** index lag, hot shards, inconsistent relevance, multi-region replication, rollback of bad relevance configs.

## Streaming Analytics Pipeline
- **Scope:** ingest high-volume events, aggregate metrics, serve dashboards + alerts.
- **Architecture Notes:** ingestion (Kafka/PubSub) -> stream processing (Flink/Beam) -> OLAP store (BigQuery/Druid/ClickHouse) -> API layer.
- **Data Strategy:** late-arriving data handling, windowing strategy, schema evolution, cold storage archiving.
- **Risks & Mitigations:** backpressure, stateful operator scaling, exactly-once semantics, cost containment.

## Interview Playbook
- Clarify requirements, SLAs, growth projections, compliance constraints.
- Draw high-level architecture first (traffic entry, core services, data stores, async flows).
- Detail scaling strategies: sharding, caching, replication, read/write separation.
- Address observability, deployment, incident handling, and cost trade-offs.
- Summarize trade-offs and potential evolutions; highlight what you would prototype first.

## Related Notes
- [[Architecture And Domain Design]]
- [[High-Traffic Resilience]]
- [[Performance And Observability]]
- [[Security And Compliance]]
