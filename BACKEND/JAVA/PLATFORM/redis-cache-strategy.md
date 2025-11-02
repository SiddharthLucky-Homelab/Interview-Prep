---
tags: [redis, caching, architecture, java]
title: Redis Cache Strategy
aliases: ["Redis Cache Strategy"]
updated: 2025-11-05
---

# Redis Cache Strategy

Redis underpins low-latency interactions for Aurora Orders (sessions, carts, inventory snapshots) and supports resilience patterns like rate limiting and distributed locks.

## Use Cases
- **Session & Auth Tokens:** Store short-lived JWT/session state with TTL and rolling expiration.
- **Cart & Checkout:** Hashes for cart contents keyed by customer ID; use write-through updates to Postgres on checkout commit.
- **Inventory Snapshots:** Maintain per-region availability caches to avoid hammering transactional DBs.
- **Rate Limiting & Idempotency Keys:** Sliding window counters and token buckets to protect critical APIs.
- **Task Queues & Pub/Sub:** Optional lightweight queues for background tasks or WebSocket fan-out.

## Architecture Choices
- Redis Cluster with at least 3 shards + replicas, managed via cloud provider (Elasticache, Memorystore) for automatic failover.
- Eviction policies tuned per data type (e.g., `volatile-lru` for sessions, `allkeys-lfu` for cache-aside lookups).
- Persistence strategy: AOF every second for cart durability; RDB snapshots nightly for backups.
- Client libraries: Lettuce (reactive) or Redisson (distributed structures, locks) with circuit breakers and retry policies.

## Operational Considerations
- Monitor latency, hit rate, memory fragmentation, replication lag, and keyspace size.
- Enable keyspace notifications for cache invalidation flows.
- Implement cache-miss budgets: alert when hit rate drops below threshold; fallback to Postgres read replicas.
- Secure with TLS, AUTH, network access control; rotate credentials through secret manager.

## Interview Talking Points
- Demonstrate cache-aside vs. write-through trade-offs.
- Explain avoiding cache stampede (request coalescing, mutex, stale-while-revalidate).
- Discuss testing strategies: fault injection (kill nodes), TTL expiry simulations, eventual consistency with DB.

## Related Notes
- [[Concept Application Overview]]
- [[Postgres Data Architecture]]
- [[Kubernetes Operations]]
