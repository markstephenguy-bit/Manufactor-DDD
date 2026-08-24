# ManuFactor Macro-Service Boundary Tests — Pass 1

**Status:** Discovery-only boundary testing  
**Date:** 2026-08-24  
**Gate:** DDD-driven design remains prohibited until the discovery-completion gate is satisfied.

## Purpose

Test the four macro-service areas with the greatest overlap/misclassification risk:

1. Forms & Structured Capture
2. Retained History & Audit
3. Catalog, Reference & Hierarchy
4. Integration, Source Acquisition & Provenance

The goal is not to design these services. The goal is to determine whether each is a coherent reusable macro-service responsibility, what belongs inside its semantic perimeter, and what must remain elsewhere.

---

# Test 1 — Forms & Structured Capture

## Materially different consumers tested

### Operations Record

Operations Record contains **operations forms** such as First Hour Production Summary and End of Shift Report. These forms capture operational information and can share substantial mechanics without becoming separate domain types. The form is a capture mechanism under the Operations Record semantic boundary.

### LTV Form Management

LTV has a stronger domain relationship to its form artifacts. It owns the LTV Template, LTV Form Instance, exact template-version traceability, issue/return/scan/match lifecycle, and retained `Recorded` transition. The reusable mechanics cannot own those meanings.

### Reliability Verification / AFAL

AFAL/category checks capture structured criteria, checkpoint results, findings, recorder, and evidence. The business object is a Reliability Verification/AFAL Record, not a generic Form.

## Common responsibility that survives all three

The common reusable need is narrower than the earlier "Form Engine" idea:

- present a structured set of fields/controls;
- collect values;
- apply technical field constraints and common input behavior;
- support reusable sections/layout fragments where appropriate;
- bind captured values to an owning business record/reference;
- distinguish displayed/defaulted/pulled/calculated/manual values where the owning context requires provenance;
- support common save/edit user interaction mechanics without defining whether business correction is allowed.

## Responsibilities that do **not** survive the three-context test

These cannot be promoted into the shared macro service merely because forms are involved:

- a universal Form aggregate;
- a universal Form lifecycle;
- universal Draft/Submitted/Closed states;
- template/version ownership for every form (LTV proves this as domain-significant, but OR/RV do not yet prove equivalent semantics);
- generic approval;
- generic completion;
- generic evidence sufficiency;
- generic business validation rules;
- generic form-definition-as-data assumption.

## Relationship to existing Ports

- `Data` persists owning records/technical capture data.
- `Catalog` may support lookup/registration/reference data.
- `Documents.Compose` renders/exportable artifacts.
- `Storage` persists artifacts.
- `Printing` emits physical copies.

None of those individual Ports is the Forms macro service. Conversely, Forms does not own those capabilities merely because it uses them.

## Pass-1 finding

**Forms & Structured Capture remains a strong macro-service candidate, but its boundary is narrower than the historical Form Engine concept.**

The reusable center is structured input/capture mechanics. Template/version semantics, record lifecycle, validation meaning, and approval/recording remain context-owned.

### Major discovery still required

- Determine whether form definitions/schemas are technically reusable configuration across OR, LTV, and RV or whether only rendering/capture shell mechanics are common.
- Determine whether draft/save/edit mechanics are sufficiently common without importing lifecycle semantics.
- Determine whether field provenance is a Forms concern, a Record/History concern, or a cross-cutting provenance mechanism consumed by Forms.

**Boundary status:** NOT CLOSED.

---

# Test 2 — Retained History & Audit

## Materially different consumers tested

### Asset Lifecycle

Must retain accepted condition/lifecycle-state changes, evidence/reason, and asset identity/history across external MP2 hierarchy restructuring.

### Operations Record

Must preserve what was submitted and correction history rather than destructively rewriting the record.

### Quality Verification

Must preserve Quality Concern identity/status/disposition history, Quality Check evidence, and historical Quality Target/evaluation basis.

### Operational Training and Qualification

Must preserve qualification grant/withdrawal/supersession history, reason, supervisor/authority, evidence references, and effective time.

## Common responsibility that survives all four

- retained prior state/revision/reference rather than destructive replacement;
- actor identity;
- effective/recorded time;
- reason or change basis where required;
- source/provenance reference where the changed fact came from elsewhere;
- correction/supersession relationship;
- ability to retrieve an object's historical sequence;
- immutable/retained evidence references where history depends on them.

## What is *not* common

- allowed transitions;
- status vocabulary;
- what constitutes a correction versus a new business event;
- whether a change is effective immediately or after approval;
- whether a historical entry is itself domain-significant;
- whether history is aggregate-internal state, separate domain record, or technical audit data;
- one universal event schema containing all domain meaning.

## Important distinction: domain history vs technical audit

The DDD demonstrates two related but non-identical concerns:

1. **Domain-retained history** — history is part of the business model (Asset state history, Qualification history, Quality Concern history, Operations Record corrections).
2. **Technical audit trail** — who/when/what technical change occurred for traceability/security/diagnostics.

The shared macro service may provide common retention/audit mechanics, but it must not force domain-retained history into a generic technical audit table and call the domain requirement satisfied.

## Pass-1 finding

**Retained History & Audit is confirmed as a macro-service family, but it likely contains two distinguishable capability layers: domain-history support mechanics and technical audit mechanics.**

This is a classification result only. It does not decide whether they use one store, multiple stores, interceptors, temporal features, events, or anything else.

### Major discovery still required

- Test whether the shared macro-service boundary is one family with two capability layers or two separate macro services.
- Examine all 10 contexts for which history is business-visible versus merely audit/supporting.
- Determine how external-source provenance/history interacts with local history without transferring source authority.

**Boundary status:** CONFIRMED FAMILY; INTERNAL CLASSIFICATION NOT CLOSED.

---

# Test 3 — Catalog, Reference & Hierarchy

## Materially different consumers tested

### Asset Lifecycle

Needs drillable physical-asset identity/hierarchy while reusing MP2 hierarchy as an external structural backbone. Stable ManuFactor Asset identity must survive MP2 restructuring.

### Quality Verification

Needs mill/machine/process contextual references to determine applicable Quality Targets, while Quality itself owns the target semantics.

### Reliability Verification

References Asset identity and may correlate promoted findings with MP2 work.

### LTV Form Management

References applicable job/equipment/process context without owning those external concepts.

### Operations Record / Project Tracking

Need reusable subject lookup/reference/cross-link behavior without absorbing the lifecycle of referenced concepts.

## Common responsibility that survives the test

- internal stable technical identifiers;
- subject/reference lookup;
- hierarchy/navigation where an internal or translated hierarchy is needed;
- typed cross-links/reference metadata;
- resolving a reference sufficiently for display/navigation;
- retaining enough reference provenance to know what is being referred to.

## Responsibilities that do **not** belong in this macro service

### External-system identity translation

MP2 identifier-to-Asset mapping, learning source identities, ProTrack source identities, HR/timekeeping identities, etc. require source-specific provenance and translation. Those belong primarily under **Integration / Source Acquisition & Provenance**, even if Catalog provides internal reference targets.

### Current operational data composition

Resolving `AssetId -> Asset name` is a reference concern. Building `Asset + current MP2 work + Reliability finding` is an **Operational Read Composition** concern.

### Domain ownership

Catalog must not become the authoritative Asset, Person, Project, Job, Quality Target, Qualification, Nonconformity, or Process model.

## Existing `manufactor.thing` / `thing_links`

These mechanisms fit the macro service only if they remain:

- typed technical identity/reference envelopes;
- cross-link/navigation mechanics;
- non-authoritative linking metadata.

They do not justify a universal Thing domain model.

## Pass-1 finding

**Catalog, Reference & Hierarchy is confirmed as a macro service, but its boundary should end at internal reference/hierarchy/cross-link mechanics. External identity translation belongs with Integration; operational composition belongs with Operational Query/Read Composition.**

This finding reduces overlap between three previously blurred areas.

### Major discovery still required

- Test whether `Catalog.Entities` currently mixes domain registration with reference/hierarchy concerns.
- Determine which canonical concepts genuinely require hierarchical navigation versus simple typed references.
- Determine whether business reference dictionaries (shift/crew, job, mill, process) belong here or need context-owned reference projections.

**Boundary status:** CORE BOUNDARY CONFIRMED; CONTENT CLASSIFICATION NOT CLOSED.

---

# Test 4 — Integration, Source Acquisition & Provenance

## Source families tested

### MP2 -> Asset Lifecycle

Requires source maintenance facts, hierarchy/reference correlation, and preservation of MP2 authority. Detailed mapping is explicitly not implementation-ready and remains discovery-required.

### ProTrack -> Kiln Operations

Provides authoritative moisture measurements. Kiln may statistically interpret by lumber dimension but must not invent kiln-run lineage absent from ProTrack.

### Learning system -> Operational Training and Qualification

Provides completion evidence. Completion is not qualification.

### HR/timekeeping -> Operational Workforce Availability

Provides absence/leave/personnel facts. Those facts constrain availability but do not determine operational coverage judgment.

### MES/source data -> Operations Record / Analytics

Operations forms should pull reliable source facts where possible. Downstream publication/analytics may consume Operations Record facts. This demonstrates both inbound acquisition and outbound publication needs without changing Operations Record ownership.

## Common responsibility that survives all source families

- connection/execution lifecycle;
- credential/configuration reference (without defining credential technology);
- source/destination endpoint identity;
- retry and failure classification;
- cursor/checkpoint/watermark mechanics where extraction is incremental;
- raw/source envelope metadata;
- observed/extracted time;
- source record/query reference where available;
- source-system provenance;
- adapter health/diagnostics;
- explicit unavailable/unresolved states;
- transport-level idempotency/correlation where a destination interaction requires it.

## Responsibilities that remain adapter/contract-local

- source-specific query/schema knowledge;
- transformation from source vocabulary into consuming-context meaning;
- authority rules;
- matching heuristics;
- whether two records refer to the same real-world thing;
- business interpretation of a source fact;
- whether a source fact triggers a domain action;
- whether a missing source identifier can be inferred (default: no).

## Inbound acquisition vs outbound publication

The evidence shows both directions:

- inbound: MP2, ProTrack, LMS, HR/timekeeping, MES/process data;
- outbound: Operations Record facts toward an analytical/data-lake destination and potentially other explicit publication needs.

These share transport/provenance/checkpoint concepts but differ materially in authority and contract direction.

## Relationship to Analytics

Integration owns source/destination interaction mechanics and provenance. Analytics owns analytical transformation/projection meaning. Therefore:

- extracting MES facts is Integration;
- converting those facts into analytical grains/time-series is Analytics;
- displaying current authoritative facts in an operational screen is Operational Read Composition;
- writing domain state is the owning Bounded Context.

## Pass-1 finding

**Integration, Source Acquisition & Provenance is confirmed as a broad macro-service family. The current `Integration` Port is too narrowly described by its MP2/SQL Server adapter.**

The macro family likely has at least inbound-acquisition and outbound-publication capability classes, but this pass does not decide whether those are one service, two services, or one contract family.

### Major discovery still required

- Test common behavior against each source family's real connection/data shape before declaring a closed contract.
- Classify external identity mapping as shared mechanics versus adapter-local translation.
- Determine where ETL checkpoint/provenance responsibilities divide between Integration and Analytics.
- Determine whether generic outbound publication is sufficiently repeated outside Operations Record to justify its own macro-service sub-boundary.

**Boundary status:** CONFIRMED FAMILY; INTERNAL CLASSIFICATION NOT CLOSED.

---

# Cross-test findings

## A. Three responsibilities must remain separate

The pass establishes a strong separation among:

1. **Catalog / Reference** — what internal thing/reference/hierarchy is being pointed at.
2. **Integration / Translation** — what external fact/reference came from which source and how it is translated/correlated.
3. **Operational Read Composition** — how current independently authoritative facts are combined for application use.

Collapsing these would create a universal data/entity layer that the DDD does not support.

## B. Forms is not Documents

Forms captures structured user/system input. Documents produces/stores/transfers artifacts. A form may produce a document, but that does not make the two the same macro service.

## C. History is not Data persistence

Data persistence is necessary for history, but it does not supply the reusable semantics/mechanics of retained revision, correction, supersession, provenance, and historical retrieval. History/Audit remains a separate macro-service family for discovery purposes.

## D. Integration is not Analytics

Integration obtains/publishes facts and preserves source/destination provenance. Analytics transforms/projects facts for analysis. One may invoke the other, but their responsibilities are different.

---

# Discovery gate result after Pass 1

The gate remains **CLOSED**. Major discovery work remains.

The first four tests reduce ambiguity but expose further whole-model work:

1. complete the History/Audit split/classification test;
2. complete the Forms definition-vs-capture test;
3. audit actual Catalog contents against the confirmed boundary;
4. test Integration against real source-family evidence without designing adapters;
5. run Operational Read Composition boundary test;
6. run Identity/Responsibility boundary test;
7. run Workflow/Routing boundary test;
8. run Notification boundary test;
9. run Scheduling/Background boundary test;
10. run Rules capability promotion/rejection test;
11. run a full command/query/use-case coverage audit against the macro-service map.

No implementation or DDD-driven design work is authorized by this pass.