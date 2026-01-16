---
name: eventcatalog-project-setup
description: Guide for creating and bootstrapping new EventCatalog projects. Use when setting up a new catalog from scratch, initializing directory structure, creating configuration files, and adding initial resources like domains, services, and events.
license: MIT
metadata:
  author: eventcatalog
  version: "1.0.0"
---

# EventCatalog Project Setup

Comprehensive guide for creating new EventCatalog projects from scratch. Contains 15 rules across 5 categories, prioritized by impact.

## When to Apply

Reference these guidelines when:
- Creating a brand new EventCatalog project
- Setting up the initial directory structure
- Configuring eventcatalog.config.js
- Adding the first domains, services, and events
- Defining initial users and teams

## Rule Categories by Priority

| Priority | Category | Impact | Prefix |
|----------|----------|--------|--------|
| 1 | Project Initialization | CRITICAL | `init-` |
| 2 | Configuration | CRITICAL | `config-` |
| 3 | First Resources | HIGH | `first-` |
| 4 | Users & Teams | HIGH | `team-` |
| 5 | Content Bootstrap | MEDIUM | `bootstrap-` |

## Quick Reference

### 1. Project Initialization (CRITICAL)

- `init-create-project` - Use create-eventcatalog to scaffold new project
- `init-directory-structure` - Create standard directory structure
- `init-package-json` - Configure package.json with required scripts

### 2. Configuration (CRITICAL)

- `config-basic-settings` - Configure essential settings in eventcatalog.config.js
- `config-generators` - Set up generators for AsyncAPI/OpenAPI import
- `config-custom-branding` - Add logo and organization branding

### 3. First Resources (HIGH)

- `first-domain` - Create your first domain with proper structure
- `first-service` - Add an initial service with relationships
- `first-event` - Create events with schemas and documentation

### 4. Users & Teams (HIGH)

- `team-define-users` - Create user profiles for ownership
- `team-define-teams` - Set up team definitions
- `team-assign-ownership` - Assign owners to resources

### 5. Content Bootstrap (MEDIUM)

- `bootstrap-example-content` - Add meaningful example content
- `bootstrap-channels` - Set up initial channel definitions
- `bootstrap-custom-docs` - Add getting started documentation

---

## Detailed Rules

### init-create-project
**Impact:** CRITICAL
**Category:** Project Initialization

Use `create-eventcatalog` to scaffold a new EventCatalog project. This creates the base structure with all necessary dependencies.

**Good:**
```bash
# Create new project with npx
npx create-eventcatalog@latest my-catalog

# Or with specific version
npx create-eventcatalog@2.0.0 my-catalog

# Navigate to project
cd my-catalog

# Install dependencies
npm install

# Start development server
npm run dev
```

**Bad:**
```bash
# Don't manually create from scratch
mkdir my-catalog
cd my-catalog
npm init -y
npm install astro  # Missing EventCatalog dependencies

# Don't clone the repo directly
git clone https://github.com/event-catalog/eventcatalog my-catalog
# This gives you the source code, not a new project
```

### init-directory-structure
**Impact:** CRITICAL
**Category:** Project Initialization

Create the standard directory structure after project initialization. These directories are required for EventCatalog to function.

**Good:**
```bash
# Create required directories
mkdir -p domains
mkdir -p services
mkdir -p events
mkdir -p commands
mkdir -p queries
mkdir -p channels
mkdir -p flows
mkdir -p containers
mkdir -p entities
mkdir -p users
mkdir -p teams
mkdir -p docs
mkdir -p public
```

Resulting structure:
```
my-catalog/
├── eventcatalog.config.js    # Configuration
├── package.json
├── astro.config.mjs          # Astro configuration
├── domains/                  # Domain-driven organization
├── services/                 # Standalone services
├── events/                   # Event definitions
├── commands/                 # Command definitions
├── queries/                  # Query definitions
├── channels/                 # Message channels
├── flows/                    # Business flows
├── containers/               # Data stores
├── entities/                 # DDD entities
├── users/                    # User profiles
├── teams/                    # Team definitions
├── docs/                     # Custom documentation
└── public/                   # Static assets (logo, images)
```

**Bad:**
```
my-catalog/
├── src/                      # Wrong - not needed
├── content/                  # Wrong - use specific folders
├── api/                      # Wrong - not an API project
└── config.json               # Wrong - should be .js
```

### init-package-json
**Impact:** CRITICAL
**Category:** Project Initialization

Ensure package.json has the required scripts and dependencies for EventCatalog.

**Good:**
```json
{
  "name": "my-company-eventcatalog",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "ec dev",
    "build": "ec build",
    "preview": "ec preview",
    "generate": "ec generate"
  },
  "dependencies": {
    "@eventcatalog/core": "^2.0.0"
  },
  "devDependencies": {
    "@eventcatalog/sdk": "^2.0.0"
  }
}
```

**Bad:**
```json
{
  "name": "my-catalog",
  "scripts": {
    "start": "node index.js"  // Wrong script
  }
  // Missing EventCatalog dependencies
}
```

### config-basic-settings
**Impact:** CRITICAL
**Category:** Configuration

Configure essential settings in eventcatalog.config.js including title, organization, and homepage.

**Good:**
```javascript
// eventcatalog.config.js
export default {
  // Required settings
  title: 'ACME Event Catalog',
  tagline: 'Discover and understand our event-driven architecture',
  organizationName: 'ACME Corporation',

  // Homepage configuration
  homepageLink: 'https://eventcatalog.dev',

  // Edit URL for documentation (optional but recommended)
  editUrl: 'https://github.com/acme/eventcatalog/edit/main',

  // Landing page configuration
  landingPage: {
    title: 'Welcome to ACME Event Catalog',
    subtitle: 'Your source of truth for events, services, and domains',

    // Quick stats to show
    stats: {
      events: true,
      services: true,
      domains: true,
      channels: true
    }
  },

  // Enable/disable features
  docs: {
    sidebar: {
      showPageHeadings: true
    }
  }
};
```

**Bad:**
```javascript
// Minimal config missing essential info
export default {
  title: 'Catalog'
  // Missing organizationName, tagline, etc.
};

// Or using wrong export format
module.exports = {  // Should use ES modules
  title: 'Catalog'
};
```

### config-generators
**Impact:** CRITICAL
**Category:** Configuration

Configure generators to automatically import from AsyncAPI, OpenAPI, or other specification formats.

**Good:**
```javascript
// eventcatalog.config.js
export default {
  title: 'ACME Event Catalog',
  organizationName: 'ACME Corporation',

  generators: [
    // AsyncAPI generator
    [
      '@eventcatalog/generator-asyncapi',
      {
        services: [
          {
            path: './specs/inventory-service.asyncapi.yaml',
            id: 'InventoryService'
          },
          {
            path: './specs/orders-service.asyncapi.yaml',
            id: 'OrdersService'
          }
        ],
        domain: { id: 'Orders', name: 'Orders Domain', version: '1.0.0' },
        debug: true
      }
    ],

    // OpenAPI generator
    [
      '@eventcatalog/generator-openapi',
      {
        services: [
          {
            path: './specs/api.openapi.yaml',
            id: 'PublicAPI'
          }
        ]
      }
    ]
  ]
};
```

Then run:
```bash
npm run generate
```

**Bad:**
```javascript
// Not using generators - manually copying from specs
export default {
  title: 'Catalog'
  // No generators configured
};

// Then manually creating events that could be auto-generated
// This leads to drift between specs and catalog
```

### config-custom-branding
**Impact:** CRITICAL
**Category:** Configuration

Add your organization's logo and custom branding to the catalog.

**Good:**
```javascript
// eventcatalog.config.js
export default {
  title: 'ACME Event Catalog',
  organizationName: 'ACME Corporation',

  // Logo configuration
  logo: {
    src: '/logo.svg',        // Path relative to public/
    alt: 'ACME Corporation',
    text: 'Event Catalog'    // Text shown next to logo
  },

  // Optional: Custom CSS
  // styles: '/custom.css'
};
```

Place logo in public folder:
```
my-catalog/
├── public/
│   ├── logo.svg           # SVG preferred for scaling
│   ├── logo.png           # Or PNG fallback
│   └── favicon.ico        # Browser tab icon
└── eventcatalog.config.js
```

**Bad:**
```javascript
// External logo URL - may break or slow loading
export default {
  logo: {
    src: 'https://example.com/logo.png'  // External URL
  }
};

// Or missing alt text
export default {
  logo: {
    src: '/logo.png'
    // Missing alt - accessibility issue
  }
};
```

### first-domain
**Impact:** HIGH
**Category:** First Resources

Create your first domain to organize services by business capability. Start with a top-level domain.

**Good:**
```bash
mkdir -p domains/Orders
```

```yaml
# domains/Orders/index.mdx
---
id: Orders
name: Orders Domain
version: 1.0.0
summary: |
  The Orders domain handles all operations related to customer orders,
  from creation through fulfillment. This is the core domain for our
  e-commerce platform.
owners:
  - orders-team
badges:
  - content: Core Domain
    backgroundColor: blue
    textColor: blue
---

## Overview

The Orders domain is responsible for:

- Order creation and validation
- Inventory management
- Order fulfillment and shipping
- Customer notifications

## Architecture

<NodeGraph />

## Services in this Domain

This domain contains the following services:

- [[service|OrdersService]] - Core order processing
- [[service|InventoryService]] - Stock management
- [[service|ShippingService]] - Fulfillment operations

## Getting Started

If you're new to this domain, start with:

<Tiles>
    <Tile
        icon="BookOpenIcon"
        href="/docs/guides/orders-overview"
        title="Orders Overview"
        description="Learn about the orders architecture"
    />
    <Tile
        icon="UserGroupIcon"
        href="/docs/teams/orders-team"
        title="Contact the Team"
        description="Reach out to the Orders team"
    />
</Tiles>
```

**Bad:**
```yaml
# Missing essential fields
---
id: orders
name: orders
---

Just some orders stuff.

# Problems:
# - id should be PascalCase
# - No version
# - No summary
# - No owners
# - Minimal content
```

### first-service
**Impact:** HIGH
**Category:** First Resources

Create your first service with proper relationships to events, commands, and data stores.

**Good:**
```bash
mkdir -p domains/Orders/services/OrdersService
```

```yaml
# domains/Orders/services/OrdersService/index.mdx
---
id: OrdersService
name: Orders Service
version: 1.0.0
summary: |
  Core service responsible for order lifecycle management including
  creation, validation, and state transitions.
owners:
  - orders-team
  - dboyne

# Messages this service produces
sends:
  - id: OrderConfirmed
    version: 1.0.0
    to:
      - id: orders-domain-eventbus
  - id: OrderCancelled
    version: 1.0.0
    to:
      - id: orders-domain-eventbus

# Messages this service consumes
receives:
  - id: PaymentReceived
    version: 1.0.0
    from:
      - id: payments-eventbus
  - id: PlaceOrder
    version: 1.0.0
    from:
      - id: orders-command-queue

# Data stores
writesTo:
  - id: orders-db
    version: 1.0.0
readsFrom:
  - id: orders-db
    version: 1.0.0
  - id: inventory-cache
    version: 1.0.0

repository:
  language: TypeScript
  url: https://github.com/acme/orders-service
---

## Overview

The Orders Service is the core component of the [[domain|Orders]] domain,
handling all order lifecycle operations.

## Architecture

<NodeGraph />

## Messages

<MessageTable format="all" />

## API Reference

The service exposes a REST API for order operations:

- `POST /orders` - Create new order
- `GET /orders/{id}` - Get order by ID
- `PUT /orders/{id}/cancel` - Cancel an order

## Getting Started

<Steps title="How to integrate">
  <Step title="Request access">
    Contact the Orders team for API credentials.
  </Step>
  <Step title="Install SDK">
    ```bash
    npm install @acme/orders-sdk
    ```
  </Step>
  <Step title="Create your first order">
    ```typescript
    import { OrdersClient } from '@acme/orders-sdk';

    const client = new OrdersClient({ apiKey: 'your-key' });
    const order = await client.createOrder({
      customerId: 'cust-123',
      items: [{ sku: 'PROD-001', quantity: 2 }]
    });
    ```
  </Step>
</Steps>
```

**Bad:**
```yaml
---
id: OrdersService
name: Orders Service
version: 1.0.0
# No relationships defined
# Service appears isolated in visualizations
---

This service handles orders.

# Minimal content - not useful for developers
```

### first-event
**Impact:** HIGH
**Category:** First Resources

Create your first event with a proper schema file and comprehensive documentation.

**Good:**
```bash
mkdir -p domains/Orders/services/OrdersService/events/OrderConfirmed
```

```yaml
# domains/Orders/services/OrdersService/events/OrderConfirmed/index.mdx
---
id: OrderConfirmed
name: Order Confirmed
version: 1.0.0
summary: |
  Published when an order has been successfully validated and confirmed.
  Consumers should use this event to trigger fulfillment processes.
owners:
  - orders-team
badges:
  - content: Critical Path
    backgroundColor: red
    textColor: red
schemaPath: schema.json
---

## Overview

The `OrderConfirmed` event is published by the [[service|OrdersService]]
when an order passes all validation checks and is ready for fulfillment.

This event triggers:
- Inventory reservation via [[service|InventoryService]]
- Customer notification via [[service|NotificationService]]
- Shipping preparation via [[service|ShippingService]]

## Architecture

<NodeGraph />

## Schema

<SchemaViewer file="schema.json" title="Order Confirmed Schema" />

## Example Payload

```json
{
  "eventId": "evt-123-456",
  "eventType": "OrderConfirmed",
  "timestamp": "2024-01-15T10:30:00Z",
  "data": {
    "orderId": "ord-789",
    "customerId": "cust-456",
    "items": [
      {
        "sku": "PROD-001",
        "name": "Widget Pro",
        "quantity": 2,
        "unitPrice": 29.99
      }
    ],
    "totalAmount": 59.98,
    "currency": "USD",
    "shippingAddress": {
      "street": "123 Main St",
      "city": "San Francisco",
      "state": "CA",
      "zipCode": "94105",
      "country": "US"
    }
  },
  "metadata": {
    "correlationId": "corr-abc-123",
    "source": "OrdersService"
  }
}
```

## Consuming this Event

<Tabs>
  <TabItem title="TypeScript">
    ```typescript
    interface OrderConfirmedEvent {
      eventId: string;
      eventType: 'OrderConfirmed';
      timestamp: string;
      data: {
        orderId: string;
        customerId: string;
        items: OrderItem[];
        totalAmount: number;
        currency: string;
        shippingAddress: Address;
      };
      metadata: {
        correlationId: string;
        source: string;
      };
    }

    consumer.on('OrderConfirmed', async (event: OrderConfirmedEvent) => {
      console.log(`Order ${event.data.orderId} confirmed`);
      await processOrder(event.data);
    });
    ```
  </TabItem>
</Tabs>
```

Create the schema file:
```json
// domains/Orders/services/OrdersService/events/OrderConfirmed/schema.json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "$id": "OrderConfirmed",
  "title": "Order Confirmed Event",
  "type": "object",
  "required": ["eventId", "eventType", "timestamp", "data"],
  "properties": {
    "eventId": {
      "type": "string",
      "description": "Unique identifier for this event instance"
    },
    "eventType": {
      "type": "string",
      "const": "OrderConfirmed"
    },
    "timestamp": {
      "type": "string",
      "format": "date-time",
      "description": "ISO 8601 timestamp when event was created"
    },
    "data": {
      "type": "object",
      "required": ["orderId", "customerId", "items", "totalAmount"],
      "properties": {
        "orderId": {
          "type": "string",
          "description": "Unique order identifier"
        },
        "customerId": {
          "type": "string",
          "description": "Customer who placed the order"
        },
        "items": {
          "type": "array",
          "items": {
            "$ref": "#/definitions/OrderItem"
          }
        },
        "totalAmount": {
          "type": "number",
          "description": "Total order amount"
        },
        "currency": {
          "type": "string",
          "default": "USD"
        }
      }
    }
  },
  "definitions": {
    "OrderItem": {
      "type": "object",
      "required": ["sku", "quantity", "unitPrice"],
      "properties": {
        "sku": { "type": "string" },
        "name": { "type": "string" },
        "quantity": { "type": "integer", "minimum": 1 },
        "unitPrice": { "type": "number" }
      }
    }
  }
}
```

**Bad:**
```yaml
---
id: OrderConfirmed
name: Order Confirmed
version: 1.0.0
# No schema, no owners, no summary
---

Order was confirmed.

# No schema, no examples, no consumer guidance
```

### team-define-users
**Impact:** HIGH
**Category:** Users & Teams

Create user profiles for individuals who own or contribute to resources.

**Good:**
```bash
mkdir -p users
```

```yaml
# users/dboyne.md
---
id: dboyne
name: David Boyne
avatarUrl: https://avatars.githubusercontent.com/u/3268013
role: Principal Engineer
email: david@acme.com
slackDirectMessageUrl: https://acme.slack.com/team/U123456
---

## About

Principal Engineer working on the Orders domain and event-driven architecture
initiatives. Primary contact for architectural decisions and EventCatalog
questions.

## Areas of Ownership

- Orders domain architecture
- Event schema standards
- EventCatalog maintenance
```

```yaml
# users/asmith.md
---
id: asmith
name: Alice Smith
avatarUrl: https://avatars.githubusercontent.com/u/7654321
role: Senior Software Engineer
email: alice@acme.com
slackDirectMessageUrl: https://acme.slack.com/team/U789012
---

## About

Senior Engineer on the Inventory team, focusing on real-time stock management
and warehouse integrations.
```

**Bad:**
```yaml
# users/user1.md
---
id: user1
name: User 1
# No avatar, email, or contact info
---

# Unhelpful - users can't identify or contact this person
```

### team-define-teams
**Impact:** HIGH
**Category:** Users & Teams

Create team definitions to group users and enable team-based ownership.

**Good:**
```bash
mkdir -p teams
```

```yaml
# teams/orders-team.md
---
id: orders-team
name: Orders Team
summary: |
  Team responsible for all order processing systems including the OrdersService,
  InventoryService, and related events.
email: orders-team@acme.com
slackDirectMessageUrl: https://acme.slack.com/archives/C123ORDERS
members:
  - dboyne
  - asmith
  - bjohnson
---

## About

The Orders team owns and maintains all services within the Orders domain.
We're responsible for:

- Order lifecycle management
- Inventory tracking and reservations
- Fulfillment coordination

## Office Hours

We hold office hours every Tuesday at 2pm PT. Join us in #orders-support
on Slack to discuss integrations or ask questions.

## On-Call

For production issues, page us via PagerDuty: orders-oncall@acme.pagerduty.com
```

```yaml
# teams/platform-team.md
---
id: platform-team
name: Platform Team
summary: Maintains shared infrastructure, messaging systems, and EventCatalog.
email: platform@acme.com
members:
  - dboyne
  - cjones
---

## About

The Platform team provides shared services and tooling for all engineering teams.
```

**Bad:**
```yaml
# teams/team1.md
---
id: team1
name: Team 1
# No members, no contact info
---

# Can't contact this team or know who's on it
```

### team-assign-ownership
**Impact:** HIGH
**Category:** Users & Teams

Assign owners to all resources using user and team IDs defined in the users/ and teams/ folders.

**Good:**
```yaml
# In any resource frontmatter:
---
id: OrderConfirmed
name: Order Confirmed
version: 1.0.0
owners:
  - orders-team      # Team ownership (preferred for services/domains)
  - dboyne           # Individual backup/expert
  - asmith           # Additional contributor
---
```

Best practices:
- Assign at least one team owner for continuity
- Add individual owners for specific expertise
- Keep owner lists current during reorgs

**Bad:**
```yaml
---
id: OrderConfirmed
name: Order Confirmed
version: 1.0.0
owners:
  - john@email.com   # Should be user ID, not email
  - "Orders Team"    # Should be team ID, not display name
  - unknown-user     # References non-existent user
---

# Or no owners at all:
---
id: OrderConfirmed
name: Order Confirmed
version: 1.0.0
# No owners - no accountability
---
```

### bootstrap-example-content
**Impact:** MEDIUM
**Category:** Content Bootstrap

Add meaningful example content that demonstrates the catalog's value. Don't leave default placeholder text.

**Good:**
```yaml
# Create a realistic example event with your actual domain terminology
---
id: CustomerRegistered
name: Customer Registered
version: 1.0.0
summary: |
  Published when a new customer completes registration. Contains customer
  profile data and consent preferences for downstream services.
owners:
  - identity-team
---

## Overview

The `CustomerRegistered` event is the first event in a new customer's journey.
It triggers:

- Welcome email via [[service|NotificationService]]
- CRM record creation via [[service|CRMIntegration]]
- Analytics tracking via [[service|AnalyticsService]]

## When This Event Fires

This event is published when:
1. Customer completes registration form
2. Email is verified (if required)
3. Customer record is persisted to database

## Payload

<SchemaViewer file="schema.json" />

## Integration Checklist

New consumers should:

- [ ] Subscribe to the `customer-events` topic
- [ ] Handle idempotency (event may be delivered multiple times)
- [ ] Store the `customerId` for correlation with future events
- [ ] Respect consent preferences in payload
```

**Bad:**
```yaml
---
id: ExampleEvent
name: Example Event
version: 1.0.0
summary: This is an example event
---

## Overview

This is an example event for the catalog.

## TODO

Add real content later.

# Placeholder content provides no value
```

### bootstrap-channels
**Impact:** MEDIUM
**Category:** Content Bootstrap

Set up initial channel definitions for your messaging infrastructure.

**Good:**
```bash
mkdir -p channels/orders-domain-eventbus
```

```yaml
# channels/orders-domain-eventbus/index.mdx
---
id: orders-domain-eventbus
name: Orders Domain Event Bus
version: 1.0.0
summary: |
  Central event bus for all order-related events. Uses Apache Kafka
  with guaranteed ordering per order ID.
owners:
  - platform-team
address: orders.events.{env}
protocols:
  - kafka
parameters:
  env:
    description: Deployment environment
    enum:
      - dev
      - staging
      - prod
    default: dev
---

## Overview

The Orders Domain Event Bus is the primary channel for order lifecycle events.
All services in the [[domain|Orders]] domain publish and subscribe to this bus.

<ChannelInformation />

## Topics

| Topic | Purpose | Retention |
|-------|---------|-----------|
| `orders.events.{env}` | Order lifecycle events | 7 days |
| `orders.commands.{env}` | Order commands | 3 days |

## Connection Details

### Development
```
Bootstrap servers: kafka-dev.acme.internal:9092
Security: SASL_SSL
```

### Production
```
Bootstrap servers: kafka-prod.acme.internal:9092
Security: SASL_SSL with mTLS
```

## Access

Request access through the Platform team. You'll need:
1. Service account credentials
2. Network access (VPN or internal network)
3. Topic-level ACLs for your service
```

**Bad:**
```yaml
---
id: kafka-topic
name: Kafka Topic
version: 1.0.0
# No address, no protocols, no parameters
---

Events go here.

# Not useful - no connection details or purpose
```

### bootstrap-custom-docs
**Impact:** MEDIUM
**Category:** Content Bootstrap

Add getting started documentation in the docs/ folder to help new developers onboard.

**Good:**
```bash
mkdir -p docs/getting-started
```

```yaml
# docs/getting-started/01-introduction.mdx
---
id: introduction
title: Introduction to Our Event-Driven Architecture
sidebar:
  label: Introduction
  order: 1
---

## Welcome

Welcome to the ACME Event Catalog! This is your source of truth for understanding
our event-driven architecture.

## What You'll Find Here

<Tiles>
    <Tile
        icon="RectangleGroupIcon"
        href="/docs/domains"
        title="Domains"
        description="Business capabilities organized by domain"
    />
    <Tile
        icon="ServerIcon"
        href="/docs/services"
        title="Services"
        description="Microservices and their responsibilities"
    />
    <Tile
        icon="BoltIcon"
        href="/docs/events"
        title="Events"
        description="Events published across our platform"
    />
    <Tile
        icon="ArrowPathIcon"
        href="/docs/flows"
        title="Flows"
        description="Business process visualizations"
    />
</Tiles>

## Quick Start

1. **Explore a Domain**: Start with the [[domain|Orders]] domain to see how
   we organize services and events.

2. **Find an Event**: Search for events you need to consume or understand.

3. **Contact Owners**: Each resource has owners you can reach out to.

## Getting Help

- **Slack**: #event-catalog-support
- **Email**: platform-team@acme.com
- **Office Hours**: Tuesdays at 2pm PT
```

```yaml
# docs/getting-started/02-consuming-events.mdx
---
id: consuming-events
title: How to Consume Events
sidebar:
  label: Consuming Events
  order: 2
---

## Prerequisites

Before consuming events, you'll need:
- Kafka client library for your language
- Service account credentials (request from Platform team)
- Network access to Kafka brokers

## Step-by-Step Guide

<Steps>
  <Step title="Request credentials">
    Submit a request in #platform-support with:
    - Your service name
    - Topics you need to consume
    - Environment (dev/staging/prod)
  </Step>
  <Step title="Configure your consumer">
    ```typescript
    const kafka = new Kafka({
      clientId: 'your-service-name',
      brokers: ['kafka-dev.acme.internal:9092'],
      ssl: true,
      sasl: {
        mechanism: 'plain',
        username: process.env.KAFKA_USERNAME,
        password: process.env.KAFKA_PASSWORD
      }
    });
    ```
  </Step>
  <Step title="Subscribe to topics">
    ```typescript
    const consumer = kafka.consumer({ groupId: 'your-consumer-group' });
    await consumer.subscribe({ topic: 'orders.events.dev' });
    ```
  </Step>
  <Step title="Process events">
    ```typescript
    await consumer.run({
      eachMessage: async ({ topic, partition, message }) => {
        const event = JSON.parse(message.value.toString());
        await handleEvent(event);
      }
    });
    ```
  </Step>
</Steps>

## Best Practices

:::tip
Always implement idempotent handlers. Events may be delivered multiple times.
:::

:::warning
Never block the consumer thread with long-running operations. Use async processing.
:::
```

**Bad:**
```
docs/
└── README.md

# Contents:
This is the documentation folder.

# Not helpful - no actual onboarding content
```
