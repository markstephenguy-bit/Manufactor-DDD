# ManuFactor DDD ↔ Macro-Service Interaction Map

**Status:** broad-brush discovery baseline  
**Date:** 2026-08-24  
**Authority:** canonical DDD defines business meaning; this map only shows how shared services support it.  
**Design gate:** closed.

## Purpose

Shore up the shared-service landscape at whole-system scale and make the interaction between the canonical DDD and those services explicit.

This is intentionally broad. It does not define APIs, database schemas, endpoints, deployment boundaries, or source-specific integration contracts.

## Governing relationship

The DDD owns:
- business concepts and language;
- aggregate boundaries;
- invariants and lifecycle meaning;
- business authority and decisions;
- source-authority interpretation.

Macro services own reusable mechanics that many Bounded Contexts need.

The governing direction is:

```text
Bounded Context decides WHAT something means and WHETHER an action is valid
                      ↓
Shared macro service performs reusable HOW-mechanics
```

A macro service must never become the semantic owner merely because many contexts call it.

---

# 1. Broad service landscape

The whole-DDD pass currently supports five broad service groups.

## A. Foundation services

### Data & State Persistence
Provides durable state mechanics for all Bounded Contexts.

### Catalog, Reference & Hierarchy
Provides shared lookup, hierarchy/navigation, stable technical references, and typed links.

### Identity, Access & Responsibility
Provides authenticated actor identity, ManuFactor profile/role facts, and reusable enforcement mechanics.

### Retained History
Provides reusable history, correction, supersession, actor/time/reason, and historical-reference mechanics.

These are system-wide foundational services. They support domain state but do not define domain meaning.

---

## B. Interaction / capture services

### Forms & Structured Capture
Provides reusable form/input mechanics where the domain is represented through structured user capture.

### Documents, Artifacts & Evidence
Provides storage, document generation, scan/upload, print, artifact integrity, and evidence references.

### Notification & Attention
Provides reusable delivery of messages/attention without owning escalation meaning.

These services sit between users/processes and the owning Bounded Contexts.

---

## C. Integration / coordination services

### Integration, Source Acquisition & Provenance
Provides reusable mechanics for reading/publishing to external systems while preserving source authority and provenance.

### Work Routing & External Reference Coordination
Provides reusable mechanics for sending or referencing work that is owned elsewhere.

These services connect domain contexts to external systems or externally owned work without transferring ownership.

---

## D. Read / knowledge services

### Operational Query & Read Composition
Combines current facts from multiple authoritative contexts/systems for operational use while preserving provenance and freshness.

### Analytics, Projection & Data Lake
Creates historical/analytical copies, aggregates, projections, dashboards, and statistical views.

These two must remain distinct:
- Operational Read Composition answers **what is true now for an operational decision?**
- Analytics answers **what patterns/history/trends can be derived from retained data?**

---

## E. Platform execution service

### Scheduling & Background Execution
Runs recurring/background technical work for integration, analytics, notification, and other platform jobs.

It does not own business schedules such as kiln plans, project dates, or workforce vacation schedules.

---

# 2. DDD interaction by Bounded Context

The purpose of this table is not to enumerate every possible service call. It shows the **major service relationships** that shape the architecture.

| Bounded Context | Primary shared-service interactions | Domain meaning that stays local |
|---|---|---|
| **Asset Lifecycle** | Data, Catalog/Reference, Identity, History, Integration, Operational Reads, Documents/Evidence | Physical Asset identity, condition, lifecycle state, asset history meaning, acceptance of external MP2 facts |
| **Corrective Action** | Data, Identity, History, Evidence, Work Routing, Notifications, Operational Reads | Nonconformity admission, CAPA lifecycle, root cause, corrective-task meaning, effectiveness/closure rules |
| **Kiln Operations** | Data, Identity, History, Integration, Operational Reads, Analytics, Catalog/Reference | Kiln planning, run/charge meaning, drying interpretation, relationship between schedule/run and moisture statistics |
| **Operations Record** | Data, Forms, Identity, History, Integration, Analytics, Documents/Evidence | Meaning of each operations form/record, its fields/rules, correction semantics, authoritative submitted record |
| **Project Tracking** | Data, Identity, History, Catalog/Reference, Work Routing, Notification, Operational Reads | What qualifies as a Project, project lifecycle, milestone/task semantics, cancellation/abandonment meaning |
| **Quality Verification** | Data, Identity, History, Evidence, Catalog/Reference, Operational Reads, Work Routing, Notification | Quality Concern, Quality Check, target applicability, evaluation rules, meaning of failure and escalation |
| **Reliability Verification** | Data, Forms, Identity, History, Evidence, Catalog/Reference, Integration, Operational Reads, Work Routing, Notification | Verification/checkpoint semantics, finding meaning, supervisor promotion decision, distinction from Asset state |
| **LTV Form Management** | Data, Forms, Identity, History, Documents/Evidence, Catalog/Reference, Notification | LTV template/version meaning, instance lifecycle, scan/match rule, Recorded transition |
| **Operational Training & Qualification** | Data, Identity, History, Evidence, Integration, Operational Reads, Catalog/Reference | Qualification decision, practical evidence sufficiency, authority to grant/withdraw, qualification lifecycle |
| **Operational Workforce Availability** | Data, Identity, History, Integration, Operational Reads, Notification, Catalog/Reference | Staffing gap, availability/coverage meaning, supervisor replacement judgment, vacation/coverage plan semantics |

---

# 3. Strongest cross-DDD service interactions

These are the most important relationships to shore up because they recur across materially different Bounded Contexts.

## Data + History

Nearly every Bounded Context owns durable state **and** business-significant history.

Therefore:
- Data is not enough by itself.
- Technical audit is not enough by itself.
- History is a first-class reusable service family because prior state/reason/evidence often remains part of business meaning.

Examples include Asset state history, Quality Concern history, LTV retained lifecycle, qualification withdrawal history, project cancellation history, and operational-record corrections.

## Catalog/Reference + Domain Ownership

Many contexts need to reference the same subjects: people, assets, jobs, mills, crews, processes, projects, external records.

Therefore Catalog/Reference is central, but it must remain a **reference/navigation service**.

It must not become a universal enterprise domain model or generic owner of Templates, Assets, Projects, Qualifications, Concerns, etc.

## Integration + Operational Read Composition

ManuFactor is fundamentally a gap-filler and source-system reader/aggregator.

Integration gets authoritative facts into reach.

Operational Read Composition combines those facts with ManuFactor-owned facts for current decisions.

Those are separate responsibilities:
- Integration gets/normalizes/provenances source facts.
- Read Composition combines already-authoritative facts for use.

## Integration + Analytics

These also interact heavily but remain distinct:
- Integration handles acquisition/publication mechanics and source provenance.
- Analytics transforms/copies data for trends, history, dashboards, and statistical use.

A single ETL flow may pass through both responsibilities without collapsing them into one service.

## Forms + Documents/Evidence

Forms capture structured data.

Documents/Evidence handle rendered/attached/scanned artifacts.

They often appear together in LTV, Reliability, and Operations, but they are not the same service and neither owns the domain lifecycle represented by the form.

## Work Routing + External Ownership

Corrective Action, Reliability, Project, and Asset-related work may refer or route to externally owned execution.

The shared service should preserve reference/correlation/acknowledgement mechanics while leaving the actual work lifecycle with the owning system or Bounded Context.

---

# 4. Service families that should be shored up first

At broad-brush level, four areas need the most architectural strengthening before any design phase begins.

## 1. Integration / Read / Analytics separation

The current architecture historically overlaps these through `Integration`, `Analytics.ETL`, source wrappers, Cron-ETL, and direct component couplings.

The broad responsibility line should be made unambiguous:

```text
External/other authoritative source
        ↓
Integration + Provenance
        ↓
Authoritative BC or source-backed read
        ├── Operational Read Composition → current operational use
        └── Analytics / Projection → history, trends, BI, statistical use
```

This is probably the single most important macro-service interaction in ManuFactor.

## 2. Catalog / Reference / Identity separation

Three different ideas have historically been close together:
- what a business thing **is**;
- how it is **referenced/navigated**;
- how an external identifier is **mapped**.

Broad rule:
- Domain owns what it is.
- Catalog/Reference owns technical lookup/hierarchy/linking.
- Integration owns external identifier translation/provenance.

## 3. Data / History / Audit separation

Broad rule:
- Data persists current authoritative state.
- Retained History preserves business-significant prior state and correction lineage.
- Observability/Audit records technical execution/access facts.

These may share infrastructure later, but they are not the same responsibility.

## 4. Forms / Evidence / Workflow separation

Broad rule:
- Forms capture structured inputs.
- Evidence/Documents retain/render artifacts.
- Workflow runtime, when used, executes mechanics.
- The Bounded Context owns lifecycle and transition validity.

This prevents a generic form/workflow platform from swallowing Operations Record, LTV, Reliability, Quality, CAPA, or Training semantics.

---

# 5. Broad architecture gaps exposed by the DDD

The DDD shows four service responsibilities more clearly than the current architecture workbook does:

1. **Forms & Structured Capture** as a reusable service family.
2. **Retained History** as distinct from Data and technical Audit.
3. **Operational Query & Read Composition** as distinct from Integration and Analytics.
4. **Work Routing & External Reference Coordination** as distinct from generic workflow runtime.

These are broad architecture gaps, not implementation requests.

Conversely, several current architecture areas are too low-level or optional to act as top-level macro services by themselves:
- Rules;
- Workflow runtime/Elsa;
- Search;
- Computation;
- Realtime;
- Printing;
- Document Compose;
- Storage;
- technical Audit;
- Testing/DevOps/Observability.

They remain valuable capabilities under or alongside the macro-service structure.

---

# 6. Broad interaction model

The resulting whole-system picture is:

```text
                         USERS / MILL OPERATIONS
                                  │
                 ┌────────────────┼────────────────┐
                 │                │                │
              Forms          Documents        Identity
             /Capture         /Evidence       /Access
                 │                │                │
                 └──────────────┬─┴────────────────┘
                                │
                      CANONICAL BOUNDED CONTEXTS
             Asset | Quality | CAPA | Kiln | Operations | etc.
                                │
             ┌──────────────────┼───────────────────┐
             │                  │                   │
          Data +             Catalog/           Retained
          State              Reference           History
             │                  │                   │
             └──────────────────┼───────────────────┘
                                │
                ┌───────────────┼────────────────┐
                │               │                │
          Integration      Operational        Work Routing
          /Provenance      Read Composition    /External Refs
                │               │                │
                └───────────────┼────────────────┘
                                │
                         Analytics / Lake
                                │
                        Dashboards / Trends

Supporting across the system:
Notification + Scheduling/Background Execution
```

The Bounded Contexts remain in the center because they own the business meaning. Shared services surround them and provide reusable mechanics.

---

# 7. Current conclusion

At broad-brush level, the DDD and the macro-service landscape are compatible.

The major work is no longer to invent additional services. It is to **shore up the boundaries among the existing service families**, especially:

1. Integration vs Operational Reads vs Analytics;
2. Catalog/Reference vs Domain Ownership vs external identity mapping;
3. Data vs Retained History vs technical Audit;
4. Forms vs Evidence/Documents vs Workflow;
5. Work Routing vs externally owned work lifecycles.

Those five boundary groups should be considered the remaining broad-brush service-discovery work before any detailed design begins.
