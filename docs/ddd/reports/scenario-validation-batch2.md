# ManuFactor Scenario-Based DDD Validation — Batch 2

**Date:** 2026-08-23  
**Scenarios:** SCENARIO-011 through SCENARIO-020  
**Purpose:** Stress the existing DDD with temporal reversals, late data, ambiguity, failed verification, external hierarchy changes, and non-happy-path lifecycle behavior.

## Summary

| Scenario | Situation | Result |
|---|---|---|
| SCENARIO-011 | Quality target changes after a Quality Check begins | PASS |
| SCENARIO-012 | Qualification withdrawal considered after replacement selection | PASS WITH MODEL REFINEMENT |
| SCENARIO-013 | MP2 hierarchy is restructured after ManuFactor Asset identity exists | PASS WITH MODEL REFINEMENT |
| SCENARIO-014 | LTV is scanned but cannot initially be matched to its electronic record | PASS |
| SCENARIO-015 | CAPA tasks are complete but effectiveness verification fails | PASS |
| SCENARIO-016 | Kiln schedule is revised after a Kiln Run has already started | PASS |
| SCENARIO-017 | Two quality targets overlap so applicability is ambiguous | PASS |
| SCENARIO-018 | Operations Record is corrected after analytics consumed the old value | PASS |
| SCENARIO-019 | A genuine Project is cancelled/abandoned after work begins | PASS WITH MODEL REFINEMENT |
| SCENARIO-020 | Late ProTrack measurements change previously viewed dimension statistics | PASS |

**Final batch result:** 7 PASS, 3 PASS WITH MODEL REFINEMENT, 0 DOMAIN QUESTION REQUIRED, 0 MODEL DEFECT.

## SCENARIO-011 — Quality target changes after a check begins

A Quality Check preserves the historical target/evaluation basis applicable to that retained check. Later target revisions do not silently reinterpret earlier evidence.

**Result: PASS**

## SCENARIO-012 — Supervisor-managed qualification and coverage

The original stress scenario assumed a person could be selected for coverage and then have qualification withdrawn as an independent event that surprised the supervisor's staffing decision. That assumption was invalid.

Mark clarified that **the supervisor manages the qualification**. Qualification withdrawal is therefore a supervisor-controlled decision, not an unrelated external event. The same supervisor is responsible for operational coverage judgment and must use the person's current qualification state when making or revising future coverage decisions.

This clarification also corrected an earlier inference that a designated trainer/SME could withdraw qualification merely because that role may participate in granting qualification. Granting may involve the supervisor or designated trainer/SME; withdrawal/supersession is supervisor-managed.

No separate automatic-invalidation versus explicit-reselection domain rule is required from this artificial scenario.

**Result: PASS WITH MODEL REFINEMENT**  
**ISSUE-0013:** removed as an invalid scenario assumption.

## SCENARIO-013 — MP2 hierarchy restructuring

MP2 may reparent/restructure an equipment node after ManuFactor has already established a physical Asset identity and history. The hierarchy change must not silently create a different physical Asset. Asset Lifecycle wording was refined accordingly.

**Result: PASS WITH MODEL REFINEMENT**

## SCENARIO-014 — LTV scan cannot initially be matched

The returned form may be scanned and retained, but `Recorded` is prohibited until a reliable originating-record match exists. A later successful match may support the single Recorded transition.

**Result: PASS**

## SCENARIO-015 — CAPA effectiveness verification fails

Completion of required corrective tasks is insufficient. Failed effectiveness evidence keeps the Nonconformity/CAPA active and permits further corrective work without creating independent child Corrective Action aggregates.

**Result: PASS**

## SCENARIO-016 — Kiln schedule revised after run starts

Kiln Schedule and Kiln Run are separate aggregates. Planning may be revised while the already-started run remains the retained execution fact tied to what actually occurred.

**Result: PASS**

## SCENARIO-017 — Ambiguous overlapping quality targets

If two target parameters appear equally applicable, Quality Verification must not arbitrarily choose one. Evaluation waits until applicability is unambiguous.

**Result: PASS**

## SCENARIO-018 — Operations Record corrected after analytics use

Operations Record remains authoritative for the record and correction history. Analytics may refresh its projection but does not become lifecycle authority.

**Result: PASS**

## SCENARIO-019 — Project cancellation/abandonment

When a genuine Project is cancelled or abandoned after work has begun, work already performed remains logged and retained as work but is no longer attached to the Project as current project work. Ending the Project association does not erase the work history or transfer unrelated work ownership.

The Project Tracking context was refined to make this rule explicit.

**Result: PASS WITH MODEL REFINEMENT**  
**ISSUE-0014:** resolved.

## SCENARIO-020 — Late ProTrack measurements

Late authoritative ProTrack records can change dimension-level statistical analysis and dashboard projections without creating run-level lineage or changing source authority.

**Result: PASS**

# Cross-batch conclusion

After twenty scenarios, no confirmed boundary failure requires a Bounded Context merge/split or Aggregate redesign. Batch 2 produced three useful model refinements and no remaining domain question.

Current open scenario-discovered issues: **none**.
