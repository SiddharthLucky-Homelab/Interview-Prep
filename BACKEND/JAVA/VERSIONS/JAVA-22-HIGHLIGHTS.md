---
tags: [java, versions, java-22]
title: Java 22 Highlights
aliases: ["Java 22 Highlights"]
updated: 2025-11-05
---

# Java 22 Highlights

Java 22 (Mar 2024) is a feature release that sharpens developer productivity and advances the Loom/Amber/Babylon roadmaps. Even if it is not LTS, interviews use it to gauge how you track upcoming capabilities.

## Language & Syntax Evolution
- **String Templates (JEP 459, 2nd preview):** inline templating with validation and custom processors; discuss how it reduces manual concatenation and injection risk.
- **Unnamed Patterns & Variables (JEP 456, 2nd preview):** underscore `_` for ignore slots in pattern matching, catch blocks, records; cleaner exhaustive handling.
- **Implicit Class & Instance Main Methods (JEP 463, 2nd preview):** simplify small programs, teaching, and scripting.
- **Statements before `super(...)` (JEP 447, preview):** initialize state or validate arguments prior to superclass constructor calls.

## Concurrency & Loom Track
- **Structured Concurrency (JEP 462, 2nd preview):** scope concurrent subtasks, propagate cancellation, and streamline error handling.
- **Scoped Values (JEP 464, 2nd incubator):** immutable, inheritable context for threads/virtual threads; alternative to ThreadLocal with better safety.

## Performance & Platform
- **Vector API (JEP 460, 7th incubator):** SIMD acceleration for numerical workloads.
- **Stream Gatherers (JEP 461, preview):** extend Stream API with custom intermediate operations (e.g., sliding windows) without custom collectors.
- **Class-File API (JEP 457, preview):** programmatic access to class files replacing ASM/DIY code.
- **Launch Multi-File Source Programs (JEP 458, preview):** run multi-file source trees directly (`java MyApp/*.java`), aiding scripting and prototyping.

## Interview Talking Points
- Explain how string templates and pattern matching reduce boilerplate and improve safety.
- Describe using structured concurrency + scoped values to manage virtual threads in microservices.
- Highlight tooling opportunities: dynamic bytecode generation with the Class-File API.

## Related Notes
- [[Java Version Updates]]
- [[Java 21 Highlights]]
- [[Performance And Observability]]
- [[Java 23 Highlights]]
