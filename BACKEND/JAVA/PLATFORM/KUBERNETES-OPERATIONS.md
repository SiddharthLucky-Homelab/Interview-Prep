---
tags: [kubernetes, operations, devops, java]
title: Kubernetes Operations
aliases: ["Kubernetes Operations"]
updated: 2025-11-05
---

# Kubernetes Operations

Aurora Orders runs on Kubernetes to deliver consistent deployments, autoscaling, and multi-region resilience.

## Cluster Topology
- Regional clusters (US, EU, APAC) managed via managed Kubernetes (GKE/EKS/AKS) with 3 AZ worker pools.
- Namespaces per environment (dev, staging, prod) and per platform component (core services, data plane, observability).
- Service mesh (Istio/Linkerd) for mTLS, traffic shaping, and observability.

## Deployment Patterns
- GitOps with Argo CD: manifests + Helm charts stored in dedicated repo; promote via pull requests.
- Progressive delivery: canary releases (Argo Rollouts) with automated metrics analysis; blue/green for critical services.
- Workload types: `Deployment` for stateless services, `StatefulSet` for Kafka/Redis (managed services when possible), `CronJob` for batch tasks.

## Scaling & Resilience
- HPA based on CPU, memory, and custom metrics (request rate, queue depth); KEDA adds event-driven scale from Kafka lag.
- Pod disruption budgets to protect availability during upgrades.
- Multi-cluster services with global load balancer + DNS; use traffic splitting for regional failover.
- Chaos testing: simulate node drain, eviction, pod crash, network latency using Litmus or Chaos Mesh.

## Platform Operations
- Centralized secrets via external secrets operator syncing from Vault/Secret Manager.
- Policy enforcement: Gatekeeper/OPA for security (no privileged pods, approved images).
- Observability: OpenTelemetry collector as DaemonSet, Prometheus/Grafana for infra metrics, logs shipped to Elastic.
- Cost controls: cluster autoscaler, spot/preemptible pools for stateless workloads, resource quota governance.

## Deployment Topology

```mermaid
flowchart LR
  subgraph Edge
    DNS["Global DNS / CDN"]
    WAF["WAF / API Gateway"]
  end

  subgraph "US-East Cluster"
    direction TB
    USEIngress["Istio Ingress Gateway"]
    USEWorkloads["Deployments:<br/>Cart, Checkout, Orders, Payments"]
    USEKafka["Kafka (Managed)"]
    USERedis["Redis (Managed Cluster)"]
    USEPostgres["Postgres Primary<br/>+ Read Replicas"]
    USEObservability["Telemetry Agents<br/>(OTel, Prometheus)"]
  end

  subgraph "EU-West Cluster"
    direction TB
    EUIngress["Istio Ingress Gateway"]
    EUWorkloads["Deployments:<br/>Cart, Checkout, Orders, Payments"]
    EUKafka["Kafka (Managed)"]
    EURedis["Redis (Managed Cluster)"]
    EUPostgres["Postgres Primary<br/>+ Read Replicas"]
    EUObservability["Telemetry Agents"]
  end

  subgraph "APAC Cluster"
    direction TB
    APIngress["Istio Ingress Gateway"]
    APWorkloads["Deployments:<br/>Cart, Checkout, Orders, Payments"]
    APKafka["Kafka (Managed)"]
    APRedis["Redis (Managed Cluster)"]
    APPostgres["Postgres Primary<br/>+ Read Replicas"]
    APObservability["Telemetry Agents"]
  end

  subgraph "Shared Services"
    Argo["Argo CD / GitOps"]
    Vault["Secrets Manager"]
    Elastic["Elasticsearch + Kibana"]
    ObjectStore["Object Storage / Backups"]
  end

  DNS --> WAF
  WAF --> USEIngress
  WAF --> EUIngress
  WAF --> APIngress

  USEWorkloads --> USEKafka
  USEWorkloads --> USERedis
  USEWorkloads --> USEPostgres
  USEWorkloads --> USEObservability

  EUWorkloads --> EUKafka
  EUWorkloads --> EURedis
  EUWorkloads --> EUPostgres
  EUWorkloads --> EUObservability

  APWorkloads --> APKafka
  APWorkloads --> APRedis
  APWorkloads --> APPostgres
  APWorkloads --> APObservability

  Argo --> USEWorkloads
  Argo --> EUWorkloads
  Argo --> APWorkloads

  Vault --> USEWorkloads
  Vault --> EUWorkloads
  Vault --> APWorkloads

  USEObservability --> Elastic
  EUObservability --> Elastic
  APObservability --> Elastic

  USEPostgres --> ObjectStore
  EUPostgres --> ObjectStore
  APPostgres --> ObjectStore
```

## Interview Talking Points
- Detail deployment pipeline from commit → build → security scan → GitOps sync → progressive rollout.
- Explain how you ensure cluster multi-tenancy and security boundaries.
- Discuss incident response: detecting failed deploy, automated rollback, postmortem integration.

## Related Notes
- [[CI CD And Deployments]]
- [[High-Traffic Resilience]]
- [[Delivery And Operational Excellence]]
- [[Concept Application Overview]]
