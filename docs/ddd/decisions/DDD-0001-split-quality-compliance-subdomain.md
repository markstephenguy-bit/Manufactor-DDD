# DDD-0001 — Split "Quality & Compliance" into three separate candidate subdomains

## Status
Accepted (candidate-level correction, not yet strategic-stable)

## Context
Phase 4's first pass grouped Safety Compliance & Procedure Sign-Off, Corrective &
Preventive Action (CAPA), and Environmental Compliance Reporting into one
subdomain, `subdomain.quality-compliance`, on the shared trait that all three
produce compliance evidence. External audit (ChatGPT) challenged whether that
grouping reflects shared domain language/rules, or only shared "prove
compliance" behavior.

## Evidence
- Safety Compliance: LTV Template's Draft → SubmittedForApproval → Approved →
  Superseded lifecycle, gated by Site Safety Manager approval (addendum rev
  1/4/40; Rules sheet, "LTV Template SSM approval circumstance").
- CAPA: addendum session 020 — "CAPA was reframed from an early 'domain hub'
  tag to a capability — it dispatches corrective-action work items to
  whichever system already owns that class of work." Reactive, not a gate.
- Environmental: only one real fact on record (Data Gathering item 21 — are
  forms ever physically printed). No rule structure, no vocabulary overlap
  with the other two found in any evidence read.

## Decision
Split into three separate candidate subdomains: `subdomain.safety-compliance-signoff`,
`subdomain.corrective-preventive-action`, `subdomain.environmental-compliance`.
No shared vocabulary or rule structure links the three beyond the generic
label "compliance." Each has a genuinely distinct process shape: proactive
approval gate (Safety), reactive finding-to-resolution dispatch (CAPA),
periodic regulatory reporting (Environmental).

## Alternatives Considered
- Keep as one broad "Quality & Compliance" subdomain — rejected, this is
  exactly the risk the audit flagged: behavioral similarity mistaken for
  domain-language similarity.
- Merge only two of the three (e.g. Safety + CAPA) — rejected, no pair has
  meaningfully stronger shared evidence than any other pairing does.

## Consequences
Three narrower core subdomains instead of one broad one. Three of the
resulting subdomains are now single-capability — this is an evidence-driven
outcome of un-merging a wrong grouping, not a default one-capability-per-
subdomain pattern (which remains something to avoid absent a specific reason).
Phase 5 (Bounded Context discovery) should treat each of the three
independently rather than assuming one's boundary follows another's.

## Affected Model Objects
`docs/ddd/subdomains/index.yaml` — `subdomain.quality-compliance` removed,
replaced by three records.

## Unresolved Questions
Whether any of the three might later prove to share domain language with a
capability not yet in the landscape (e.g. a future Non-Conformance capability)
remains open — not foreclosed by this split.
