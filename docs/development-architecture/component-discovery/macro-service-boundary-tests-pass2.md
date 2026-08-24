# ManuFactor Macro-Service Boundary Tests — Pass 2

**Status:** Discovery-only boundary testing  
**Date:** 2026-08-24  
**Gate:** DDD-driven design remains prohibited until the discovery-completion gate is satisfied.

## Purpose

Test the remaining ambiguous macro-service families:

1. Operational Query & Read Composition
2. Identity, Access & Responsibility
3. Workflow / Routing / Coordination
4. Notification & Attention
5. Scheduling & Background Execution
6. Rules / Evaluation

---

# Test 5 — Operational Query & Read Composition

## Materially different consumers tested

### Workforce Availability

Needs qualification from Training plus authoritative HR/timekeeping absence facts plus locally owned supervisor coverage decisions. Qualification + absence does not deterministically produce the replacement decision.

### Reliability Verification

Needs Asset identity/context while preserving separation between verification result and Asset condition/state.

### Quality Verification

Needs Quality-owned concern/check/target data and may display independently owned Corrective Action references without importing CAPA lifecycle authority.

### Kiln Operations

Needs local schedule/run history together with authoritative ProTrack moisture measurements/statistics, while explicitly preserving the absence of kiln-run lineage in ProTrack.

### Asset Lifecycle

Needs local rich Asset state/history together with MP2 hierarchy/work facts while preserving MP2 authority for its transactions.

## Common responsibility that survives the test

- invoke/query independently authoritative read sources;
- combine results for one application use case/view;
- preserve source/context identity per contribution;
- expose freshness/observed-at information where material;
- distinguish missing/unavailable/stale values from valid local values;
- preserve unresolved references rather than guessing;
- return a composite presentation/read result without creating composite write authority.

## What does not belong here

- repository writes;
- aggregate commands;
- source extraction jobs;
- analytical grain construction;
- long-term warehouse transformation;
- external identity matching decisions;
- domain interpretation that belongs to an owning context.

## Pass-2 finding

**Operational Query & Read Composition is confirmed as a distinct macro-service family.**

It is not the same as:

- `Data` persistence/query;
- `Catalog` reference resolution;
- `Integration` source acquisition;
- `Analytics` projection/data-lake processing.

The existing architecture does not currently expose this responsibility cleanly as a macro-service boundary.

**Boundary status:** CONFIRMED FAMILY; CONTRACT DETAILS NOT CLOSED.

---

# Test 6 — Identity, Access & Responsibility

## Materially different authority cases tested

### Quality Verification

Salaried management governs Quality Target creation/change.

### Corrective Action

Department salaried Quality office owns active Nonconformity/CAPA progression and closure.

### Reliability Verification

Millwright may record a finding; maintenance supervisor decides promotion; Reliability Manager is accountable.

### LTV Form Management

Safety Office performs returned-paper intake, scan/match, and retained `Recorded` transition.

### Training / Qualification

Supervisor or designated trainer/SME may establish qualification; responsible supervisor manages withdrawal/supersession.

### Workforce Availability

Supervisor makes and retains the operational replacement/coverage judgment.

## Common responsibility that survives the test

- identify/authenticate the actor;
- resolve the actor's application profile;
- expose application permission/grant facts;
- expose organizational/responsibility attributes needed by contexts;
- evaluate generic permission prerequisites where those are not domain decisions;
- provide consistent deny/allow enforcement hooks around context-owned authority rules.

## Responsibilities that remain context-owned

- what `salaried management` means for a specific Quality action if that definition is business-specific;
- whether a maintenance supervisor may promote a particular finding;
- whether evidence is sufficient to establish qualification;
- whether a Safety Office workflow condition is satisfied;
- whether a supervisor's coverage decision is valid in the circumstances.

## Existing architecture conflict

The current architecture inventory describes `Identity.Permissions` as AD-driven, while newer implementation evidence says Windows/AD supplies login identity and ManuFactor Profile data owns application role/tier/grants.

This is not a minor driver choice. It affects the macro-service authority model and must be reconciled during discovery.

## Pass-2 finding

**Identity, Access & Responsibility is confirmed as a macro-service family with at least three semantic layers:**

1. authentication identity;
2. application profile/permission/responsibility facts;
3. context-owned business authority decision.

Only the first two belong to the shared macro service. The third remains in the owning Bounded Context/application rule boundary.

**Boundary status:** FAMILY CONFIRMED; AUTHORITY-SOURCE RECONCILIATION NOT CLOSED.

---

# Test 7 — Workflow, Routing & Coordination

The earlier map grouped several different concerns too aggressively. This test separates them.

## Cases tested

### Corrective Action

Strong long-running coordination case: correction, root cause, corrective tasks, routing, effectiveness verification, closure. External execution may be referenced without local ownership.

### LTV Form Management

Has a multi-step domain lifecycle: electronic origin -> print/issue -> field use -> Safety Office return -> scan -> match -> `Recorded`. The lifecycle is domain-owned and may or may not need a generic workflow runtime.

### Reliability Verification

A finding can be recorded, reviewed/promoted, and routed to maintenance/CAPA. This proves routing/reference coordination but not necessarily a long-running workflow engine requirement.

### Project Tracking

Contains milestones/tasks/dependencies and may reference CAPA/assets/maintenance work. Project lifecycle does not prove a shared workflow engine because project management semantics are themselves domain-owned.

## Two different reusable concerns emerge

### A. Work Routing & External Reference Coordination

Common across CA, RV, PT and likely future consumers:

- route/reference externally owned work;
- retain destination identity/reference;
- retain correlation and acknowledgement where available;
- preserve local coordination history;
- distinguish requested/routed/referenced work from authoritative execution state.

This concern survives the multi-context test.

### B. Long-Running Workflow Runtime

Common technical mechanics may include:

- waiting;
- timers;
- signals;
- resumable execution;
- orchestration state;
- human-step coordination.

But only CAPA strongly proves the need today. LTV and RV prove multi-step business processes, not necessarily a reusable workflow-runtime requirement.

## Pass-2 finding

The earlier single **Workflow, Routing & Coordination** macro-service candidate should be split for discovery purposes:

1. **Work Routing & External Reference Coordination — confirmed macro-service need.**
2. **Long-Running Workflow Runtime — constrained supporting capability, not yet proven as a macro service.**

`Integration.Workflows`/Elsa belongs to the second category and must not define CAPA/LTV/RV business lifecycle semantics.

**Boundary status:** ROUTING CONFIRMED; WORKFLOW-RUNTIME PROMOTION NOT CLOSED.

---

# Test 8 — Notification & Attention

## Materially different consumers tested

- Corrective Action — responsible parties may need attention around active CAPA work/effectiveness verification.
- LTV — Safety Office/participants may need notification around form workflow state.
- Reliability Verification — findings/promotion decisions may require attention.
- Project Tracking — project responsibility/due work may require attention.
- Workforce Availability — staffing gaps/coverage changes may require attention.
- Quality Verification — concerns/checks may require responsible review.

## Common responsibility that survives the test

- send/deliver a message/attention item;
- retry failed delivery;
- resolve technical channels/endpoints;
- render reusable notification templates/mechanics;
- retain delivery result;
- optionally retain acknowledgement as a technical fact if required.

## What does not survive as generic shared meaning

- escalation rules;
- business due dates;
- severity/priority meaning;
- who is accountable for the underlying business item;
- whether acknowledgement changes domain state;
- whether failure to respond violates a business rule.

## Pass-2 finding

**Notification & Attention is confirmed as a macro-service family, but its reusable center is delivery/attention mechanics only.**

Escalation semantics remain context-owned unless future evidence proves a separate repeated technical escalation capability.

**Boundary status:** CORE BOUNDARY CONFIRMED.

---

# Test 9 — Scheduling & Background Execution

## Evidence tested

- recurring/periodic ETL/source acquisition;
- analytical refresh/publication;
- scheduled technical checks;
- potential due-date/reminder triggers in CAPA/Project/etc.;
- existing Cron-Jobs/Cron-ETL architecture evidence.

## Common responsibility that survives the test

- execute a technical job now/later/recurringly;
- retry/resume technical work;
- retain job execution state/checkpoint;
- limit duplicate technical execution;
- observe execution health.

## What does not belong here

- business schedules such as a Kiln Schedule;
- Project schedule semantics;
- vacation/crew scheduling;
- qualification expiration (which current DDD explicitly rejects by elapsed time alone);
- CAPA closure deadlines as domain meaning;
- automatic business transitions just because a timer fired.

## Pass-2 finding

**Scheduling & Background Execution is a platform macro service, not a domain-derived application macro service.**

It should remain in the overall ManuFactor macro-service map because many macro services consume it, but it should be classified separately from business-facing reusable capabilities.

Cron-Jobs, Cron-ETL, Hangfire/background workers are execution mechanisms inside this platform macro service.

**Boundary status:** CLASSIFICATION CONFIRMED.

---

# Test 10 — Rules / Evaluation

## Existing evidence tested

The architecture has a `Rules` Port and rule-evaluation wrapper. Canonical DDD contains explicit invariants/authority rules across all contexts.

Potential repeated technical needs include:

- configurable condition evaluation;
- input validation;
- applicability checks;
- technical policy evaluation.

## Three-context test

### Quality Verification

Applicable target resolution and authorization are domain semantics, not generic rules-engine ownership.

### Reliability Verification

Promotion authority and verification/result meaning are domain semantics.

### LTV

Whether scan/match permits `Recorded` is domain lifecycle logic.

### Corrective Action

Closure requires completed tasks plus successful effectiveness verification; this is a CAPA invariant.

These examples show **why a central Rules macro service would be dangerous**: the most important rules are aggregate/context-owned.

## What may still be reusable

- execute a caller-supplied/configured predicate/rule definition;
- expression evaluation;
- technical validation helpers;
- policy lookup where the policy itself remains owned/named by the consuming context.

## Pass-2 finding

**Rules is not promoted to a macro service.**

It remains a **supporting technical evaluation capability**. A future promotion would require evidence of materially different contexts sharing configurable rule-management semantics, not merely sharing the fact that they all have business rules.

**Boundary status:** MACRO-SERVICE PROMOTION REJECTED FOR CURRENT EVIDENCE.

---

# Revised macro-service classification after Pass 2

## Confirmed domain/application macro-service families

1. Data & State Persistence
2. Catalog, Reference & Hierarchy
3. Documents, Artifacts & Evidence
4. Identity, Access & Responsibility
5. Integration, Source Acquisition & Provenance
6. Operational Query & Read Composition
7. Analytics, Projection & Data Lake
8. Retained History & Audit
9. Work Routing & External Reference Coordination
10. Notification & Attention

## Strong candidate still requiring boundary closure

11. Forms & Structured Capture

## Platform macro service

12. Scheduling & Background Execution

## Supporting capabilities, not macro services under current evidence

- Long-Running Workflow Runtime / Elsa
- Rules / Evaluation
- Printing
- Storage
- Documents.Compose
- Realtime
- Search
- Computation

## Ambient concerns outside macro-service discovery

- Observability
- Security
- DevOps
- Testing
- generic Governance/configuration

## Existing optional capability outside DDD-derived macro-service discovery

- Intelligence / AI

---

# Major discovery still remaining

The discovery-completion gate remains **CLOSED**.

Remaining major work:

1. close the Forms & Structured Capture boundary;
2. finish History/Audit internal classification;
3. audit actual current Catalog contents/mechanisms against the confirmed Catalog boundary;
4. audit current Integration contents/mechanisms against the broader confirmed Integration boundary;
5. reconcile Identity permission/profile source authority;
6. audit current `thing_links`, Form Engine, Analytics, Integration.Workflows, Cron, Data and Catalog for overlaps/duplicate ownership;
7. run a full canonical command/query/use-case coverage audit against the revised macro-service map;
8. verify no major reusable capability category is missing;
9. only then assess whether the discovery-completion gate can be considered.

No DDD-driven design or implementation-shaping work is authorized by this pass.