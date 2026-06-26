# System Invariants

> **Question this document answers:** What laws must always hold and may never be violated?

This document states the platform's invariants — the laws that every component,
in every state, must uphold. They are neither functional requirements nor
architecture; they are non-negotiable constraints. Any change to an invariant
requires a superseding ADR. The component that enforces each invariant is named
so the law has an owner.

---

## Invariants

- **INV-01** Every `Feature` has exactly one producer. *(Enforced by: Feature Engine, `21_FEATURE_ENGINE.md`.)*
- **INV-02** The Feature Store is the only source of Features; no subsystem computes its own. *(Enforced by: Feature Store, `22_FEATURE_STORE.md`.)*
- **INV-03** Every `Recommendation` maps to at most one `Decision`. *(Enforced by: Decision Engine, `38_DECISION_ENGINE.md`.)*
- **INV-04** Every `Decision` passes the Risk Engine before any execution. *(Enforced by: Risk Engine, `32_RISK_ENGINE.md`.)*
- **INV-05** The Risk Engine veto cannot be bypassed by any actor, human or AI. *(Enforced by: Risk Engine, `32_RISK_ENGINE.md`.)*
- **INV-06** AI never executes trades and never overrides Risk or business rules. *(Enforced by: AI Gateway, `23_AI_GATEWAY.md`.)*
- **INV-07** Every `Execution` has an audit record traceable to its originating `Market Event`. *(Enforced by: Execution Layer, `31_EXECUTION_LAYER.md`.)*
- **INV-08** Every domain event has exactly one publisher and is immutable. *(Enforced by: Event Bus, `15_EVENT_BUS.md`.)*
- **INV-09** Business logic never knows which provider or venue it operates on. *(Enforced by: adapters, `30_MARKET_ADAPTERS.md`.)*
- **INV-10** Removing AI entirely leaves a coherent, functioning platform. *(Enforced by: architecture, `10_ARCHITECTURE.md`.)*

## Status of Invariants

- **INV-11** These invariants MUST NOT be weakened by any feature; a conflicting feature is rejected by design.
- **INV-12** Any change to an invariant MUST be recorded as a superseding ADR (see Architecture Freeze, governing spec §5).

---

## Related Documents

- `01_PRODUCT_PHILOSOPHY.md` — the principles these laws operationalize
- `05_DOMAIN_MODEL.md` — the entities these laws constrain
- `08_NON_FUNCTIONAL_REQUIREMENTS.md` — quality targets some invariants support
- `32_RISK_ENGINE.md` — enforcer of the risk invariants (INV-04, INV-05)
