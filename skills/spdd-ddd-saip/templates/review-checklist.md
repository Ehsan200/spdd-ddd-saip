# Review Checklists

## A. Per-Canvas review (Step 4, before code generation)
☐ R: DoD verifiable; Scope Out explicit
☐ E: all names in glossary; invariants numbered with a home aggregate; cross-aggregate by ID only
☐ A: every decision cites scenario + tactic; tradeoff points have ADRs; no aesthetic-only justifications
☐ S: dependency arrows all inward; context-map relationship named for cross-context touchpoints
☐ O: steps testable, ordered, signature-precise; union covers all ACs; nothing exceeds Scope In
☐ N: framework + project norms present
☐ Safeguards: every item verifiable; response measures carried in from touched QA scenarios

## B. Per-code review (Step 5/6, after generation)
☐ Architecture conformance: files in declared layers; dependency rule holds
☐ Business logic matches Canvas intent (E invariants enforced where declared)
☐ Scope confinement: no changes outside Canvas boundary
☐ DDD flags (ddd-rules.md §5): no anemic aggregates, no business `if`s in handlers, no primitive obsession
☐ Tests: domain = pure unit, adapters = integration; names in ubiquitous language
☐ Canvas sync status updated; Canvas + code in same PR

## C. Mini-ATAM (recurring / pre-release)
1. ☐ Present current architecture (context map + per-context structure), 10 min max.
2. ☐ Walk each top utility-tree scenario: trace stimulus → artifact → response; does the design meet the response measure? Evidence?
3. ☐ Record findings:
   - Sensitivity points: <decision ↔ single QA>
   - Tradeoff points: <decision ↔ multiple QAs> → ☐ each has an ADR
   - Risks: <…> → ☐ each converted to ADR proposal or story
   - Non-risks: <…> (record to avoid re-litigation)
4. ☐ Re-rank the utility tree if business priorities moved.
5. ☐ Check drift: do committed Canvases still describe the current code? Run sync where stale.
