---
name: eventcatalog-sdk-usage
description: Correct usage of @eventcatalog/sdk for programmatic catalog manipulation. Use when writing generators, automation scripts, or migration tools that interact with EventCatalog. Covers SDK initialization, reading/writing resources, versioning, and file management.
license: MIT
metadata:
  author: eventcatalog
  version: "1.0.0"
---

# EventCatalog SDK Usage

Comprehensive guide for using `@eventcatalog/sdk` to programmatically read and write EventCatalog resources. Contains 15 rules across 5 categories, prioritized by impact.

## When to Apply

Reference these guidelines when:
- Writing generators for AsyncAPI/OpenAPI specifications
- Creating automation scripts for catalog updates
- Building migration scripts between catalog versions
- Developing custom integrations with EventCatalog

## Rule Categories by Priority

| Priority | Category | Impact | Prefix |
|----------|----------|--------|--------|
| 1 | SDK Initialization | CRITICAL | `init-` |
| 2 | Reading Resources | HIGH | `read-` |
| 3 | Writing Resources | HIGH | `write-` |
| 4 | Advanced Operations | MEDIUM | `advanced-` |
| 5 | Utility Functions | MEDIUM | `utility-` |

## Quick Reference

### 1. SDK Initialization (CRITICAL)

- `init-catalog-path` - Initialize SDK with correct catalog path
- `init-async-await` - All SDK methods are async, always use await
- `init-error-handling` - Handle undefined returns for missing resources

### 2. Reading Resources (HIGH)

- `read-single-resource` - Use get methods for single resource retrieval
- `read-multiple-resources` - Use plural methods with latestOnly option
- `read-version-check` - Check if version exists before operations
- `read-attach-schema` - Use attachSchema option when schema content is needed

### 3. Writing Resources (HIGH)

- `write-resource-structure` - Provide all required fields when writing
- `write-options` - Use options for path, override, and versioning
- `write-nested-resources` - Use ToService/ToDomain methods for nested resources
- `write-relationships` - Set up sends/receives relationships correctly

### 4. Advanced Operations (MEDIUM)

- `advanced-versioning` - Version resources before major updates
- `advanced-file-management` - Add files and schemas to resources
- `advanced-data-stores` - Connect services to data stores

### 5. Utility Functions (MEDIUM)

- `utility-dump-catalog` - Export entire catalog to JSON
- `utility-resource-path` - Get file paths for resources

---

## Detailed Rules

### init-catalog-path
**Impact:** CRITICAL
**Category:** SDK Initialization

Initialize the SDK with the absolute path to your EventCatalog directory. The SDK returns a collection of functions to interact with the catalog.

**Good:**
```typescript
import catalogSDK from '@eventcatalog/sdk';

// Use absolute path to catalog directory
const {
  getEvent,
  writeEvent,
  getService,
  writeService,
  // ... other methods
} = catalogSDK('/path/to/eventcatalog');

// Or use process.cwd() for current directory
const sdk = catalogSDK(process.cwd());
```

**Bad:**
```typescript
import catalogSDK from '@eventcatalog/sdk';

// Relative paths may not resolve correctly
const sdk = catalogSDK('./eventcatalog');

// Missing path argument
const sdk = catalogSDK();
```

### init-async-await
**Impact:** CRITICAL
**Category:** SDK Initialization

All SDK methods are asynchronous and return Promises. Always use async/await or .then() to handle them.

**Good:**
```typescript
import catalogSDK from '@eventcatalog/sdk';

const { getEvent, writeEvent, getEvents } = catalogSDK('/path/to/catalog');

async function processEvents() {
  // Single resource
  const event = await getEvent('OrderConfirmed', '1.0.0');

  // Multiple resources
  const allEvents = await getEvents({ latestOnly: true });

  // Writing
  await writeEvent({
    id: 'NewEvent',
    name: 'New Event',
    version: '1.0.0',
    markdown: '## Overview\n\nDescription here.'
  });
}

processEvents();
```

**Bad:**
```typescript
import catalogSDK from '@eventcatalog/sdk';

const { getEvent, writeEvent } = catalogSDK('/path/to/catalog');

// Missing await - returns Promise, not actual data
const event = getEvent('OrderConfirmed');
console.log(event.name); // undefined - event is a Promise

// Forgetting await on write operations
writeEvent({ id: 'NewEvent', version: '1.0.0' });
// Script may exit before write completes
```

### init-error-handling
**Impact:** CRITICAL
**Category:** SDK Initialization

SDK methods return undefined when resources don't exist. Always check for undefined before using the result.

**Good:**
```typescript
const { getEvent, getService } = catalogSDK('/path/to/catalog');

async function getEventDetails(id: string, version?: string) {
  const event = await getEvent(id, version);

  if (!event) {
    console.log(`Event ${id} not found`);
    return null;
  }

  return event;
}

// Or use optional chaining
const eventName = (await getEvent('OrderConfirmed'))?.name ?? 'Unknown';
```

**Bad:**
```typescript
const { getEvent } = catalogSDK('/path/to/catalog');

async function process() {
  const event = await getEvent('NonExistent');
  // Throws error if event is undefined
  console.log(event.name);
  console.log(event.version);
}
```

### read-single-resource
**Impact:** HIGH
**Category:** Reading Resources

Use singular get methods (getEvent, getService, etc.) to retrieve a single resource by ID. Optionally specify version for a specific version.

**Good:**
```typescript
const {
  getEvent,
  getCommand,
  getQuery,
  getService,
  getDomain,
  getChannel,
  getEntity
} = catalogSDK('/path/to/catalog');

// Get latest version
const event = await getEvent('OrderConfirmed');

// Get specific version (supports semver)
const eventV1 = await getEvent('OrderConfirmed', '1.0.0');
const eventLatestPatch = await getEvent('OrderConfirmed', '1.0.x');

// Works the same for all resource types
const service = await getService('InventoryService', '0.0.2');
const domain = await getDomain('Orders');
const channel = await getChannel('orders-domain-eventbus');
```

**Bad:**
```typescript
// Don't use getEvents when you need a single event
const events = await getEvents();
const event = events.find(e => e.id === 'OrderConfirmed');

// This is inefficient and loads all events into memory
```

### read-multiple-resources
**Impact:** HIGH
**Category:** Reading Resources

Use plural get methods (getEvents, getServices, etc.) to retrieve multiple resources. Use `latestOnly: true` to exclude versioned copies.

**Good:**
```typescript
const { getEvents, getServices, getDomains } = catalogSDK('/path/to/catalog');

// Get all events including all versions
const allEvents = await getEvents();

// Get only latest versions (recommended for most use cases)
const latestEvents = await getEvents({ latestOnly: true });

// Same pattern for other resources
const latestServices = await getServices({ latestOnly: true });
const latestDomains = await getDomains({ latestOnly: true });
```

**Bad:**
```typescript
// Without latestOnly, you get duplicates from versioned folders
const events = await getEvents();
// May return: OrderConfirmed@1.0.0, OrderConfirmed@1.0.1, OrderConfirmed@2.0.0
// when you likely only want the latest
```

### read-version-check
**Impact:** HIGH
**Category:** Reading Resources

Use version check methods (eventHasVersion, serviceHasVersion, etc.) before operations that depend on specific versions existing.

**Good:**
```typescript
const { eventHasVersion, writeEvent, versionEvent } = catalogSDK('/path/to/catalog');

async function updateEvent(id: string, newVersion: string) {
  // Check if version already exists
  const exists = await eventHasVersion(id, newVersion);

  if (exists) {
    console.log(`Version ${newVersion} already exists`);
    return;
  }

  // Version the current content first
  await versionEvent(id);

  // Then write the new version
  await writeEvent({
    id,
    name: 'Order Confirmed',
    version: newVersion,
    markdown: '## Updated content'
  });
}
```

**Bad:**
```typescript
// Writing without checking may overwrite existing content
await writeEvent({
  id: 'OrderConfirmed',
  version: '1.0.0', // May already exist!
  markdown: '## New content'
});
```

### read-attach-schema
**Impact:** HIGH
**Category:** Reading Resources

Use the `attachSchema: true` option when you need the schema file content included in the response.

**Good:**
```typescript
const { getEvent, getEvents } = catalogSDK('/path/to/catalog');

// Get single event with schema content
const event = await getEvent('OrderConfirmed', '1.0.0', {
  attachSchema: true
});
console.log(event.schema); // Contains the actual schema content

// Get multiple events with schemas
const events = await getEvents({
  latestOnly: true,
  attachSchema: true
});

events.forEach(e => {
  if (e.schema) {
    console.log(`${e.id} schema:`, e.schema);
  }
});
```

**Bad:**
```typescript
// Without attachSchema, schema property won't contain the file content
const event = await getEvent('OrderConfirmed');
console.log(event.schema); // undefined, even if schemaPath is set
console.log(event.schemaPath); // 'schema.json' - path only, no content
```

### write-resource-structure
**Impact:** HIGH
**Category:** Writing Resources

Provide all required fields when writing resources: `id`, `name`, `version`, and `markdown` for content.

**Good:**
```typescript
const { writeEvent, writeService } = catalogSDK('/path/to/catalog');

// Write an event
await writeEvent({
  id: 'OrderConfirmed',
  name: 'Order Confirmed',
  version: '1.0.0',
  summary: 'Indicates that an order has been successfully confirmed',
  owners: ['order-team'],
  markdown: `## Overview

The Order Confirmed event is published when an order is successfully placed.

## Schema

<SchemaViewer file="schema.json" />
`
});

// Write a service
await writeService({
  id: 'OrdersService',
  name: 'Orders Service',
  version: '1.0.0',
  summary: 'Handles order processing',
  sends: [
    { id: 'OrderConfirmed', version: '1.0.0' }
  ],
  receives: [
    { id: 'PaymentReceived', version: '1.0.0' }
  ],
  markdown: '## Overview\n\nService documentation here.'
});
```

**Bad:**
```typescript
// Missing required fields
await writeEvent({
  id: 'OrderConfirmed'
  // Missing: name, version, markdown
});

// Empty markdown creates useless documentation
await writeEvent({
  id: 'OrderConfirmed',
  name: 'Order Confirmed',
  version: '1.0.0',
  markdown: ''
});
```

### write-options
**Impact:** HIGH
**Category:** Writing Resources

Use write options to control behavior: `path` for custom location, `override` to replace existing, `versionExistingContent` to auto-version.

**Good:**
```typescript
const { writeEvent, writeService } = catalogSDK('/path/to/catalog');

// Override existing content
await writeEvent(
  {
    id: 'OrderConfirmed',
    name: 'Order Confirmed',
    version: '1.0.0',
    markdown: '## Updated content'
  },
  { override: true }
);

// Auto-version existing before writing new
await writeEvent(
  {
    id: 'OrderConfirmed',
    name: 'Order Confirmed',
    version: '2.0.0',
    markdown: '## New version'
  },
  { versionExistingContent: true }
);

// Custom path (e.g., within a domain)
await writeService(
  {
    id: 'PaymentService',
    name: 'Payment Service',
    version: '1.0.0',
    markdown: '## Overview'
  },
  { path: 'domains/Payment/services' }
);

// Use MDX format explicitly
await writeEvent(event, { format: 'mdx' });
```

**Bad:**
```typescript
// Without override, may fail if resource exists
await writeEvent(event);
// Error: Resource already exists

// Forgetting to version before major updates
await writeEvent({
  id: 'OrderConfirmed',
  version: '2.0.0', // Overwrites 1.0.0 without keeping history
  markdown: '## Breaking changes'
});
```

### write-nested-resources
**Impact:** HIGH
**Category:** Writing Resources

Use specialized methods to write resources nested within services or domains.

**Good:**
```typescript
const {
  writeEventToService,
  writeCommandToService,
  writeServiceToDomain
} = catalogSDK('/path/to/catalog');

// Write event nested within a service directory
await writeEventToService(
  {
    id: 'InventoryAdjusted',
    name: 'Inventory Adjusted',
    version: '1.0.0',
    markdown: '## Overview'
  },
  { id: 'InventoryService', version: '1.0.0' }
);

// Write command to a service
await writeCommandToService(
  {
    id: 'UpdateInventory',
    name: 'Update Inventory',
    version: '1.0.0',
    markdown: '## Overview'
  },
  { id: 'InventoryService' }
);

// Write service to a domain
await writeServiceToDomain(
  {
    id: 'PaymentService',
    name: 'Payment Service',
    version: '1.0.0',
    markdown: '## Overview'
  },
  { id: 'Payment' }
);
```

**Bad:**
```typescript
// Don't manually construct paths for nested resources
await writeEvent(event, {
  path: 'domains/Orders/services/InventoryService/events'
});
// Use writeEventToService instead
```

### write-relationships
**Impact:** HIGH
**Category:** Writing Resources

Set up message relationships using `sends` and `receives` arrays with message IDs and optional channel routing.

**Good:**
```typescript
const { writeService } = catalogSDK('/path/to/catalog');

await writeService({
  id: 'InventoryService',
  name: 'Inventory Service',
  version: '1.0.0',
  // Messages this service produces
  sends: [
    {
      id: 'InventoryAdjusted',
      version: '1.0.0',
      to: [{ id: 'orders-domain-eventbus' }]
    },
    {
      id: 'OutOfStock',
      version: '1.0.0',
      to: [{ id: 'orders-domain-eventbus' }]
    }
  ],
  // Messages this service consumes
  receives: [
    {
      id: 'OrderConfirmed',
      version: '1.0.0',
      from: [{ id: 'orders-domain-eventbus' }]
    },
    {
      id: 'UpdateInventory',
      version: '1.0.0',
      from: [{ id: 'inventory-domain-eventbus' }]
    }
  ],
  markdown: '## Overview'
});
```

**Bad:**
```typescript
// Don't use string arrays for relationships
await writeService({
  id: 'InventoryService',
  sends: ['InventoryAdjusted', 'OutOfStock'], // Wrong type
  receives: ['OrderConfirmed'], // Wrong type
});

// Missing version in relationship
await writeService({
  id: 'InventoryService',
  sends: [{ id: 'InventoryAdjusted' }], // Missing version
});
```

### advanced-versioning
**Impact:** MEDIUM
**Category:** Advanced Operations

Use version methods to move current content to versioned directory before making major updates.

**Good:**
```typescript
const {
  versionEvent,
  versionService,
  versionDomain,
  eventHasVersion,
  writeEvent
} = catalogSDK('/path/to/catalog');

async function publishNewVersion(eventId: string, newVersion: string) {
  // Check if new version already exists
  if (await eventHasVersion(eventId, newVersion)) {
    throw new Error(`Version ${newVersion} already exists`);
  }

  // Move current content to versioned folder
  await versionEvent(eventId);

  // Write new version as the latest
  await writeEvent({
    id: eventId,
    name: 'Order Confirmed',
    version: newVersion,
    markdown: '## New version content'
  });
}

// Version services before major updates
await versionService('InventoryService');

// Version domains
await versionDomain('Orders');
```

**Bad:**
```typescript
// Overwriting without versioning loses history
await writeEvent({
  id: 'OrderConfirmed',
  version: '2.0.0',
  markdown: '## Overwrites everything'
}, { override: true });
// Version 1.0.0 is lost forever
```

### advanced-file-management
**Impact:** MEDIUM
**Category:** Advanced Operations

Add files and schemas to resources using dedicated methods.

**Good:**
```typescript
const { addFileToEvent, addSchemaToEvent, addFileToService } = catalogSDK('/path/to/catalog');

// Add a schema file to an event
await addSchemaToEvent(
  'OrderConfirmed',
  {
    schema: JSON.stringify({
      type: 'object',
      properties: {
        orderId: { type: 'string' },
        customerId: { type: 'string' }
      }
    }, null, 2),
    fileName: 'schema.json'
  },
  '1.0.0' // Optional: specific version
);

// Add a generic file to an event
await addFileToEvent(
  'OrderConfirmed',
  {
    content: '# Example\n\nExample payload here.',
    fileName: 'example.md'
  }
);

// Add file to a service
await addFileToService(
  'InventoryService',
  {
    content: 'openapi: 3.0.0\ninfo:\n  title: Inventory API',
    fileName: 'openapi.yaml'
  }
);
```

**Bad:**
```typescript
// Don't try to write files directly to disk
import fs from 'fs';
fs.writeFileSync('events/OrderConfirmed/schema.json', schemaContent);
// Bypasses SDK and may cause issues

// Include schema in markdown instead of proper file
await writeEvent({
  id: 'OrderConfirmed',
  version: '1.0.0',
  markdown: `## Schema\n\`\`\`json\n${schemaContent}\n\`\`\``
});
// SchemaViewer component won't work
```

### advanced-data-stores
**Impact:** MEDIUM
**Category:** Advanced Operations

Connect services to data stores (containers) using relationship methods.

**Good:**
```typescript
const {
  writeDataStore,
  writeService,
  addDataStoreToService
} = catalogSDK('/path/to/catalog');

// Create a data store
await writeDataStore({
  id: 'orders-db',
  name: 'Orders Database',
  version: '1.0.0',
  container_type: 'database',
  technology: 'PostgreSQL',
  markdown: '## Overview\n\nPrimary database for order data.'
});

// Link service to data store via relationship
await addDataStoreToService(
  'OrdersService',
  'writesTo',
  { id: 'orders-db', version: '1.0.0' }
);

await addDataStoreToService(
  'OrdersService',
  'readsFrom',
  { id: 'orders-db', version: '1.0.0' }
);

// Or include in service definition
await writeService({
  id: 'OrdersService',
  name: 'Orders Service',
  version: '1.0.0',
  writesTo: [{ id: 'orders-db', version: '1.0.0' }],
  readsFrom: [{ id: 'orders-db', version: '1.0.0' }],
  markdown: '## Overview'
});
```

**Bad:**
```typescript
// Don't document data stores only in markdown
await writeService({
  id: 'OrdersService',
  markdown: '## Data Stores\n\nThis service uses PostgreSQL.'
  // No structured relationship - won't appear in visualizations
});
```

### utility-dump-catalog
**Impact:** MEDIUM
**Category:** Utility Functions

Use `dumpCatalog` to export the entire catalog to JSON for analysis or migration.

**Good:**
```typescript
const { dumpCatalog } = catalogSDK('/path/to/catalog');

// Export catalog structure
const catalog = await dumpCatalog();

console.log('Events:', catalog.events.length);
console.log('Services:', catalog.services.length);
console.log('Domains:', catalog.domains.length);

// Include markdown content in export
const catalogWithContent = await dumpCatalog({
  includeMarkdown: true
});

// Save to file for backup
import fs from 'fs';
fs.writeFileSync(
  'catalog-backup.json',
  JSON.stringify(catalog, null, 2)
);
```

**Bad:**
```typescript
// Don't manually traverse the file system
import glob from 'glob';
const files = glob.sync('**/index.mdx');
// Use dumpCatalog instead for reliable structure
```

### utility-resource-path
**Impact:** MEDIUM
**Category:** Utility Functions

Use `getResourcePath` to find the file system location of any resource.

**Good:**
```typescript
const { getResourcePath, getResourceFolderName } = catalogSDK('/path/to/catalog');

// Get full path info for a resource
const pathInfo = await getResourcePath('/path/to/catalog', 'OrderConfirmed', '1.0.0');

if (pathInfo) {
  console.log('Full path:', pathInfo.fullPath);
  console.log('Relative:', pathInfo.relativePath);
  console.log('Directory:', pathInfo.directory);
}

// Get just the folder name
const folderName = await getResourceFolderName('/path/to/catalog', 'OrderConfirmed');
console.log('Folder:', folderName); // 'OrderConfirmed'
```

**Bad:**
```typescript
// Don't assume path structure
const assumedPath = `events/${eventId}/index.mdx`;
// Events could be nested in services or domains
```
