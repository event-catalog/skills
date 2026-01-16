---
name: eventcatalog-components-reference
description: Reference guide for EventCatalog's built-in MDX components. Use when adding architecture diagrams, displaying schemas and message tables, creating navigation tiles, or adding collapsible sections. Covers visualization, data display, layout, and content components.
license: MIT
metadata:
  author: eventcatalog
  version: "1.0.0"
---

# EventCatalog Components Reference

Comprehensive reference for EventCatalog's built-in MDX components. Contains 15 rules across 5 categories, prioritized by impact.

## When to Apply

Reference these guidelines when:
- Adding architecture diagrams and visualizations
- Displaying message tables and schema viewers
- Creating navigation tiles and step-by-step guides
- Adding collapsible sections and admonitions
- Accessing frontmatter data in MDX

## Rule Categories by Priority

| Priority | Category | Impact | Prefix |
|----------|----------|--------|--------|
| 1 | Visualization Components | CRITICAL | `viz-` |
| 2 | Data Display Components | HIGH | `data-` |
| 3 | Layout Components | MEDIUM | `layout-` |
| 4 | Content Components | MEDIUM | `content-` |
| 5 | Advanced Components | LOW | `advanced-` |

## Quick Reference

### 1. Visualization Components (CRITICAL)

- `viz-nodegraph` - NodeGraph for architecture diagrams
- `viz-entitymap` - EntityMap for entity relationships
- `viz-flow` - Flow component for process visualization

### 2. Data Display Components (HIGH)

- `data-messagetable` - MessageTable for listing messages
- `data-schemaviewer` - SchemaViewer for interactive schema display
- `data-entityproperties` - EntityPropertiesTable for entity fields
- `data-channelinformation` - ChannelInformation for channel details

### 3. Layout Components (MEDIUM)

- `layout-tiles` - Tiles and Tile for navigation cards
- `layout-steps` - Steps and Step for procedural guides
- `layout-accordion` - Accordion for collapsible sections

### 4. Content Components (MEDIUM)

- `content-schema` - Schema for raw schema display
- `content-resourcegrouptable` - ResourceGroupTable for related resources
- `content-admonitions` - Admonitions for callouts and warnings

### 5. Advanced Components (LOW)

- `advanced-frontmatter` - Access frontmatter data in MDX
- `advanced-tabs` - Tabs for code examples in multiple languages

---

## Detailed Rules

### viz-nodegraph
**Impact:** CRITICAL
**Category:** Visualization Components

Use `<NodeGraph />` to display an automatic architecture diagram showing the current resource and its relationships (producers, consumers, channels).

**Good:**
```mdx
## Overview

The Inventory Service manages product stock levels and inventory movements.

## Architecture diagram

<NodeGraph />

<!-- Optional: Add a title -->
<NodeGraph title="Service Dependencies" />
```

The NodeGraph automatically:
- Shows the current resource as the central node
- Displays connected services, events, commands, and queries
- Visualizes channel connections
- Links to related documentation

**Bad:**
```mdx
## Architecture

Here's a diagram of the service:

![architecture](./architecture.png)
<!-- Static image doesn't update when relationships change -->

<!-- Or manual ASCII art -->
```
[InventoryService] --> [InventoryAdjusted]
                   --> [OutOfStock]
```
<!-- Doesn't link to actual resources -->
```

### viz-entitymap
**Impact:** CRITICAL
**Category:** Visualization Components

Use `<EntityMap />` to display entity relationships within a domain. Shows how entities reference each other.

**Good:**
```mdx
## Domain Entities

This domain contains the following entities and their relationships:

<EntityMap title="Orders Domain Entity Map" />
```

The EntityMap automatically:
- Shows all entities in the current domain
- Visualizes relationships (hasOne, hasMany, belongsTo)
- Displays property types and references
- Links to entity documentation

**Bad:**
```mdx
## Entities

- Order: has many OrderItems
- OrderItem: belongs to Order
- Customer: has many Orders

<!-- Manual list doesn't visualize relationships -->
```

### viz-flow
**Impact:** CRITICAL
**Category:** Visualization Components

Use `<Flow />` to embed a business process flow diagram from a defined flow resource.

**Good:**
```mdx
## Order Processing Flow

The following diagram shows how orders are processed:

<Flow id="OrderProcessing" version="latest" />

<!-- With additional options -->
<Flow
  id="CancelSubscription"
  version="1.0.0"
  includeKey={true}      <!-- Show legend -->
/>
```

**Bad:**
```mdx
## Order Processing Flow

1. Customer places order
2. Inventory is checked
3. Payment is processed
4. Order is confirmed

<!-- Text list doesn't visualize branching or relationships -->

<!-- Or static image -->
![flow](./order-flow.png)
<!-- Doesn't link to services, messages, or update automatically -->
```

### data-messagetable
**Impact:** HIGH
**Category:** Data Display Components

Use `<MessageTable />` to display a table of messages (events, commands, queries) related to the current resource.

**Good:**
```mdx
## Messages

All messages that flow through this service:

<MessageTable format="all" />

<!-- Events only -->
<MessageTable format="events" />

<!-- Commands only -->
<MessageTable format="commands" />

<!-- With options -->
<MessageTable
  format="all"
  limit={10}                <!-- Limit number shown -->
  showChannels={true}       <!-- Show channel column -->
  title="Service Messages"  <!-- Custom title -->
/>
```

**Bad:**
```mdx
## Messages

| Event | Description |
|-------|-------------|
| InventoryAdjusted | Inventory changed |
| OutOfStock | Item out of stock |

<!-- Manual table doesn't link to resources or update automatically -->
```

### data-schemaviewer
**Impact:** HIGH
**Category:** Data Display Components

Use `<SchemaViewer />` to display an interactive, collapsible view of a schema file (JSON Schema, Avro, etc.).

**Good:**
```mdx
## Message Schema

<SchemaViewer
  file="schema.json"
  title="Order Confirmed Schema"
  maxHeight="500"          <!-- Limit height with scroll -->
/>

<!-- Avro schema -->
<SchemaViewer file="schema.avro" title="Avro Schema" />

<!-- YAML schema -->
<SchemaViewer file="schema.yml" />

<!-- Show required fields -->
<SchemaViewer file="schema.json" showRequired={true} />
```

**Bad:**
```mdx
## Schema

```json
{
  "type": "object",
  "properties": {
    "orderId": { "type": "string" },
    "amount": { "type": "number" }
  }
}
```
<!-- Raw code block isn't collapsible or interactive -->
```

### data-entityproperties
**Impact:** HIGH
**Category:** Data Display Components

Use `<EntityPropertiesTable />` to display a formatted table of entity properties with types, descriptions, and relationships.

**Good:**
```mdx
## Entity Properties

<EntityPropertiesTable />
```

This automatically renders properties from the frontmatter:

```yaml
---
id: Order
properties:
  - name: orderId
    type: UUID
    required: true
    description: Unique identifier
  - name: customerId
    type: UUID
    references: Customer
    relationType: hasOne
---
```

**Bad:**
```mdx
## Properties

| Property | Type | Description |
|----------|------|-------------|
| orderId | UUID | Unique identifier |
| customerId | UUID | Customer reference |

<!-- Manual table doesn't show relationships or required status -->
```

### data-channelinformation
**Impact:** HIGH
**Category:** Data Display Components

Use `<ChannelInformation />` in channel documentation to display protocols, parameters, and address information.

**Good:**
```mdx
## Channel Details

<ChannelInformation />
```

This renders from frontmatter:

```yaml
---
id: inventory.{env}.events
address: inventory.{env}.events
protocols:
  - kafka
parameters:
  env:
    enum: [dev, staging, prod]
    description: Environment
---
```

**Bad:**
```mdx
## Channel Details

**Address:** `inventory.{env}.events`
**Protocols:** Kafka
**Parameters:** env (dev, staging, prod)

<!-- Manual text doesn't format consistently -->
```

### layout-tiles
**Impact:** MEDIUM
**Category:** Layout Components

Use `<Tiles>` and `<Tile>` to create navigation cards with icons, linking to important resources or actions.

**Good:**
```mdx
<Tiles>
    <Tile
        icon="UserGroupIcon"
        href="/docs/teams/order-management"
        title="Contact the team"
        description="Questions? Contact the owners"
    />
    <Tile
        icon="DocumentIcon"
        href={`/docs/services/${frontmatter.id}/${frontmatter.version}/changelog`}
        title="View changelog"
        description="See version history"
    />
    <Tile
        icon="BoltIcon"
        href={`/visualiser/services/${frontmatter.id}/${frontmatter.version}`}
        title={`Sends ${frontmatter.sends?.length || 0} messages`}
        description="View in visualizer"
    />
    <Tile
        icon="CodeBracketIcon"
        href={frontmatter.repository?.url}
        title="View source"
        description="See the code on GitHub"
    />
</Tiles>
```

Available icons: UserGroupIcon, DocumentIcon, BoltIcon, CodeBracketIcon, RectangleGroupIcon, ShieldCheckIcon, and more from Heroicons.

**Bad:**
```mdx
## Quick Links

- [Contact team](/docs/teams/order-management)
- [Changelog](/docs/services/InventoryService/changelog)
- [Visualizer](/visualiser/services/InventoryService)

<!-- Plain links don't have visual impact -->
```

### layout-steps
**Impact:** MEDIUM
**Category:** Layout Components

Use `<Steps>` and `<Step>` for procedural instructions like setup guides or integration steps.

**Good:**
```mdx
<Steps title="How to integrate with this service">
  <Step title="Obtain API credentials">
    Request credentials from the service team via Slack or email.
  </Step>
  <Step title="Install the SDK">
    ```bash
    npm install @acme/inventory-sdk
    ```
  </Step>
  <Step title="Configure the client">
    ```typescript
    import { InventoryClient } from '@acme/inventory-sdk';

    const client = new InventoryClient({
      apiKey: process.env.INVENTORY_API_KEY,
      environment: 'production'
    });
    ```
  </Step>
  <Step title="Make your first request">
    ```typescript
    const inventory = await client.getInventory('SKU-123');
    console.log(inventory.quantity);
    ```
  </Step>
</Steps>
```

**Bad:**
```mdx
## Integration Steps

1. Get credentials
2. Install SDK: `npm install @acme/inventory-sdk`
3. Configure client
4. Make request

<!-- Numbered list doesn't visually separate steps -->
```

### layout-accordion
**Impact:** MEDIUM
**Category:** Layout Components

Use `<Accordion>` for collapsible sections, useful for FAQs, detailed explanations, or optional information.

**Good:**
```mdx
## Frequently Asked Questions

<Accordion title="How do I handle out-of-stock events?">
  When you receive an `OutOfStock` event, you should:
  1. Update your local inventory cache
  2. Disable purchasing for the affected SKU
  3. Optionally notify customers with items in cart
</Accordion>

<Accordion title="What happens if the message fails to process?">
  The service uses a dead-letter queue for failed messages.
  After 3 retries, messages are moved to the DLQ for manual inspection.
</Accordion>

<Accordion title="How do I request increased rate limits?">
  Contact the platform team with your use case and expected volume.
  Standard limits are 1000 requests/minute.
</Accordion>
```

**Bad:**
```mdx
## FAQ

**Q: How do I handle out-of-stock events?**
A: Update cache, disable purchasing, notify customers.

**Q: What happens if processing fails?**
A: Messages go to DLQ after 3 retries.

<!-- All content visible - can be overwhelming -->
```

### content-schema
**Impact:** MEDIUM
**Category:** Content Components

Use `<Schema>` to display raw schema content with syntax highlighting. Different from SchemaViewer which is interactive.

**Good:**
```mdx
## Raw Schema

<Schema file="schema.json" title="Order Confirmed Schema (JSON)" />

<!-- Or with language specification -->
<Schema file="schema.avro" language="json" title="Avro Schema" />
```

**Bad:**
```mdx
## Schema

```json
{
  // Manually copied schema content
  // May get out of sync with actual schema file
}
```
```

### content-resourcegrouptable
**Impact:** MEDIUM
**Category:** Content Components

Use `<ResourceGroupTable>` to display tables of related resources defined in frontmatter resourceGroups.

**Good:**
```yaml
# In frontmatter:
---
id: Orders
resourceGroups:
  - id: related-resources
    title: Core Resources
    items:
      - id: Order
        type: entity
      - id: OrdersService
        type: service
      - id: OrderConfirmed
        type: event
    limit: 10
---
```

```mdx
## Related Resources

<ResourceGroupTable
  id="related-resources"
  limit={5}
  showOwners={true}
  title="Core resources for this domain"
  description="Resources that are essential to the Orders domain"
/>
```

**Bad:**
```mdx
## Related Resources

- [Order entity](/docs/entities/Order)
- [OrdersService](/docs/services/OrdersService)
- [OrderConfirmed event](/docs/events/OrderConfirmed)

<!-- Manual list doesn't show metadata like owners -->
```

### content-admonitions
**Impact:** MEDIUM
**Category:** Content Components

Use admonitions (:::note, :::warning, :::danger, :::tip) for callouts that highlight important information.

**Good:**
```mdx
:::note
This service requires authentication. See the [auth guide](/docs/auth) for details.
:::

:::warning
Please ensure all services are **updated** to the latest version for compatibility.
:::

:::danger
This endpoint is deprecated and will be removed in v3.0. Migrate to the new endpoint before March 2025.
:::

:::tip
For better performance, batch your inventory updates rather than sending individual requests.
:::
```

**Bad:**
```mdx
**Note:** This service requires authentication.

**Warning:** Update to latest version.

**Important:** Endpoint deprecated.

<!-- Plain bold text doesn't stand out -->
```

### advanced-frontmatter
**Impact:** LOW
**Category:** Advanced Components

Access frontmatter data in MDX using the `frontmatter` object. Useful for dynamic content.

**Good:**
```mdx
---
id: InventoryService
name: Inventory Service
version: 0.0.2
sends:
  - id: InventoryAdjusted
  - id: OutOfStock
receives:
  - id: OrderConfirmed
  - id: UpdateInventory
---

## Overview

The **{frontmatter.name}** (v{frontmatter.version}) manages inventory operations.

<Tiles>
    <Tile
        title={`Sends ${frontmatter.sends?.length || 0} messages`}
        href={`/visualiser/services/${frontmatter.id}/${frontmatter.version}`}
    />
    <Tile
        title={`Receives ${frontmatter.receives?.length || 0} messages`}
        href={`/visualiser/services/${frontmatter.id}/${frontmatter.version}`}
    />
</Tiles>

<!-- Dynamic link to schema -->
<Tile
    href={`/generated/events/${frontmatter.id}/schema.json`}
    title="View schema"
/>
```

**Bad:**
```mdx
## Overview

The **Inventory Service** (v0.0.2) manages inventory operations.

<!-- Hardcoded values get out of sync with frontmatter -->
<!-- When version updates, this text needs manual update -->
```

### advanced-tabs
**Impact:** LOW
**Category:** Advanced Components

Use `<Tabs>` and `<TabItem>` to show code examples in multiple languages or frameworks.

**Good:**
```mdx
## Consuming the Event

<Tabs>
  <TabItem title="Python">
    ```python
    from kafka import KafkaConsumer
    import json

    consumer = KafkaConsumer(
        'inventory.adjusted',
        bootstrap_servers=['localhost:9092'],
        value_deserializer=lambda v: json.loads(v.decode('utf-8'))
    )

    for message in consumer:
        print(f"Received: {message.value}")
    ```
  </TabItem>
  <TabItem title="TypeScript">
    ```typescript
    import { Kafka } from 'kafkajs';

    const kafka = new Kafka({ brokers: ['localhost:9092'] });
    const consumer = kafka.consumer({ groupId: 'my-group' });

    await consumer.subscribe({ topic: 'inventory.adjusted' });
    await consumer.run({
      eachMessage: async ({ message }) => {
        console.log('Received:', message.value?.toString());
      },
    });
    ```
  </TabItem>
  <TabItem title="Java">
    ```java
    Properties props = new Properties();
    props.put("bootstrap.servers", "localhost:9092");
    props.put("group.id", "my-group");

    KafkaConsumer<String, String> consumer = new KafkaConsumer<>(props);
    consumer.subscribe(List.of("inventory.adjusted"));

    while (true) {
        ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(100));
        for (ConsumerRecord<String, String> record : records) {
            System.out.println("Received: " + record.value());
        }
    }
    ```
  </TabItem>
</Tabs>
```

**Bad:**
```mdx
## Consuming the Event

### Python

```python
# Python code
```

### TypeScript

```typescript
// TypeScript code
```

### Java

```java
// Java code
```

<!-- Separate sections don't show language options side by side -->
<!-- Takes more vertical space and harder to compare -->
```
