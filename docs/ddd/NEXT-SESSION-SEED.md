# ManuFactor — Next Session Seed

Continue the **ManuFactor DDD** project from GitHub source of truth:

`markstephenguy-bit/Manufactor-DDD`

## Role

ChatGPT is the whole-DDD reusable-component / macro-service discovery lead, while Mark remains the domain authority.

## Current DDD state

The canonical DDD is discovery-complete for the business evidence modeled so far and remains the semantic baseline. However, the current project phase is **not implementation/design**. The active phase is a whole-model reusable-component / macro-service discovery pass.

- Strategic maturity: **Strategic Stable**
- Tactical maturity: **Tactical Candidate**
- Specification Phases 1–15: complete
- Whole-model consolidation: complete
- Scenario validation: complete
- Open domain-rule issues: **0**
- Bounded Contexts: 10

Before doing current-phase work, read:

1. `docs/development-architecture/component-discovery/discovery-completion-gate.md`
2. `docs/development-architecture/component-discovery/component-discovery-state.yaml`
3. `docs/ddd/SESSION-HANDOFF.yaml`
4. `docs/ddd/SPECIFICATION.md`
5. `docs/ddd/CANONICAL-REGISTRY.yaml`
6. `docs/ddd/reports/validation-report.md`
7. `docs/ddd/issues/unresolved.yaml`

Do **not** reconstruct canonical DDD state from chat when GitHub contains it.

## Critical phase gate

Do **not** switch into any form of "design using the DDD" until the whole-DDD macro-service/component discovery pass has no major work remaining and Mark explicitly agrees that the discovery gate is satisfied.

During the current phase, do not advance:

- vertical-slice design;
- implementation contracts;
- persistence schemas;
- destination-specific integration designs;
- deployment decisions;
- field-level implementation mapping;
- code-level topology changes;
- technology choices for boundaries that are still under discovery.

Previously created design-oriented artifacts are parked. They may be read as exploratory evidence but must not drive the phase forward.

## Current objective

Perform a **whole-DDD reusable macro-service/component discovery pass** across all 10 current Bounded Contexts.

The purpose is to identify the major reusable technical/application/platform services needed across ManuFactor without collapsing domain ownership.

Do **not** default to one service, one component, or one vertical slice per Bounded Context.

For each candidate reusable macro service:

1. Identify the repeated responsibility that makes it a candidate.
2. Identify every Bounded Context that consumes it.
3. Test it against at least three materially different Bounded Contexts where possible.
4. Identify what capability is genuinely shared.
5. Identify what domain meaning must remain owned locally.
6. Check whether the existing ManuFactor Port/shared-service substrate already covers it.
7. Check for overlap or duplication with other existing services/Ports.
8. Decompose services that are too broad.
9. Reject candidates that are merely domain-local helpers.
10. Preserve source-system authority and provenance implications.
11. Record major evidence gaps.
12. Classify confidence/status: confirmed reusable need, strong candidate, weak candidate, rejected, or local-only.

## Required discovery areas

Explicitly inspect the whole DDD for recurring needs in at least these areas, without assuming these are the final service boundaries:

- identity and reference resolution;
- catalog/hierarchy/reference data;
- persistence/data access;
- retained history/audit;
- forms/structured capture mechanics;
- evidence/documents/storage;
- external-system integration;
- translation/anti-corruption/provenance;
- analytics/data-lake/projection;
- operational read composition;
- authorization/responsibility;
- rules/evaluation mechanics;
- workflow/orchestration;
- routing/external work references;
- notification/attention;
- scheduling/jobs;
- search;
- printing/document composition;
- any other repeated responsibility exposed by the DDD.

This is a discovery checklist, not an approved service catalog.

## Critical semantic guardrail

Reusable services may own **technical mechanics**, but they must not absorb **domain meaning**.

A different form, screen, report layout, field set, timing point, database table, or API shape is not sufficient evidence for a separate domain type or service. Semantic promotion requires a real difference in business meaning, identity, lifecycle, invariants, authority, or state ownership.

## Concrete use cases

Concrete cases such as End-of-Shift Reporting may be used only as **tests** of candidate macro-service reuse during this phase. They must not become the main workstream unless Mark explicitly changes the phase.

## Exit criteria

Do not enter design until:

- all 10 Bounded Contexts have been checked against the candidate macro-service inventory;
- every candidate has an explicit consumer map;
- materially different consumers have been compared where possible;
- duplicate/overlapping services have been reconciled;
- overly broad services have been decomposed or justified;
- weak structural abstractions have been rejected;
- major source-authority/integration implications are identified;
- no major reusable capability category remains unexamined;
- remaining questions are narrow enough not to threaten the macro-service map;
- Mark confirms that no major discovery work remains.

## ManuFactor framing

ManuFactor is a **gap filler, source-system reader/aggregator/correlator, and owner of missing workflows/records where real gaps exist**. It is not a wholesale ERP, MES, CMMS, HR, QMS, Safety, or analytics replacement.

Reading, correlating, or statistically analyzing source data does not transfer source authority and must not create lineage absent from the source data.

## Working method

Perform a **whole-model pass**, not small disconnected bites.

Do substantial analysis before asking Mark a question. Ask a business question only when discovery exposes a genuine domain ambiguity that cannot be resolved from the canonical DDD.

## Immediate objective

Produce the complete **whole-DDD macro-service candidate map** with consuming Bounded Contexts, repeated responsibilities, domain guardrails, overlap/duplication findings, decomposition findings, source-authority implications, confidence, and remaining major discovery gaps. Stop before design and review completeness with Mark.
