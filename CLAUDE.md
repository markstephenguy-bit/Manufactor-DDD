# ManuFactor-DDD — Claude Code Instructions

This repository exists to build and maintain the canonical Domain-Driven Design model of ManuFactor, under a normative specification authored by Mark. This is not a general architecture-discovery repo — it has one governing document, and that document is binding.

## Read first, every session

1. **`docs/ddd/README.md`** — one page, orients everything else in `docs/ddd/`.
2. **`docs/ddd/SPECIFICATION.md`** — the governing modeling contract for all work in this repository. Read it in full, not skimmed, before any modeling work. Per its own §113: treat it as the modeling contract, not as a request for general DDD recommendations. Do not reinterpret, redesign, or substitute another framework during ordinary modeling work. Do not introduce ArchiMate, UML, C4, or any other notation as anything other than a later, explicitly non-canonical projection (§3, §4, §100).
3. **`ManuFactor_DDD_Modeling_Specification.docx`** (repo root) — a duplicate of the same specification, not an independent source. `docs/ddd/SPECIFICATION.md` is canonical (plain-text, versionable, consistent with the specification's own requirement that canonical content be machine-readable — §62, §99). If the two ever appear to diverge, flag it to Mark rather than silently picking one.

## Working procedure

Follow the specification's own §104 Working Procedure (31 steps, Evidence Inventory through Validation Report and model summary) in order. Do not skip from evidence-gathering directly to Aggregate design — §47-61 (Phases 1-11) exist specifically to prevent that. The canonical repository layout is defined in §62 (`/docs/ddd/...`); create it incrementally as content exists, not upfront as empty scaffolding (§62's own instruction).

## Source of truth for domain evidence

This repository does not itself contain ManuFactor's business/domain facts. Evidence lives in:
- `E:\projects\ManuFactor-arch` — the architecture/discovery substrate (Components, business rules, mill hierarchy, external systems, `supplementals/addendum.jsonl`).
- `E:\projects\ManuFactor` — the code repository (`DEV-PLAN.md`, M0-M3 implementation).

Treat both as evidence per the specification's §7 (evidence informs but does not automatically override the canonical model) and §47 (Phase 1 — Evidence Inventory). Neither repo's existing structure (Components, project boundaries, table schemas) automatically defines a Bounded Context, Aggregate, or Entity — see §69's explicit non-equivalences.

## Session-start self-check

Before any modeling work:
- Confirm which model maturity state (§90: Discovery → Strategic Candidate → Strategic Stable → Tactical Candidate → Tactical Stable → Validated → Maintained) the model currently sits in.
- Confirm no item in `docs/ddd/issues/unresolved.yaml` (once it exists) is being silently treated as resolved.
- Confirm new work uses the specification's own status vocabulary (§10) and provenance discipline (§8) — never an ad hoc label.
- Confirm §68's boundary is respected: this repository models the domain. It does not choose architecture, frameworks, message brokers, or notations. Those are downstream decisions this model may later inform.
