---
tags: [messaging, streaming, kafka, backend]
title: Messaging And Streaming
aliases: ["Messaging And Streaming"]
updated: 2025-11-05
---

# Messaging And Streaming

Event-driven systems let teams decouple services, but demand disciplined design to avoid data drift and operational surprises.

## Core Choices
- Transport: Kafka, Google Pub/Sub, RabbitMQ, Pulsar, managed cloud offerings.
- Delivery semantics: at-most-once, at-least-once, effectively-once; idempotent consumers are mandatory.
- Topology: queue vs. topic, consumer groups, fan-out, multi-cluster mirroring.

## Design Patterns
- **Outbox / Change Data Capture:** transactional emit, offset tracking, retry strategy, poison message handling.
- **Schema governance:** Avro/Protobuf registry, compatibility modes, schema evolution contract.
- **Ordering:** key-based partitioning, per-aggregate streams, sequence tokens.
- **Exactly-once goals:** idempotent producers, transactional writes + consumer offsets, dedupe caches.
- **Stream processing:** Kafka Streams, Flink, Beam; windowing, watermarking, state stores.
- **Dead-letter strategy:** capture context, alerting, replay tooling, triage workflows.

## Operational Playbook
- Capacity planning: partition counts, retention (hot/warm tiers), compaction policies, quota management.
- Disaster recovery: cross-region replication (MirrorMaker 2, Confluent Cluster Linking), client failover configs.
- Security: TLS, SASL/OAuth, ACL governance, auditing; private network endpoints.
- Observability: consumer lag metrics, end-to-end latency histograms, schema error dashboards.

## Interview Scenarios
- Design a global event bus for order lifecycle events (multi-region failover, schema evolution, ordering).
- Explain the end-to-end flow using outbox pattern with Kafka and Spring Boot.
- Discuss handling poison messages that repeatedly fail processing.
- Compare webhook, queue, and streaming approaches for third-party integrations.

## Related Notes
- [[Distributed Data And Transactions]]
- [[High-Traffic Resilience]]
- [[Delivery And Operational Excellence]]
