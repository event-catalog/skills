# EventCatalog AI Agent Skills

> **Note:** This repository is currently in testing/experimental phase as we learn and experiment with AI agent skills. Rules and structure may change as we gather feedback and refine the approach. Contributions and feedback are welcome!

A collection of AI agent skills for [EventCatalog](https://eventcatalog.dev) that help developers write better documentation, follow best practices, and use the SDK correctly.

These skills follow the [Vercel Agent Skills](https://github.com/vercel/agents) format and can be installed via `npx add-skill`.

## Installation

Install all skills at once:

```bash
npx add-skill https://github.com/event-catalog/eventcatalog-skills
```

Or install individual skills:

```bash
# Project setup (start here for new projects)
npx add-skill https://github.com/event-catalog/eventcatalog-skills/tree/main/skills/eventcatalog-project-setup

# Documentation best practices
npx add-skill https://github.com/event-catalog/eventcatalog-skills/tree/main/skills/eventcatalog-documentation-best-practices

# SDK usage
npx add-skill https://github.com/event-catalog/eventcatalog-skills/tree/main/skills/eventcatalog-sdk-usage

# Catalog structure
npx add-skill https://github.com/event-catalog/eventcatalog-skills/tree/main/skills/eventcatalog-catalog-structure

# Event-driven architecture documentation
npx add-skill https://github.com/event-catalog/eventcatalog-skills/tree/main/skills/event-driven-architecture-documentation

# Components reference
npx add-skill https://github.com/event-catalog/eventcatalog-skills/tree/main/skills/eventcatalog-components-reference
```

## Available Skills

### 1. EventCatalog Project Setup (15 rules)

Guide for creating and bootstrapping new EventCatalog projects from scratch.

**Categories:**
- Project Initialization (Critical)
- Configuration (Critical)
- First Resources (High)
- Users & Teams (High)
- Content Bootstrap (Medium)

**Use when:**
- Creating a brand new EventCatalog project
- Setting up initial directory structure and configuration
- Adding your first domains, services, and events
- Defining users and teams for ownership

### 2. EventCatalog Documentation Best Practices (16 rules)

Guidelines for writing high-quality EventCatalog MDX documentation.

**Categories:**
- Frontmatter Structure (Critical)
- Content Structure (High)
- Documentation Quality (Medium)
- Metadata & Organization (Medium)

**Use when:**
- Creating documentation for new events, services, domains
- Reviewing existing documentation for best practices
- Adding proper cross-references and visual components

### 3. EventCatalog SDK Usage (15 rules)

Correct usage of `@eventcatalog/sdk` for programmatic catalog manipulation.

**Categories:**
- SDK Initialization (Critical)
- Reading Resources (High)
- Writing Resources (High)
- Advanced Operations (Medium)
- Utility Functions (Medium)

**Use when:**
- Writing generators for AsyncAPI/OpenAPI specs
- Automation scripts for catalog updates
- Migration scripts between catalog versions

### 4. EventCatalog Catalog Structure (15 rules)

Proper directory structure, organization, and naming conventions.

**Categories:**
- Directory Structure (Critical)
- Domain Organization (High)
- Naming Conventions (High)
- Supporting Files (Medium)
- Configuration (Medium)

**Use when:**
- Setting up new catalogs
- Reorganizing existing catalogs
- Adding new resource types

### 5. Event-Driven Architecture Documentation (15 rules)

EDA/DDD best practices applied to EventCatalog documentation.

**Categories:**
- Message Types (Critical)
- Service Relationships (High)
- Channel Documentation (High)
- Flow Documentation (Medium)
- Entity & Domain Modeling (Medium)

**Use when:**
- Deciding between event vs command
- Documenting service relationships correctly
- Setting up DDD-style domain structure

### 6. EventCatalog Components Reference (15 rules)

Reference guide for EventCatalog's built-in MDX components.

**Categories:**
- Visualization Components (Critical)
- Data Display Components (High)
- Layout Components (Medium)
- Content Components (Medium)
- Advanced Components (Low)

**Use when:**
- Adding architecture diagrams
- Displaying message tables and schemas
- Creating navigation tiles and collapsible sections

## Rule Priority Levels

| Priority | Description |
|----------|-------------|
| CRITICAL | Must follow - breaking issues or major problems if ignored |
| HIGH | Should follow - significant impact on quality or functionality |
| MEDIUM | Recommended - improves quality and maintainability |
| LOW | Nice to have - minor improvements |

## Contributing

Contributions are welcome! Please read our contributing guidelines and submit pull requests to the repository.

## License

MIT License - see LICENSE file for details.
