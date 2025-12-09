---
tags: [security, compliance, backend, java]
title: Security And Compliance
aliases: ["Security And Compliance"]
updated: 2025-11-05
---

# Security And Compliance

Senior engineers own application security posture and contribute to regulatory readiness in addition to building features.

## Security Framework
- Threat modeling: assets, attack surfaces, STRIDE/PASTA analysis, architectural mitigations.
- Authentication & authorization: OAuth2/OIDC flows, JWT validation, token lifetimes, fine-grained scopes, service-to-service mTLS.
- Secrets management: envelope encryption, HSM/KMS usage, rotation cadences, runtime injection (Kubernetes secrets, Secret Manager).
- API defense: rate limiting, WAF, schema validation, input sanitization, output encoding, security headers.
- Data protection: encryption at rest/in transit, PII handling, key management, data retention policies.

## Compliance & Governance
- Frameworks: SOC2, ISO 27001, PCI DSS, HIPAA, GDPR/CPRA. Understand mapping of controls to engineering work.
- Audit trails: immutable logs, who/what/when records, tamper detection.
- Change management: deployment approvals, segregation of duties, traceable rollbacks.
- Third-party risk: vendor security reviews, SBOM, dependency scanning (Snyk, OWASP Dependency-Check).
- Incident response: runbooks, breach notification timelines, tabletop exercises.

## Secure SDLC Practices
- Static/dynamic scanning, SCA, IaC scanning (tfsec, Checkov), secrets detection.
- Secure coding guidelines (OWASP Top 10, ASVS), code review checklists, pair programming for sensitive features.
- Bug bounty integration, disclosure policy, vulnerability remediation SLAs.

## Interview Conversations
- Walk through designing secure multi-tenant APIs (per-tenant keys, scopes, RBAC, audit logs).
- Explain how you’d achieve SOC2 readiness while shipping weekly.
- Describe a security incident you led: detection, containment, recovery, retrospective.

## Related Notes
- [[Performance And Observability]]
- [[Delivery And Operational Excellence]]
- [[Distributed Data And Transactions]]
