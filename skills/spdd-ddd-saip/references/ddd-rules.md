# DDD Rules (Strategic + Tactical)

These rules govern the **E (Entities)**, **S (Structure)**, and **N (Norms)** sections of every REASONS Canvas, and the DDD pass of every analysis.

## 1. Strategic DDD

### Bounded contexts
- Every piece of work belongs to exactly one bounded context (named in `docs/context-map.md`). If a story spans contexts, split it or define the integration explicitly.
- Context relationships must be one of: shared kernel, customer–supplier, conformist, anti-corruption layer (ACL), open host service / published language, separate ways. Name the relationship in the context map.
- A model concept never leaks across a context boundary with its meaning intact by accident. Cross-context communication goes through published events, an open host API, or an ACL — chosen via a SAiP integrability/modifiability scenario (see saip-decisions.md).

### Ubiquitous language
- `docs/glossary.md` is the single source of naming truth. Code identifiers, Canvas text, test names, and conversation all use glossary terms.
- One term = one meaning *per context*. The same word may mean different things in different contexts; the glossary records this per-context.
- **New-concept protocol:** when analysis surfaces a concept not in the glossary, propose it to the user (term, definition, context, related terms). Only after approval does it enter the glossary and the Canvas.

### Subdomain classification
Classify the affected area in every analysis:
- **Core domain** → full tactical DDD (aggregates, value objects, domain events, rich behavior).
- **Supporting subdomain** → simpler models allowed; transaction script acceptable.
- **Generic subdomain** → prefer buying/library over building; thin wrappers.
Over-engineering a supporting subdomain is a defect, not diligence.

## 2. Tactical DDD

### Value objects
- Immutable. Compared by value. Validated at construction — an invalid value object cannot exist.
- Prefer value objects over primitives for any concept with rules (Money, Quota, ModelId, Email…). "Primitive obsession" is a review flag.

### Entities & aggregates
- An entity has identity and a lifecycle. An aggregate is a cluster of entities/VOs with one **aggregate root** that is the only entry point.
- **Invariant rule:** every invariant that must be transactionally consistent lives inside one aggregate. List the invariants explicitly in the Canvas (E section) and enforce them in Safeguards.
- **One aggregate per transaction.** Cross-aggregate consistency is eventual, via domain events.
- **Reference by ID:** aggregates hold IDs of other aggregates, never object references.
- Keep aggregates small. If an aggregate accumulates unrelated invariants, split it (and record the decision as an ADR).

### Domain events
- Named in past tense, in ubiquitous language (`BillCalculated`, `QuotaExceeded`).
- Raised inside the aggregate when an invariant-relevant state change happens; dispatched after the transaction commits.
- Events are the default integration mechanism between aggregates and between contexts.

### Repositories
- One repository per aggregate root. Interface declared in the domain layer; implementation in infrastructure.
- Repositories return whole aggregates (or summaries for read models) — never partial aggregate internals.

### Domain services vs. application services
- **Domain service:** stateless domain logic that doesn't naturally belong to one aggregate. Lives in the domain layer, speaks ubiquitous language.
- **Application service / command handler:** orchestrates a use case — load aggregate(s), invoke behavior, persist, dispatch events. Contains **no business rules**. If you see an `if` encoding a business rule in a handler, it's in the wrong layer.

### Factories
- Complex aggregate creation goes through a factory (method or class) that guarantees invariants from birth.

## 3. Architecture style: Hexagonal / Ports & Adapters

```
internal/<context>/
├── domain/          # aggregates, VOs, events, domain services, repo interfaces — depends on NOTHING
├── application/     # command/query handlers, app services — depends on domain
├── ports/           # inbound/outbound interface definitions (if not already in domain)
└── infrastructure/  # HTTP, DB, messaging adapters — depends on application + domain
```

**The dependency rule (a Safeguard in every Canvas):** dependencies point inward only. Domain imports nothing from application or infrastructure. Application imports nothing from infrastructure. Violations fail review regardless of test results.

CQRS is permitted per-context when a SAiP performance or modifiability scenario justifies it (record as ADR). Event sourcing requires an explicit ADR with the operational costs analyzed — never adopt by default.

## 4. Norms (paste-ready for Canvas N section)

1. Dependency rule: domain → nothing; application → domain; infrastructure → application + domain.
2. All names from `docs/glossary.md`; new concepts via the new-concept protocol.
3. Value objects immutable & self-validating; no primitive obsession for ruled concepts.
4. One aggregate per transaction; cross-aggregate via domain events; reference by ID.
5. Domain events: past tense, raised in aggregate, dispatched post-commit.
6. Repositories: per aggregate root; interface in domain, implementation in infrastructure.
7. No business rules in handlers/controllers/adapters.
8. Domain unit tests: pure, no mocks, no I/O. Adapter tests: integration. Test names in ubiquitous language.
9. Errors crossing the port boundary are translated; domain exceptions never leak raw to clients.

## 5. Review flags (used in Step 5/6 review)

- Anemic aggregate (getters/setters, logic elsewhere) → move behavior into the aggregate.
- Handler with business `if`s → push down to domain.
- Aggregate loading another aggregate by reference → switch to ID + event.
- A glossary term used with two meanings → glossary fix + rename.
- New tactical machinery in a supporting subdomain → simplify; cite subdomain classification.
