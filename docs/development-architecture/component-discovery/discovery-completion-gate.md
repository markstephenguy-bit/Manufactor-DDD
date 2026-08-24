# ManuFactor Macro-Service Discovery Completion Gate

**Status:** Active process guardrail  
**Date:** 2026-08-24  
**Authority:** Mark direct

## Rule

Do **not** switch from discovery into "design using the DDD" while major whole-model discovery work remains.

The current phase is **discovery-only**.

DDD artifacts may be used to identify, classify, compare, and validate candidate reusable macro services/components, but they must not yet be used to prematurely design:

- implementation contracts;
- destination-specific integration contracts;
- persistence schemas;
- deployment topology changes;
- vertical slices;
- application workflows;
- code-level component boundaries beyond documenting current evidence;
- field-level source mappings except where needed as evidence for macro-service discovery;
- technology selection for a not-yet-settled macro-service boundary.

Previously created design-oriented artifacts are retained as exploratory work, but they are **parked** and must not drive further work until this gate is passed.

## Current objective

Complete a whole-DDD **macro-service / reusable-component discovery pass** across all 10 Bounded Contexts.

The purpose is to determine the major reusable services/capabilities needed across ManuFactor without collapsing domain ownership.

For every candidate macro service, discovery must establish:

1. the repeated responsibility that causes it to exist;
2. every current Bounded Context that appears to consume it;
3. materially different usage examples from at least three contexts where possible;
4. what common capability is genuinely shared;
5. what domain meaning remains local to each Bounded Context;
6. whether an existing ManuFactor Port/shared service already covers the capability;
7. whether current Ports/services overlap or duplicate one another;
8. whether the candidate is actually too broad and should be decomposed;
9. whether the candidate is merely a domain-local helper rather than a macro service;
10. source-system authority/provenance implications;
11. major unresolved questions or evidence gaps;
12. confidence/status: confirmed reusable need, strong candidate, weak candidate, rejected, or local-only.

## Required discovery coverage

At minimum, explicitly evaluate the whole model for recurring needs in these areas without assuming the final service boundaries in advance:

- identity and reference resolution;
- catalog/hierarchy/reference data;
- persistence/data access;
- retained history/audit;
- forms and structured capture mechanics;
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
- printing/document composition where reused;
- other repeated responsibilities exposed by the DDD.

This list is a discovery checklist, not an approved service catalog.

## Exit criteria

The phase may move into design only when all of the following are true:

- all 10 Bounded Contexts have been checked against the candidate macro-service inventory;
- every candidate macro service has an explicit consumer map;
- materially different consumers have been compared where possible;
- obvious duplicate/overlapping services have been reconciled;
- broad services that hide multiple responsibilities have been decomposed or explicitly justified;
- weak abstractions based only on structural similarity have been rejected;
- major integration/source-authority implications are identified;
- no major reusable capability category remains unexamined;
- remaining open items are narrow enough that they do not threaten the macro-service map;
- Mark agrees that no major discovery work remains.

Only after this gate passes should the DDD become an input to implementation/design decisions.

## Working rule

A concrete use case such as End-of-Shift Reporting may be used only as a **test case** for a candidate macro service during this phase. It must not become the primary workstream unless Mark explicitly changes the phase.
