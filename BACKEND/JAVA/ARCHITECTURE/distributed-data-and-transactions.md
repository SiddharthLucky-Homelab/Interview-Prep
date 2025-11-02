---
tags: [data, transactions, backend, consistency, java]
title: Distributed Data And Transactions
aliases: ["Distributed Data And Transactions"]
updated: 2025-11-05
---

# Distributed Data And Transactions

Senior backend engineers must design data flows that survive scale, region failures, and evolving schemas while keeping business invariants correct.

## Decision Framework
- **Workload shape:** OLTP vs. analytics, hot partitions, latency budgets, read/write ratios.
- **Consistency needs:** strong, bounded staleness, eventual, or per-operation semantic guarantees.
- **Failure domains:** AZ, region, cloud. Define recovery point (RPO) and recovery time (RTO) goals.

## Patterns & Tools
- **Data ownership:** service owns writes; publish change events (CDC/outbox) for consumers.
- **Transactions:** local ACID + sagas/compensations. Avoid distributed 2PC unless transactional DB provides it and latency is tolerable.
- **Storage mix:** relational (PostgreSQL, MySQL, Cloud Spanner), NoSQL (Cassandra, DynamoDB), caches (Redis), search (Elasticsearch, OpenSearch), object storage (GCS/S3).
- **Multi-region:** active-active (conflict resolution, CRDTs, session affinity) vs. active-passive (binlog shipping, log replay).
- **Schema evolution:** expand → backfill → contract; backwards compatibility for events and APIs.
- **Consistency levers:** idempotency keys, version checks, quorum writes/reads, read repair.

## Operational Concerns
- Capacity planning: table partitioning, index management, connection pool sizing, query budgets.
- Observability: query latency histograms, replication lag, deadlock counts, page cache metrics.
- Backups & restores: PITR testing, corruption drills, encryption at rest & in transit.
- Security: masking PII, tokenization, envelope encryption, key rotation, audit logging.

## Interview Flashcards
- Describe how you’d migrate a large table with zero downtime (dual writes, shadow tables, feature flags).
- Explain dealing with cross-region write conflicts (Last Write Wins, vector clocks, business rule arbitration).
- Outline an architecture for multi-tenant data isolation (shared DB with tenant ID vs. shard-per-tenant).
- Walk through designing an idempotent saga with outbox pattern and retries.

## Related Notes
- [[Architecture And Domain Design]]
- [[High-Traffic Resilience]]
- [[Messaging And Streaming]]
- [[Security And Compliance]]
