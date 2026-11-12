# [Feat|Fix] <Story-id> — <slug>
Canvas version: <n> · Analysis: <link> · Date: <yyyy-mm-dd>
Sync status: ☐ in sync with code @ <commit>

## R — Requirements
- Story: <link>
- Problem: <2–4 sentences>
- Scope In: <…> · Scope Out: <…>
- Definition of Done: all ACs below + response measures in Safeguards
- ACs: <copied or linked from story>

## E — Entities  *(DDD tactical model — glossary terms only)*
**Bounded context:** <name>

### Aggregates
| Root | Members | Invariants | Notes |
|---|---|---|---|
| <Bill> | <LineItem (entity), Money (VO)> | INV-1: <…>; INV-2: <…> | <lifecycle> |

### Value objects
| Name | Validation rules | Used by |
|---|---|---|

### Domain events
| Event (past tense) | Payload | Raised when | Consumed by |
|---|---|---|---|

### Repositories
| Interface (domain layer) | Key methods | Implementation (infrastructure) |
|---|---|---|

### Glossary delta
<approved new/changed terms | none>

## A — Approach  *(every decision: scenario → tactic → outcome)*
D1: <decision>
  - QA scenario: <QA-XXX-nnn — response measure>
  - Tactic: <category — tactic name> (SAiP 4e Ch. <n>)
  - ADR: <ADR-nnn | n/a (sensitivity point only)>
  - Rejected alternative: <what — why>

D2: …

## S — Structure
- Layer placement:
  - domain/: <new/changed files>
  - application/: <…>
  - infrastructure/: <…>
- Dependency directions (all arrows inward): <infra → app → domain>
- Cross-context touchpoints: <context — relationship — mechanism>

## O — Operations  *(ordered, testable, signature-precise)*
1. [domain] <step — types/signatures> · serves: <AC-n / INV-n>
2. [application] <…>
3. [infrastructure] <…>
4. [migration] <…>

## N — Norms
<paste framework norms (ddd-rules.md §4) + project norms; reference, don't rewrite, if a shared norms file exists>

## S — Safeguards  *(non-negotiable, each verifiable)*
- INV-1: <invariant — behavior on violation>
- QA: <e.g. POST /api/usage p95 < 300 ms @ 100 rps (QA-PERF-001)>
- Security: <…>
- Compatibility: <…>

---
### Change log
| Date | Direction (req→prompt→code / code→prompt) | Sections | Summary |
|---|---|---|---|
