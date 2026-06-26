# State Machines

> **Question this document answers:** What lifecycle states do entities move through, and what transitions are legal?

This document defines the lifecycle state machines for the entities that change
state over time. It complements `05_DOMAIN_MODEL.md` (which defines the entities)
by specifying their legal states and transitions. Illegal transitions are
defects; every state change is observable.

---

## Recommendation Lifecycle

```
Created → Validated → Approved
                   ↘ Rejected
```

- **STATE-01** A `Recommendation` MUST begin in `Created` and reach `Approved` only through `Validated`.
- **STATE-02** A `Recommendation` MUST move to `Rejected` from `Validated` when the Risk Engine vetoes it; `Rejected` is terminal.

## Order Lifecycle

```
Created → Submitted → Accepted → Filled → Closed
                   ↘ Rejected
```

- **STATE-03** An `Order` MUST NOT move to `Submitted` unless its originating `Decision` is in the `Approved` state (`STATE-01`).
- **STATE-04** `Filled` and `Closed` are the only success-terminal states; `Rejected` is the failure-terminal state.

## Position Lifecycle

```
Opening → Opened → Closing → Closed
```

- **STATE-05** A `Position` MUST progress monotonically through `Opening → Opened → Closing → Closed`; no state may be skipped.
- **STATE-06** A `Position` MUST reach `Opened` only after at least one `Order` reached `Filled`.

## Transition Rules

- **STATE-07** Every state transition MUST emit a corresponding domain event (see `07_DOMAIN_EVENTS.md`).
- **STATE-08** Every state transition MUST be auditable after the fact, recording prior state, new state, and cause.

---

## Related Documents

- `05_DOMAIN_MODEL.md` — the entities whose lifecycles are defined here
- `07_DOMAIN_EVENTS.md` — events emitted on each transition
- `32_RISK_ENGINE.md` — the component that drives Recommendation to Rejected
- `31_EXECUTION_LAYER.md` — the component that drives the Order lifecycle
