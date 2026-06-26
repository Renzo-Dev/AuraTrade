# Non-Functional Requirements

> **Question this document answers:** What are the system's quality attributes and their target thresholds?

This document is the single home for the platform's non-functional requirements.
It consolidates the NFRs previously embedded in `02_REQUIREMENTS.md` and adds
quality-attribute targets. Functional requirements remain in `02_REQUIREMENTS.md`.

---

## Quality Attributes and Targets

| Attribute | Target | Notes |
|-----------|--------|-------|
| Availability | 99.9% for analysis path | execution path may be intentionally halted by Risk |
| Latency | feature compute p99 < 250 ms from event receipt | hot path served via Redis |
| Scalability | horizontal by instrument partition | modular monolith first, seams per bounded context |
| Reliability | no lost domain events | at-least-once + idempotent consumers (`07`) |
| Fault tolerance | degrade, never bypass Risk | a failed engine drops its insights, never its veto |
| Consistency | per-aggregate ordering | global ordering not required |
| Security | secrets never in business logic | see `64_SECURITY.md` |
| Recovery | event replay from store | deterministic rebuild via `42_EVENT_STORAGE.md` |
| Observability | every pipeline stage traceable | see `65_OBSERVABILITY.md` |

- **NFR-QA-01** The analysis path MUST remain available even when the execution path is disabled.
- **NFR-QA-02** A failure in any single Analysis Engine MUST NOT prevent other engines from producing insights, and MUST NOT weaken the Risk Engine veto.
- **NFR-QA-03** The platform MUST be able to deterministically rebuild derived state by replaying persisted domain events.

## Migrated Architecture and Quality NFRs

The following clauses were relocated verbatim from `02_REQUIREMENTS.md`; their
identifiers are unchanged so existing cross-references remain valid.

### Architecture Quality

- **NFR-ARCH-01** The system MUST be implemented initially as a modular monolith.
- **NFR-ARCH-02** Every major component MUST be independently replaceable.
- **NFR-ARCH-03** Migration to microservices SHOULD require minimal changes.
- **NFR-ARCH-04** The architecture MUST follow layered, clean, and event-driven
  principles.

### Modularity and Coupling

- **NFR-MOD-01** Components MUST communicate through well-defined interfaces and
  events.
- **NFR-MOD-02** The codebase MUST maintain high cohesion and low coupling.

### Observability

- **NFR-OBS-01** Every pipeline stage MUST be observable through logging and
  monitoring.
- **NFR-OBS-02** Decisions and executions MUST be auditable after the fact.

### Configurability

- **NFR-CFG-01** Behavior that varies by market, provider, or deployment MUST be
  expressed as configuration, not hardcoded.

### Testability

- **NFR-TEST-01** Components MUST be testable in isolation.
- **NFR-TEST-02** Business logic MUST be deterministic and verifiable independent
  of external providers.

### Security

- **NFR-SEC-01** Provider credentials and secrets MUST be protected and never
  embedded in business logic.
- **NFR-SEC-02** Execution-capable paths MUST be guarded by explicit configuration.

---

## Related Documents

- `02_REQUIREMENTS.md` — functional requirements; its NFR section points here
- `09_SYSTEM_INVARIANTS.md` — laws that some NFRs operationalize
- `64_SECURITY.md` — security NFRs in detail
- `65_OBSERVABILITY.md` — observability NFRs in detail
