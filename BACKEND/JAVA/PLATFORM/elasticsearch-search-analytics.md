---
tags: [elasticsearch, search, analytics, java]
title: Elasticsearch Search Analytics
aliases: ["Elasticsearch Search Analytics"]
updated: 2025-11-05
---

# Elasticsearch Search Analytics

Elastic powers search, discovery, and near real-time analytics dashboards for Aurora Orders.

## Search Use Cases
- Product catalog search with multi-language support, typo tolerance, synonym matching, and faceted navigation.
- Promotions and personalization via boosting by merchant priority, popularity, and inventory levels.
- Auto-complete and suggestions using completion suggesters and search-as-you-type indices.

## Analytics & Monitoring
- Write order events and customer interactions to time-series indices for operational analytics (conversion funnel, abandonment rate).
- Kibana Lens dashboards for category managers; anomaly detection via Elastic ML jobs.
- Log centralization: ingest app logs, API Gateway access logs, and infrastructure metrics for correlation.

## Indexing Strategy
- Dual pipelines: near real-time indexing via Debezium CDC + Kafka Connect; nightly full reindex for relevancy changes.
- Index templates with mappings for keyword vs. text fields; use nested objects for variant SKUs.
- Hot-warm architecture: hot nodes on NVMe for active data, warm nodes for historical analytics; ILM policies move/expire indices.

## Performance & Resilience
- Shard sizing: target 20–50 GB per shard; allocate replica shards per AZ for HA.
- Query caches and request cache tuned for top searches; use search slow log to optimize analyzers.
- Snapshot to S3/GCS; cross-cluster replication for DR and regional read latency.
- Rate limit indexing and search throughput; coordinate with Redis caches to reduce repeated queries.

## Interview Talking Points
- Explain how you maintain relevancy and freshness at scale.
- Discuss fallback strategies if Elastic cluster degrades (graceful degradation in UI, read-through to Postgres).
- Walk through monitoring plan: shard health, query latency, queue depth, JVM heap usage.

## Related Notes
- [[Concept Application Overview]]
- [[Kibana Observability Stack]]
- [[Redis Cache Strategy]]
