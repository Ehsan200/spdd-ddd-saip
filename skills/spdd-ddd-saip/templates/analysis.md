# [Analysis] <Story-id> — <slug>
Date: <yyyy-mm-dd> · Story: <link> · Status: draft | reviewed

## 1. Restated understanding
**Core logic:** <what actually has to happen, in ubiquitous language>
**Scope boundaries:** <what we are NOT doing>
**Definition of done:** <ACs restated with the implementation-level detail needed to verify, incl. non-functional contract>

## 2. Codebase scan
Domain keywords: <…> → relevant files/modules scanned: <…>
Existing concepts touched: <…> · New concepts proposed: <…> (await user approval before glossary entry)

## 3. DDD pass
- Bounded context(s): <name(s)> · Subdomain classification: core | supporting | generic → treatment: full tactical DDD | transaction script | wrapper
- Aggregates affected: <root — invariants at risk (INV-n)>
- New aggregates / VOs / events proposed: <…>
- Cross-context interactions: <relationship type from context map>
- Glossary conflicts found: <term used inconsistently? | none>

## 4. SAiP pass
- Utility-tree scenarios touched: <QA-XXX-nnn (importance, risk)>
- New scenarios needed: <drafted in six-part form below | none>
- Candidate tactics (cite by name + chapter): <…>
- Sensitivity points: <decision ↔ single QA>
- Tradeoff points: <decision ↔ multiple QAs → needs ADR>
- ADRs to write/update: <ADR-nnn — title | none>

## 5. Risks, edge cases, open questions
| # | Item | Type (risk/edge/question) | Resolution / owner |
|---|---|---|---|

## 6. Strategic direction (the "what & why", no implementation detail)
<3–8 sentences: the chosen direction, with each structural element traced to a scenario+tactic.>

## Review gate
☐ User reviewed §3 (model fits the domain) ☐ User reviewed §4 (scenarios & tactics agreed) ☐ New glossary terms approved ☐ ADR drafts approved → proceed to Canvas
