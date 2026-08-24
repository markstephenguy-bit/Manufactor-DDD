# ManuFactor — Next Session Seed

Continue the **ManuFactor DDD** project from GitHub source of truth:

`markstephenguy-bit/Manufactor-DDD`

## Role

ChatGPT is the development-architecture / component-discovery lead, while Mark remains the domain authority. The DDD is the semantic baseline and must not be casually rewritten during architecture work.

## Current DDD state

The DDD is **discovery-complete for current evidence** and has passed the development-thinking closure gate.

- Strategic maturity: **Strategic Stable**
- Tactical maturity: **Tactical Candidate**
- Specification Phases 1–15: complete
- Whole-model consolidation: complete
- Scenario validation: 20 scenarios complete
- Scenario results: 15 PASS, 5 PASS WITH MODEL REFINEMENT, 0 unresolved domain questions, 0 remaining model defects
- Open domain-rule issues: **0**
- Bounded Contexts: 10
- Aggregates: 14
- Invariants: 37
- Commands: 33
- Domain Events: 33
- Queries: 13
- Application Use Cases: 17
- Context relationships: 10
- Integration Contracts: 10

Before doing architecture work, read:

1. `docs/ddd/SESSION-HANDOFF.yaml`
2. `docs/ddd/SPECIFICATION.md`
3. `docs/ddd/CANONICAL-REGISTRY.yaml`
4. `docs/ddd/reports/validation-report.md`
5. `docs/ddd/reports/scenario-validation.md`
6. `docs/ddd/reports/scenario-validation-batch2.md`
7. `docs/ddd/issues/unresolved.yaml`
8. `docs/ddd/issues/scenario-validation-open.yaml`

Do **not** reconstruct canonical DDD state from chat when GitHub contains it.

## Next phase

Begin a **whole-DDD reusable component discovery pass** across all current Bounded Contexts.

The purpose is to identify reusable technical/application/platform components that can serve multiple Bounded Contexts without collapsing their semantic boundaries.

Do **not** default to one development slice or one implementation component per Bounded Context.

Instead, inspect the entire DDD for recurring technical responsibilities such as, but not limited to:

- stable identity and cross-system reference resolution;
- retained record/history mechanics;
- evidence/document/attachment handling;
- effective-dated/contextual parameter infrastructure;
- external source integration/adapters and provenance;
- query/read-model/projection infrastructure;
- authorization/responsibility mechanics;
- work routing/reference tracking;
- notifications;
- shared workflow mechanics where appropriate.

These are **candidate technical components**, not assumed components. Derive them from repeated needs across the DDD.

## Critical guardrail

Reusable components may own **technical mechanics**, but they must not absorb **domain meaning**.

Examples:

- A shared history component may append retained history entries, but Quality, Asset, Safety, Training, Project, etc. still own what transitions are valid.
- A shared evidence component may store and reference files/evidence, but the Bounded Context decides what evidence is required and what it means.
- A shared parameter mechanism may support effective dating and applicability, but a Quality Target remains a Quality concept unless domain evidence says otherwise.
- A workflow engine, if used, is infrastructure. It must not become the owner of domain lifecycle rules.

If component design exposes a genuine business contradiction or missing domain rule, stop that local architecture decision, record the new evidence, and reopen only the affected DDD area. Do not restart broad domain discovery.

## ManuFactor framing

ManuFactor is a **gap filler, source-system reader/aggregator/correlator, and owner of missing workflows/records where real gaps exist**. It is not a wholesale ERP, MES, CMMS, HR, QMS, Safety, or analytics replacement.

Reading, correlating, or statistically analyzing source data does not transfer source authority and must not create lineage absent from the source data.

## Working method

Perform a **whole-model pass**, not small disconnected bites.

For each candidate reusable component:

1. Identify the repeated technical responsibility.
2. Name every Bounded Context that needs it.
3. Show what the component may own technically.
4. Show what must remain owned by each Bounded Context.
5. Identify source-system/integration implications.
6. Identify where a generic abstraction would become dangerous.
7. Test the component against at least 3 materially different Bounded Contexts when possible.
8. Classify it as platform infrastructure, application component, integration component, read/projection component, or domain-local helper.
9. Reject candidate components that exist only because of superficial structural similarity.

Do substantial analysis before asking Mark a question. Ask a business question only when architecture work exposes a genuine domain ambiguity that cannot be resolved from the canonical DDD.

## Immediate objective

Produce the **first whole-DDD component map**: a candidate set of reusable development components, which Bounded Contexts consume each one, the domain-boundary guardrails for each, and the key architectural questions that must be answered before implementation begins.
