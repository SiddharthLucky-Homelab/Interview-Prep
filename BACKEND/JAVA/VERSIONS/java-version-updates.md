---
tags: [java, roadmap, versions]
title: Java Version Updates
aliases: ["Java Version Updates"]
updated: 2025-11-05
---

# Java Version Updates

Use this hub to recall recent Java release cadence, LTS timelines, and interview-worthy feature highlights.

## Release Cadence
- Feature releases ship every six months (March/September); LTS releases arrive every three years.
- Support window depends on vendor: Oracle (commercial), Eclipse Temurin/Adoptium, Azul, Amazon Corretto.
- Stay current by targeting the latest LTS in production and experimenting with the latest feature release in non-prod.

## Need-To-Know LTS Versions
- [[Java 17 Highlights]] — baseline LTS since 2021; sealed classes, records, pattern matching for `instanceof`, JDK Flight Recorder updates.
- [[Java 21 Highlights]] — LTS since Sept 2023; virtual threads, structured concurrency preview, record patterns, sequenced collections.
- Track upcoming LTS (Java 25) and feature releases (Java 22–24) for interview questions on industry adoption.

## Feature Releases To Watch
- [[Java 22 Highlights]] — string templates preview, structured concurrency updates, Class-File API.
- [[Java 23 Highlights]] — Loom preview refinements, Markdown Javadoc, Vector API progress.

## Quick Reference Tables

### Release & GC Defaults
| Version | Release | Support | Default GC | Notable Highlights |
|---------|---------|---------|------------|--------------------|
| Java 17 | Sep 2021 | LTS (support to ~2029) | G1 GC (server), ZGC/Shenandoah available | Records, sealed classes, pattern matching `instanceof`, strong encapsulation |
| Java 21 | Sep 2023 | LTS | G1 GC; virtual threads integrate with Loom | Virtual threads GA, record/switch patterns, sequenced collections, gen ZGC preview |
| Java 22 | Mar 2024 | Feature (18 months) | Same defaults as 21 | String templates preview, structured concurrency preview 2, scoped values incubator |
| Java 23 | Sep 2024 | Feature | Same defaults; Vector API incubator v8 | Loom previews (structured concurrency 3), Markdown Javadoc preview, string templates preview 3 |

### Upgrade Planning Checklist
- Validate build tool + framework support (Maven/Gradle, Spring Boot, Jakarta EE).
- Review GC/heap flags when upgrading major versions; defaults evolve slowly but ergonomics change.
- Test preview features with `--enable-preview`; gate behind flags per environment.
- Update container base images and CI pipelines; ensure security scans cover new runtimes.

## Upgrade Strategy Talking Points
- Validate library/tool compatibility (Spring Boot 3.x, Gradle/Maven, containers).
- Use multi-stage Docker builds with distroless JREs, align container memory settings with new GC defaults.
- Leverage runtime flags for previews (`--enable-preview`) during experiments.

## Interview Prompts
- Explain why your team chose a particular LTS and how you planned the upgrade.
- Discuss how virtual threads or pattern matching change API/service design.
- Highlight performance/observability gains from new GC or monitoring tools.

## Related Notes
- [[Architecture And Domain Design]]
- [[Performance And Observability]]
- [[Delivery And Operational Excellence]]
