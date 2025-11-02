---
tags: [observability, kibana, elastic, logging]
title: Kibana Observability Stack
aliases: ["Kibana Observability Stack"]
updated: 2025-11-05
---

# Kibana Observability Stack

Kibana sits on top of Elasticsearch to deliver unified observability for Aurora Orders, bridging logs, metrics, traces, and analytics.

## Data Sources
- **Application Logs:** Ingested via Filebeat/Fluent Bit with JSON structure (service name, traceId, tenant).
- **Metrics:** Prometheus metrics scraped via OpenTelemetry Collector and pushed into Elastic APM store.
- **Traces:** OpenTelemetry spans exported to Elastic APM; tie into user sessions and order IDs.
- **Audit & Security Events:** API Gateway logs, admin actions, authentication events for compliance tracking.

## Dashboards & Alerts
- Golden signals dashboard per service (latency p95/p99, error rates, saturation).
- Order funnel dashboard correlating checkout latency with conversion.
- Incident workspace: timeline view with correlated logs/traces/metrics for triage.
- Alert rules using KQL and threshold alerts; notify via PagerDuty, Slack, and email with context (runbook links).

## Operational Practices
- Role-based access control: separate operator, developer, analyst roles; fine-grained index privileges.
- Data retention tiers: hot (7 days detailed logs), warm (30 days aggregated), cold (archived to object storage).
- Cost management: index lifecycle policies, downsampling metrics, log sampling for verbose debug entries.
- Runbook library stored alongside dashboards; embed evidence links in postmortems.

## Interview Talking Points
- Explain how full-fidelity observability accelerates incident response and SLO management.
- Discuss correlation strategies (traceId propagation, user/session metadata).
- Show how observability feeds continuous improvement (dashboards -> experiments -> improved SLIs).

## Related Notes
- [[Elasticsearch Search Analytics]]
- [[Performance And Observability]]
- [[High-Traffic Resilience]]
