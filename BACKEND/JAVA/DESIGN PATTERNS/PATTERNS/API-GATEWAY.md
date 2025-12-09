---
tags: [pattern, gateway, microservices]
title: API Gateway
updated: 2025-10-30
---

# API Gateway

Overview
- Single entry point for client traffic; handles routing, auth, rate limit, aggregation.

When To Use
- Many backend services, need consistent cross-cutting concerns and versioning.

Java/Spring Notes
- Spring Cloud Gateway; external platforms like Apigee for enterprise policies.

Trade-offs
- Pros: Simplified clients; Cons: Gateway becomes a critical dependency.

Related
- [[Microservices Patterns]] • [[circuit-breaker]] • [[saga-pattern]] • [[Java Design Patterns Hub]]

## Problem
- Clients need a stable entry point while backends evolve independently.
- Cross‑cutting concerns (authN/Z, rate limiting, CORS, TLS) must be centralized.
- Mobile/web clients benefit from payload aggregation and protocol adaptation.

## Solution
- Place a gateway between clients and services to handle cross‑cutting concerns.
- Route by path/host/header, transform requests/responses, aggregate where needed.
- Enforce security (OAuth2/JWT), quotas, and expose observability at the edge.

## Architecture Notes
- Modes: Layer 7 reverse proxy, API management (plans, keys, monetization), BFF per app.
- Composition: prefer client‑side composition for resiliency; server aggregation for UX.
- Avoid hardcoding service addresses; use service discovery/registry.

## Java/Spring Example (Spring Cloud Gateway)
```yaml
# application.yml
spring:
  cloud:
    gateway:
      default-filters:
        - RemoveRequestHeader=Cookie
        - DedupeResponseHeader=Access-Control-Allow-Origin, RETAIN_UNIQUE
      routes:
        - id: orders
          uri: http://orders:8080
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1
            - name: CircuitBreaker
              args:
                name: ordersCB
                fallbackUri: forward:/fallback/orders
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 20
                redis-rate-limiter.burstCapacity: 40
```

```java
// JWT filter sketch
@Component
public class JwtAuthGatewayFilter implements GlobalFilter, Ordered {
  @Override
  public Mono<Void> filter(ServerWebExchange ex, GatewayFilterChain chain) {
    // validate token, set headers/claims; short‑circuit 401 if invalid
    return chain.filter(ex);
  }
  public int getOrder() { return -1; } // run early
}
```

## Operational Concerns
- Observability: log client IP, userId, traceId; emit SLIs (p95 latency, 5xx rate, saturation).
- Resiliency: apply timeouts, retries with backoff, circuit breakers at the edge.
- Performance: enable response compression and caching for static/GET endpoints.
- Security: terminate TLS; validate JWTs; enforce scopes/roles; strip sensitive headers.

## Testing
- Contract tests per route using WireMock or Spring `WebTestClient`.
- Negative tests for auth, rate limit, and malformed inputs.
- Load tests to uncover head‑of‑line blocking and saturation.

## Pitfalls
- Gateway as a bottleneck or SPOF; run multiple instances and keep stateless.
- Over‑aggregation increases coupling and latency; keep BFFs per client when needed.
- Hidden retries amplify traffic during incidents; cap retries and add jitter.

## Alternatives
- Service Mesh Ingress (e.g., Istio Gateway) for L7 routing with policies.
- API management platforms (Apigee, Kong) when you need plans/monetization.

## See Also
- [[circuit-breaker]] • [[Microservices Patterns]] • [[Tech Stack Profile]]

## Interview Checklist
- Define API Gateway vs. BFF vs. Ingress.
- Outline cross‑cutting concerns handled (authN/Z, rate limit, CORS, TLS).
- Show a simple Spring Cloud Gateway route config and a custom filter.
- Discuss pros/cons and SPOF mitigation; when to aggregate vs. client‑compose.
- Mention observability (traceId, p95), timeouts/retries, and security headers.

## Practice Questions
- When would you prefer a dedicated BFF per client over a shared API Gateway? Explain trade‑offs in latency, coupling, and team ownership.
- Answer Outline: distinct UX per client, tailored aggregation/shape, faster iteration per team; less coupling; trade‑offs include duplicate logic, more deployments, ops overhead.
- How do you roll out gateway rule changes safely (e.g., canary, shadowing) and avoid it becoming a single point of failure?
- Answer Outline: staged rollout (shadow traffic, canary by header/percent), health/rollback, multi‑AZ instances, stateless scaling, config as code with review, circuit breakers and timeouts at edge.
