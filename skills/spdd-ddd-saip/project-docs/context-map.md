# Context Map — <system name>

## Bounded contexts
| Context | Subdomain type | Purpose | Owner |
|---|---|---|---|
| <Billing> | core | <…> | <…> |
| <Identity> | generic | <…> | <…> |

## Relationships
Allowed types: shared kernel · customer–supplier · conformist · anti-corruption layer (ACL) · open host service / published language · separate ways.

| Upstream | Downstream | Relationship | Mechanism | Justifying QA scenario / ADR |
|---|---|---|---|---|
| <Billing> | <Reporting> | open host service | published events (<BillCalculated>) | QA-MOD-001 / ADR-003 |

## Diagram
<optional: ASCII or linked diagram of contexts and arrows>

## Rules
- Cross-context calls/events use only the mechanisms listed here; new relationships require an ADR.
- A concept crossing a boundary is translated (ACL) or published deliberately (published language) — never shared implicitly.
