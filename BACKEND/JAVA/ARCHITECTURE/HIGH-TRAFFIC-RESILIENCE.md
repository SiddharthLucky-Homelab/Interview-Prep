---
tags: [resilience, reliability, backend, sre]
title: High-Traffic Resilience
aliases: ["High-Traffic Resilience"]
updated: 2025-11-05
---

# High-Traffic Resilience

Design for graceful degradation, predictable performance, and fast recovery when load spikes or components fail.

## Reliability Mindset
- Define SLIs/SLOs with product stakeholders; derive error budgets and release policies.
- Build capacity envelopes: peak/day averages, burst multipliers, regional failover scenarios.
- Favor incremental deploys with fast rollback or fast-forward paths.

## Core Patterns
- **Load management:** autoscaling (HPA/KPA), queue buffering, request shedding (503 with retry-after), priority lanes.
- **Resilience patterns:** circuit breakers, bulkheads, timeouts, retries with jitter, hedged requests, fallback responses.
- **Chaos & game days:** inject failure (shutdown pods, network partition, dependency latency), measure detection + recovery.
- **Incident response:** paging policies, runbooks, automation (synthetic checks, auto-remediation).

## Capacity & Performance
- Trend concurrency vs. throughput; ensure database, cache, message brokers handle failover load.
- Precompute hot paths (materialized views, caches, prefetch).
- Edge acceleration: CDN, global load balancers, request coalescing.

## Tooling & Telemetry
- Alerting on golden signals (latency, traffic, errors, saturation) with multi-window burn rate SLO alerts.
- Observability stack: OpenTelemetry traces, metrics (Prometheus), logs (ELK/GCP Logging).
- Feature flag ramps with automated rollback on SLO burn.

## Interview Readiness
- Story: major incident postmortem (what failed, detection, resolution, lasting fixes).
- Design Prompt: scale checkout flow for Black Friday (capacity multipliers, caching, shed non-critical features).
- Explain how you monitor and test resilience (synthetic traffic, chaos experiments, distributed tracing).

## Related Notes
- [[Performance And Observability]]
- [[Messaging And Streaming]]
- [[Delivery And Operational Excellence]]
