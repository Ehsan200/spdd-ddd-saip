# REASONS Canvas Guide (DDD + SAiP extended)

The Canvas is the structured prompt: a version-controlled spec that the code must correspond to one-to-one. This guide sets the quality bar per section. Template: `templates/reasons-canvas.md`.

**Abstract parts (intent & design):** R, E, A, S — reviewed for *intent alignment*.
**Specific part (execution):** O — reviewed for *fidelity to the abstract parts*.
**Governance parts:** N, S — reviewed for *completeness and enforceability*.

## R — Requirements
- Link the story file. Restate the problem in 2–4 sentences.
- Definition of Done = the acceptance criteria + the response measures of every QA scenario this change touches.
- Scope Out is as important as Scope In; list what we are deliberately NOT doing.

**Bar:** a reviewer who reads only R knows exactly when the work is finished.

## E — Entities (the DDD section)
For each affected bounded context:
- Aggregates: root, members, **invariants (numbered: INV-1, INV-2…)**, lifecycle notes.
- Value objects: name, validation rules, immutability.
- Domain events: name (past tense), payload, raised-when, consumed-by.
- Repository interfaces: per aggregate root, key methods.
- Glossary delta: any term added/changed (must already be user-approved).

**Bar:** every name appears in the glossary; every transactional invariant has a number and a home aggregate; nothing references another aggregate by object.

## A — Approach
The strategy as a sequence of justified decisions:

```
D1: <decision>
    QA scenario: QA-MOD-002 (time-to-add-new-plan < 2 dev-days)
    Tactic: reduce coupling — use an intermediary (Strategy pattern at the plan-calculation port)
    ADR: ADR-007
    Rejected: <alternative> — <why>
```

**Bar:** zero decisions without a scenario+tactic citation. Tradeoff points cite an ADR. If a decision has no scenario, either write the scenario (and get it into the utility tree) or drop the decision.

## S — Structure
- The bounded context and layer placement of every new/changed component.
- A small dependency diagram or list with arrows; arrows must all point inward (dependency rule).
- Cross-context touchpoints: which relationship from the context map is used (event, OHS, ACL…).

**Bar:** an engineer can place every file from this section alone; no arrow violates the dependency rule.

## O — Operations
- Ordered, testable steps, each scoped to one layer, precise to signatures and types.
- Each step names the AC or invariant it serves.
- Steps that change persistence/state include the migration/backfill operation.

**Bar:** a step is small enough to verify independently; the union of steps covers all ACs; no step exceeds the Scope In of R.

## N — Norms
- Paste the framework norms from `references/ddd-rules.md` §4, then project-specific norms (naming, logging/observability conventions, error model, lint rules).
- Norms are cross-cutting and stable across Canvases; don't restate per-feature rules here (those are Safeguards).

## S — Safeguards
Non-negotiable boundaries, each verifiable:
- Aggregate invariants by number (INV-1…), with the failing behavior defined.
- QA response measures from touched scenarios (e.g., "POST /api/usage p95 < 300 ms at 100 rps").
- Security rules (authn/authz requirements, input validation, data exposure limits).
- Compatibility constraints (API versioning, historical-data behavior, migration safety).

**Bar:** every safeguard could be turned into a test or a check; nothing here is aspirational prose.

## Change protocol
- **Requirements change** → update Canvas first (only affected sections), then regenerate affected code. Direction: requirements → prompt → code.
- **Refactoring** → change code in small steps, then sync Canvas. Direction: code → prompt.
- Never let the Canvas become a historical snapshot; it documents the *current* system.
- Both Canvas and code changes ship in the same commit/PR.
