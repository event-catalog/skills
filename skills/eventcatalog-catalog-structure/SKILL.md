---
name: eventcatalog-catalog-structure
description: Proper directory structure, organization, and naming conventions for EventCatalog projects. Use when setting up new catalogs, reorganizing existing catalogs, or adding new resource types. Covers directory patterns, naming conventions, versioning, and configuration.
license: MIT
metadata:
  author: eventcatalog
  version: "1.0.0"
---

# EventCatalog Catalog Structure

Comprehensive guide for organizing EventCatalog directory structure, naming conventions, and configuration. Contains 15 rules across 5 categories, prioritized by impact.

## When to Apply

Reference these guidelines when:
- Setting up a new EventCatalog project
- Reorganizing existing catalog structure
- Adding new resource types (events, services, domains, etc.)
- Understanding where files should be placed

## Rule Categories by Priority

| Priority | Category | Impact | Prefix |
|----------|----------|--------|--------|
| 1 | Directory Structure | CRITICAL | `dir-` |
| 2 | Domain Organization | HIGH | `domain-` |
| 3 | Naming Conventions | HIGH | `naming-` |
| 4 | Supporting Files | MEDIUM | `files-` |
| 5 | Configuration | MEDIUM | `config-` |

## Quick Reference

### 1. Directory Structure (CRITICAL)

- `dir-root-folders` - Use standard root-level folder names
- `dir-resource-pattern` - Each resource in its own folder with index.mdx
- `dir-versioning` - Use versioned subdirectory for historical versions
- `dir-nested-resources` - Resources can be nested within services/domains

### 2. Domain Organization (HIGH)

- `domain-subdomain-structure` - Use subdomains folder for bounded contexts
- `domain-ubiquitous-language` - Add ubiquitous-language.md for DDD terminology
- `domain-service-grouping` - Group related services within domains

### 3. Naming Conventions (HIGH)

- `naming-resource-ids` - Use PascalCase for message and service IDs
- `naming-channel-ids` - Use parameterized patterns for channels
- `naming-file-names` - Use index.mdx for main files, lowercase for others

### 4. Supporting Files (MEDIUM)

- `files-schemas` - Place schemas alongside resource index files
- `files-specifications` - Store OpenAPI/AsyncAPI in resource folders
- `files-changelog` - Add changelog.mdx for version history

### 5. Configuration (MEDIUM)

- `config-eventcatalog-config` - Configure eventcatalog.config.js properly
- `config-users-teams` - Define users and teams in root folders

---

## Detailed Rules

### dir-root-folders
**Impact:** CRITICAL
**Category:** Directory Structure

Use standard root-level folder names that EventCatalog recognizes. These include `domains`, `services`, `events`, `commands`, `queries`, `channels`, `flows`, `users`, `teams`, `containers`, `entities`, and `docs`.

**Good:**
```
eventcatalog/
├── domains/           # Domain-driven organization
├── services/          # Standalone services (not in domains)
├── events/            # Standalone events
├── commands/          # Standalone commands
├── queries/           # Standalone queries
├── channels/          # Message channels/topics
├── flows/             # Business process flows
├── containers/        # Data stores (databases, caches)
├── entities/          # DDD entities
├── users/             # User definitions
├── teams/             # Team definitions
├── docs/              # Custom documentation pages
└── eventcatalog.config.js
```

**Bad:**
```
eventcatalog/
├── src/               # Not recognized
├── messages/          # Should be events/commands/queries
├── microservices/     # Should be services
├── topics/            # Should be channels
└── eventcatalog.config.js
```

### dir-resource-pattern
**Impact:** CRITICAL
**Category:** Directory Structure

Each resource should be in its own folder containing an `index.mdx` (or `index.md`) file. The folder name should match the resource ID.

**Good:**
```
events/
├── OrderConfirmed/
│   ├── index.mdx         # Main documentation
│   ├── schema.json       # Optional schema file
│   └── example.json      # Optional example payload
├── OrderCancelled/
│   └── index.mdx
└── PaymentReceived/
    ├── index.mdx
    └── schema.avro
```

**Bad:**
```
events/
├── OrderConfirmed.mdx    # Wrong: should be in folder
├── OrderCancelled.md     # Wrong: should be in folder
└── events.json           # Wrong: no combined files
```

### dir-versioning
**Impact:** CRITICAL
**Category:** Directory Structure

Use a `versioned` subdirectory to store historical versions. Each version gets its own folder with the version number as the name.

**Good:**
```
events/
└── OrderConfirmed/
    ├── index.mdx              # Current/latest version (2.0.0)
    ├── schema.json
    └── versioned/
        ├── 1.0.0/
        │   ├── index.mdx      # Historical v1.0.0
        │   └── schema.json
        └── 1.5.0/
            ├── index.mdx      # Historical v1.5.0
            └── schema.json
```

**Bad:**
```
events/
├── OrderConfirmed-v1/
│   └── index.mdx
├── OrderConfirmed-v2/
│   └── index.mdx
└── OrderConfirmed/
    └── index.mdx

# Or mixing versions in same folder
events/
└── OrderConfirmed/
    ├── index-v1.mdx           # Wrong versioning pattern
    ├── index-v2.mdx
    └── index.mdx
```

### dir-nested-resources
**Impact:** CRITICAL
**Category:** Directory Structure

Resources can be nested within services or domains. Events, commands, queries, channels, and containers can all live inside service folders.

**Good:**
```
domains/
└── E-Commerce/
    └── subdomains/
        └── Orders/
            ├── index.mdx                    # Domain documentation
            ├── services/
            │   └── InventoryService/
            │       ├── index.mdx            # Service documentation
            │       ├── events/
            │       │   ├── InventoryAdjusted/
            │       │   │   └── index.mdx
            │       │   └── OutOfStock/
            │       │       └── index.mdx
            │       ├── commands/
            │       │   └── UpdateInventory/
            │       │       └── index.mdx
            │       ├── queries/
            │       │   └── GetInventoryStatus/
            │       │       └── index.mdx
            │       └── containers/
            │           └── inventory-db/
            │               └── index.mdx
            └── entities/
                └── Order/
                    └── index.mdx
```

**Bad:**
```
# Flat structure loses organizational context
events/
├── InventoryAdjusted/
├── OutOfStock/
├── OrderConfirmed/
└── PaymentReceived/

services/
├── InventoryService/
├── OrdersService/
└── PaymentService/

# No clear ownership or domain boundaries
```

### domain-subdomain-structure
**Impact:** HIGH
**Category:** Domain Organization

Use `subdomains` folder within domains to organize bounded contexts. Subdomains can contain their own services, entities, and channels.

**Good:**
```
domains/
└── E-Commerce/
    ├── index.mdx                     # Parent domain
    └── subdomains/
        ├── Orders/
        │   ├── index.mdx             # Subdomain documentation
        │   ├── services/
        │   ├── entities/
        │   └── channels/
        ├── Payment/
        │   ├── index.mdx
        │   └── services/
        └── Shipping/
            ├── index.mdx
            └── services/
```

**Bad:**
```
domains/
├── E-Commerce/
│   └── index.mdx
├── Orders/                           # Should be subdomain
│   └── index.mdx
├── Payment/                          # Should be subdomain
│   └── index.mdx
└── Shipping/
    └── index.mdx
```

### domain-ubiquitous-language
**Impact:** HIGH
**Category:** Domain Organization

Add a `ubiquitous-language.md` file in domain folders to define DDD terminology. This creates a glossary for the bounded context.

**Good:**
```
domains/
└── Orders/
    ├── index.mdx
    └── ubiquitous-language.md
```

```yaml
# ubiquitous-language.md
---
dictionary:
  - id: order
    name: Order
    summary: A customer's request to purchase products
    description: |
      An order represents the entire lifecycle of a customer purchase,
      from creation through fulfillment.
  - id: line-item
    name: Line Item
    summary: A single product entry within an order
    icon: ShoppingCartIcon
  - id: fulfillment
    name: Fulfillment
    summary: The process of preparing and shipping an order
---
```

**Bad:**
```
# Terminology buried in markdown
domains/
└── Orders/
    └── index.mdx
    <!-- Contains inline glossary that's not structured -->
```

### domain-service-grouping
**Impact:** HIGH
**Category:** Domain Organization

Group related services within domains. Services that work together on the same bounded context should be co-located.

**Good:**
```
domains/
└── Orders/
    ├── index.mdx
    └── services/
        ├── OrdersService/           # Core order processing
        │   └── index.mdx
        ├── InventoryService/        # Related: inventory management
        │   └── index.mdx
        ├── NotificationService/     # Related: order notifications
        │   └── index.mdx
        └── ShippingService/         # Related: order shipping
            └── index.mdx
```

**Bad:**
```
# Services scattered at root level
services/
├── OrdersService/
├── InventoryService/
├── UserService/                     # Different domain
├── NotificationService/
├── PaymentService/                  # Different domain
└── ShippingService/

# No domain context or grouping
```

### naming-resource-ids
**Impact:** HIGH
**Category:** Naming Conventions

Use PascalCase for event, command, query, and service IDs. IDs should be descriptive and use domain terminology.

**Good:**
```yaml
# Events - past tense, describing what happened
---
id: OrderConfirmed
id: PaymentReceived
id: InventoryAdjusted
id: UserSignedUp
---

# Commands - imperative, describing action to perform
---
id: PlaceOrder
id: UpdateInventory
id: SendNotification
id: CancelShipment
---

# Queries - describe what data is being requested
---
id: GetOrderStatus
id: GetInventoryList
id: GetUserProfile
---

# Services - describe the capability
---
id: OrdersService
id: InventoryService
id: NotificationService
---
```

**Bad:**
```yaml
# Inconsistent casing
---
id: order_confirmed          # Wrong: snake_case
id: PAYMENT_RECEIVED         # Wrong: SCREAMING_CASE
id: orderCancelled           # Wrong: camelCase
id: order-shipped            # Wrong: kebab-case
---

# Non-descriptive
---
id: Event1
id: Service2
id: Handler
---
```

### naming-channel-ids
**Impact:** HIGH
**Category:** Naming Conventions

Use parameterized patterns with `{parameter}` syntax for channels that exist across environments or have variable segments.

**Good:**
```yaml
# Parameterized channel with environment
---
id: inventory.{env}.events
name: Inventory Events Channel
address: inventory.{env}.events
parameters:
  env:
    enum:
      - dev
      - sit
      - prod
    description: Environment to use
---

# Hierarchical naming for event buses
---
id: orders-domain-eventbus
name: Orders Domain Event Bus
---

# Topic naming with clear hierarchy
---
id: payments.{region}.transactions
parameters:
  region:
    enum:
      - us-east
      - eu-west
      - ap-south
---
```

**Bad:**
```yaml
# No parameterization for multi-env
---
id: inventory-events-dev
id: inventory-events-prod     # Duplicated definitions
---

# Unclear naming
---
id: topic1
id: my-queue
id: events
---
```

### naming-file-names
**Impact:** HIGH
**Category:** Naming Conventions

Use `index.mdx` (or `index.md`) for main resource files. Use lowercase with hyphens for supporting files.

**Good:**
```
events/OrderConfirmed/
├── index.mdx                # Main documentation (required)
├── schema.json              # Schema file
├── schema.avro              # Alternative schema format
├── example-payload.json     # Example file
└── changelog.mdx            # Version history

services/InventoryService/
├── index.mdx
├── openapi.yaml             # API specification
├── asyncapi.yaml            # Async API specification
└── architecture.png         # Architecture diagram
```

**Bad:**
```
events/OrderConfirmed/
├── OrderConfirmed.mdx       # Wrong: should be index.mdx
├── Schema.JSON              # Wrong: uppercase
├── CHANGELOG.md             # Wrong: uppercase
└── Example Payload.json     # Wrong: spaces in filename
```

### files-schemas
**Impact:** MEDIUM
**Category:** Supporting Files

Place schema files alongside the resource `index.mdx`. Reference them using `schemaPath` in frontmatter.

**Good:**
```
events/OrderConfirmed/
├── index.mdx
└── schema.json

# In index.mdx frontmatter:
---
id: OrderConfirmed
name: Order Confirmed
version: 1.0.0
schemaPath: schema.json      # References local schema file
---
```

```json
// schema.json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "type": "object",
  "properties": {
    "orderId": { "type": "string" },
    "customerId": { "type": "string" },
    "totalAmount": { "type": "number" }
  },
  "required": ["orderId", "customerId"]
}
```

**Bad:**
```
# Schema in separate global folder
schemas/
└── order-confirmed.json

events/OrderConfirmed/
└── index.mdx
# schemaPath: ../../schemas/order-confirmed.json  # Fragile path

# Or embedding schema in markdown
---
id: OrderConfirmed
---
## Schema
```json
{ "type": "object", ... }
```
```

### files-specifications
**Impact:** MEDIUM
**Category:** Supporting Files

Store OpenAPI and AsyncAPI specifications in the resource folder. Reference them using the `specifications` frontmatter field.

**Good:**
```
services/InventoryService/
├── index.mdx
├── openapi.yaml
└── asyncapi.yaml

# In index.mdx frontmatter:
---
id: InventoryService
name: Inventory Service
version: 1.0.0
specifications:
  - type: openapi
    path: openapi.yaml
    name: REST API
  - type: asyncapi
    path: asyncapi.yaml
    name: Event API
---
```

**Bad:**
```
# Specifications in global folder
specs/
├── inventory-openapi.yaml
└── inventory-asyncapi.yaml

services/InventoryService/
└── index.mdx
# Hard to maintain relationship

# Or multiple spec files without proper reference
services/InventoryService/
├── index.mdx
├── api-v1.yaml
├── api-v2.yaml
└── events.yaml
# No clear indication of which is current
```

### files-changelog
**Impact:** MEDIUM
**Category:** Supporting Files

Add `changelog.mdx` files to track version history for resources. Place alongside the `index.mdx` file.

**Good:**
```
services/InventoryService/
├── index.mdx
└── changelog.mdx

# changelog.mdx content:
---
createdAt: 2024-01-15
---

## 0.0.2

### Added
- Support for bulk inventory updates
- Real-time stock notifications

### Changed
- Improved error handling for out-of-stock scenarios

## 0.0.1

### Added
- Initial release
- Basic inventory tracking
- Stock level queries
```

**Bad:**
```
# No changelog file
services/InventoryService/
└── index.mdx
# Version history lost

# Or changelog in wrong location
changelogs/
└── inventory-service.md
# Not associated with the service
```

### config-eventcatalog-config
**Impact:** MEDIUM
**Category:** Configuration

Configure `eventcatalog.config.js` at the project root with required settings for title, organization, and optional features.

**Good:**
```javascript
// eventcatalog.config.js
export default {
  // Required settings
  title: 'ACME Event Catalog',
  tagline: 'Documentation for all our events and services',
  organizationName: 'ACME Corp',
  homepageLink: 'https://eventcatalog.dev',

  // Optional: Custom logo
  logo: {
    src: '/logo.svg',
    alt: 'ACME Logo'
  },

  // Optional: Enable features
  chat: {
    enabled: true
  },

  // Optional: Configure generators
  generators: [
    ['@eventcatalog/generator-asyncapi', {
      services: [
        { path: 'specs/asyncapi.yaml', id: 'InventoryService' }
      ]
    }]
  ]
};
```

**Bad:**
```javascript
// Missing or minimal config
export default {
  title: 'My Catalog'
  // Missing organizationName, tagline, etc.
};

// Or wrong file format
// eventcatalog.config.json - should be .js or .mjs
{
  "title": "My Catalog"
}
```

### config-users-teams
**Impact:** MEDIUM
**Category:** Configuration

Define users and teams in dedicated root folders. Users and teams are referenced by ID in resource `owners` fields.

**Good:**
```
users/
├── dboyne.md
└── msmith.md

teams/
├── order-management.md
└── platform-team.md
```

```yaml
# users/dboyne.md
---
id: dboyne
name: David Boyne
avatarUrl: https://avatars.githubusercontent.com/u/123456
role: Principal Engineer
email: david@example.com
slackDirectMessageUrl: https://slack.com/users/dboyne
---

## About

Principal Engineer working on the Orders domain.
```

```yaml
# teams/order-management.md
---
id: order-management
name: Order Management Team
summary: Team responsible for order processing systems
email: orders@example.com
members:
  - dboyne
  - msmith
---

## About

The Order Management team owns all services in the Orders domain.
```

**Bad:**
```
# No user/team definitions
events/OrderConfirmed/index.mdx
---
owners:
  - dboyne        # References undefined user
  - order-team    # References undefined team
---

# Users can't be contacted, no team information available
```
