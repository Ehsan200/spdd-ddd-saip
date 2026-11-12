# spdd-ddd-saip

An agent skill for building software with a structured, reviewable process. It combines three sources into one workflow:

| Source | Owns | Question it answers |
|---|---|---|
| **SPDD**: Structured-Prompt-Driven Development (Thoughtworks / martinfowler.com) | The process and its artifacts | *How do we turn intent into governed, reviewable code?* |
| **DDD**: Domain-Driven Design | The domain model | *What are the concepts, boundaries and invariants?* |
| **SAiP**: *Software Architecture in Practice*, 4th ed. (Bass, Clements, Kazman) | Architectural decisions | *Which structures and tactics satisfy our quality attributes?* |

Works with **Claude Code** and any agent that loads `SKILL.md` skills. It is stack-agnostic, because the skill governs process and design rather than one framework.

## The three rules

1. **The prompt is the source of truth.** The structured prompt (a **REASONS Canvas**) is a version-controlled artifact. When behavior has to change, update the Canvas first and the code second. A refactor syncs back into the Canvas.
2. **No architecture decision without a reason you can cite.** Each structural choice names the quality-attribute scenario it serves and the SAiP tactic it applies. *"It's cleaner"* doesn't count. *"Modifiability scenario M2, tactic: use an intermediary"* does.
3. **Ubiquitous language.** Names in prompts, code and tests come from `docs/glossary.md`. New domain concepts are proposed to the user first, never made up silently.

## Workflow

```
0 Init → 1 Story → 2 Clarify → 3 Analysis → 4 REASONS Canvas → 5 Generate → 6 Verify → 7 Close the loop
```

| Step | What happens | Output |
|---|---|---|
| **0 Init** (once per repo) | Interview the user for the glossary, the context map and the top 3–7 quality-attribute scenarios | `docs/glossary.md`, `docs/context-map.md`, `docs/quality/utility-tree.md`, `docs/adr/` |
| **1 Story** | Shape the raw request into INVEST stories (1–5 days each) with Given/When/Then acceptance criteria that use concrete numbers | `requirements/<story>.md` |
| **2 Clarify** | Restate core logic, scope boundaries and definition of done, and surface ambiguities while they are cheap to fix | conversation |
| **3 Analysis** | **DDD pass**: bounded contexts, aggregates, invariants, events. **SAiP pass**: touched scenarios, candidate tactics, sensitivity and tradeoff points. Plus risks and open questions | `spdd/analysis/…`, ADRs for significant decisions |
| **4 Canvas** | Write the structured prompt (below), which the user reviews | `spdd/prompt/…` |
| **5 Generate** | Code task by task, strictly from the Canvas's Operations, Norms and Safeguards, with nothing beyond its scope | code |
| **6 Verify** | Functional checks from the acceptance criteria → human review → unit tests (pure domain tests, integration tests for adapters) | tests |
| **7 Close the loop** | Logic change: update the Canvas and then regenerate. Refactor: change the code and then sync the Canvas. Hotfix: fix first, then run a short post-mortem loop | updated Canvas / ADR |

The agent stops for your review after the story, the analysis and the Canvas.

A recurring **mini-ATAM review** (`templates/review-checklist.md`) walks the top scenarios against the current design and turns unresolved items into ADRs.

### The REASONS Canvas

| | Section | Contains |
|---|---|---|
| **R** | Requirements | story reference, acceptance criteria, definition of done |
| **E** | Entities | DDD tactical model: aggregates, value objects, domain events, repositories |
| **A** | Approach | strategy, each choice justified as `QA scenario → tactic → decision` (citing ADRs) |
| **S** | Structure | bounded context, layer, components and dependency directions |
| **O** | Operations | ordered, testable implementation steps, down to signatures |
| **N** | Norms | project norms + DDD norms (domain depends on nothing, cross-aggregate refs by ID, one aggregate per transaction, …) |
| **S** | Safeguards | invariants, QA response measures (e.g. *p95 < 300 ms*), security and compatibility constraints |

### When not to apply full rigor

- **Spikes, one-off scripts, purely visual work:** skip the Canvas and say so.
- **Supporting or generic subdomains:** a transaction script or plain CRUD is fine. Full tactical DDD is for the core domain.
- **Unclear rules or boundaries:** do discovery first (EventStorming-style). A Canvas written while the domain is still unclear does more harm than having none.

## Install

Not published anywhere yet. Install from a clone of this repo:

```
./install.sh            # copies the skill into ~/.claude/skills and ~/.codex/skills
./install.sh --link     # symlinks instead (edits in this folder apply immediately)
./install.sh --uninstall
```

For one project only, copy `skills/spdd-ddd-saip` into `<project>/.claude/skills/`.

## Use

The skill activates when you start a feature, write a story, analyze requirements, generate code from a spec, or mention SPDD, DDD, ADRs, quality attributes or a REASONS Canvas. You can also name it directly:

```
Use spdd-ddd-saip to add password reset.
Run step 0 of spdd-ddd-saip for this repo.
Run a mini-ATAM review against the utility tree.
```

### What it writes in your repo

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

Commit the Canvas and the analysis in the same PR as the code they produced.

## Contents

```
skills/spdd-ddd-saip/
├── SKILL.md                      the workflow the agent follows
├── references/
│   ├── ddd-rules.md              strategic + tactical DDD rules, layering, norms
│   ├── saip-decisions.md         QA scenario form, utility tree, tactics index, ADR discipline
│   └── reasons-canvas-guide.md   section-by-section guidance for the Canvas
├── templates/
│   ├── story.md  analysis.md  reasons-canvas.md  adr.md
│   └── qa-scenario.md  utility-tree.md  review-checklist.md
└── project-docs/
    ├── glossary.md               seed files copied into a repo at step 0
    └── context-map.md
```

## Credits

- **Structured-Prompt-Driven Development (SPDD)** and the REASONS Canvas: Thoughtworks, published on martinfowler.com.
- **Domain-Driven Design**: Eric Evans, *Domain-Driven Design: Tackling Complexity in the Heart of Software*.
- **Software Architecture in Practice**, 4th edition: Len Bass, Paul Clements, Rick Kazman.

This skill is an independent synthesis and is not affiliated with these authors or publishers.

## Develop

```
claude plugin validate .               # manifest check
```

## License

MIT
