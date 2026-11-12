# Architectural Decisions per *Software Architecture in Practice*, 4th ed.

Bass, Clements & Kazman, 4th edition (2021) is the **sole authority** for architectural decision-making in this framework. This file is an operating guide and index into the book — when depth is needed, consult the cited chapters; do not improvise beyond the catalogs.

## 1. The governing principle
Architecture is driven by **quality attributes (QAs)**, not functionality. Functionality determines *what* the system does; quality attribute requirements determine its *structures*. Therefore: **every structural decision in a Canvas (A and S sections) must trace to a QA scenario and name the tactic(s) applied.**

Decision chain: `business goal → QA scenario → utility tree priority → tactic selection → pattern/structure → ADR → Canvas (A/S) → Safeguard (response measure)`

## 2. Quality attribute scenarios (the requirements format)
All non-functional requirements are written as six-part scenarios:

1. **Source of stimulus** (user, system, attacker, ops…)
2. **Stimulus** (the event/condition)
3. **Artifact** (what part of the system is stimulated)
4. **Environment** (normal operation, peak load, degraded, startup…)
5. **Response** (what the system does)
6. **Response measure** (the testable number: latency, MTTR, % availability, time-to-change…)

A scenario without a measurable response measure is not done. Vague words ("fast", "reliable", "secure") are review failures. Template: `templates/qa-scenario.md`. Scenarios live in `docs/quality/scenarios/` and are indexed in the utility tree.

## 3. The utility tree (prioritization)
`docs/quality/utility-tree.md` holds the tree: root = "utility", branches = quality attributes, sub-branches = refinements, leaves = concrete scenarios. Each leaf is rated **(business importance, technical risk)** on H/M/L. (H,H) leaves get deep analysis and explicit tactics; (L,L) leaves get defaults. The tree decides where engineering attention goes — re-rank it when business goals shift.

## 4. Tactics index (4th edition)
Tactics are the atomic design moves. Below are the categories per attribute with the book chapter to consult for the full catalogs, descriptions, and trade-offs. Cite tactics by name in Canvases and ADRs.

| Quality attribute | Ch. | Tactic categories |
|---|---|---|
| Availability | 4 | Detect faults · Recover from faults: preparation & repair · Recover: reintroduction · Prevent faults |
| Deployability | 5 | Manage the deployment pipeline · Manage the deployed system |
| Energy efficiency | 6 | Monitor resources · Allocate resources · Reduce resource demand |
| Integrability | 7 | Limit dependencies · Adapt · Coordinate |
| Modifiability | 8 | Increase cohesion · Reduce coupling · Defer binding |
| Performance | 9 | Control resource demand · Manage resources |
| Safety | 10 | Unsafe-state avoidance · Unsafe-state detection · Containment · Recovery |
| Security | 11 | Detect attacks · Resist attacks · React to attacks · Recover from attacks |
| Testability | 12 | Control & observe system state · Limit complexity |
| Usability | 13 | Support user initiative · Support system initiative |

Other relevant 4th-ed chapters: quality attribute fundamentals & scenarios (Ch. 3), interfaces (Ch. 15), virtualization (Ch. 16), the cloud & distributed computing (Ch. 17), mobility (Ch. 18), architecturally significant requirements & utility tree (Ch. 19–20), design & ADD (Ch. 20), evaluation/ATAM (Ch. 21), documentation (Ch. 22).

**Tactic → DDD mapping notes:**
- *Modifiability: reduce coupling / encapsulate / use an intermediary* ⇒ bounded contexts, ACLs, published events, ports & adapters.
- *Modifiability: increase cohesion* ⇒ aggregate sizing, subdomain splitting.
- *Integrability: limit dependencies* ⇒ published language between contexts; reference-by-ID.
- *Performance: introduce concurrency / maintain multiple copies* ⇒ CQRS read models, caching at adapters (never inside the domain layer).
- *Availability tactics* ⇒ live in infrastructure/adapters (retry, circuit breaker as intermediary, health checks); domain stays pure.
- *Testability: limit nondeterminism / abstract data sources* ⇒ the dependency rule + repository interfaces already implement these; preserve them.

## 5. Sensitivity points, tradeoff points, risks
During analysis and mini-ATAM reviews, label findings:
- **Sensitivity point:** a decision that significantly affects *one* QA response (e.g., DB index choice ↔ query latency).
- **Tradeoff point:** a decision affecting *multiple* QAs in different directions (e.g., encryption: security ↑, performance ↓; event-driven integration: modifiability ↑, observability/availability complexity ↑). Every tradeoff point needs an ADR.
- **Risk:** a decision whose consequences are not fully understood. **Non-risk:** a safe, justified decision — record it so it isn't re-litigated.

## 6. ADR discipline
File: `docs/adr/ADR-<nnn>-<slug>.md` (template: `templates/adr.md`). An ADR is **mandatory** when a decision: creates/changes a dependency direction, chooses a communication style between contexts or services, adopts a tactic with a tradeoff point, introduces a technology, or supersedes a prior ADR.

ADR quality bar: it names the QA scenario(s) driving it, the tactics chosen, ≥1 rejected alternative with the reason, and what was consciously traded away. The trade-off analysis lives in the ADR; the Canvas A-section cites the ADR and records only the outcome.

## 7. Mini-ATAM (lightweight evaluation)
Full ATAM (Ch. 21) is heavyweight; this framework uses a compressed form via `templates/review-checklist.md`: present the architecture → walk the top (H,H) scenarios against it → record sensitivity points, tradeoff points, risks, non-risks → convert unresolved risks into ADR proposals or stories. Run it before major releases or after significant structural change.

## 8. Anti-patterns to reject in review
- A structural choice justified only aesthetically ("cleaner", "more modern") — demand the scenario.
- A scenario invented post-hoc to bless a decision already made — the utility tree must precede the design.
- Tactics named without response measures in Safeguards — unverifiable.
- "Resume-driven" tactic stacking on (L,L) scenarios — defaults suffice there.
