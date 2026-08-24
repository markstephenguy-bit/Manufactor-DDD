# ManuFactor Macro-Service Boundary Closure Pass

**Status:** Discovery-only normalization  
**Date:** 2026-08-24  
**Design gate:** CLOSED

## Purpose

Close the macro-service boundary questions that can be resolved from the canonical DDD plus current implementation evidence, without crossing into design.

This pass does **not** decide code structure, deployables, databases, frameworks, contracts, APIs, tables, or technologies.

---

# 1. Forms & Structured Capture — boundary closed

## Final discovery classification

**Macro service:** Forms & Structured Capture

## Shared responsibility

- present structured fields/sections to users;
- collect structured values;
- support common field/control behavior;
- support technical display/default/pulled/calculated/manual provenance presentation where required;
- support reusable input-shell behavior and common technical field constraints;
- bind captured values to an owning context record/reference.

## Explicitly excluded

- universal Form aggregate;
- universal Form lifecycle;
- generic Draft/Submitted/Closed semantics;
- domain ownership of LTV Template/version;
- domain ownership of Operations Record;
- domain ownership of AFAL/Reliability Verification;
- approval/completion/recording semantics;
- evidence sufficiency;
- business validation/invariants;
- assumption that all form definitions must be data-driven/configurable.

## Evidence threshold

The boundary survives three materially different contexts:

- Operations Record operations forms;
- LTV forms/templates/instances;
- Reliability Verification / AFAL structured capture.

The commonality is **structured capture mechanics**, not common domain meaning.

**Status:** CONFIRMED MACRO SERVICE.

---

# 2. Retained History vs Technical Audit — classification closed

The earlier combined family contains two different concerns.

## 2.1 Retained Business History

**Macro service:** Retained History Services

Shared responsibility:

- revision/change sequence support;
- correction/supersession linkage;
- actor/time/reason/provenance support;
- retrieval of retained historical versions/changes;
- immutable historical references where required.

Excluded:

- allowed transitions;
- state vocabulary;
- business meaning of change;
- replacement of context-owned historical concepts with one generic event model.

This is directly required across all 10 Bounded Contexts.

## 2.2 Technical Audit

**Classification:** ambient/platform concern, not a DDD-derived macro service.

Technical audit covers security/diagnostic trace of technical operations. It may reuse metadata from Retained History, but it does not satisfy a domain requirement merely because a technical log exists.

## Result

The previous combined name **Retained History & Audit** is normalized to:

- **Retained History Services** — confirmed macro service;
- **Technical Audit** — ambient platform concern.

**Status:** CLOSED.

---

# 3. Catalog, Reference & Hierarchy — boundary closed

## Final macro-service responsibility

- stable internal technical references;
- typed subject lookup;
- hierarchy/navigation/reference-tree mechanics;
- typed cross-links;
- reference metadata/provenance sufficient for navigation/resolution;
- technical registration/indexing of context-owned records where useful.

## Explicit exclusions

- authoritative persistence of domain concepts as generic `catalog_entities` definitions;
- external-system identity translation;
- operational read composition;
- domain lifecycle/state;
- universal Thing semantics;
- universal Status semantics.

## Classification of current mechanisms

### `manufactor.thing`

May remain a technical reference envelope/registration mechanism only.

### `thing_links`

May remain a technical typed-link mechanism. The meaning of a link belongs to the consuming context/macro-service use case.

### External identity mappings

Belong under Integration/Source Acquisition & Provenance, because source-specific authority/provenance is intrinsic to the mapping problem.

### Current-state composite lookups

Belong under Operational Query & Read Composition.

**Status:** CLOSED MACRO-SERVICE BOUNDARY; CURRENT ARCHITECTURE CONTENT MUST LATER BE RECONCILED TO IT.

---

# 4. Integration vs Analytics — boundary closed

## Integration, Source Acquisition & Provenance

Owns reusable mechanics for crossing a system boundary:

- source/destination endpoint interaction;
- extraction/publication execution;
- transport failure/retry/checkpoint mechanics;
- source/destination provenance;
- observed/extracted/published timestamps;
- raw/source record/query/correlation references;
- adapter health;
- unresolved/unavailable states.

It does **not** own analytical modeling.

## Analytics, Projection & Data Lake

Owns reusable analytical mechanics:

- transformation into analytical projections/grains;
- historical aggregation;
- OLAP/time-series structures;
- analytical refresh;
- BI/dashboard consumption;
- analytical retention/query.

It does **not** own source acquisition semantics or operational source records.

## ETL overlap resolution

`ETL` is a process label that may involve both macro services:

1. Integration acquires the source facts and preserves provenance.
2. Analytics transforms/loads facts into analytical structures.

Therefore `ETL` itself is not a semantic owner and cannot be used to collapse the two macro services.

**Status:** CLOSED.

---

# 5. Identity authority model — boundary closed from current evidence

Current implementation evidence establishes:

- `Profile.WindowsUsername` as the link to authenticated Windows identity;
- ManuFactor `ProfileRole` (`Guest`, `NormalUser`, `PowerUser`, `Admin`) stored in the ManuFactor Profile;
- profile enrichment may use directory information, but application role is retained in ManuFactor Profile data.

Canonical DDD separately establishes business authority rules such as salaried-management Quality authority, Safety Office LTV recording responsibility, supervisor qualification authority, and maintenance-supervisor reliability promotion authority.

## Final macro-service layers

### Authentication identity

Shared macro-service responsibility: establish trusted login identity from Windows/enterprise authentication.

### Application profile / permissions / responsibility facts

Shared macro-service responsibility: resolve ManuFactor Profile, app role/grants, organizational metadata used as inputs to authorization.

### Business authority decision

Not shared macro-service ownership. The owning Bounded Context decides whether the actor may perform the business action in the business circumstance.

## Reconciliation result

The older architecture description of `Identity.Permissions` as AD-driven is not sufficient as the macro-service authority model. AD/Windows supplies identity/directory facts; ManuFactor Profile supplies app role; context rules supply business authority.

**Status:** CLOSED FOR DISCOVERY.

---

# 6. Work Routing vs Workflow Runtime — boundary closed

## Confirmed macro service

**Work Routing & External Reference Coordination**

Shared responsibility:

- route/reference externally owned work;
- retain destination reference/correlation;
- retain acknowledgement when available;
- retain local coordination/reference history;
- preserve uncertainty about external execution state.

## Supporting capability only

**Long-Running Workflow Runtime**

Waiting, timers, signals, resumable orchestration, workflow-instance state are technical runtime mechanics. CAPA strongly needs orchestration; LTV/RV prove multi-step processes but do not prove a universal workflow-runtime business boundary.

`Integration.Workflows` / Elsa therefore remains a supporting capability beneath application orchestration rather than a macro service.

**Status:** CLOSED.

---

# 7. Rules — classification closed

`Rules` is not a macro service under current evidence.

## Shared technical capability allowed

- expression/predicate evaluation;
- technical validation helpers;
- execution of caller-owned/configured rules where useful.

## Prohibited ownership

- aggregate invariants;
- domain state transitions;
- business authority semantics;
- CAPA closure rule;
- LTV `Recorded` rule;
- qualification validity;
- Quality Target applicability meaning;
- Reliability promotion decision.

**Status:** SUPPORTING TECHNICAL CAPABILITY; MACRO-SERVICE PROMOTION REJECTED.

---

# 8. Scheduling & Background Execution — classification closed

This remains a **platform macro service** used by Integration, Analytics, Notification, and other technical jobs.

It is explicitly separate from business scheduling concepts.

**Status:** CLOSED.

---

# 9. Operational Query & Read Composition — boundary closed at discovery level

## Final shared responsibility

- compose current read results from independently authoritative sources/contexts;
- preserve contribution provenance/freshness;
- represent unavailable/stale/unresolved contributions;
- support application read use cases across context boundaries;
- expose no write authority.

## Explicitly separate from

- Data persistence;
- Catalog/reference resolution;
- Integration acquisition;
- Analytics/data-lake transformation.

**Status:** CONFIRMED MACRO SERVICE; IMPLEMENTATION CONTRACT REMAINS A FUTURE DESIGN CONCERN.

---

# 10. Notification & Attention — boundary closed

Shared macro service owns delivery/attention mechanics only.

Escalation meaning, severity, due dates, accountability and domain effects remain local.

**Status:** CLOSED.

---

# Normalized macro-service inventory after closure pass

## Domain/application-supporting macro services

1. **Data & State Persistence Services**
2. **Catalog, Reference & Hierarchy Services**
3. **Forms & Structured Capture Services**
4. **Documents, Artifacts & Evidence Services**
5. **Identity, Access & Responsibility Services**
6. **Integration, Source Acquisition & Provenance Services**
7. **Operational Query & Read Composition Services**
8. **Analytics, Projection & Data-Lake Services**
9. **Retained History Services**
10. **Work Routing & External Reference Coordination Services**
11. **Notification & Attention Services**

## Platform macro service

12. **Scheduling & Background Execution Services**

## Supporting capabilities, not macro services

- Long-Running Workflow Runtime / Elsa
- Rules / Evaluation
- Storage
- Documents.Compose
- Printing
- Search
- Computation
- Realtime

## Ambient/platform concerns outside DDD-derived macro-service classification

- Technical Audit
- Observability
- Security
- DevOps
- Testing
- generic configuration/governance

## Existing optional platform capability outside current DDD-derived macro map

- Intelligence / AI

---

# What remains major discovery work

The category and conceptual boundary work is now substantially complete. Remaining major work is **reconciliation/verification**, not invention:

1. reconcile the normalized macro-service map against the live `ManuFactor-Architecture-Workbook.xlsx` rather than relying only on text snapshots;
2. identify every row/current architecture capability that must be reclassified under the normalized macro-service taxonomy;
3. verify whether the live workbook contains additional current capabilities not present in `lists/ports.json` / `lists/components.json`;
4. reconcile current component-to-Port mappings to macro-service consumers without designing implementations;
5. run one final whole-model macro-service consistency audit after that live architecture reconciliation.

Until those are complete, the discovery-completion gate remains **CLOSED**.