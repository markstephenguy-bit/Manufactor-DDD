# ManuFactor Scenario-Based DDD Validation

**Date:** 2026-08-23  
**Scope:** Ten realistic ManuFactor situations exercised end-to-end against the canonical DDD model.  
**Method:** For each situation, test ownership, Aggregate boundary, Commands/Events, invariants, policies, integration/source authority, retained history, and whether the model can explain the outcome without inventing semantics.

## Result codes

- **PASS** — scenario traverses the model without a semantic defect.
- **PASS WITH MODEL REFINEMENT** — business rules were already known, but the scenario exposed stale/missing model representation that was corrected.
- **DOMAIN QUESTION REQUIRED** — scenario exposes a material business rule not currently known.
- **MODEL DEFECT** — current boundaries/rules contradict the scenario or duplicate ownership.

## Summary

| Scenario | Situation | Result |
|---|---|---|
| SCENARIO-001 | Suspected lumber-size problem through Quality Concern, verification, Nonconformity, corrective work, and effectiveness verification | PASS WITH MODEL REFINEMENT |
| SCENARIO-002 | ProTrack moisture statistics suggest drying trouble but contain no kiln-run identity | PASS |
| SCENARIO-003 | Operator calls in sick and a supervisor must select qualified/available replacement coverage | PASS |
| SCENARIO-004 | Worker shadows competent operator, is qualified, and later qualification is explicitly withdrawn | PASS |
| SCENARIO-005 | Millwright records failed reliability verification; supervisor decides whether it becomes action/Asset-state change | PASS |
| SCENARIO-006 | Used LTV paper returns to Safety, is scanned/matched, and becomes Recorded | PASS |
| SCENARIO-007 | CAPA corrective task is routed to another work owner while CAPA retains coordination and effectiveness responsibility | PASS |
| SCENARIO-008 | Operational record is corrected later without erasing what was originally reported | PASS |
| SCENARIO-009 | Corrective work is substantial enough that the business manages it as a real Project | PASS WITH MODEL REFINEMENT |
| SCENARIO-010 | Environmental concern is proposed but no concrete ManuFactor Environmental workflow/gap is yet known | PASS |

**Final scenario result:** 10/10 situations are explainable by the model after two scenario-driven refinements. No new unresolved domain-rule question was required.

---

## SCENARIO-001 — Suspected lumber quality problem

### Situation
A person suspects lumber is out of size. A Quality Concern is retained. Measurements may be taken against the applicable mill/machine/process target. If retained evidence establishes nonfulfillment of an applicable requirement, an authorized reviewer admits a Nonconformity. Corrective tasks are completed and the correction is later verified effective before CAPA closure.

### Traversal
1. `bc.quality-verification` owns Quality Concern and optional Quality Check.
2. Applicable Quality Target Parameter resolves the correct contextual target.
3. Failed measurement/check remains evidence; it is not automatically a Nonconformity.
4. `policy.corrective-action.determine-nonconformity-admission` explicitly decides admission.
5. `bc.corrective-action` owns Nonconformity/CAPA.
6. Corrective work is represented as tasks under the parent Nonconformity.
7. Routed execution remains owned by the destination context/system.
8. Required tasks alone are insufficient for closure; retained effectiveness verification must show that the correction worked.
9. Original Quality Concern remains referenceable through its status/history.

### Finding
The scenario exposed stale model text: the Corrective Action context did not yet state the effectiveness-verification closure gate, and the Policy registry still described several already-resolved issues as unresolved.

### Correction
Both stale representations were corrected. No new business rule was invented.

**Result: PASS WITH MODEL REFINEMENT**

---

## SCENARIO-002 — Kiln/ProTrack moisture warning without run identity

### Situation
A dashboard shows moisture behavior for a lumber dimension worsening after drying. Operators want to know whether a kiln is performing poorly. ProTrack measurements contain thickness x width x length but no Kiln Run/Charge identifier.

### Traversal
1. ProTrack remains authoritative for source moisture measurements.
2. `bc.kiln-operations` owns Kiln Schedule, Kiln Run/Charge, retained operating history, and drying-performance interpretation.
3. `contract.protrack-moisture-to-kiln-operations` translates ProTrack data at the supported grain.
4. Analysis can compare retained kiln history with downstream moisture distributions/statistics by dimension.
5. No individual measurement may be attributed to a specific kiln run because the identifier does not exist.
6. Dashboard output remains a projection, not authoritative state.

### Validation
The model gives a useful analytical answer while refusing false lineage. It preserves the distinction between statistical evidence and run-level traceability.

**Result: PASS**

---

## SCENARIO-003 — Sick-call replacement coverage

### Situation
An operator calls in sick shortly before a shift. Operations needs someone who can cover the job.

### Traversal
1. HR/timekeeping remains authoritative for absence facts.
2. `bc.training-competency` supplies current Operational Qualification.
3. `bc.workforce-availability` owns the staffing gap, replacement options, and coverage decision.
4. `policy.workforce-availability.replacement-eligibility` requires both current qualification and availability.
5. Qualification + availability makes a person eligible, but does not automatically select the replacement.
6. Supervisor judgment determines the actual replacement and is retained as business history.
7. Workforce Availability does not rewrite qualification or HR/timekeeping records.

### Validation
The model separates facts from the supervisor's operational judgment and retains the decision without turning HR into the operational staffing model.

**Result: PASS**

---

## SCENARIO-004 — Qualification through shadowing and later withdrawal

### Situation
A worker is being prepared for a mill job. The worker shadows the current competent operator and is observed sufficiently for an authorized supervisor/trainer to judge capability. Months or years later, someone explicitly withdraws that qualification.

### Traversal
1. Learning/LMS completion may be supporting evidence but does not establish qualification.
2. Practical job shadowing and observed capability are the operative qualification evidence.
3. Supervisor or designated trainer/SME grants the qualification.
4. Qualification persists indefinitely absent an explicit withdrawal/supersession.
5. Elapsed time does not create an `Expired` state.
6. Withdrawal ends current validity but retains the original decision, evidence, actor, reason, and effective time.
7. Workforce Availability consumes only current qualification status for staffing eligibility.

### Validation
The model supports the actual informal-to-explicit mill qualification process without importing LMS semantics or inventing recertification timers.

**Result: PASS**

---

## SCENARIO-005 — Failed reliability verification and Asset state

### Situation
A millwright performs a reliability verification and records a failed finding on a machine. The machine is still physically running. The finding may require maintenance or an Asset-state change.

### Traversal
1. `bc.reliability-verification` owns the verification checkpoint, criteria, evidence, and result.
2. The failed result is a verification fact, not an Asset Condition.
3. A millwright may record the finding but cannot automatically promote it into formal action or mutate Asset state.
4. Maintenance supervisor decides whether the finding is promoted.
5. Reliability Manager remains accountable for the reliability process.
6. `bc.asset-lifecycle` owns any explicit Asset Condition/Lifecycle State change.
7. Asset Condition and Asset Lifecycle State remain separate: e.g. `Degraded` may coexist with `In Service`.
8. MP2 remains authoritative for its WR/WO transaction lifecycle.

### Validation
The scenario demonstrates that the model does not collapse verification, maintenance work, and Asset state into one status.

**Result: PASS**

---

## SCENARIO-006 — LTV paper return and recording

### Situation
An LTV form originates electronically, is printed and used in the field, then is returned to the Safety Office.

### Traversal
1. `bc.safety-signoff` / LTV Form Management owns the controlled LTV record lifecycle.
2. Printing/issuing is an early status only.
3. Safety Office receives/picks up the used paper form.
4. Returned form is scanned.
5. Scan is reliably matched to the originating electronic record.
6. Only after that match may `RecordLTVForm` transition the retained record to `Recorded`.
7. Scan/match evidence and status history remain retained.
8. The LTV record never disappears; later lifecycle changes are status/history changes.

### Validation
The model represents the actual paper/electronic reconciliation process and prevents `Recorded` from meaning merely `printed` or `issued`.

**Result: PASS**

---

## SCENARIO-007 — CAPA task routed to an external work owner

### Situation
A Nonconformity requires work that is properly executed by another department/system—for example maintenance work owned by MP2 or another operational work process.

### Traversal
1. Corrective Action owns the Nonconformity and corrective-task coordination.
2. `policy.corrective-action.route-to-work-owner` determines the proper execution owner.
3. Routed work remains authoritative in the destination context/system.
4. CAPA retains the task/reference and coordination history but must not mirror or invent the destination work lifecycle.
5. Destination work completion may satisfy a corrective-task requirement but does not by itself close the Nonconformity.
6. CAPA still requires post-correction effectiveness verification.

### Validation
The model preserves one owner for actual execution and one owner for CAPA coordination/closure.

**Result: PASS**

---

## SCENARIO-008 — Correcting an operational record

### Situation
A structured ManuFactor operational record replaces information formerly sent by email. Later, someone discovers that a field in the submitted record was wrong and corrects it.

### Traversal
1. `bc.operations-record` owns the specific retained operational record because a real structured-recording gap exists.
2. The record type remains tied to a distinct business need rather than a universal catch-all form.
3. Correction updates current meaning while preserving the original submitted information and correction history.
4. Referenced Asset, Person, Quality, or Safety concepts remain externally owned.
5. Downstream Superset/analytics may consume the corrected/current view and retained facts, but analytics does not become the record authority.

### Validation
The scenario confirms Operations Record is a transactional gap-filler rather than a generic form engine or analytics context.

**Result: PASS**

---

## SCENARIO-009 — CAPA-related work becomes a real Project

### Situation
A corrective task requires substantial coordinated work and the business explicitly decides to manage that work as a Project, with owner, dates, milestones/tasks, dependencies, progress, and project completion.

### Traversal
1. The corrective task begins in `bc.corrective-action` under a Nonconformity.
2. The work does not become a Project merely because it is difficult, expensive, or long-running.
3. When the business actually recognizes/manages it as a Project, `bc.project-tracking` owns the Project lifecycle.
4. Corrective Action retains the Nonconformity/corrective-task relationship/reference.
5. Project status/completion remains Project-owned.
6. Project completion does not automatically close CAPA.
7. CAPA closure still requires its own required corrective-task state plus effectiveness verification.

### Finding
The model already stated these rules in both contexts, but the Context Map intentionally omitted the interaction. The scenario establishes that this is a material cross-context flow when it occurs.

### Correction
Added:
- `rel.corrective-action-to-project-tracking`
- `contract.corrective-action-to-project-tracking`

The relationship is conditional on the business actually treating the work as a Project and does not create shared lifecycle ownership.

**Result: PASS WITH MODEL REFINEMENT**

---

## SCENARIO-010 — Environmental request with no concrete ManuFactor gap

### Situation
Someone proposes that ManuFactor should handle an Environmental concern because every mill has an Environmental department, but no concrete Environmental workflow, missing record, system gap, or integration need has yet been identified.

### Traversal
1. Department existence is not sufficient evidence for a Bounded Context.
2. Current model recognizes Environmental as a real business/opportunity area.
3. No concrete process gap exists from which to derive owned concepts, Aggregate boundaries, invariants, Commands, or integrations.
4. ManuFactor must not manufacture Environmental Compliance semantics merely to make the map look complete.
5. If a concrete Environmental gap is later identified, that new evidence should begin a focused discovery cycle.

### Validation
The correct model response is intentional deferral, not forced placement under Operations, Safety, Quality, or a speculative Environmental context.

**Result: PASS**

---

# Cross-scenario conclusions

## Boundary integrity
The scenarios did not justify merging any current Bounded Contexts. Quality Verification/Corrective Action, Training/Workforce Availability, Reliability Verification/Asset Lifecycle, and CAPA/Project Tracking all benefit from explicit separation because each side owns a different decision or lifecycle.

## Source authority
The model consistently preserves source authority:
- ProTrack owns moisture measurements.
- MP2 owns WR/WO transactions and supplies equipment hierarchy facts.
- HR/timekeeping owns absence facts.
- learning systems own course/completion records.
- ManuFactor owns the gap workflows and richer semantics it explicitly creates.

## Retained-history pattern
Scenario validation reinforced a recurring domain rule: significant ManuFactor records are historical business facts. Quality Concerns, Operational Qualifications, LTV records, Asset state history, operational records, Projects, and Nonconformities remain referenceable after active handling ends. Current state is represented through status/transitions and retained history rather than destructive replacement.

This is a recurring pattern, not a license to invent identical status models across contexts; each Aggregate still owns its own valid transitions.

## Analytics boundary
The scenarios confirm that dashboards/statistics are projections. They may combine authoritative facts, but they do not become authoritative lifecycle state and must not manufacture missing lineage.

## Scenario-discovered model defects
Two classes of model defect were found and corrected:
1. **Stale tactical representation** — known CAPA effectiveness and resolved-policy state were not fully reflected in current context/policy documentation.
2. **Missing explicit integration boundary** — CAPA-to-Project flow was supported in prose but absent from Context Map/Integration Contracts.

Neither defect required a new business question.

## Unresolved domain questions
**None were created by this ten-scenario pass.** `docs/ddd/issues/unresolved.yaml` remains `issues: []`.

# Overall scenario-validation conclusion

**PASS WITH MODEL REFINEMENTS**

The ManuFactor DDD explains all ten situations without requiring a new Bounded Context, Aggregate, or unresolved business rule. Scenario testing improved the model by exposing representation/integration omissions that structural validation alone had not found.

The canonical counts after scenario validation are unchanged except for the newly explicit CAPA-to-Project integration:
- 10 Bounded Contexts
- 14 Aggregates
- 37 Invariants
- 33 Commands
- 33 Domain Events
- 3 Policies
- 13 Queries
- 17 Application Use Cases
- **10 Context Map relationships**
- **10 Integration Contracts**
- 0 open domain-rule issues
