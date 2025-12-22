---
tags: [pattern, cqrs, microservices]
title: CQRS
updated: 2025-10-30
---

# CQRS

Overview
- Split write (commands) and read (queries) models to scale independently and optimize data access.

When To Use
- High read/write asymmetry, complex aggregates, or need for denormalized read models.

Java/Spring Notes
- Command side with JPA; read side with projections/materialized views. Consider messaging (Kafka/Pub/Sub) to build read models.

Trade-offs
- Pros: Performance and separation; Cons: Eventual consistency and complexity.

Related
- [[Microservices Patterns]] • [[event-sourcing]] • [[saga-pattern]]

## Problem
- Single, normalized model cannot serve both write integrity and fast, flexible reads.
- Reporting/UX requires denormalized views without harming transactional performance.

## Solution
- Separate write model (commands mutate aggregates) and read model (queries optimized for reads).
- Propagate changes from write to read via events (domain events, outbox) asynchronously.

## Data Flow
- Command → validate invariants → persist aggregate → publish event(s) → project into read store.
- Reads query materialized views (SQL tables, caches, search indexes) independently.

## Java Example (Order domain)
```java
// Command side: aggregate + repository (JPA)
@Entity
public class OrderAggregate {
  @Id Long id; OrderStatus status; BigDecimal total;
  public List<Object> approve() {
    if (status != PENDING) throw new IllegalStateException("Bad state");
    this.status = APPROVED;
    return List.of(new OrderApprovedEvent(id));
  }
}
```

```java
// Outbox pattern sketch (transactionally persist events)
@Transactional
public void handleApprove(Long id) {
  var order = repo.findById(id).orElseThrow();
  var events = order.approve();
  repo.save(order);
  outbox.saveAll(events.stream().map(Outbox::from).toList());
}
```

```java
// Read side projector (Kafka)
@Component
public class OrderProjector {
  @KafkaListener(topics = "order-events")
  public void on(Event evt) {
    if (evt instanceof OrderApprovedEvent e) {
      jdbc.update("update order_view set status=? where id=?", "APPROVED", e.id());
    }
  }
}
```

## Operational Concerns
- Eventual consistency: reflect status in UI; offer refresh and reconciliation jobs.
- Idempotency: upsert projections; use event keys and versioning to avoid duplicates.
- Backfills: rebuild projections from event history or changelog streams.

## Testing
- Unit: aggregate invariants (given‑when‑then with events).
- Integration: ensure outbox writes within same TX; consumer applies idempotently.
- Contract: ensure read model schema meets UI/report needs.

## Pitfalls
- Overusing CQRS where CRUD suffices adds complexity.
- Tight coupling between events and projections harms evolvability; version events.

## See Also
- [[event-sourcing]] • [[saga-pattern]] • [[Microservices Patterns]]

## Interview Checklist
- Define command vs. query models and why to separate.
- Describe propagation via events/outbox and eventual consistency.
- Provide a small aggregate example and a projector snippet.
- Explain idempotency, rebuilding projections, and versioning events.
- Pitfalls: overuse, tight coupling of projections to events.

## Practice Questions
- How would you design the outbox and consumer to guarantee exactly‑once effects in the read model without distributed transactions?
- Answer Outline: transactional outbox table with write in same TX, log‑tailer publishes to Kafka, consumer upserts idempotently using event id/version; dedupe keys, retries with DLQ, idempotent projections.
- When is plain CRUD preferable to CQRS, and how would you justify that choice in a review?
- Answer Outline: low complexity domain, modest scale, symmetrical R/W; CQRS adds operational complexity and eventual consistency; prefer CRUD for speed and simplicity.
