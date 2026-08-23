# ManuFactor Scenario-Based DDD Validation — Batch 2

**Date:** 2026-08-23  
**Scenarios:** SCENARIO-011 through SCENARIO-020  
**Purpose:** Stress the existing DDD with temporal reversals, late data, ambiguity, failed verification, external hierarchy changes, and non-happy-path lifecycle behavior.

## Summary

| Scenario | Situation | Result |
|---|---|---|
| SCENARIO-011 | Quality target changes after a Quality Check begins | PASS |
| SCENARIO-012 | Qualification is withdrawn after replacement selection but before coverage occurs | DOMAIN QUESTION REQUIRED |
| SCENARIO-013 | MP2 hierarchy is restructured after ManuFactor Asset identity exists | PASS WITH MODEL REFINEMENT |
| SCENARIO-014 | LTV is scanned but cannot initially be matched to its electronic record | PASS |
| SCENARIO-015 | CAPA tasks are complete but effectiveness verification fails | PASS |
| SCENARIO-016 | Kiln schedule is revised after a Kiln Run has already started | PASS |
| SCENARIO-017 | Two quality targets overlap so applicability is ambiguous | PASS |
| SCENARIO-018 | Operations Record is corrected after analytics consumed the old value | PASS |
| SCENARIO-019 | A genuine Project is cancelled/abandoned after work begins | DOMAIN QUESTION REQUIRED |
| SCENARIO-020 | Late ProTrack measurements change previously viewed dimension statistics | PASS |

**Batch result:** 7 PASS, 1 PASS WITH MODEL REFINEMENT, 2 DOMAIN QUESTION REQUIRED, 0 MODEL DEFECT.

---

## SCENARIO-011 — Quality target changes after a check begins

The Quality Check must preserve the historical target/evaluation basis applicable to that retained check. A later target revision applies prospectively and does not silently reinterpret earlier evidence. Existing Applicable Quality Target revision history and Quality Check basis-retention rules explain the situation.

**Result: PASS**

## SCENARIO-012 — Qualification withdrawn after replacement selection

A person is qualified and available when selected as replacement coverage. Before the shift/coverage occurs, Training and Qualification explicitly withdraws that person's qualification.

Confirmed rules establish that the withdrawn qualification is no longer current and cannot satisfy new replacement eligibility. What is not established is what happens to an already-selected Operational Coverage Plan: automatic invalidation, reopening/reselection, or an explicit supervisor transition.

This is a real temporal domain rule, not an implementation detail.

**Result: DOMAIN QUESTION REQUIRED**  
**Created:** `ISSUE-0013`

## SCENARIO-013 — MP2 hierarchy restructuring

MP2 may reparent/restructure an equipment node after ManuFactor has already established a physical Asset identity and history. Because MP2 hierarchy is external structural/navigation evidence and ManuFactor owns the richer stable Asset identity, a hierarchy change must not silently create a different physical Asset.

The existing model strongly implied this through stable identity and source-authority rules, but the Asset Lifecycle context did not state the hierarchy-change case explicitly. The context was refined to preserve Asset identity and mapping provenance/history across MP2 hierarchy restructuring.

**Result: PASS WITH MODEL REFINEMENT**

## SCENARIO-014 — LTV scan cannot initially be matched

The returned form may be scanned and retained, but `Recorded` is prohibited until a reliable originating-record match exists. A later successful match may support the single Recorded transition; no fabricated match or duplicate recording is allowed.

**Result: PASS**

## SCENARIO-015 — CAPA effectiveness verification fails

Completion of all required corrective tasks is insufficient. Failed effectiveness evidence keeps the Nonconformity/CAPA active. The same parent lifecycle can coordinate further corrective work; task completion does not force closure or create independent child Corrective Action aggregates.

**Result: PASS**

## SCENARIO-016 — Kiln schedule revised after run starts

Kiln Schedule and Kiln Run are separate aggregates. Planning may be revised while the already-started run remains the retained execution fact tied to what actually occurred. Historical schedule revisions are preserved rather than rewriting execution history.

**Result: PASS**

## SCENARIO-017 — Ambiguous overlapping quality targets

If two target parameters appear equally applicable, Quality Verification must not arbitrarily choose one. Target resolution fails until applicability is unambiguous; retained measurements/check evidence remain without a false evaluation or automatic Nonconformity.

**Result: PASS**

## SCENARIO-018 — Operations Record corrected after analytics use

Operations Record remains authoritative for the record and its correction history. Analytics may refresh its projection, but it neither overwrites the historical source record nor becomes lifecycle authority. Prior reported state remains retained.

**Result: PASS**

## SCENARIO-019 — Project cancellation/abandonment

A real Project can plausibly end without successful completion. The current Project aggregate models creation and completion and already says historical identity should be retained once work begins, but no confirmed cancellation/abandonment command, authority, status transition, or evidence rule exists.

This is a genuine Project lifecycle gap and must not be filled by destructive deletion or inferred from CAPA state.

**Result: DOMAIN QUESTION REQUIRED**  
**Created:** `ISSUE-0014`

## SCENARIO-020 — Late ProTrack measurements

Late authoritative ProTrack records can change dimension-level statistical analysis and dashboard projections. This does not create a domain contradiction because dashboards are projections and ProTrack remains source authority. Late data still cannot be assigned to an invented Kiln Run/Charge identity.

**Result: PASS**

---

# Cross-batch findings

After twenty total scenarios, the DDD has produced no confirmed boundary failure requiring a Bounded Context merge/split or Aggregate redesign. Scenario testing continues to expose two different classes of useful findings:

1. **Representation refinements** where an already-supported rule was not explicit enough in canonical artifacts.
2. **Real lifecycle questions** that structural review did not reveal because they appear only under temporal reversal/non-happy-path conditions.

Batch 2 created two nonblocking open issues:

- `ISSUE-0013` — selected coverage after prerequisite qualification is withdrawn before execution.
- `ISSUE-0014` — Project cancellation/abandonment lifecycle after work begins.

The second batch therefore does not invalidate the strategic model. It strengthens the evidence that scenario testing is the correct next validation mechanism because it can uncover missing temporal business rules without manufacturing new contexts.
