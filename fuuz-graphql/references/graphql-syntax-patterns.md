# Fuuz GraphQL Syntax & Patterns

## Overview
This skill provides guidance on Fuuz platform's GraphQL API syntax, query patterns, mutations, and aggregations. Fuuz uses a specific GraphQL schema with consistent patterns for querying and manipulating industrial IoT and manufacturing data.

## When to Use This Skill
- Writing GraphQL queries for Fuuz data models
- Creating mutations (create, update, delete operations)
- Building aggregation queries for telemetry or production data
- Filtering records with where clauses
- Working with relationships between data models
- Implementing pagination or sorting

## Core Concepts

### 1. Edges/Node Pattern
Fuuz queries return results wrapped in `edges` and `node` structure:
```graphql
query GetWorkcenters {
  workcenter(where: { isActive: { _eq: true } }) {
    edges {
      node {
        id
        code
        name
      }
    }
  }
}
```

**Pattern:**
- Collection query returns `{ edges: [...] }`
- Each edge contains `{ node: { ...fields } }`
- Always unwrap in code: `$.workcenter.edges.map(e => e.node)`

### 2. Field Naming Conventions
- **IDs:** Always `id` (never `_id`)
- **Foreign Keys:** `{modelName}Id` (camelCase, e.g., `workcenterId`, `productId`)
- **Booleans:** Start with `is` or `has` (e.g., `isActive`, `hasError`)
- **Timestamps:** End with `At` (e.g., `occurAt`, `createdAt`, `scheduledStartAt`)
- **Relations:** Same as model name (e.g., `workcenter`, `product`, `dataPoint`)

### 3. Where Clause Operators

#### Equality
```graphql
where: { status: { _eq: "Active" } }
where: { id: { _eq: "WC-001" } }
```

#### Comparison
```graphql
where: { quantity: { _gte: 100 } }  # Greater than or equal
where: { quantity: { _lte: 1000 } } # Less than or equal
where: { quantity: { _gt: 0 } }     # Greater than
where: { quantity: { _lt: 100 } }   # Less than
```

#### List Operations
```graphql
where: { status: { _in: ["Active", "InProgress", "Completed"] } }
where: { id: { _in: ["ID1", "ID2", "ID3"] } }
```

#### Null Checks
```graphql
where: { endAt: { _isNull: true } }   # IS NULL
where: { endAt: { _isNull: false } }  # IS NOT NULL
```

**CRITICAL:** Use `_isNull` NOT `_is_null` (underscore placement matters!)

#### Date/Time Comparisons
```graphql
where: {
  scheduledStartAt: { _lte: $now }
  scheduledEndAt: { _gte: $now }
}
```

#### Logical Operators
```graphql
# AND (all conditions must be true)
where: {
  _and: [
    { status: { _eq: "Active" } }
    { quantity: { _gte: 100 } }
  ]
}

# OR (any condition can be true)
where: {
  _or: [
    { status: { _eq: "Active" } }
    { status: { _eq: "InProgress" } }
  ]
}

# Complex combinations
where: {
  _and: [
    { isActive: { _eq: true } }
    {
      _or: [
        { status: { _eq: "Running" } }
        { status: { _eq: "Idle" } }
      ]
    }
  ]
}
```

### 4. Ordering Results
```graphql
# Single field
orderBy: { field: createdAt, direction: desc }

# CRITICAL: No quotes around field name - it's an enum value
# WRONG: orderBy: { field: "createdAt", direction: desc }
# RIGHT: orderBy: { field: createdAt, direction: desc }
```

**Directions:**
- `asc` - Ascending (oldest first, A-Z, lowest first)
- `desc` - Descending (newest first, Z-A, highest first)

### 5. Pagination
```graphql
# Get first N records
query GetRecords {
  telemetryRaw(first: 1000) {
    edges {
      node {
        id
        value
      }
    }
  }
}

# Get records after a cursor
query GetNextPage($cursor: String!) {
  telemetryRaw(first: 1000, after: $cursor) {
    edges {
      node {
        id
        value
      }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

### 6. Aggregations

#### Basic Aggregations
```graphql
query GetTelemetryStats {
  telemetryRaw(
    where: {
      recordedAt: { _gte: $startTime }
      recordedAt: { _lte: $endTime }
      dataPointId: { _eq: $dpId }
    }
  ) {
    edges {
      node {
        dataPointId
        _aggregate {
          value {
            count
            minimum
            maximum
            average
            sum
            standardDeviation
            first
            last
          }
        }
      }
    }
  }
}
```

**Available Aggregate Functions:**
- `count` - Total records (returns integer)
- `minimum` - Min value (returns array with single value)
- `maximum` - Max value (returns array with single value)
- `average` - Mean value (returns float)
- `sum` - Total sum (returns float)
- `standardDeviation` - Standard deviation (returns float)
- `first` - First value (returns array with single value)
- `last` - Last value (returns array with single value)
- `median` - Median value (returns float)
- `uniqueCount` - Distinct count (returns integer)
- `percentile` - Percentiles (returns array: [p05, p25, p50, p75, p95])

**CRITICAL:** `minimum`, `maximum`, `first`, `last` return **arrays** - must unwrap with `[0]`

#### Grouping Aggregations
```graphql
query GetStatsByDataPoint {
  telemetryRaw(
    where: { recordedAt: { _gte: $startTime } }
    groupBy: [dataPointId]
  ) {
    edges {
      node {
        dataPointId
        _aggregate {
          value {
            count
            average
            minimum
            maximum
          }
        }
      }
    }
  }
}
```

### 7. Mutations

#### Create (Single Record)
```graphql
mutation CreateWorkcenter($input: WorkcenterCreatePayloadInput!) {
  createWorkcenter(payload: [$input]) {
    id
    code
    name
  }
}
```

**Variables:**
```json
{
  "input": {
    "create": {
      "code": "WC-001",
      "name": "Assembly Line 1",
      "isActive": true,
      "siteId": "SITE-001"
    }
  }
}
```

#### Create (Bulk Records)
```graphql
mutation CreateTelemetryBulk($payload: [TelemetryRawCreatePayloadInput!]!) {
  createTelemetryRaw(payload: $payload) {
    id
    dataPointId
  }
}
```

**Variables:**
```json
{
  "payload": [
    {
      "create": {
        "dataPointId": "DP-001",
        "recordedAt": "2026-02-07T12:00:00Z",
        "value": 25.5
      }
    },
    {
      "create": {
        "dataPointId": "DP-002",
        "recordedAt": "2026-02-07T12:00:00Z",
        "value": 78.3
      }
    }
  ]
}
```

**CRITICAL Pattern:**
```javascript
// Fuuz mutation format: wrap each record with {create: {...}}
{
  "payload": [
    { "create": { ...fields } },
    { "create": { ...fields } }
  ]
}
```

#### Update (Single Record)
```graphql
mutation UpdateWorkcenter($id: ID!, $input: WorkcenterUpdatePayloadInput!) {
  updateWorkcenter(where: { id: $id }, payload: [$input]) {
    id
    code
    name
  }
}
```

**Variables:**
```json
{
  "id": "WC-001",
  "input": {
    "update": {
      "name": "Assembly Line 1 - Updated",
      "isActive": false
    }
  }
}
```

#### Update (Bulk Records)
```graphql
mutation UpdateMultipleWorkcenters($payload: [WorkcenterUpdatePayloadInput!]!) {
  updateWorkcenter(payload: $payload) {
    id
    code
  }
}
```

**Variables:**
```json
{
  "payload": [
    {
      "where": { "id": "WC-001" },
      "update": { "currentModeId": "production" }
    },
    {
      "where": { "id": "WC-002" },
      "update": { "currentModeId": "maintenance" }
    }
  ]
}
```

#### Delete (Single Record)
```graphql
mutation DeleteWorkcenter($id: ID!) {
  deleteWorkcenter(where: { id: $id }) {
    id
  }
}
```

### 8. Relationships and Nested Queries
```graphql
query GetWorkcentersWithDetails {
  workcenter(where: { isActive: { _eq: true } }) {
    edges {
      node {
        id
        code
        name

        # Related site (many-to-one)
        site {
          id
          name
          timezone
        }

        # Related schedule group (many-to-one)
        scheduleGroup {
          id
          code
          name
        }

        # Related work orders (one-to-many)
        workOrders(where: { status: { _eq: "Active" } }) {
          edges {
            node {
              id
              code
              status
            }
          }
        }
      }
    }
  }
}
```

### 9. Format Specifiers for DateTime Fields

Some fields support format conversion in queries:
```graphql
query GetWorkOrders {
  workOrder {
    edges {
      node {
        id
        scheduledStartAt(format: milliseconds)  # Get as epoch milliseconds
        scheduledEndAt(format: iso)            # Get as ISO string (default)
      }
    }
  }
}
```

**Available Formats:**
- `iso` - ISO 8601 string (default): "2026-02-07T12:00:00.000Z"
- `milliseconds` - Unix epoch milliseconds: 1738933200000

## Common Patterns

### Pattern 1: Get Open Records
```graphql
query GetOpenRecords {
  workcenterHistory(where: { endAt: { _isNull: true } }) {
    edges {
      node {
        id
        workcenterId
        occurAt
        modeId
      }
    }
  }
}
```

### Pattern 2: Time Range Filtering
```graphql
query GetRecordsInRange($start: DateTime!, $end: DateTime!) {
  telemetryRaw(
    where: {
      _and: [
        { recordedAt: { _gte: $start } }
        { recordedAt: { _lte: $end } }
      ]
    }
  ) {
    edges {
      node {
        id
        value
        recordedAt
      }
    }
  }
}
```

### Pattern 3: Get Records with Related Data
```graphql
query GetProductionWithDetails {
  production(
    where: { occurAt: { _gte: $startDate } }
    orderBy: { field: occurAt, direction: desc }
    first: 100
  ) {
    edges {
      node {
        id
        code
        quantityGood
        quantityScrap

        workcenter {
          id
          code
          name
        }

        workOrder {
          id
          code
          status
        }

        product {
          id
          code
          name
        }
      }
    }
  }
}
```

### Pattern 4: Aggregate by Multiple Fields
```graphql
query GetOeeByWorkcenterAndShift($date: Date!) {
  oeeDaily(
    where: { periodAt: { _eq: $date } }
    groupBy: [workcenterId, shiftId]
  ) {
    edges {
      node {
        workcenterId
        shiftId
        _aggregate {
          availability {
            average
          }
          performance {
            average
          }
          quality {
            average
          }
          oee {
            average
          }
        }
      }
    }
  }
}
```

## JSONata Integration

When processing Fuuz GraphQL results in JSONata transforms:
```jsonata
/* Extract nodes from edges */
$.workcenter.edges.node

/* Map to specific fields */
$.workcenter.edges.node.{ "id": id, "name": name }

/* Filter results */
$.workcenter.edges[node.isActive = true].node

/* Unwrap aggregates */
$.telemetryRaw.edges.node.{
  "dataPointId": dataPointId,
  "count": _aggregate.value.count,
  "min": _aggregate.value.minimum[0],    /* Unwrap array */
  "max": _aggregate.value.maximum[0],    /* Unwrap array */
  "avg": _aggregate.value.average
}
```

## Common Errors and Solutions

### Error: "Field '_is_null' doesn't exist"
**Problem:** Wrong syntax for null check
**Solution:** Use `_isNull` not `_is_null`
```graphql
# WRONG
where: { endAt: { _is_null: true } }

# RIGHT
where: { endAt: { _isNull: true } }
```

### Error: "Float cannot represent non numeric value: [25.5]"
**Problem:** Aggregate functions return arrays, not unwrapped
**Solution:** Access array element `[0]`
```jsonata
/* WRONG */
"min": node._aggregate.value.minimum

/* RIGHT */
"min": node._aggregate.value.minimum[0]
```

### Error: "orderBy field must be an enum"
**Problem:** Quoted field name in orderBy
**Solution:** Remove quotes from field name
```graphql
# WRONG
orderBy: { field: "createdAt", direction: desc }

# RIGHT
orderBy: { field: createdAt, direction: desc }
```

### Error: "Mongo bulk write error"
**Problem:** Sequence collision on ID generation
**Solution:** Update sequence start value above existing max
```javascript
// Find max ID value, update sequence to start above it
```

## Best Practices

1. **Always specify first for large datasets**
   - Limit: `first: 10000` for queries that might return many records
   - Prevents memory issues and timeouts

2. **Use specific filters**
   - Filter as much as possible in GraphQL (database level)
   - Avoid filtering large datasets in JavaScript/JSONata

3. **Request only needed fields**
   - Don't query for fields you won't use
   - Reduces payload size and improves performance

4. **Use variables for dynamic values**
   - Never concatenate strings into queries
   - Always use GraphQL variables for safety

5. **Handle null/undefined gracefully**
   - Check for existence: `$.field || defaultValue`
   - Use optional chaining in JavaScript: `edge?.node?.field`

6. **Batch mutations when possible**
   - Use bulk mutations instead of individual calls
   - Maximum efficiency: single mutation with array payload

7. **Group aggregations strategically**
   - Group by fields that reduce result set size
   - Example: Group by dataPointId reduces 1000s of records to 10s

## Examples by Use Case

### Use Case: Get Active Work Orders for Production
```graphql
query GetActiveWorkOrders($now: DateTime!) {
  workOrder(
    where: {
      _and: [
        { status: { _in: ["Released", "InProgress"] } }
        { scheduledStartAt: { _lte: $now } }
        { workcenterId: { _isNull: false } }
      ]
    }
    orderBy: { field: scheduledStartAt, direction: asc }
    first: 1000
  ) {
    edges {
      node {
        id
        code
        status
        quantityTarget
        quantityCompleted
        workcenterId
        productId
        product {
          id
          code
          standardCycleTime
          idealCycleTime(format: milliseconds)
        }
      }
    }
  }
}
```

### Use Case: Hourly Telemetry Aggregation
```graphql
query GetHourlyAggregates($start: DateTime!, $end: DateTime!, $dataPointIds: [ID!]!) {
  telemetryRaw(
    where: {
      _and: [
        { recordedAt: { _gte: $start } }
        { recordedAt: { _lt: $end } }
        { dataPointId: { _in: $dataPointIds } }
      ]
    }
    groupBy: [dataPointId]
  ) {
    edges {
      node {
        dataPointId
        _aggregate {
          value {
            count
            minimum
            maximum
            average
            sum
            standardDeviation
            first
            last
            median
            uniqueCount
            percentile
          }
        }
      }
    }
  }
}
```

### Use Case: Close Open Workcenter History Records
```graphql
mutation CloseWorkcenterHistory($payload: [WorkcenterHistoryUpdatePayloadInput!]!) {
  updateWorkcenterHistory(payload: $payload) {
    id
    endAt
    duration
  }
}
```

**Variables:**
```json
{
  "payload": [
    {
      "where": { "id": "WCH-001" },
      "update": {
        "endAt": "2026-02-07T12:00:00Z",
        "duration": 3600000,
        "runTime": 3600000,
        "plannedDowntime": 0,
        "unplannedDowntime": 0
      }
    }
  ]
}
```

## Summary Checklist

When writing Fuuz GraphQL:
- Use `edges` -> `node` pattern for collections
- Use `_isNull` not `_is_null` for null checks
- Use `_and` / `_or` for logical combinations
- Unwrap aggregate arrays: `minimum[0]`, `maximum[0]`
- Use unquoted field names in `orderBy`
- Wrap mutations: `{ "payload": [{ "create": {...} }] }`
- Specify `first` for large queries
- Use camelCase for field names
- End timestamps with `At` suffix
- Use GraphQL variables, never string concatenation