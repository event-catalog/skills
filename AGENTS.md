# EventCatalog AI Agent Skills - Complete Reference

This document combines all EventCatalog agent skills for quick reference. For detailed rules with code examples, see individual skill files.

## Overview

| Skill | Rules | Purpose |
|-------|-------|---------|
| [Project Setup](#project-setup) | 15 | Creating and bootstrapping new projects |
| [Documentation Best Practices](#documentation-best-practices) | 16 | Writing high-quality MDX documentation |
| [SDK Usage](#sdk-usage) | 15 | Programmatic catalog manipulation |
| [Catalog Structure](#catalog-structure) | 15 | Directory organization and naming |
| [EDA Documentation](#eda-documentation) | 15 | Event-driven architecture patterns |
| [Components Reference](#components-reference) | 15 | Built-in MDX components |

---

## Project Setup

Guide for creating and bootstrapping new EventCatalog projects from scratch.

### Project Initialization (CRITICAL)

| Rule | Description |
|------|-------------|
| `init-create-project` | Use create-eventcatalog to scaffold new project |
| `init-directory-structure` | Create standard directory structure |
| `init-package-json` | Configure package.json with required scripts |

### Configuration (CRITICAL)

| Rule | Description |
|------|-------------|
| `config-basic-settings` | Configure essential settings in eventcatalog.config.js |
| `config-generators` | Set up generators for AsyncAPI/OpenAPI import |
| `config-custom-branding` | Add logo and organization branding |

### First Resources (HIGH)

| Rule | Description |
|------|-------------|
| `first-domain` | Create your first domain with proper structure |
| `first-service` | Add an initial service with relationships |
| `first-event` | Create events with schemas and documentation |

### Users & Teams (HIGH)

| Rule | Description |
|------|-------------|
| `team-define-users` | Create user profiles for ownership |
| `team-define-teams` | Set up team definitions |
| `team-assign-ownership` | Assign owners to resources |

### Content Bootstrap (MEDIUM)

| Rule | Description |
|------|-------------|
| `bootstrap-example-content` | Add meaningful example content |
| `bootstrap-channels` | Set up initial channel definitions |
| `bootstrap-custom-docs` | Add getting started documentation |

---

## Documentation Best Practices

Guidelines for writing high-quality EventCatalog MDX documentation.

### Frontmatter Structure (CRITICAL)

| Rule | Description |
|------|-------------|
| `frontmatter-required-fields` | Include id, name, and version in all resources |
| `frontmatter-summary` | Always provide a meaningful summary |
| `frontmatter-owners` | Assign owners to every resource |
| `frontmatter-version-format` | Use semantic versioning (e.g., "1.0.0") |

### Content Structure (HIGH)

| Rule | Description |
|------|-------------|
| `content-overview-section` | Start with an Overview section |
| `content-cross-references` | Use wiki-style links `[[type\|id]]` for cross-references |
| `content-visual-components` | Include `<NodeGraph />` for architecture visualization |
| `content-code-examples` | Provide code examples with proper formatting |

### Documentation Quality (MEDIUM)

| Rule | Description |
|------|-------------|
| `quality-mermaid-diagrams` | Use Mermaid for sequence and architecture diagrams |
| `quality-tiles-navigation` | Use Tiles for key navigation links |
| `quality-steps-component` | Use Steps for procedural instructions |
| `quality-schema-viewer` | Include SchemaViewer for message schemas |

### Metadata & Organization (MEDIUM)

| Rule | Description |
|------|-------------|
| `metadata-badges` | Use badges for status and categorization |
| `metadata-deprecation` | Document deprecation with dates and migration paths |
| `metadata-draft-mode` | Use draft mode for work-in-progress documentation |
| `metadata-repository-link` | Link to source code repositories |

---

## SDK Usage

Correct usage of `@eventcatalog/sdk` for programmatic catalog manipulation.

### SDK Initialization (CRITICAL)

| Rule | Description |
|------|-------------|
| `init-catalog-path` | Initialize SDK with correct catalog path |
| `init-async-await` | All SDK methods are async, always use await |
| `init-error-handling` | Handle undefined returns for missing resources |

### Reading Resources (HIGH)

| Rule | Description |
|------|-------------|
| `read-single-resource` | Use get methods for single resource retrieval |
| `read-multiple-resources` | Use plural methods with `latestOnly` option |
| `read-version-check` | Check if version exists before operations |
| `read-attach-schema` | Use `attachSchema` option when schema content needed |

### Writing Resources (HIGH)

| Rule | Description |
|------|-------------|
| `write-resource-structure` | Provide id, name, version, markdown when writing |
| `write-options` | Use options for path, override, and versioning |
| `write-nested-resources` | Use ToService/ToDomain methods for nested resources |
| `write-relationships` | Set up sends/receives relationships correctly |

### Advanced Operations (MEDIUM)

| Rule | Description |
|------|-------------|
| `advanced-versioning` | Version resources before major updates |
| `advanced-file-management` | Add files and schemas to resources |
| `advanced-data-stores` | Connect services to data stores |

### Utility Functions (MEDIUM)

| Rule | Description |
|------|-------------|
| `utility-dump-catalog` | Export entire catalog to JSON |
| `utility-resource-path` | Get file paths for resources |

---

## Catalog Structure

Proper directory structure, organization, and naming conventions.

### Directory Structure (CRITICAL)

| Rule | Description |
|------|-------------|
| `dir-root-folders` | Use standard root-level folder names |
| `dir-resource-pattern` | Each resource in folder with index.mdx |
| `dir-versioning` | Use versioned subdirectory for historical versions |
| `dir-nested-resources` | Resources can be nested within services/domains |

### Domain Organization (HIGH)

| Rule | Description |
|------|-------------|
| `domain-subdomain-structure` | Use subdomains folder for bounded contexts |
| `domain-ubiquitous-language` | Add ubiquitous-language.md for DDD terminology |
| `domain-service-grouping` | Group related services within domains |

### Naming Conventions (HIGH)

| Rule | Description |
|------|-------------|
| `naming-resource-ids` | Use PascalCase for message and service IDs |
| `naming-channel-ids` | Use parameterized patterns for channels |
| `naming-file-names` | Use index.mdx for main files |

### Supporting Files (MEDIUM)

| Rule | Description |
|------|-------------|
| `files-schemas` | Place schemas alongside resource index files |
| `files-specifications` | Store OpenAPI/AsyncAPI in resource folders |
| `files-changelog` | Add changelog.mdx for version history |

### Configuration (MEDIUM)

| Rule | Description |
|------|-------------|
| `config-eventcatalog-config` | Configure eventcatalog.config.js properly |
| `config-users-teams` | Define users and teams in root folders |

---

## EDA Documentation

EDA and DDD best practices applied to EventCatalog documentation.

### Message Types (CRITICAL)

| Rule | Description |
|------|-------------|
| `msg-events-past-tense` | Events describe facts that happened (past tense) |
| `msg-commands-imperative` | Commands are requests for action (imperative) |
| `msg-queries-read-only` | Queries request data without side effects |
| `msg-naming-conventions` | Use consistent naming patterns |

### Service Relationships (HIGH)

| Rule | Description |
|------|-------------|
| `svc-sends-receives` | Document producer/consumer relationships |
| `svc-channel-routing` | Specify which channels messages flow through |
| `svc-data-store-connections` | Document read/write relationships to data stores |

### Channel Documentation (HIGH)

| Rule | Description |
|------|-------------|
| `channel-address-protocols` | Specify address patterns and protocols |
| `channel-parameters` | Define parameterized channel segments |
| `channel-message-routing` | Document which messages flow through channels |

### Flow Documentation (MEDIUM)

| Rule | Description |
|------|-------------|
| `flow-step-types` | Use appropriate step types (service, message, actor) |
| `flow-branching` | Document decision points with next_steps |
| `flow-external-systems` | Include external system integrations |

### Entity & Domain Modeling (MEDIUM)

| Rule | Description |
|------|-------------|
| `model-entity-properties` | Define entity properties with types and relationships |
| `model-aggregate-roots` | Mark aggregate root entities appropriately |
| `model-bounded-contexts` | Organize domains around bounded contexts |

---

## Components Reference

Reference guide for EventCatalog's built-in MDX components.

### Visualization Components (CRITICAL)

| Component | Usage |
|-----------|-------|
| `<NodeGraph />` | Architecture diagram showing relationships |
| `<EntityMap />` | Entity relationship visualization |
| `<Flow id="..." />` | Business process flow diagram |

### Data Display Components (HIGH)

| Component | Usage |
|-----------|-------|
| `<MessageTable format="all" />` | Table of messages |
| `<SchemaViewer file="schema.json" />` | Interactive schema display |
| `<EntityPropertiesTable />` | Entity properties table |
| `<ChannelInformation />` | Channel details display |

### Layout Components (MEDIUM)

| Component | Usage |
|-----------|-------|
| `<Tiles><Tile ... /></Tiles>` | Navigation cards |
| `<Steps><Step ... /></Steps>` | Procedural guides |
| `<Accordion title="...">` | Collapsible sections |

### Content Components (MEDIUM)

| Component | Usage |
|-----------|-------|
| `<Schema file="..." />` | Raw schema display |
| `<ResourceGroupTable />` | Related resources table |
| `:::note`, `:::warning`, `:::danger` | Admonitions/callouts |

### Advanced Components (LOW)

| Component | Usage |
|-----------|-------|
| `{frontmatter.id}` | Access frontmatter data in MDX |
| `<Tabs><TabItem>` | Multi-language code examples |

---

## Quick Lookup Tables

### Resource Frontmatter Fields

| Field | Required | Description |
|-------|----------|-------------|
| `id` | Yes | Unique identifier (PascalCase) |
| `name` | Yes | Human-readable name |
| `version` | Yes | Semantic version string |
| `summary` | Recommended | Brief description |
| `owners` | Recommended | User/team IDs |
| `badges` | Optional | Status indicators |
| `deprecated` | Optional | Deprecation info |
| `draft` | Optional | Draft mode settings |
| `schemaPath` | Optional | Path to schema file |
| `repository` | Optional | Source code link |

### Service-Specific Fields

| Field | Description |
|-------|-------------|
| `sends` | Messages the service produces |
| `receives` | Messages the service consumes |
| `writesTo` | Data stores written to |
| `readsFrom` | Data stores read from |
| `entities` | Related entities |

### Message-Specific Fields

| Field | Description |
|-------|-------------|
| `producers` | Services that produce this message |
| `consumers` | Services that consume this message |
| `channels` | Channels this message flows through |

### Channel-Specific Fields

| Field | Description |
|-------|-------------|
| `address` | Channel address pattern |
| `protocols` | Supported protocols |
| `parameters` | Parameterized segments |
| `messages` | Messages on this channel |

### Entity-Specific Fields

| Field | Description |
|-------|-------------|
| `aggregateRoot` | Is this an aggregate root? |
| `identifier` | Primary identifier property |
| `properties` | Entity properties with types |

### SDK Methods Quick Reference

```typescript
// Initialize
const sdk = catalogSDK('/path/to/catalog');

// Read single
await sdk.getEvent(id, version?, { attachSchema? });
await sdk.getService(id, version?);
await sdk.getDomain(id, version?);

// Read multiple
await sdk.getEvents({ latestOnly?, attachSchema? });
await sdk.getServices({ latestOnly? });

// Write
await sdk.writeEvent(event, { override?, versionExistingContent?, path? });
await sdk.writeEventToService(event, { id, version? });

// Version
await sdk.versionEvent(id);
await sdk.eventHasVersion(id, version?);

// Files
await sdk.addSchemaToEvent(id, { schema, fileName }, version?);
await sdk.addFileToEvent(id, { content, fileName }, version?);

// Relationships
await sdk.addEventToService(serviceId, direction, { id, version });
await sdk.addDataStoreToService(serviceId, operation, { id, version });

// Utility
await sdk.dumpCatalog({ includeMarkdown? });
await sdk.getResourcePath(catalogDir, id, version?);
```

---

## File Structure Template

```
eventcatalog/
├── eventcatalog.config.js
├── domains/
│   └── {Domain}/
│       ├── index.mdx
│       ├── ubiquitous-language.md
│       └── subdomains/
│           └── {Subdomain}/
│               ├── index.mdx
│               ├── services/
│               │   └── {Service}/
│               │       ├── index.mdx
│               │       ├── changelog.mdx
│               │       ├── events/
│               │       │   └── {Event}/
│               │       │       ├── index.mdx
│               │       │       └── schema.json
│               │       ├── commands/
│               │       └── queries/
│               ├── entities/
│               │   └── {Entity}/
│               │       └── index.mdx
│               └── channels/
│                   └── {Channel}/
│                       └── index.mdx
├── services/           # Standalone services
├── events/             # Standalone events
├── channels/           # Standalone channels
├── flows/              # Business flows
├── containers/         # Data stores
├── users/              # User definitions
├── teams/              # Team definitions
└── docs/               # Custom documentation
```
