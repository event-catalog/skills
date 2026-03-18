# EventCatalog AI Skills

A collection of AI agent skills for [EventCatalog](https://eventcatalog.dev) that help you document your architectures.

> Contributions welcome! Run into a problem or have a question? [Open an issue](https://github.com/event-catalog/skills/issues).

## Available Skills

| Skill | Description |
|-------|-------------|
| [catalog-documentation-creator](skills/catalog-documentation-creator/) | Generates EventCatalog documentation files (services, events, commands, queries, domains, flows, channels, containers) with correct frontmatter, folder structure, and best practices. |
| [flow-wizard](skills/flow-wizard/) | Interactive, conversational skill that guides you through documenting business flows step-by-step, cross-referencing your existing catalog resources. |

## Installation

### Option 1: CLI Install (Recommended)

Use [npx skills](https://github.com/vercel-labs/skills) to install skills directly:

```bash
# Install all skills
npx skills add event-catalog/skills

# Install a specific skill
npx skills add event-catalog/skills --skill catalog-documentation-creator
```

### Option 2: Clone and Copy

Clone the entire repo and copy the skills you need:

```bash
git clone https://github.com/event-catalog/skills.git
cp -r eventcatalog-skills/skills/catalog-documentation-creator .claude/skills/
```

### Option 3: Git Submodule

Add as a submodule for easy updates:

```bash
git submodule add https://github.com/event-catalog/skills.git .claude/skills/eventcatalog
```

### Option 4: Fork and Customize

Fork the repo and tailor skills to your team's conventions:

1. Fork this repository
2. Modify skills to fit your needs
3. Install from your fork:
   ```bash
   npx add-skill https://github.com/YOUR_ORG/eventcatalog-skills
   ```

## Usage Examples

Once installed, the skills are automatically available to your AI agent. Here are some things you can ask:

**Document a service:**
> "Document my OrderService that receives OrderCreated events and sends OrderConfirmed events"

**Create a full domain:**
> "Create a Payments domain with a PaymentService, PaymentProcessed event, and ProcessPayment command"

**Document from your codebase:**
> "Look at my src/ directory and generate EventCatalog documentation for the services and events you find"

**Add a business flow:**
> "Document the checkout flow from cart submission through payment processing to order confirmation"

**Scaffold a new catalog:**
> "I don't have an EventCatalog yet, help me create one and document my architecture"

## License

MIT
