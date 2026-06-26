# Dependency Rules

> **Question this document answers:** Which dependency directions are allowed?

This document states the enforceable dependency rules of the platform — the
concrete, checkable form of the Clean dependency direction declared in
`10_ARCHITECTURE.md` (`ARCH-03`) and the bounded-context isolation declared in
`05_DOMAIN_MODEL.md` (`DM-07`). A violation of any rule here is a build defect.

---

## Allowed Directions

```
Interface ─┐
           ├─▶ Application ─▶ Domain
Infrastructure ─┘                 ▲
   (adapters, persistence, AI) ───┘ via ports only

Forbidden: any arrow pointing OUT of Domain, or INTO business logic from a provider SDK.
```

- **DEP-01** The `Domain` layer MUST NOT depend on `Application`, `Infrastructure`, or `Interface`.
- **DEP-02** The `Application` layer MUST depend only on `Domain` abstractions.
- **DEP-03** `Infrastructure` and `Interface` MUST depend inward (on `Application`/`Domain` abstractions), never the reverse.
- **DEP-04** Business logic MUST NOT import any provider or venue SDK; only `market-adapter` modules may (`INV-09`, `FR-CONN-02`).
- **DEP-05** A bounded context MUST NOT depend on another context's internals; cross-context interaction MUST be via domain events or a published interface (`DM-07`).
- **DEP-06** The `Decision` context MUST NOT depend on any specific provider or venue; only the `Execution` context touches venues, via adapters (`DM-08`).
- **DEP-07** Dependency violations MUST be detectable by static analysis or a build-time check; the build SHOULD fail on a violation.
- **DEP-08** The `ai-gateway` module MUST be the only inbound dependency on external AI providers; no business module may depend on an AI SDK directly (`FR-AI-03`, `INV-06`).

## Enforcement

| Mechanism | Enforces |
|-----------|----------|
| Layer import boundaries (build check) | `DEP-01`, `DEP-02`, `DEP-03` |
| Adapter-only SDK imports | `DEP-04`, `DEP-08` |
| Context package boundaries | `DEP-05`, `DEP-06` |

---

## Related Documents

- `10_ARCHITECTURE.md` — `ARCH-03`, the dependency direction these rules enforce
- `05_DOMAIN_MODEL.md` — the bounded contexts isolated by `DEP-05`/`DEP-06`
- `12_MODULES.md` — the modules these rules constrain
- `60_ENGINEERING_PRINCIPLES.md` — how these rules are applied in practice
