# Semantic Promotion Guardrail

**Status:** Active modeling guardrail derived from a corrected Operations Record classification
**Date:** 2026-08-24
**Applies to:** DDD-to-implementation reconciliation and future component discovery

## Rule

A separate form, screen, report layout, timing point, workflow step, field set, endpoint, table, file, or UI page is **not sufficient evidence** for a separate domain type, Aggregate, Entity, Bounded Context, or implementation component with independent semantic ownership.

Promotion to a distinct domain concept requires evidence of at least one material semantic difference such as:

- distinct business meaning or identity;
- distinct lifecycle;
- distinct invariants or consistency rules;
- distinct authority/decision rights;
- distinct ownership of state;
- materially different business behavior that cannot be represented as variation/configuration beneath the existing concept.

Structural difference alone is implementation or presentation evidence until business semantics prove otherwise.

## Corrected forcing example

The Diboll dry-end **First Hour Production Summary** and **End of Shift Report** are Operations Forms within `bc.operations-record`.

They:

- occur at different timing points;
- have overlapping but non-identical fields;
- may have different downstream uses;
- may eventually use different source-population rules.

Those facts do **not** establish separate domain record types by themselves.

The correct starting model is:

```text
Operations Record
  -> Operations Form
       -> First Hour Production Summary form
       -> End of Shift Report form
```

Any future promotion below `Operations Form` to distinct domain types requires additional business evidence under this guardrail.

## Implementation consequence

Reuse may legitimately occur at the form-mechanics level:

- field rendering;
- source prepopulation;
- validation plumbing;
- save/edit/correction mechanics;
- provenance capture;
- document/export mechanics;
- publication hooks.

But reusable form mechanics do not become the owner of Operations Record business semantics.

## Review test

Before creating a new domain type or domain-aligned component from an artifact difference, answer:

1. What business meaning changes?
2. What lifecycle changes?
3. What invariant changes?
4. What authority changes?
5. What state ownership changes?

If all five answers are effectively “none; only presentation/timing/fields differ,” keep the variation under the existing semantic concept.

## Architecture fitness implication

Code organization may use folders/classes/configuration per form for maintainability, but namespace/module boundaries must not be interpreted as semantic ownership without the DDD evidence above.
