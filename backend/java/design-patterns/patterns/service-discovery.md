---
tags: [pattern, microservices, discovery]
title: Service Discovery
updated: 2025-11-01
---

# Service Discovery

Overview
- Mechanism for services to find and communicate with each other without hardcoding network locations.

When To Use
- Dynamic microservices environments where service instances are frequently scaled up/down or moved.

Approaches
- **Client-Side Discovery:** Client queries a service registry (e.g., Eureka, Consul, Zookeeper) to find available service instances.
- **Server-Side Discovery:** Router/Load Balancer queries a service registry and forwards requests to available service instances (e.g., AWS ALB, Kubernetes Service).

Java/Spring Notes
- Spring Cloud Eureka for client-side discovery. Spring Cloud Consul for a more feature-rich registry. Kubernetes provides built-in service discovery.

Trade-offs
- Pros: Decoupling, flexibility, resilience to service changes; Cons: Adds complexity, requires a reliable service registry.

Related
- [[Microservices Patterns]]

## Problem
- Service IPs/ports change due to autoscaling, restarts, or deployments.
- Hardcoded endpoints break; manual configuration becomes a bottleneck.

## Solution
- Use a registry where services register/deregister. Clients or load balancers resolve
  destinations dynamically and apply health awareness.

## Architecture Notes
- Registration: self‑registration (service registers itself) vs. third‑party registration
  (sidecar/agent registers).
- Health: heartbeat/TTL or HTTP health probes; evict unhealthy instances quickly.
- Discovery data: address, port, tags/metadata (version, zone) for smart routing.
- Topologies: multi‑region with zone‑aware routing and failover.

## Java/Spring Examples
```yaml
# Eureka client
eureka:
  client:
    serviceUrl:
      defaultZone: http://eureka:8761/eureka/
  instance:
    lease-renewal-interval-in-seconds: 10
    lease-expiration-duration-in-seconds: 30
```

```java
// Client-side load balancing
@Bean @LoadBalanced RestTemplate restTemplate() { return new RestTemplate(); }

// Usage: logical service name instead of host:port
var resp = restTemplate.getForObject("http://inventory/api/stock/{sku}", Integer.class, sku);
```

```java
// WebClient with discovery (Spring Cloud LoadBalancer)
@Bean @LoadBalanced WebClient.Builder webClientBuilder() { return WebClient.builder(); }
```

## Kubernetes Notes
- Built‑in server‑side discovery via Services and kube‑proxy/IPVS.
- Access by DNS: `http://inventory.default.svc.cluster.local` or simply `http://inventory`.
- Health: readiness/liveness probes; remove failing pods from endpoints.
- Use Service `labels`/`selectors`; for version routing, use separate Services or Ingress rules.

## Service Mesh Angle
- Mesh (Istio, Linkerd) adds discovery, mTLS, retries, and traffic policies via sidecars.
- Declarative routing (subset by version/labels), circuit breaking, and timeouts at the data plane.

## Operational Concerns
- Consistency: eventual consistency in registries; set eviction/renewal carefully.
- Zone awareness: prefer same‑zone endpoints first to reduce latency/cost.
- Backoff: retry with jitter on resolution failures; cache endpoints with TTL.

## Testing
- Local registry with Testcontainers (Eureka/Consul) to validate registration and discovery.
- Integration tests asserting zone/metadata‑based routing decisions.
- Failure injection to validate evictions and fallback behavior.

## Pitfalls
- Stale registry entries cause black‑hole traffic; ensure timely heartbeats and eviction.
- Overreliance on client libraries complicates polyglot stacks; prefer DNS/mesh when mixed.
- Cross‑zone chatter increases latency and cost; enable zone‑aware policies.

## See Also
- [[api-gateway]] • [[circuit-breaker]] • [[Microservices Patterns]]

## Interview Checklist
- Define client‑side vs. server‑side discovery with examples.
- Name common registries (Eureka, Consul, ZooKeeper) and K8s Service/DNS.
- Explain health checks, eviction, and zone‑aware routing.
- Show Spring config (`@LoadBalanced`), and K8s DNS naming.
- Discuss trade‑offs and when to prefer mesh or DNS‑only.

## Practice Questions
- Compare client‑side discovery with Eureka versus server‑side discovery behind a Kubernetes Service/Ingress. When is each preferable?
- Answer Outline: client‑side offers fine control and metadata‑aware routing but requires libs; server‑side (K8s/ALB) simplifies polyglot stacks; choose based on platform maturity and needs.
- How would you implement zone‑aware routing and protect against stale registry entries causing black‑hole traffic?
- Answer Outline: tag instances with zone, prefer same‑zone, fall back with weights; fast heartbeats/TTL, eviction on probe failures, local caches with TTL and backoff.
