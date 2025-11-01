---
tags: [pattern, event-sourcing, microservices]
title: Event Sourcing
updated: 2025-10-30
---

# Event Sourcing

Overview
- Persist state changes as an append-only stream of events; rebuild state by replaying events.

When To Use
- Audit/history, temporal queries, or complex domain invariants.

Java/Spring Notes
- Use Kafka or an event store; add snapshots; version events and handlers.

Trade-offs
- Pros: Auditability, decoupled reads; Cons: evolution and operational complexity.

Related
- [[cqrs]] • [[saga-pattern]] • [[Microservices Patterns]] • [[Java Design Patterns Hub]] • [[Tech Stack Profile]]

## Problem
- Need an immutable audit trail and ability to answer "what did we know when?" questions.
- Complex aggregates with many invariants that are hard to capture in CRUD deltas.

## Solution
- Store domain events as the source of truth; rebuild aggregates by replaying events.
- Periodically snapshot to bound replay time; keep events immutable and versioned.

## Core Concepts
- Event: fact in past tense (e.g., `OrderApproved`), append‑only, immutable.
- Aggregate: applies events to reach state; emits new events from commands.
- Snapshot: periodic materialized state to speed up loads; events still authoritative.

## Java Example (minimal skeleton)
```java
sealed interface Event permits OrderCreated, OrderApproved {}
record OrderCreated(Long id, BigDecimal total) implements Event {}
record OrderApproved(Long id) implements Event {}

final class OrderAgg {
  enum Status { PENDING, APPROVED }
  Long id; BigDecimal total; Status status = Status.PENDING; long version;

  void apply(Event e) {
    if (e instanceof OrderCreated ec) { id = ec.id(); total = ec.total(); }
    else if (e instanceof OrderApproved ea) { status = Status.APPROVED; }
    version++;
  }

  List<Event> decideApprove() {
    if (status != Status.PENDING) throw new IllegalStateException();
    return List.of(new OrderApproved(id));
  }
}
```

## Storage & Delivery
- Event Store: SQL table (id, aggId, version, type, payload, ts) or specialized store.
- Ordering: guarantee per‑aggregate ordering; global ordering optional.
- Delivery: outbox + log (Kafka/Pub/Sub) to feed projections and integrations.

## Evolution
- Version events and handlers; prefer additive changes.
- Migrate via up‑casters (transform old payloads at read) or offline re‑writes.

## Operational Concerns
- Rebuild: provide projection rebuild jobs from the event log.
- Idempotency: ensure projections upsert; use version checks to prevent double apply.
- Backpressure: batch and paginate event reads; stream with checkpoints.

## Testing
- Given‑When‑Then style on aggregates using events as fixtures and assertions.
- Replay tests to ensure handlers remain backward compatible.

## Pitfalls
- Overkill for simple CRUD; operationally heavier (storage growth, tooling).
- Deleting PII requires cryptographic erasure/tokenization or envelope encryption.

## See Also
- [[cqrs]] • [[saga-pattern]] • [[Microservices Patterns]]

## Interview Checklist
- Define events, aggregates, snapshots; explain immutability and replay.
- Walk through decide/apply flow with a minimal example.
- Address schema evolution: versioning, up‑casters, migrations.
- Discuss storage layout, ordering guarantees, and outbox usage.
- Pitfalls: operational overhead, PII deletion strategies.

## Practice Questions
- How do you handle event schema evolution across teams without breaking old consumers? Include approaches for up‑casting and version negotiation.
- Answer Outline: version events and handlers, additive changes, up‑casters on read, topic versioning if needed, contract testing, deprecate old after migration windows.
- What strategies can you use to comply with data deletion (e.g., GDPR) in an event‑sourced system while keeping event integrity?
- Answer Outline: encrypt PII with per‑user keys for crypto‑erasure, tokenize data, store PII off‑log with references, redact in projections, maintain legal audit trail policies.
