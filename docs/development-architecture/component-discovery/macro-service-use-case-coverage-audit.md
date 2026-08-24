# ManuFactor Macro-Service Coverage Audit Against Canonical Use Cases

**Status:** Discovery-only coverage audit  
**Date:** 2026-08-24  
**Coverage:** All 17 canonical Application Use Cases  
**Design gate:** CLOSED

## Purpose

Verify that the revised macro-service inventory is sufficient to support the technical/application needs exposed by every canonical use case **without**:

- inventing a new generic domain service;
- forcing every use case through every macro service;
- treating a supporting capability as domain authority;
- using implementation architecture as semantic evidence.

This is a discovery coverage test, not solution design.

## Macro-service abbreviations

| Code | Macro service |
|---|---|
| DS | Data & State Persistence |
| CR | Catalog, Reference & Hierarchy |
| FC | Forms & Structured Capture (candidate) |
| DE | Documents, Artifacts & Evidence |
| IA | Identity, Access & Responsibility |
| IN | Integration, Source Acquisition & Provenance |
| OQ | Operational Query & Read Composition |
| AN | Analytics, Projection & Data Lake |
| HA | Retained History & Audit |
| WR | Work Routing & External Reference Coordination |
| NT | Notification & Attention |
| BG | Scheduling & Background Execution (platform macro service) |

Supporting capabilities that are deliberately not promoted to macro services:

- long-running workflow runtime;
- Rules/evaluation;
- effective-dated parameter helper;
- Search;
- Computation;
- Storage/Compose/Printing individually.

---

# Coverage matrix

`R` = required/repeated macro-service need directly exposed by the canonical use case.  
`S` = supporting/reference use may be present but is not essential to the use case's semantic core.  
Blank = no current evidence requiring that macro service for this use case.

| Canonical use case | DS | CR | FC | DE | IA | IN | OQ | AN | HA | WR | NT | BG |
|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| OR — Capture and Correct a Specific Operational Record | R | S | R | S | R | S | S | S | R |  | S | S |
| KO — Plan What Lumber Needs Drying | R | S |  |  | R | S | S |  | R |  | S | S |
| KO — Record Kiln Runs / Review Dimension Moisture | R | S |  |  | R | R | R | R | R |  | S | S |
| PT — Manage a Project | R | S |  | S | R | S | S | S | R | S | R | S |
| QV — Intake a Quality Concern | R | S |  | S | R |  | S | S | R |  | R | S |
| QV — Review / Disposition a Quality Concern | R | R |  | S | R | S | R |  | R | R | R | S |
| QV — Perform and Evaluate a Quality Check | R | R |  | R | R | S | S | S | R | S | S | S |
| QV — Manage Applicable Quality Target Parameters | R | R |  |  | R | S | S |  | R |  | S | S |
| CA — Manage a Nonconformity Through CAPA | R | R |  | R | R | R | R | S | R | R | R | S |
| AL — Manage Rich Asset State and History | R | R |  | S | R | R | R | S | R | S | S | S |
| RV — Record and Review a Reliability Finding | R | R | S | R | R | S | R | S | R | R | R | S |
| LTV — Publish and Issue LTV Form Records | R | R | R | R | R | S | S |  | R |  | R | S |
| TQ — Establish Operational Qualification | R | S |  | R | R | R | R | S | R |  | S | S |
| WA — Fill an Operational Staffing Gap | R | R |  |  | R | R | R | S | R |  | R | S |
| AL — Change Asset Lifecycle State | R | R |  | S | R | S | S |  | R |  | S | S |
| LTV — Record Returned LTV Form | R | R | R | R | R | S | S |  | R |  | R | S |
| TQ — Withdraw Operational Qualification | R | S |  | S | R | S | R |  | R |  | S | S |

The many `S` marks intentionally do not mean every use case should call that macro service. They indicate a supporting capability may be consumed depending on the concrete workflow while the macro-service category remains valid.

---

# Coverage findings by macro service

## DS — Data & State Persistence

**Coverage:** all 17 use cases.

This confirms persistence as ubiquitous technical substrate. It does not make one generic data model or repository the owner of all state.

Result: **CONFIRMED / NO GAP**.

## CR — Catalog, Reference & Hierarchy

**Coverage:** direct reference/context resolution across most contexts.

Strongest needs include:

- Asset identity/hierarchy;
- Quality mill/machine/process applicability;
- Reliability -> Asset reference;
- LTV job/equipment/process applicability;
- Workforce job/crew/shift references;
- CAPA/Project/external-work links.

Result: **CONFIRMED, WITH PREVIOUSLY IDENTIFIED OVERREACH RISK**.

No use case requires Catalog to persist arbitrary domain records as generic entities.

## FC — Forms & Structured Capture

**Direct coverage:** Operations Record and LTV.  
**Supporting evidence:** Reliability Verification/AFAL.

The canonical use cases do not show a broad requirement that every interactive record be treated as a Form. This is important.

Result: **REMAINS STRONG CANDIDATE, NOT UNIVERSAL**.

The macro service must remain a structured-capture capability, not a replacement model for QV checks, CAPA, Projects, Assets, Qualifications, etc.

## DE — Documents, Artifacts & Evidence

**Strong coverage:** QV, CA, RV, LTV, TQ; optional/supporting in OR/PT/AL.

The needs are materially different enough to prove reuse:

- measurements/evaluation evidence;
- effectiveness verification;
- reliability evidence;
- returned LTV scan/exact form artifact;
- qualification evidence.

Result: **CONFIRMED / NO NEW CATEGORY REQUIRED**.

## IA — Identity, Access & Responsibility

**Coverage:** all 17 use cases have actors/authority.

The canonical use cases strongly prove that shared login/permission mechanics are insufficient without responsibility context, but responsibility semantics remain BC-owned.

Result: **CONFIRMED; SOURCE/AUTHORITY MODEL RECONCILIATION REMAINS MAJOR WORK**.

## IN — Integration, Source Acquisition & Provenance

**Direct coverage:** AL/MP2, KO/ProTrack, TQ/learning, WA/HR-timekeeping, CA routing/external work, OR source pulls/downstream data movement.

This is materially heterogeneous and confirms a broad macro-service family rather than an MP2-only Integration Port.

Result: **CONFIRMED; CURRENT ARCHITECTURE TAXONOMY INCOMPLETE**.

## OQ — Operational Query & Read Composition

**Direct coverage:** KO, QV/CA, AL/MP2, RV/AL, TQ/learning evidence where current facts are needed, WA/TQ/HR.

This need cannot be satisfied semantically by Analytics alone.

Result: **CONFIRMED MAJOR MISSING MACRO-SERVICE CATEGORY IN CURRENT PORT TAXONOMY**.

## AN — Analytics, Projection & Data Lake

**Coverage:** strongest for KO, OR, WA, with additional downstream analytical uses across AL/QV/CA/PT/RV.

No canonical command requires Analytics to own business state.

Result: **CONFIRMED / BOUNDARY WITH INTEGRATION MUST REMAIN EXPLICIT**.

## HA — Retained History & Audit

**Coverage:** all 17 use cases either explicitly require retained history or participate in a context whose lifecycle/history is required.

This is the strongest evidence that current architecture is missing a first-class macro-service family.

Result: **CONFIRMED MAJOR GAP**.

## WR — Work Routing & External Reference Coordination

**Strongest coverage:** CA, RV, QV/CA linking, PT references; AL may consume maintenance/work references.

The coverage supports shared routing/reference coordination but does not support a generic Work or Task domain.

Result: **CONFIRMED, NARROWLY SCOPED**.

## NT — Notification & Attention

**Coverage:** several human-owned workflows can require attention, but canonical use cases generally do not make notification itself the business outcome.

Result: **CONFIRMED DELIVERY MACRO SERVICE; ESCALATION SEMANTICS REMAIN LOCAL**.

## BG — Scheduling & Background Execution

**Coverage:** mostly supporting/technical across integration, analytics, notifications, periodic work.

No canonical use case makes background execution a business domain concept.

Result: **CONFIRMED PLATFORM MACRO SERVICE, NOT DOMAIN/APPLICATION SEMANTIC OWNER**.

---

# Supporting-capability audit

## Long-running Workflow Runtime

No coverage gap appears when it remains a supporting capability rather than a macro service. CAPA is the strongest forcing case; LTV/RV have multi-step processes but do not prove the runtime itself as a macro-service boundary.

Result: **DO NOT PROMOTE YET**.

## Rules / Evaluation

All use cases contain rules/invariants, but those rules are owned by their contexts. No coverage gap requires a central Rules macro service.

Result: **DO NOT PROMOTE**.

## Effective-Dated Parameters

Quality Target management requires effective/historical applicability. No other canonical use case currently proves a materially equivalent shared parameter-management need.

Result: **DOMAIN-LOCAL/HELPER CANDIDATE; NOT MACRO SERVICE**.

## Search

Reference lookup and operational query services cover current needs.

Result: **NO INDEPENDENT MACRO-SERVICE GAP**.

## Computation

Calculations occur, but computation is a technical helper. Business calculation meaning remains local.

Result: **NO INDEPENDENT MACRO-SERVICE GAP**.

---

# Missing-category audit

Every canonical application use case can be classified using:

1. an owning Bounded Context/domain model;
2. the 10 confirmed application/domain-supporting macro-service families;
3. the Forms candidate where directly evidenced;
4. the Background Execution platform macro service;
5. local/supporting capabilities where appropriate.

**No additional major reusable macro-service category is exposed by the 17-use-case coverage audit.**

This is a meaningful discovery result: the macro-service inventory appears **category-complete for current canonical use cases**, though several boundaries are still unresolved.

---

# Discovery gate result after coverage audit

The gate remains **CLOSED**, but the reason has narrowed.

Major work is no longer "find missing categories." The major remaining work is now **boundary and ownership reconciliation**:

1. close Forms & Structured Capture boundary;
2. classify History/Audit internal layers across all contexts;
3. reconcile Catalog generic-entity overreach;
4. reconcile Integration vs Analytics at ETL/source acquisition;
5. reconcile Identity login/Profile/permission authority;
6. classify current `thing_links` and external-reference mapping responsibilities;
7. reconcile current architecture taxonomy level (macro services vs Ports vs ambient concerns);
8. confirm the revised map against current `ManuFactor-arch` live architecture substrate before declaring discovery complete.

No implementation design is authorized.