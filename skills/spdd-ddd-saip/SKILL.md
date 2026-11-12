---
name: spdd-ddd-saip
description: A software development framework combining Structured-Prompt-Driven Development (SPDD, Thoughtworks/martinfowler.com), Domain-Driven Design (DDD), and Software Architecture in Practice 4th edition (Bass, Clements, Kazman) for architectural decisions. Use this skill whenever the user wants to build, enhance, refactor, or analyze software in a governed way — including any mention of SPDD, REASONS Canvas, structured prompts, DDD, bounded contexts, aggregates, quality attributes, architecture decisions, ADRs, utility trees, or tactics. Also trigger when the user asks to start a new feature, write a user story, analyze requirements, generate code from a spec, or review architecture, even if they don't name the framework explicitly.
---

# SPDD + DDD + SAiP Development Framework

This skill governs how Claude develops software with the user. It merges three sources into one workflow:

| Source | Owns | Question it answers |
|---|---|---|
| **SPDD** (Structured-Prompt-Driven Development) | The process & artifacts | *How do we turn intent into governed, reviewable code?* |
| **DDD** (Domain-Driven Design) | The domain model | *What are the concepts, boundaries, and invariants?* |
| **SAiP 4th ed.** (Software Architecture in Practice) | Architectural decisions | *Which structures and tactics satisfy our quality attributes?* |

**Core rule of the framework:** the structured prompt (REASONS Canvas) is a first-class, version-controlled artifact. When reality diverges, fix the prompt first, then the code. Code refactorings sync back to the prompt.

**Architecture decision rule:** no structural decision may be made without citing (a) the quality attribute scenario it serves and (b) the SAiP tactic it applies. "It's cleaner" is not a justification; "modifiability scenario M2, tactic: use an intermediary" is.

**Domain rule:** all naming in prompts, code, and tests must use the ubiquitous language in `docs/glossary.md`. New domain concepts must be proposed to the user before being added — never silently invented.

## The Workflow

Follow these steps in order for any feature or enhancement. For trivial changes (typo fixes, config bumps) skip to step 5 with a one-line note. For hotfixes, fix first, then close the loop afterward (step 7).

### Step 0 — Project initialization (once per repo)
If the repo lacks framework docs, create them from `project-docs/`:
- `docs/glossary.md` — ubiquitous language
- `docs/context-map.md` — bounded contexts and their relationships
- `docs/quality/utility-tree.md` — prioritized quality attribute scenarios
- `docs/adr/` — architecture decision records
- `spdd/` — analysis docs and REASONS Canvases live here, committed with code

Interview the user to fill the glossary, context map, and the top 3–7 quality attribute scenarios. Read `references/saip-decisions.md` for how to write scenarios and build the utility tree.

### Step 1 — Story
Shape the raw requirement into one or more INVEST user stories (1–5 days each). Each story has: Background, Business Value, Scope In, Scope Out, and 3–5 acceptance criteria in Given/When/Then form with concrete numbers. Use `templates/story.md`. Review with the user before proceeding.

### Step 2 — Clarify
With the user, restate the story along three dimensions: core logic, scope boundaries, definition of done. Surface ambiguities now — this is the cheapest place to fix them.

### Step 3 — Analysis
Produce an analysis document using `templates/analysis.md`. This is the strategic step where DDD and SAiP do their heaviest work:
1. Extract domain keywords; scan only the relevant code.
2. **DDD pass:** identify affected bounded context(s), existing vs. new domain concepts, aggregates touched, invariants at risk, events to raise. Check every term against the glossary. Read `references/ddd-rules.md` for the rules.
3. **SAiP pass:** identify which utility-tree scenarios this change touches. If a needed scenario doesn't exist, draft it (six-part form) and confirm with the user. Select candidate tactics from the 4th-edition catalogs. Identify sensitivity points and tradeoff points. Read `references/saip-decisions.md`.
4. List risks, edge cases, and open questions.
5. If a significant decision was made (new dependency direction, new communication style between contexts, new tactic adopted), record it as an ADR using `templates/adr.md`. The ADR holds the trade-off analysis; the Canvas will record only the outcome.

Stop and review the analysis with the user before generating the Canvas.

### Step 4 — REASONS Canvas (the structured prompt)
Generate the Canvas using `templates/reasons-canvas.md`, which extends the standard seven parts with DDD and SAiP hooks:

- **R — Requirements:** story reference, ACs, definition of done.
- **E — Entities:** the DDD tactical model — aggregates (roots, boundaries, invariants), value objects, domain events, repositories. Glossary terms only.
- **A — Approach:** the strategy, with each structural choice justified by `QA scenario → tactic → decision` (cite ADR numbers where they exist).
- **S — Structure:** where the change lands — bounded context, layer (domain / application / infrastructure / ports), components and dependency directions. Must respect the dependency rule (see Norms).
- **O — Operations:** concrete, ordered, testable implementation steps down to signatures.
- **N — Norms:** project norms + framework norms (see `references/ddd-rules.md` § Norms): domain layer depends on nothing; aggregates referenced by ID across boundaries; one aggregate per transaction; events in past tense; repositories interface in domain, implementation in infrastructure.
- **S — Safeguards:** non-negotiables: aggregate invariants, QA response measures from the touched scenarios (e.g. "p95 < 300 ms"), security rules, backwards-compatibility constraints.

The user reviews the Canvas. Never hand-edit a Canvas file silently — apply changes through dialogue and update the file, so the change is deliberate and visible.

### Step 5 — Generate code
Generate code task by task, strictly following Operations, Norms, and Safeguards. No improvisation, no scope beyond the Canvas. Review priorities: (1) architecture conformance, (2) business logic vs. intent, (3) scope confinement.

### Step 6 — Verify
- Functional verification first (API tests / scripts covering normal, boundary, error cases — derived from the ACs).
- Then code review for what only humans judge: logic, trade-offs, smells.
- Then unit tests: domain logic gets pure unit tests with no mocks; adapters get integration tests. Test names use the ubiquitous language.

### Step 7 — Close the loop
Two kinds of post-review change, two directions of sync:
- **Logic correction (behavior changes):** update the Canvas first, then regenerate the affected code. If the correction reflects an architectural re-decision, update or supersede the ADR too.
- **Refactoring (no behavior change):** change the code in small steps, then sync the Canvas to match. The Canvas must remain an accurate record of the current system, not a historical snapshot.
- **Hotfixes:** stabilize production first; afterwards run a compressed loop — post-mortem, update/create the Canvas and any QA scenarios the incident exposed (an outage usually means an availability scenario was missing or its tactic failed).

### Mini-ATAM review (recurring ritual)
Periodically, or before a major release, run `templates/review-checklist.md`: walk the top utility-tree scenarios against the current design, flag sensitivity points, tradeoff points, risks, and non-risks. Propose ADRs for anything unresolved.

## When NOT to apply full rigor
Per SPDD's fitness assessment and DDD's own scope guidance:
- Exploratory spikes, one-off scripts, pure visual work → skip the Canvas; note it explicitly.
- Supporting/generic subdomains → transaction script or simple CRUD is fine; full tactical DDD (aggregates, events) is reserved for the core domain. State which treatment applies in the analysis.
- Context black holes (rules unclear, boundaries unknowable) → do discovery first (EventStorming-style exploration with the user); a Canvas built on fog is worse than none.

## Reference files — read when needed
- `references/ddd-rules.md` — strategic & tactical DDD rules, layering, norms. Read during Step 3 (DDD pass) and when writing E/N sections of the Canvas.
- `references/saip-decisions.md` — QA scenario format, utility tree, 4th-edition tactics index, sensitivity/tradeoff points, ADR discipline. Read during Step 3 (SAiP pass) and the mini-ATAM review.
- `references/reasons-canvas-guide.md` — section-by-section guidance and quality bar for the extended Canvas. Read during Step 4.

## Templates
`templates/story.md`, `templates/analysis.md`, `templates/reasons-canvas.md`, `templates/qa-scenario.md`, `templates/adr.md`, `templates/utility-tree.md`, `templates/review-checklist.md`

## Project seed docs
`project-docs/glossary.md`, `project-docs/context-map.md` — copy into new repos at Step 0.

## File naming & layout in the target repo
```
repo/
├── docs/
│   ├── glossary.md
│   ├── context-map.md
│   ├── quality/
│   │   ├── utility-tree.md
│   │   └── scenarios/QA-<attr>-<nnn>.md
│   └── adr/ADR-<nnn>-<slug>.md
├── spdd/
│   ├── analysis/<story-id>-<date>-[Analysis]-<slug>.md
│   └── prompt/<story-id>-<date>-[Feat|Fix|Test]-<slug>.md
└── requirements/<story files>
```
Canvases and analyses are committed alongside the code they produced, in the same PR.
