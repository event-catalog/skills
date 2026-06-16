# C4 to EventCatalog Mapping

Use this reference when turning a C4 model into an EventCatalog plan. The mapping is heuristic: preserve architectural intent and ask when a decision changes catalog structure.

## C4 Level Mapping

| C4 concept | Default EventCatalog mapping | Notes |
|---|---|---|
| Person | Flow actor or prose reference | EventCatalog has no primary person resource for architecture actors. Link to users/teams only for ownership. |
| Software System | Domain or service | Use `domain` when it is a bounded context containing multiple services/agents. Use `service` when it is one application/deployable boundary. External software systems can stay as external systems in flows unless the catalog should own documentation for them. |
| Container: application/API/worker | Service | Map deployable runtime units to services. Preserve technology and responsibility in the service body/frontmatter. |
| Container: AI assistant/agent/runtime | Agent | Use `agent` only when it reasons, calls tools, uses a model, has memory, or owns autonomous workflow behavior. A plain service that calls an LLM once can remain a service. |
| Container: database/cache/object store/vector store | Container | Nest under the owning service/agent when ownership is clear. Mark authoritative vs projection/cache when known. |
| Container: queue/topic/event bus/API gateway | Channel | Use channels for transport/routing infrastructure. Record protocols and routing where present. |
| Component | Usually document inside parent service | Promote to service/agent/container/message only if independently deployed, owned, consumed across a boundary, or represented as infrastructure/data/message contract. |
| Dynamic view | Flow | Map ordered interactions to EventCatalog flow steps. |
| Deployment node | Body content or infrastructure notes | EventCatalog resource creation is usually unnecessary unless it represents a channel/container that affects relationships. |

## Relationship Mapping

| C4 relationship signal | EventCatalog representation |
|---|---|
| "publishes", "emits", "raises", "subscribes", past-tense message names | Event plus service/agent `sends` and `receives`; add channel if a topic/queue/bus is named. |
| "sends command", imperative message names, one target handler | Command plus sender/handler relationship. |
| "queries", "gets", "lists", "reads", HTTP GET | Query or readsFrom container, depending on whether it is an API contract or direct data access. |
| HTTP POST/PUT/PATCH/DELETE request | Command when it changes state; otherwise document as service API relationship if no command name exists. |
| Direct database read/write | Service/agent `readsFrom` or `writesTo` a container. |
| Topic, queue, event bus, exchange, stream | Channel. If the source and target both mention it, connect sends/receives through `to` and `from`. |
| Calls third-party SaaS/API | External system in a flow, or service only if the catalog intentionally documents owned wrapper/integration details. |

## Domain Heuristics

Use domains when the C4 model shows bounded contexts, business capabilities, or groups of related services. Signals include:

- System names that represent business capabilities, such as Ordering, Payments, Fulfillment, Support.
- C4 tags or groups for bounded contexts.
- Multiple services/agents that share language, data ownership, and business process.
- Existing catalog domains that already match the model.

Avoid creating a domain when the C4 model only has one service and no meaningful bounded context. If unsure, propose a single domain and ask.

## Message Classification

Classify messages from names and relationship intent:

- Event: past-tense fact, produced after something happened, often multiple consumers.
- Command: imperative request to do work, usually one responsible handler.
- Query: read-only request that expects data back.

If the C4 source only says "uses" or "calls", do not invent an event. Mark the relationship as `needs user decision` and ask whether it is synchronous API, command, query, or event.

## Existing Catalog Reconciliation

Match C4 elements to existing resources in this order:

1. Exact `id`
2. Exact `name`
3. Normalized name match, ignoring spaces, hyphens, and casing
4. Relationship match, such as same producer/consumer/channel
5. User confirmation

Statuses:

- `new`: C4 element has no matching resource.
- `update`: matching resource exists but C4 adds or changes relationships, ownership, technology, or description.
- `unchanged`: matching resource already represents the C4 element accurately.
- `investigate`: catalog resource is not present in the C4 source. Never delete automatically.

## Ambiguity Questions

Use these question shapes when grilling structural gaps:

- "I think `<C4 element>` should become a `<resource type>` because `<evidence>`. That would create/update `<path or resource>`. Is that right?"
- "The relationship `<source> -> <target>` is labeled `<label>`, but that does not say whether it is an event, command, query, or synchronous API call. My recommendation is `<choice>` because `<evidence>`. Should I model it that way?"
- "I found `<catalog resource>` in the existing catalog but not in the C4 model. Should I mark it as `investigate` in the plan, or is it represented by another C4 element?"

Ask only one structural question at a time.

## Plan Template

```markdown
# C4 to EventCatalog Plan

## Source
- C4 source: <path or description>
- Catalog: <path or new catalog>

## Decisions
- <confirmed structural decisions>

## Resource Mapping
| C4 element | C4 type | EventCatalog target | Status | Evidence | Notes |
|---|---|---|---|---|---|

## Relationships
| Source | Relationship | Target | EventCatalog representation | Status |
|---|---|---|---|---|

## Files to Create or Update
- <planned path> - <resource type> - <new/update>

## Open Questions
- <only unresolved blockers>
```
