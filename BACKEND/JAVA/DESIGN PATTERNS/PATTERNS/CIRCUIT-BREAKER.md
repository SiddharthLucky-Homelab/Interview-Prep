---
tags: [pattern, resilience, microservices]
title: Circuit Breaker
updated: 2025-10-30
---

# Circuit Breaker

Overview
- Prevents cascading failures by opening after repeated errors and letting a dependency recover.

When To Use
- Downstream instability, timeouts, or partial outages; protect hot paths.

Java/Spring Notes
- Resilience4j for circuit breaker, retries, bulkheads, timeouts; configure SLIs/SLOs and fallback paths.

Trade-offs
- Pros: Stability under failure; Cons: Potentially masks issues if thresholds are wrong.

Related
- [[Microservices Patterns]] • [[saga-pattern]] • [[api-gateway]]

## Problem
- Unreliable downstreams cause thread/connection exhaustion via long timeouts and retries.
- Without isolation, failures cascade and amplify across services.

## Solution
- Wrap remote calls with a circuit breaker managing states: CLOSED → OPEN → HALF_OPEN.
- Combine with timeouts, retries (with jitter), bulkheads, and rate limiting.

## States & Flow
- Closed: calls flow; track failures. On threshold, trip to Open for a cool‑down.
- Open: short‑circuit and return fallback; after waitDuration, probe in Half‑Open.
- Half‑Open: allow limited trial calls; success closes, failures reopen.

## Java/Spring Example (Resilience4j)
```yaml
# application.yml
resilience4j:
  circuitbreaker:
    instances:
      inventory:
        slidingWindowType: COUNT_BASED
        slidingWindowSize: 50
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
        permittedNumberOfCallsInHalfOpenState: 5
  retry:
    instances:
      inventory:
        maxAttempts: 3
        waitDuration: 200ms
        retryExceptions: java.io.IOException, java.net.SocketTimeoutException
  timelimiter:
    instances:
      inventory:
        timeoutDuration: 2s
```

```java
@Service
public class InventoryClient {
  private final WebClient client;
  public InventoryClient(WebClient.Builder b) { this.client = b.baseUrl("http://inventory").build(); }

  @CircuitBreaker(name = "inventory", fallbackMethod = "fallback")
  @Retry(name = "inventory")
  @TimeLimiter(name = "inventory")
  public CompletableFuture<Integer> getStock(String sku) {
    return client.get().uri("/stock/{sku}", sku)
      .retrieve().bodyToMono(Integer.class).toFuture();
  }

  private CompletableFuture<Integer> fallback(String sku, Throwable t) {
    return CompletableFuture.completedFuture(0); // safe default
  }
}
```

## Observability
- Emit metrics: calls, slow calls, failure rate, state transitions; tag by dependency.
- Expose health with a degraded status when breakers are OPEN or HALF_OPEN.

## Testing
- Unit: assert state transitions using Resilience4j `CircuitBreaker` test API.
- Integration: simulate timeouts/5xx via WireMock; verify fallbacks and latency caps.
- Chaos: inject latency/faults (Toxiproxy) to validate bulkheads and timeouts.

## Pitfalls
- Overly aggressive retries worsen incidents; cap attempts and add jitter.
- Swallowing errors hides outages; expose signals to alert and degrade gracefully.
- High timeouts block threads; use non‑blocking IO or keep timeouts small.

## See Also
- [[api-gateway]] • [[Microservices Patterns]] • [[Tech Stack Profile]]

## Interview Checklist
- Define breaker states and transitions; why they prevent cascades.
- Show Resilience4j config and annotations; include timeout + retry.
- Explain bulkheads vs. breakers; when to use each.
- Discuss metrics to watch and alerting signals.
- Common pitfalls: aggressive retries, long timeouts, swallowing errors.

## Practice Questions
- Given a dependency with p95 latency spikes and intermittent 5xx, how would you tune breaker thresholds, timeouts, and retries to protect a checkout endpoint?
- Answer Outline: set small timeouts near p95 budget, enable limited retries with jitter, breaker failureRate ~50% and small sliding window, cool‑down seconds, ensure fallbacks; monitor saturation and error budget.
- How do bulkheads, connection pools, and thread pools interact with circuit breakers in a blocking vs. reactive service?
- Answer Outline: bulkheads isolate resource pools; in blocking apps tune thread/connection pools to cap contention; in reactive prefer non‑blocking IO and semaphore bulkheads; breakers short‑circuit to free capacity.
