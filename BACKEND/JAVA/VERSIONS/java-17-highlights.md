---
tags: [java, versions, java-17]
title: Java 17 Highlights
aliases: ["Java 17 Highlights"]
updated: 2025-11-05
---

# Java 17 Highlights

Java 17 (Sept 2021) is an LTS release that most enterprises have standardized on. Interviews expect you to know why it is a safe baseline and what it unlocked over Java 11/8.

## Major Language Features
- **Sealed classes & interfaces (JEP 409):** constrain inheritance to known subclasses; discuss maintainability and exhaustive pattern matching.
- **Records (finalized in JEP 395):** concise data carriers that pair nicely with DTOs and immutability.
- **Pattern matching for `instanceof` (JEP 394):** reduces casting boilerplate and paves the way for record patterns.
- **Switch improvements preview:** pattern matching for switch (JEP 406) in preview; mention how it evolves control flow.

## JVM & Platform
- New GCs: ZGC and Shenandoah production-ready with low-pause collectors; talk about GC choice trade-offs when tuning.
- Strong encapsulation of JDK internals (JEP 403) — may break reflection hacks; explain module-path vs. classpath compatibility steps.
- Foreign function & memory API incubator (JEP 412) as a modern alternative to JNI.

## Operational Benefits
- Long-term support through 2029+ by most vendors.
- Enhanced JFR and mission control; helpful for profiling and observability stories.
- Deprecations: Applets, Security Manager (JEP 411) — be ready to explain mitigations.

## Upgrade Checklist
- Run automated tests with `--illegal-access=warn` to spot reflective access issues.
- Evaluate build tool compatibility (Maven 3.8+, Gradle 7+).
- Refresh container base images to Alpine/Distroless JRE 17 variants.

## Interview Talking Points
- Compare moving from Java 8 → 17: records, switch expressions, var, new APIs.
- Explain sealed classes + pattern matching used in domain models.
- Describe GC tuning changes after adopting ZGC/Shenandoah.

## Related Notes
- [[Java Version Updates]]
- [[Performance And Observability]]
- [[Java 21 Highlights]]
