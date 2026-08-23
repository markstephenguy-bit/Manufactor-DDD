# ManuFactor Scenario-Based DDD Validation

## Purpose

This validation phase tests the DDD model with realistic ManuFactor situations rather than by inspecting model elements in isolation.

A scenario is treated as evidence against the model. The model must be able to explain, without inventing behavior:

1. where the situation enters the model;
2. which Bounded Context owns each business decision or retained record;
3. which Aggregate protects each consistency boundary;
4. which Commands may change state;
5. which Domain Events record meaningful transitions;
6. which Invariants prevent invalid transitions;
7. which Queries/read models support decisions without taking ownership;
8. which Integration Contracts cross context boundaries;
9. which external systems remain authoritative for source facts;
10. what historical identity/status/evidence must remain referenceable.

## Validation outcomes

Each scenario receives one of four outcomes:

- `PASS` — the existing model explains the situation with no new semantics.
- `PASS_WITH_MODEL_REFINEMENT` — the domain boundaries are correct but canonical tactical metadata or wording needs reconciliation.
- `DOMAIN_QUESTION_REQUIRED` — the scenario exposes a genuine business rule that cannot be derived from confirmed evidence.
- `MODEL_DEFECT` — the current DDD puts ownership, identity, state, or integration semantics in the wrong place or cannot represent a confirmed business behavior.

A `DOMAIN_QUESTION_REQUIRED` result creates a new unresolved issue only when the missing rule materially changes domain behavior. Implementation detail alone does not create a DDD issue.

## Scenario execution format

For each situation record:

- situation;
- initiating fact or actor;
- expected business outcome;
- context path;
- aggregate path;
- commands;
- events;
- invariants;
- queries/integration contracts;
- external authority retained;
- retained historical evidence;
- observed gaps or contradictions;
- validation result;
- required model changes, if any.

## Initial scenario suite

1. `SCENARIO-001` — suspected out-of-size lumber progresses from informal concern through verification and, if justified, CAPA.
2. `SCENARIO-002` — planer moisture statistics indicate a drying problem but no individual board can be traced to a kiln run.
3. `SCENARIO-003` — an operator calls in sick and a supervisor must choose a qualified, available replacement.
4. `SCENARIO-004` — a worker shadows an experienced operator, becomes qualified for a second job, and later has that qualification withdrawn.
5. `SCENARIO-005` — equipment is found degraded/down, maintenance work exists in MP2, and Asset condition/lifecycle decisions must remain distinct from MP2 transaction status.
6. `SCENARIO-006` — an LTV is printed, used in the field, returned to Safety, scanned, matched to its electronic record, and becomes Recorded.
7. `SCENARIO-007` — a Nonconformity requires multiple corrective tasks, some executed in other systems, and cannot close until effectiveness is verified.
8. `SCENARIO-008` — a retained operational record is later found wrong and must be corrected without destroying the original history.
9. `SCENARIO-009` — a genuine Project is created from an operational improvement need and must remain distinct from routine tasks and CAPA-owned work.
10. `SCENARIO-010` — an Environmental gap is proposed but has insufficient concrete process evidence; the model must defer rather than manufacture a Bounded Context workflow.

The suite should expand with real situations from mills, especially edge cases where two systems or departments appear to own the same thing.