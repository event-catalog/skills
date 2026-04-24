# Code to Catalog — Skill Design

**Date:** 2026-04-24
**Skill name:** `code-to-catalog`
**Location:** `skills/code-to-catalog/`

## Purpose

Turn an unfamiliar or undocumented codebase into EventCatalog documentation through a guided, evidence-based interview. The skill scans the code first (discovery-first), proposes an architectural model, grills the user on the structural decisions (tiered grilling — inspired by `grill-me`), produces a reviewable plan file, then optionally hands off to the existing `catalog-documentation-creator` skill to generate the actual catalog files.

Works for two audiences:

1. **No catalog yet** — user wants to document a codebase from scratch.
2. **Existing catalog** — user wants to reconcile their catalog with the current state of the code (add new resources, flag drift, surface stale entries).

## When to trigger

- "document my codebase in EventCatalog"
- "turn this repo into a catalog"
- "help me model this codebase as a catalog"
- "grill me on my architecture"
- "I don't know where to start documenting this codebase"
- "update my catalog from the code"
- "my catalog is out of date — help me reconcile it with the code"

## Design principles

- **Discovery-first.** The skill reads the codebase and forms a hypothesis before asking the user anything structural. Users with unfamiliar or inherited codebases still get somewhere. The grilling reacts to evidence, not to blank recall.
- **Tiered grilling.** Grill hard on structural decisions (domains, boundaries, service-to-domain mapping, message classification, drift reconciliation). Do not grill on per-resource details (summaries, owners, schemas) — those are low-stakes and `catalog-documentation-creator` handles them.
- **Always cite evidence.** When presenting a finding, include file paths and line numbers so the user can verify. Honest about uncertainty — if the code doesn't tell us whether something is an event or a command, say so.
- **Always provide a recommended answer.** Every question has a suggested answer with rationale (grill-me style). The user confirms, corrects, or overrides.
- **Adapts to the codebase.** Monorepo, single service, multi-language — no forced worldview. Detect structure, adjust the model.
- **Respect the existing catalog.** When catalog and code disagree, surface the conflict. Do not silently overwrite either. Never delete catalog resources — flag them as `investigate`.
- **The plan file is the source of truth.** All agreed decisions land in `.catalog-plan.md`. That artifact is reviewable, re-runnable, and survives context loss.

## Workflow — 6 phases

### Phase 1: Locate & inventory

- Ask for the **code directory** to analyze.
- Ask whether an **EventCatalog project** already exists, or if one needs to be created.
  - **If it exists:** read it. Build an inventory of domains, services, events, commands, queries, channels, owners, versions. Use the EventCatalog MCP if connected (`getResources`, `getResource`, `findResourcesByOwner`), otherwise read the filesystem (`services/`, `events/`, `domains/`, etc.).
  - **If it does not exist:** note that scaffolding happens at handoff time via `catalog-documentation-creator` (which runs `npx @eventcatalog/create-eventcatalog@latest ... --empty`).

The inventory is critical context for Phases 3–5. It changes the skill's job from "document from scratch" to "reconcile code with what's already documented."

### Phase 2: Discovery scan

Analyze the codebase to form a hypothesis. Detect:

- **Project structure** — monorepo vs single service, workspaces (pnpm/yarn/npm/turbo/nx), service directories, build configs, language/framework.
- **Service boundaries** — separate deployables, packages, modules, independently-ownable units of code.
- **Messages** — candidates for events, commands, queries. Naming patterns (`*Created`, `*Placed`, `Process*`, `Get*`), message-bus clients (Kafka, RabbitMQ, NATS, SNS/SQS, EventBridge), schema files (JSON Schema, Avro, Protobuf), DTO/type definitions.
- **Channels** — Kafka topics, RabbitMQ queues/exchanges, HTTP endpoints, SNS topics, SQS queues.
- **Possible domains** — folder grouping, naming clusters, bounded-context hints, module README headers.
- **Containers** — databases, caches, queues referenced in config/env/client instantiation.

Produce a **draft architecture map** (held internally, not yet shown): candidate domains → services → messages → channels → containers.

Detection heuristics per language/framework live in `references/discovery.md` so they can grow without bloating the main skill.

### Phase 3: Reconcile with existing catalog *(skip if none)*

Cross-reference the draft map against the existing catalog inventory. Categorize every finding:

| Status | Meaning | Action |
|---|---|---|
| `unchanged` | In catalog, matches code | Skip — no user grilling needed |
| `update` | In catalog, but code has drifted (new messages, renamed fields, changed schemas) | Flag for user confirmation in Phase 4 |
| `new` | Found in code, not in catalog | Candidate for new resource |
| `investigate` | In catalog, not found in code | Flag as possibly stale/removed — never delete, just surface |

This categorization drives the grilling in Phase 4 — it's what makes the skill valuable for existing-catalog users.

### Phase 4: Tiered grilling

Grill on **structural decisions only**. One question at a time, each with a recommended answer and cited evidence.

Topic order (dependencies between decisions resolved in order):

1. **Domains & boundaries** — which services group into which domains? Are there sub-domains? Where's the boundary between two domains?
2. **Service boundaries** — is this one service or multiple? Does this module deserve its own service?
3. **Message classification** — event vs. command vs. query. This is the most commonly-wrong call; grill it hard.
4. **Drift reconciliation** — for existing catalogs: "catalog says X, code says Y — which is right?"

Per-resource details (summary text, owners, schemas, badge styles) are **not** grilled here. They pass through to `catalog-documentation-creator`.

The full question bank with recommended-answer templates lives in `references/grilling.md`.

### Phase 5: Produce the plan file

Write `.catalog-plan.md` at a location the user chooses (default: repo root). Structure:

```markdown
# Catalog Plan

**Generated:** YYYY-MM-DD
**Codebase:** /path/to/repo
**Existing catalog:** /path/to/catalog (or "none — will be created")

## Summary
<1-2 paragraph narrative of what was found and the agreed model>

## Domains
- **Orders** (status: new)
  - Services: OrderService, ShippingService
  - Rationale: <why, from user confirmation>

## Services
### OrderService (status: new)
- Domain: Orders
- Path in code: /services/orders
- Receives: PlaceOrder (command)
- Sends: OrderPlaced (event), OrderCancelled (event)
- Channels: orders.commands, orders.events
- Containers: orders-db (postgres)

### PaymentService (status: update)
- Domain: Payments
- Drift: code emits new PaymentRefunded event not in catalog

## Messages
- **OrderPlaced** (event, status: new) — emitted by OrderService via orders.events
- **PaymentRefunded** (event, status: new)
- **LegacyPaymentConfirmed** (event, status: investigate) — in catalog, not in code

## Channels
- orders.events (Kafka topic, status: new)

## Open decisions / rationale
- Classified `CancelOrder` as command (user confirmed — expects a handler)
- Kept Shipping as sub-area of Orders rather than splitting (user preference)

## Next step
Run `catalog-documentation-creator` with this plan to generate resources marked `new` or `update`.
```

Status values: `new`, `update`, `unchanged`, `investigate`.

Show the plan, get explicit user approval before Phase 6.

### Phase 6: Handoff

Ask: **"Generate the catalog now, or stop here with just the plan?"**

- **Now** — invoke `catalog-documentation-creator` with the plan file as input. For existing catalogs, only resources flagged `new` or `update` get written. `unchanged` skipped. `investigate` never auto-handled — surfaced to the user with a note.
- **Later** — tell the user the plan is saved and how to resume (point `catalog-documentation-creator` at the plan file path).

## Skill files

```
skills/code-to-catalog/
├── SKILL.md          Main skill: 6-phase workflow, grilling style, handoff
└── references/
    ├── discovery.md  Detection heuristics per language/framework
    └── grilling.md   Tiered-grilling question bank with recommended-answer templates
```

No scripts, no assets. Everything is conversational + file-writing.

`SKILL.md` targets <500 lines. Discovery heuristics and the grilling question bank live in references so they can grow without bloating the main skill. `SKILL.md` points into each reference at the relevant phase.

## Conversational style

- **One question at a time.** Never batch.
- **Recommended answer + evidence.** Every question includes what the skill thinks is true and where it saw it (`src/orders/events.ts:42`).
- **Cite the code.** File paths and line numbers. The user should be able to verify without trusting us.
- **Honest about uncertainty.** If discovery can't distinguish event from command, say so — don't guess.
- **Surface conflicts.** When catalog and code disagree, the user decides — do not pick silently.
- **No interview fatigue.** Tiered grilling means short-ish sessions. If grilling is running long, summarize progress and offer to pause at the plan file.

## Out of scope

- Writing catalog files directly (`catalog-documentation-creator` does that).
- Scaffolding new catalogs (`catalog-documentation-creator` does that).
- Grilling on per-resource fields (summary text, owner, badges, schema format).
- Deleting catalog resources (even `investigate` items — only surface, never delete).
- Writing or modifying tests.
- Running the user's codebase or EventCatalog dev server.

## Open questions

None — design confirmed with user through brainstorming.
