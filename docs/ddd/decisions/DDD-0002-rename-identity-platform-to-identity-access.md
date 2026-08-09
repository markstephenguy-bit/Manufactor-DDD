# DDD-0002 — Rename "Identity & Platform" to "Identity & Access"

## Status
Accepted

## Context
Phase 4 named this subdomain "Identity & Platform" while it contains exactly
one capability, `cap.identity-access`. External audit (ChatGPT) noted the name
implies a broader technical substrate that isn't actually represented.

## Evidence
`subdomain.identity-platform`'s own `capabilities` list contained only
`cap.identity-access` — no other platform-ambient concern (Realtime,
Observability, Security, DevOps, Governance — all listed as platform-ambient
in `ev.arch.workbook "Ports"`) has been raised to capability status.

## Decision
Rename to `subdomain.identity-access` / "Identity & Access." The name now
matches actual scope.

## Alternatives Considered
Keep "Identity & Platform" and expand scope to cover the other
platform-ambient Ports — rejected: those aren't represented as capabilities
yet, and giving them capability treatment now would mean gathering new
evidence, which the current instruction explicitly says not to do unless a
boundary genuinely cannot be proposed without it.

## Consequences
Subdomain name is accurately scoped. If a platform-ambient concern beyond
identity ever gets real capability-level evidence, it needs its own
subdomain rather than being folded into this one by name alone.

## Affected Model Objects
`docs/ddd/subdomains/index.yaml` — `subdomain.identity-platform` renamed to
`subdomain.identity-access`.

## Unresolved Questions
None.
