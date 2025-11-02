---
tags: [performance, observability, java, backend]
title: Performance And Observability
aliases: ["Performance And Observability"]
updated: 2025-11-05
---

# Performance And Observability

Senior engineers must diagnose bottlenecks quickly and instrument systems so failures never hide in the dark.

## Performance Toolkit
- JVM tuning: choose GC (G1, ZGC, Shenandoah) per workload; tune heap, metaspace, thread pools, warm-up strategies.
- Async designs: reactive pipelines (Project Reactor), virtual threads, non-blocking I/O, backpressure management.
- Profiling: async-profiler, JFR, Flamegraphs, benchmarking harnesses (JMH) with reproducible baselines.
- Caching: Redis/Coherence, in-JVM caches (Caffeine), TTL vs. sliding vs. write-through; eviction + invalidation policies.
- Database tuning: query plans, index strategy, connection pool sizes, batch writes, read replicas.

## Observability Stack
- Traces: OpenTelemetry instrumentation, baggage vs. context propagation, sampling strategies, trace-based SLOs.
- Metrics: RED (Request rate, Errors, Duration), USE (Utilization, Saturation, Errors); histograms with exemplars.
- Logs: structured logging, correlation IDs, PII scrubbing, retention policies.
- Dashboards: golden signals by service, SLO burn charts, dependency maps, service mesh telemetry (Istio/Linkerd).
- Alerting: multi-window burn rates, pager hygiene, runbook links, auto-ticketing.

## Operational Practices
- Performance budgets: define latency/error budgets per endpoints; enforce in CI (k6, Gatling) or canary.
- Capacity tests: load, stress, soak, chaos; automate regular runs with dashboards.
- Observability reviews: SLO postmortems, instrumentation debt backlog, log cost governance.
- Developer experience: local tracing emulators, feature flag dashboards, synthetic monitors.

## Interview Prep
- Illustrate how you diagnosed a production latency spike (tools, metrics, remediation).
- Explain instrumenting a new service end-to-end (traces, metrics, logs, alerts).
- Discuss how you keep observability cost under control.

## Related Notes
- [[High-Traffic Resilience]]
- [[Messaging And Streaming]]
- [[Delivery And Operational Excellence]]
