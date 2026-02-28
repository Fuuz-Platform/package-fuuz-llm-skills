---
name: fuuz-graphql
description: >
  Write, debug, and optimize GraphQL queries and mutations for the Fuuz Industrial Operations Platform (fuuz.com).
  Trigger this skill whenever the user mentions Fuuz GraphQL, Fuuz queries, Fuuz mutations, Fuuz API Explorer,
  auto-generated APIs, data model queries, CRUD operations in Fuuz, schema-to-API generation, GraphQL subscriptions
  in Fuuz, or needs help constructing queries for work orders, production history, inventory, assets, OEE data,
  workcenters, or any other Fuuz data model. Also trigger when the user references the Fuuz Application API or
  System API, filtering with where clauses, ordering with orderBy, pagination with take/skip, or relationship
  traversal in Fuuz GraphQL.
---

# Fuuz GraphQL Skill

> **Purpose:** Guide Claude in writing GraphQL queries and mutations for the Fuuz Industrial Operations Platform. Fuuz auto-generates a complete GraphQL API layer from its Schema Designer, meaning every data model immediately gets full CRUD operations accessible via GraphQL.

## 1. How Fuuz GraphQL Works

### Schema-First, API-Automatic
When a user defines a data model in the Fuuz Schema Designer, the platform **automatically generates**:
- GraphQL queries (list, get-by-ID, filtered, sorted, paginated)
- GraphQL mutations (create, update, delete, batch operations)
- REST endpoints (same operations via REST)
- Relationship resolution (auto-joins between related models)

**No backend code is written.** The API layer is entirely derived from the schema definition.

### Two API Scopes
| API | Purpose | Access |
|-----|---------|--------|
| **Application API** | User-defined data models (work orders, inventory, products, etc.) | Per-tenant, per-app |
| **System API** | Platform system models (users, roles, tenants, settings, etc.) | Enterprise-wide |

### API Explorer
Fuuz includes a built-in API Explorer (similar to GraphiQL) where users can:
- Browse all auto-generated queries and mutations
- Execute queries with variables
- Inspect response shapes
- Test mutations before using in flows/screens

## 2. Query Syntax

### Basic List Query
```graphql
query {
  workOrders {
    id
    workOrderNumber
    status
    quantityPlanned
    quantityCompleted
  }
}
```

### Filtered Query (where clause)
```graphql
query {
  workOrders(
    where: {
      status: { equals: "In Progress" }
      facility: { name: { equals: "Detroit Plant" } }
    }
  ) {
    workOrderNumber
    product { name, partNumber }
    quantityPlanned
    quantityCompleted
    scheduledStart
    status
  }
}
```

### Common Filter Operators
```graphql
where: {
  # Equality
  status: { equals: "Active" }
  
  # Not equal
  status: { not: { equals: "Closed" } }
  
  # Numeric comparisons
  quantity: { gt: 100 }          # greater than
  quantity: { gte: 100 }         # greater than or equal
  quantity: { lt: 500 }          # less than
  quantity: { lte: 500 }         # less than or equal
  
  # String matching
  name: { contains: "Motor" }
  name: { startsWith: "PRD" }
  name: { endsWith: "-A" }
  
  # Date filtering
  createdAt: { gte: "2025-01-01T00:00:00.000Z" }
  createdAt: { lte: "2025-01-31T23:59:59.999Z" }
  
  # In list
  status: { in: ["Active", "In Progress", "Pending"] }
  
  # Null checks
  completedAt: { equals: null }       # is null
  completedAt: { not: { equals: null } }  # is not null
  
  # Compound (AND is implicit)
  status: { equals: "Active" }
  quantity: { gt: 0 }
  
  # Explicit OR
  OR: [
    { status: { equals: "Active" } },
    { status: { equals: "Pending" } }
  ]
  
  # Nested relationship filter
  workcenter: { name: { equals: "CNC-001" } }
}
```

### Sorting (orderBy)
```graphql
query {
  workOrders(
    orderBy: [{ createdAt: desc }]
  ) {
    id
    workOrderNumber
    createdAt
  }
}
```

Multiple sort fields:
```graphql
orderBy: [
  { priority: desc },
  { scheduledStart: asc }
]
```

### Pagination (take / skip)
```graphql
query {
  workOrders(
    take: 50
    skip: 0
    orderBy: [{ createdAt: desc }]
  ) {
    id
    workOrderNumber
  }
}
```

### Relationship Traversal
GraphQL auto-resolves relationships defined in the schema:
```graphql
query {
  workOrders {
    workOrderNumber
    status
    # Many-to-one: each work order produces one product
    product {
      name
      partNumber
      category
    }
    # One-to-many: each work order has multiple production transactions
    productionTransactions {
      id
      quantity
      occurAt
      result
    }
    # Many-to-many: work orders go through multiple operations
    operations {
      name
      sequence
      standardCycleTime
    }
    # Nested relationships
    workcenter {
      name
      facility { name, location }
      asset { id, serialNumber }
    }
  }
}
```

### Aggregation Queries
```graphql
query {
  aggregateProductionHistory(
    where: {
      createdAt: { gte: "2025-01-01T00:00:00.000Z" }
    }
  ) {
    _count { id }
    _sum { quantity }
    _avg { cycleTime }
    _max { quantity }
    _min { quantity }
  }
}
```

## 3. Mutation Syntax

### Create (Single Record)
```graphql
mutation {
  createWorkOrder(
    data: {
      workOrderNumber: "WO-2025-001"
      status: "Planned"
      quantityPlanned: 500
      product: { connect: { id: "product-id-123" } }
      workcenter: { connect: { id: "workcenter-id-456" } }
      scheduledStart: "2025-02-01T06:00:00.000Z"
    }
  ) {
    id
    workOrderNumber
    status
  }
}
```

### Update (Single Record)
```graphql
mutation {
  updateWorkOrder(
    where: { id: "work-order-id-789" }
    data: {
      status: "In Progress"
      actualStart: "2025-02-01T06:15:00.000Z"
    }
  ) {
    id
    status
    actualStart
  }
}
```

### Delete (Single Record)
```graphql
mutation {
  deleteWorkOrder(
    where: { id: "work-order-id-789" }
  ) {
    id
  }
}
```

### Batch Mutations (Multiple Records)
Used heavily in data flows for bulk operations:
```json
{
  "payload": [
    {
      "where": { "id": "record-1" },
      "data": { "status": "Complete", "completedAt": "2025-01-15T10:30:00.000" }
    },
    {
      "where": { "id": "record-2" },
      "data": { "status": "Complete", "completedAt": "2025-01-15T10:31:00.000" }
    }
  ]
}
```

### Batch Delete Format
```json
{
  "payload": [
    { "where": { "id": "record-1" } },
    { "where": { "id": "record-2" } },
    { "where": { "id": "record-3" } }
  ]
}
```

### Relationship Operations in Mutations

**Connect** (link to existing record):
```graphql
data: {
  product: { connect: { id: "existing-product-id" } }
}
```

**Disconnect** (remove link):
```graphql
data: {
  product: { disconnect: true }
}
```

**Create nested** (create related record inline):
```graphql
data: {
  productionTransactions: {
    create: [
      { quantity: 50, result: "Good", occurAt: "2025-01-15T10:00:00.000" }
    ]
  }
}
```

## 4. Using GraphQL in Fuuz Components

### In Screen Forms
```json
{
  "type": "Form",
  "query": {
    "api": "Application",
    "operation": "query",
    "name": "workOrder",
    "variables": {
      "where": { "id": { "equals": "$context.selectedId" } }
    }
  }
}
```

### In Screen Tables
```json
{
  "type": "Table",
  "query": {
    "api": "Application",
    "operation": "query",
    "name": "workOrders",
    "variables": {
      "where": { "status": { "in": ["Active", "In Progress"] } },
      "orderBy": [{ "scheduledStart": "asc" }],
      "take": 100
    }
  },
  "refreshInterval": 30000
}
```

### In Data Flow Nodes
```json
{
  "type": "query",
  "api": "Application",
  "operation": "query",
  "name": "productionHistories",
  "variables": {
    "where": {
      "createdAt": { "gte": "$moment().subtract(30, 'minutes').toISOString()" }
    },
    "take": 1000
  }
}
```

### In JSONata (via Fuuz Bindings)
```jsonata
$query("workOrders", {
  "where": { "status": { "equals": "Active" } },
  "take": 50
})

$mutate("updateWorkOrder", {
  "where": { "id": $selectedId },
  "data": { "status": "Complete" }
})

$executeSavedQuery("dailyOEEReport", { "date": $today })
```

## 5. Common Manufacturing Query Patterns

### Active Work Orders for a Workcenter
```graphql
query ActiveWorkOrders($workcenterId: String!) {
  workOrders(
    where: {
      workcenter: { id: { equals: $workcenterId } }
      status: { in: ["Released", "In Progress"] }
    }
    orderBy: [{ priority: desc }, { scheduledStart: asc }]
  ) {
    id
    workOrderNumber
    product { name, partNumber }
    quantityPlanned
    quantityCompleted
    scheduledStart
    priority
  }
}
```

### OEE Data for Date Range
```graphql
query OEEData($start: DateTime!, $end: DateTime!, $workcenterId: String) {
  oeeHourlies(
    where: {
      periodHourAt: { gte: $start, lte: $end }
      workcenterId: { equals: $workcenterId }
    }
    orderBy: [{ periodHourAt: asc }]
  ) {
    periodHourAt
    oee
    availability
    performance
    quality
    goodCount
    scrapCount
    plannedDowntimeMinutes
    unplannedDowntimeMinutes
  }
}
```

### Inventory by Location and Status
```graphql
query InventoryByLocation($locationId: String!) {
  inventories(
    where: {
      location: { id: { equals: $locationId } }
      status: { not: { equals: "Consumed" } }
      quantity: { gt: 0 }
    }
    orderBy: [{ product: { name: asc } }]
  ) {
    inventoryNumber
    product { name, partNumber }
    lotNumber
    quantity
    unitOfMeasure
    status
  }
}
```

### Production History with Full Genealogy
```graphql
query ProductionTrace($workOrderId: String!) {
  productionHistories(
    where: { workOrder: { id: { equals: $workOrderId } } }
    orderBy: [{ occurAt: asc }]
  ) {
    id
    occurAt
    quantity
    result
    workcenter { name }
    operation { name, sequence }
    operator { name }
    inventory { lotNumber, serialNumber }
  }
}
```

## 6. GraphQL Subscriptions (Real-Time)

Fuuz supports GraphQL subscriptions for real-time data on dashboards:
```graphql
subscription {
  equipmentAlerts(severity: HIGH) {
    equipmentId
    alertType
    severity
    timestamp
    description
  }
}
```

Use subscriptions for live monitoring dashboards. For periodic data refresh (OEE dashboards, production boards), use polling with `refreshInterval` on screen components instead.

## 7. Best Practices

1. **Request only needed fields** — Don't select `*`, specify exact fields to reduce payload size
2. **Use relationship traversal** — One query with nested objects beats multiple separate queries
3. **Paginate large results** — Always use `take` with a reasonable limit (50-100 for screens, up to 1000 for flows)
4. **Filter at the query level** — Don't fetch all records and filter client-side
5. **Use variables** for dynamic values — Don't hardcode IDs or dates in query strings
6. **Batch mutations** for bulk operations — One batch call beats looping individual mutations
7. **Use the API Explorer** to test queries before embedding in screens or flows
8. **Leverage auto-generated API** — Don't build custom endpoints when the schema-generated API covers your need
9. **Use `$executeSavedQuery`** in JSONata for reusable, parameterized queries
10. **Index frequently filtered fields** — Users can add indexes on data models for query performance

## 8. Common Anti-Patterns

- **Never fetch entire tables without filters or pagination** — Use `where` and `take`
- **Never hardcode record IDs in queries** — Pass them as variables from context
- **Never create custom API endpoints** for standard CRUD — the auto-generated API handles this
- **Never ignore relationship resolution** — Use nested field selection instead of separate queries
- **Never use string concatenation to build queries** — Use proper variable binding
- **Never assume field names** — Check the Schema Designer or API Explorer for exact names
- **Never mutate without a `where` clause** — Updates and deletes require explicit targeting
