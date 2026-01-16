---
name: event-driven-architecture-documentation
description: EDA and DDD best practices applied to EventCatalog documentation. Use when deciding between message types (event vs command), documenting service relationships, setting up channels, or modeling domains. Covers message semantics, service patterns, and domain-driven design concepts.
license: MIT
metadata:
  author: eventcatalog
  version: "1.0.0"
---

# Event-Driven Architecture Documentation

Comprehensive guide for applying EDA and DDD best practices to EventCatalog documentation. Contains 15 rules across 5 categories, prioritized by impact.

## When to Apply

Reference these guidelines when:
- Deciding whether to use an event, command, or query
- Documenting service relationships (producers/consumers)
- Setting up channels for message routing
- Designing domain and subdomain structure
- Modeling entities and aggregate roots

## Rule Categories by Priority

| Priority | Category | Impact | Prefix |
|----------|----------|--------|--------|
| 1 | Message Types | CRITICAL | `msg-` |
| 2 | Service Relationships | HIGH | `svc-` |
| 3 | Channel Documentation | HIGH | `channel-` |
| 4 | Flow Documentation | MEDIUM | `flow-` |
| 5 | Entity & Domain Modeling | MEDIUM | `model-` |

## Quick Reference

### 1. Message Types (CRITICAL)

- `msg-events-past-tense` - Events describe facts that happened (past tense)
- `msg-commands-imperative` - Commands are requests for action (imperative)
- `msg-queries-read-only` - Queries request data without side effects
- `msg-naming-conventions` - Use consistent naming patterns for message types

### 2. Service Relationships (HIGH)

- `svc-sends-receives` - Document producer/consumer relationships explicitly
- `svc-channel-routing` - Specify which channels messages flow through
- `svc-data-store-connections` - Document read/write relationships to data stores

### 3. Channel Documentation (HIGH)

- `channel-address-protocols` - Specify address patterns and protocols
- `channel-parameters` - Define parameterized channel segments
- `channel-message-routing` - Document which messages flow through channels

### 4. Flow Documentation (MEDIUM)

- `flow-step-types` - Use appropriate step types (service, message, actor)
- `flow-branching` - Document decision points with next_steps
- `flow-external-systems` - Include external system integrations

### 5. Entity & Domain Modeling (MEDIUM)

- `model-entity-properties` - Define entity properties with types and relationships
- `model-aggregate-roots` - Mark aggregate root entities appropriately
- `model-bounded-contexts` - Organize domains around bounded contexts

---

## Detailed Rules

### msg-events-past-tense
**Impact:** CRITICAL
**Category:** Message Types

Events represent facts that have already occurred. Name them in past tense and include when/where/what happened.

**Good:**
```yaml
# Events describe something that happened
---
id: OrderConfirmed
name: Order Confirmed
summary: Indicates that an order has been successfully confirmed by the system
---

---
id: PaymentReceived
name: Payment Received
summary: Published when a payment has been successfully processed
---

---
id: InventoryAdjusted
name: Inventory Adjusted
summary: Indicates a change in inventory level has occurred
---

---
id: UserSignedUp
name: User Signed Up
summary: Published when a new user has completed registration
---
```

**Bad:**
```yaml
# Present tense - sounds like command
---
id: ConfirmOrder
name: Confirm Order
---

# Future tense - uncertain
---
id: OrderWillBeShipped
name: Order Will Be Shipped
---

# Noun only - unclear semantics
---
id: Order
name: Order
---
```

### msg-commands-imperative
**Impact:** CRITICAL
**Category:** Message Types

Commands are requests for an action to be performed. Name them in imperative mood and include what action is requested.

**Good:**
```yaml
# Commands request an action
---
id: PlaceOrder
name: Place Order
summary: Request to create a new order for the specified items
---

---
id: UpdateInventory
name: Update Inventory
summary: Request to adjust inventory levels for a product
---

---
id: SendNotification
name: Send Notification
summary: Request to send a notification to a user
---

---
id: CancelShipment
name: Cancel Shipment
summary: Request to cancel a pending shipment
---
```

**Bad:**
```yaml
# Past tense - sounds like event
---
id: OrderPlaced
name: Order Placed
---

# Noun only - unclear semantics
---
id: Notification
name: Notification
---

# Question form - should be query
---
id: CanCancelOrder
name: Can Cancel Order
---
```

### msg-queries-read-only
**Impact:** CRITICAL
**Category:** Message Types

Queries request data and must not cause side effects. Name them with "Get" prefix or question-style naming.

**Good:**
```yaml
# Queries read data without modification
---
id: GetOrderStatus
name: Get Order Status
summary: Request current status of a specific order
---

---
id: GetInventoryList
name: Get Inventory List
summary: Request list of all inventory items with their quantities
---

---
id: GetUserProfile
name: Get User Profile
summary: Request user profile information by user ID
---

---
id: ListPendingOrders
name: List Pending Orders
summary: Request all orders in pending status
---
```

**Bad:**
```yaml
# Implies modification
---
id: UpdateAndGetOrder
name: Update And Get Order
---

# Imperative without Get prefix - sounds like command
---
id: OrderStatus
name: Order Status
---

# Past tense - sounds like event
---
id: OrderStatusRetrieved
name: Order Status Retrieved
---
```

### msg-naming-conventions
**Impact:** CRITICAL
**Category:** Message Types

Use consistent naming patterns across all message types. The name should clearly indicate the message type through its grammatical structure.

**Good:**
```yaml
# Clear pattern: Events = Past Tense
OrderConfirmed, PaymentProcessed, UserRegistered, InventoryDepleted

# Clear pattern: Commands = Imperative Verb + Noun
PlaceOrder, ProcessPayment, RegisterUser, UpdateInventory

# Clear pattern: Queries = Get/List/Find + Noun
GetOrder, ListUsers, FindAvailableInventory, GetPaymentHistory

# Domain-specific terminology
UserSignedUp           # Not: UserCreated (SignedUp is domain term)
InventoryAdjusted      # Not: InventoryChanged (Adjusted is specific)
OrderFulfilled         # Not: OrderCompleted (Fulfilled has meaning)
```

**Bad:**
```yaml
# Inconsistent patterns
Order                  # Is this event, command, or query?
DoOrder                # Unusual verb
OrderThing             # Vague
OrderHandler           # Implementation detail in name

# Technical names instead of domain names
DatabaseUpdated        # Internal implementation
KafkaMessageSent       # Infrastructure detail
APIResponseReceived    # Technical artifact
```

### svc-sends-receives
**Impact:** HIGH
**Category:** Service Relationships

Document which messages a service produces (`sends`) and consumes (`receives`). This creates the foundation for architecture visualization.

**Good:**
```yaml
---
id: InventoryService
name: Inventory Service
version: 1.0.0
summary: Manages product inventory levels

# Messages this service produces
sends:
  - id: InventoryAdjusted
    version: 1.0.0
  - id: OutOfStock
    version: 1.0.0
  - id: InventoryReplenished
    version: 1.0.0

# Messages this service consumes
receives:
  - id: OrderConfirmed
    version: 1.0.0
  - id: UpdateInventory
    version: 1.0.0
  - id: AddInventory
    version: 1.0.0
---
```

**Bad:**
```yaml
---
id: InventoryService
name: Inventory Service
version: 1.0.0
# No sends/receives - service appears isolated in visualizations
---

# Or documenting only in markdown
---
id: InventoryService
---
## Messages

This service sends InventoryAdjusted and receives OrderConfirmed.
<!-- Not structured - no visualization -->
```

### svc-channel-routing
**Impact:** HIGH
**Category:** Service Relationships

Specify which channels messages flow through using `to` and `from` properties in sends/receives.

**Good:**
```yaml
---
id: InventoryService
name: Inventory Service
version: 1.0.0

sends:
  - id: InventoryAdjusted
    version: 1.0.0
    to:
      - id: orders-domain-eventbus              # Primary channel
      - id: analytics-stream                     # Also sent to analytics
  - id: OutOfStock
    version: 1.0.0
    to:
      - id: orders-domain-eventbus
        parameters:
          env: prod                              # Channel parameters

receives:
  - id: OrderConfirmed
    version: 1.0.0
    from:
      - id: orders-domain-eventbus
        delivery_mode: push                      # How messages are delivered
  - id: UpdateInventory
    version: 1.0.0
    from:
      - id: inventory-command-queue
        delivery_mode: pull                      # Service pulls from queue
---
```

**Bad:**
```yaml
---
id: InventoryService
sends:
  - id: InventoryAdjusted
    # No channel specified - unclear how message is routed

receives:
  - id: OrderConfirmed
    # No source channel - unclear where messages come from
---
```

### svc-data-store-connections
**Impact:** HIGH
**Category:** Service Relationships

Document which data stores a service reads from and writes to using `readsFrom` and `writesTo` properties.

**Good:**
```yaml
---
id: InventoryService
name: Inventory Service
version: 1.0.0

# Data stores this service writes to
writesTo:
  - id: inventory-db
    version: 1.0.0
  - id: inventory-cache
    version: 1.0.0

# Data stores this service reads from
readsFrom:
  - id: inventory-db
    version: 1.0.0
  - id: inventory-cache
    version: 1.0.0
  - id: product-catalog-db    # Read-only access to another service's data
    version: 1.0.0
---
```

```yaml
# containers/inventory-db/index.mdx
---
id: inventory-db
name: Inventory Database
version: 1.0.0
container_type: database
technology: PostgreSQL
access_mode: readWrite
summary: Primary data store for inventory records
---
```

**Bad:**
```yaml
---
id: InventoryService
# No data store connections
# Database relationships not visible in architecture diagrams
---

# Or documenting only in markdown
---
id: InventoryService
---
## Infrastructure
Uses PostgreSQL for data storage.
<!-- Not structured - no visualization -->
```

### channel-address-protocols
**Impact:** HIGH
**Category:** Channel Documentation

Specify the channel address pattern and supported protocols. This enables runtime configuration and monitoring.

**Good:**
```yaml
---
id: inventory.{env}.events
name: Inventory Events Channel
version: 1.0.0
summary: Central event stream for inventory-related events

address: inventory.{env}.events    # Full address pattern
protocols:
  - kafka                          # Primary protocol
  - amqp                           # Alternative protocol
---

---
id: orders-api-gateway
name: Orders API Gateway
version: 1.0.0
address: /api/v1/orders
protocols:
  - http
  - https
---
```

**Bad:**
```yaml
---
id: inventory-events
name: Inventory Events
version: 1.0.0
# No address - consumers don't know where to connect
# No protocols - unclear how to integrate
---
```

### channel-parameters
**Impact:** HIGH
**Category:** Channel Documentation

Define parameterized channel segments with allowed values, defaults, and descriptions.

**Good:**
```yaml
---
id: inventory.{env}.{region}.events
name: Regional Inventory Events
version: 1.0.0
address: inventory.{env}.{region}.events

parameters:
  env:
    description: Deployment environment
    enum:
      - dev
      - staging
      - prod
    default: dev
    examples:
      - dev
      - prod
  region:
    description: Geographic region for the inventory
    enum:
      - us-east
      - us-west
      - eu-central
      - ap-southeast
    examples:
      - us-east
      - eu-central
---
```

**Bad:**
```yaml
---
id: inventory.prod.events
name: Inventory Events Prod
version: 1.0.0
---

---
id: inventory.dev.events
name: Inventory Events Dev
version: 1.0.0
---
# Duplicated channel definitions for each environment
# Should use parameters instead
```

### channel-message-routing
**Impact:** HIGH
**Category:** Channel Documentation

Document which messages flow through each channel using the `messages` field or by referencing the channel in message definitions.

**Good:**
```yaml
---
id: orders-domain-eventbus
name: Orders Domain Event Bus
version: 1.0.0
summary: Central event bus for all order-related events

# Explicitly list messages on this channel
messages:
  - collection: events
    id: OrderConfirmed
    version: 1.0.0
  - collection: events
    id: OrderCancelled
    version: 1.0.0
  - collection: events
    id: InventoryAdjusted
    version: 1.0.0
---
```

**Bad:**
```yaml
---
id: orders-domain-eventbus
name: Orders Domain Event Bus
version: 1.0.0
# No message list - unclear what flows through this channel
---

## Messages
- OrderConfirmed
- OrderCancelled
<!-- Only documented in markdown - not structured -->
```

### flow-step-types
**Impact:** MEDIUM
**Category:** Flow Documentation

Use appropriate step types in flow definitions: `service` for service invocations, `message` for events/commands, `actor` for human actions, `custom` for external systems.

**Good:**
```yaml
---
id: CancelSubscription
name: User Cancels Subscription
version: 1.0.0
summary: Flow for subscription cancellation process

steps:
  # Actor step - human initiates the flow
  - id: user_initiates
    title: User Cancels Subscription
    actor:
      name: User
      summary: End user requesting cancellation
    next_step: cancel_command

  # Message step - command is sent
  - id: cancel_command
    title: Cancel Subscription Command
    message:
      id: CancelSubscription
      version: 1.0.0
    next_step: subscription_service

  # Service step - service processes
  - id: subscription_service
    title: Subscription Service
    service:
      id: SubscriptionService
      version: 1.0.0
    next_steps:
      - id: payment_gateway
        label: Process refund
      - id: cancelled_event
        label: Cancellation complete

  # Custom step - external system
  - id: payment_gateway
    title: Stripe Payment Gateway
    externalSystem:
      name: Stripe
      summary: Third-party payment processor
      url: https://stripe.com
    next_step: cancelled_event

  # Message step - event is published
  - id: cancelled_event
    title: Subscription Cancelled
    message:
      id: SubscriptionCancelled
      version: 1.0.0
---
```

**Bad:**
```yaml
---
id: CancelSubscription
steps:
  # All steps as generic nodes
  - id: step1
    title: Cancel
    next_step: step2
  - id: step2
    title: Process
    next_step: step3
  - id: step3
    title: Done
# No typed steps - no link to actual resources
---
```

### flow-branching
**Impact:** MEDIUM
**Category:** Flow Documentation

Document decision points using `next_steps` (plural) with labeled branches showing different paths.

**Good:**
```yaml
---
id: OrderProcessing
name: Order Processing Flow
version: 1.0.0

steps:
  - id: check_inventory
    title: Check Inventory
    service:
      id: InventoryService
      version: 1.0.0
    # Multiple outcomes with clear labels
    next_steps:
      - id: reserve_inventory
        label: Inventory available
      - id: backorder
        label: Insufficient stock
      - id: notify_customer
        label: Product discontinued

  - id: reserve_inventory
    title: Reserve Inventory
    message:
      id: InventoryReserved
      version: 1.0.0
    next_step: process_payment

  - id: backorder
    title: Create Backorder
    service:
      id: BackorderService
      version: 1.0.0
    next_step: notify_customer

  - id: notify_customer
    title: Send Notification
    service:
      id: NotificationService
      version: 1.0.0
    # Terminal step - no next_step
---
```

**Bad:**
```yaml
---
id: OrderProcessing
steps:
  - id: check_inventory
    title: Check Inventory
    next_step: reserve_inventory
    # Only one path - doesn't show failure scenarios

  - id: reserve_inventory
    title: Reserve Inventory
    next_step: process_payment
    # Linear flow - no decision points documented
---
```

### flow-external-systems
**Impact:** MEDIUM
**Category:** Flow Documentation

Include external system integrations in flows using the `externalSystem` type with name, summary, and optional URL.

**Good:**
```yaml
---
id: PaymentFlow
name: Payment Processing Flow
version: 1.0.0

steps:
  - id: payment_service
    title: Payment Service
    service:
      id: PaymentService
      version: 1.0.0
    next_step: stripe_gateway

  - id: stripe_gateway
    title: Stripe Payment Gateway
    externalSystem:
      name: Stripe
      summary: Third-party payment processing platform
      url: https://stripe.com
    next_step: payment_confirmed

  - id: fraud_check
    title: Fraud Detection
    custom:
      title: MaxMind Fraud Detection
      type: external-api
      icon: ShieldCheckIcon
      summary: Third-party fraud scoring service
      url: https://maxmind.com
      properties:
        sla: 99.9%
        latency: <100ms
    next_step: payment_confirmed
---
```

**Bad:**
```yaml
---
id: PaymentFlow
steps:
  - id: payment_service
    title: Payment Service
    service:
      id: PaymentService
    next_step: payment_confirmed
    # Stripe integration hidden - incomplete flow documentation
---
```

### model-entity-properties
**Impact:** MEDIUM
**Category:** Entity & Domain Modeling

Define entity properties with types, required flags, descriptions, and relationships to other entities.

**Good:**
```yaml
---
id: Order
name: Order
version: 1.0.0
identifier: orderId
summary: Represents a customer's request to purchase products

properties:
  - name: orderId
    type: UUID
    required: true
    description: Unique identifier for the order

  - name: customerId
    type: UUID
    required: true
    description: Reference to the customer placing the order
    references: Customer
    referencesIdentifier: customerId
    relationType: hasOne

  - name: status
    type: string
    required: true
    description: Current order status
    enum:
      - Pending
      - Processing
      - Shipped
      - Delivered
      - Cancelled

  - name: orderItems
    type: array
    required: true
    description: List of items in the order
    items:
      type: OrderItem
    references: OrderItem
    referencesIdentifier: orderItemId
    relationType: hasMany

  - name: totalAmount
    type: decimal
    required: true
    description: Total monetary value of the order
---
```

**Bad:**
```yaml
---
id: Order
name: Order
version: 1.0.0
# No properties defined - entity structure unknown
---

## Properties
- orderId: string
- customerId: string
- status: string
<!-- Documented only in markdown - not structured -->
```

### model-aggregate-roots
**Impact:** MEDIUM
**Category:** Entity & Domain Modeling

Mark entities that serve as aggregate roots with `aggregateRoot: true`. Define the identifier field.

**Good:**
```yaml
---
id: Order
name: Order
version: 1.0.0
aggregateRoot: true              # This is the aggregate root
identifier: orderId              # Primary identifier

properties:
  - name: orderId
    type: UUID
    required: true
  - name: orderItems              # Child entities
    type: array
    references: OrderItem
    relationType: hasMany
---

# OrderItem is NOT an aggregate root - accessed through Order
---
id: OrderItem
name: Order Item
version: 1.0.0
aggregateRoot: false             # Explicitly not a root

properties:
  - name: orderItemId
    type: UUID
    required: true
  - name: orderId
    type: UUID
    required: true
    references: Order
    relationType: belongsTo
---
```

**Bad:**
```yaml
---
id: Order
name: Order
version: 1.0.0
# aggregateRoot not specified - unclear if this is a root
# identifier not specified - unknown how to reference
---

---
id: OrderItem
name: Order Item
version: 1.0.0
aggregateRoot: true              # Wrong - items shouldn't be accessed independently
---
```

### model-bounded-contexts
**Impact:** MEDIUM
**Category:** Entity & Domain Modeling

Organize domains around bounded contexts. Use subdomains for distinct areas within a larger domain, each with its own ubiquitous language.

**Good:**
```yaml
# domains/E-Commerce/index.mdx
---
id: E-Commerce
name: E-Commerce Platform
version: 1.0.0
summary: Core e-commerce platform domain containing all commerce-related bounded contexts
---

# domains/E-Commerce/subdomains/Orders/index.mdx
---
id: Orders
name: Orders Context
version: 1.0.0
summary: |
  Bounded context for order processing. Owns the concept of Order,
  OrderItem, and order lifecycle management.

services:
  - id: OrdersService
  - id: InventoryService

entities:
  - id: Order
  - id: OrderItem
---

# domains/E-Commerce/subdomains/Payment/index.mdx
---
id: Payment
name: Payment Context
version: 1.0.0
summary: |
  Bounded context for payment processing. Owns Payment, Transaction,
  and payment method concepts.

services:
  - id: PaymentService
  - id: FraudDetectionService

entities:
  - id: Payment
  - id: Transaction
---
```

**Bad:**
```yaml
# Flat domain structure - no bounded contexts
domains/
├── Orders/
├── Payment/
├── Inventory/
└── Shipping/
# Each treated as separate domain instead of contexts within E-Commerce

# Or monolithic domain with everything
---
id: E-Commerce
services:
  - id: OrdersService
  - id: PaymentService
  - id: InventoryService
  - id: ShippingService
  - id: UserService
  - id: NotificationService
  # Too many services - no clear bounded contexts
---
```
