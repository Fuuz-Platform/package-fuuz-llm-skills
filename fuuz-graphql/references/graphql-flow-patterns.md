# GraphQL API Patterns

Fuuz uses GraphQL for all data operations. **Fuuz provides two separate GraphQL APIs:**

## API Selection

### Application API
Used for app-specific data models. Specify `"api": "application"` in query/mutate nodes.

**Models include:**
- Production: ProductionLog, WorkOrder, Product
- OEE: OeeHourly, OeeDaily, Workcenter, WorkcenterHistory, Mode, State, Event
- Telemetry: TelemetryRaw, TelemetryRawBool, TelemetryRawString, TelemetryHourly, TelemetryDaily
- Asset Hierarchy: Site, Area, Line, Cell, Asset, DataPoint
- Alarms: Alarm, AlarmState
- Quality: InspectionLog, QualityCheck
- Inventory: InventoryTransaction, Location

**Example:**
```json
{
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetProduction { productionLog { edges { node { id quantityGood } } } }"
  }
}
```

### System API
Used for platform models (multi-tenancy, security, users). Specify `"api": "system"` in query/mutate nodes.

**Models include:**
- Tenant, Enterprise
- User, Role, Permission, Group
- ApplicationInstance
- Subscription, License

**Example:**
```json
{
  "type": "query",
  "data": {
    "api": "system",
    "query": "query GetUsers { user(where: { enterpriseId: { _eq: $eid } }) { edges { node { id email firstName lastName } } } }"
  }
}
```

**Important:** Always explicitly specify which API you're targeting in the node configuration.

---

## Query Structure

All queries (both Application and System APIs) use Relay-style connection pattern with `edges` and `node`:

```graphql
query GetModel($where: ModelWhereInput, $orderBy: [ModelOrderByInput!], $first: Int, $after: String) {
  model(where: $where, orderBy: $orderBy, first: $first, after: $after) {
    edges {
      node {
        id
        field1
        field2
        relatedModel {
          id
          name
        }
      }
    }
  }
}
```

### Result Traversal

```jsonata
/* Get all nodes as array */
$.model.edges.node

/* Get first node */
$.model.edges[0].node

/* Get specific field from all nodes */
$.model.edges.node.field1

/* Filter then get nodes */
$.model.edges[node.status = "Active"].node
```

## Where Clause Operators

### Equality and Comparison
```graphql
{ "field": { "_eq": "value" } }              # Equals
{ "field": { "_ne": "value" } }              # Not equals
{ "field": { "_in": ["a", "b", "c"] } }      # In array
{ "field": { "_nin": ["x", "y"] } }          # Not in array
{ "field": { "_gt": 100 } }                  # Greater than
{ "field": { "_gte": 100 } }                 # Greater than or equal
{ "field": { "_lt": 100 } }                  # Less than
{ "field": { "_lte": 100 } }                 # Less than or equal
```

### String Operators
```graphql
{ "field": { "_contains": "substring" } }    # Contains substring
{ "field": { "_startsWith": "prefix" } }     # Starts with
{ "field": { "_endsWith": "suffix" } }       # Ends with
```

**Fuzzy Matching with `_contains`:** UNS/SCADA tag names may not exactly match Fuuz dataPoint codes. Use `_contains` for partial ID/code matching when exact `_eq` matching fails:

```jsonata
{
  "where": {
    "_and": [
      { "code": { "_contains": $state.context.dP } },
      { "assetId": { "_contains": $state.context.p } }
    ]
  }
}
```

### Null Checks
```graphql
{ "field": { "_isNull": true } }             # Is null
{ "field": { "_isNull": false } }            # Is not null
```

### Date Range Queries
```graphql
{
  "occurAt": {
    "_gte": "2026-01-01T00:00:00Z",
    "_lt": "2026-01-02T00:00:00Z"
  }
}
```

### Logical Operators
```graphql
{
  "_and": [
    { "field1": { "_eq": "value1" } },
    { "field2": { "_gte": 10 } }
  ]
}

{
  "_or": [
    { "endAt": { "_gte": "2026-01-01" } },
    { "endAt": { "_isNull": true } }
  ]
}

{
  "_not": { "status": { "_eq": "Inactive" } }
}
```

## OrderBy Patterns

```graphql
query GetSorted($orderBy: [ModelOrderByInput!]) {
  model(orderBy: $orderBy) {
    edges { node { id field } }
  }
}
```

Variables:
```jsonata
{
  "orderBy": [
    { "occurAt": "DESC" },
    { "id": "ASC" }
  ]
}
```

## Pagination

```graphql
query GetPaged($first: Int, $after: String) {
  model(first: $first, after: $after) {
    edges {
      cursor
      node { id field }
    }
    pageInfo {
      hasNextPage
      endCursor
    }
  }
}
```

## Aggregation Queries

Fuuz supports inline aggregations via `_aggregate` on relationship fields:

```graphql
query AggregateData($where: TelemetryRawWhereInput) {
  telemetryRaw(where: $where, first: 100000) {
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
            first
            last
            standardDeviation
            median
            uniqueCount
            percentile(percentiles: [5, 25, 50, 75, 95])
          }
        }
      }
    }
  }
}
```

**JSONata to extract aggregates:**
```jsonata
$.telemetryRaw.edges.node.{
  "dataPointId": dataPointId,
  "count": _aggregate.value.count,
  "avg": _aggregate.value.average,
  "min": _aggregate.value.minimum,
  "max": _aggregate.value.maximum,
  "p50": _aggregate.value.percentile[2],
  "p95": _aggregate.value.percentile[4]
}
```

## Duration Fields

Duration fields support a `format` argument:

```graphql
query GetDurations {
  productionLog {
    edges {
      node {
        actualCycleTime(format: milliseconds)
        idealCycleTime(format: milliseconds)
        downtime(format: seconds)
      }
    }
  }
}
```

Available formats: `milliseconds`, `seconds`, `minutes`, `hours`, `days`

## Multi-Query Pattern

Combine multiple queries in a single request using aliases:

```graphql
query GetMultipleDataTypes {
  floatDataPoints: dataPoint(where: { dataType: { "_eq": "float" } }) {
    edges {
      node {
        id
        frequency
        setPoint
      }
    }
  }
  
  booleanDataPoints: dataPoint(where: { dataType: { "_eq": "boolean" } }) {
    edges {
      node {
        id
        frequency
      }
    }
  }
  
  setting: setting(where: { id: { "_eq": "TimeZone" } }) {
    edges {
      node {
        defaultValue
      }
    }
  }
}
```

**JSONata to access aliased results:**
```jsonata
{
  "floats": $.floatDataPoints.edges.node,
  "booleans": $.booleanDataPoints.edges.node,
  "timezone": $.setting.edges[0].node.defaultValue
}
```

## Mutation Patterns

### Create
```graphql
mutation CreateRecord($payload: [ModelCreatePayloadInput!]!) {
  createModel(payload: $payload) {
    id
    field1
    field2
  }
}
```

Variables (JSONata):
```jsonata
{
  "payload": [
    {
      "create": {
        "field1": $.value1,
        "field2": $.value2,
        "occurAt": $moment()
      }
    }
  ]
}
```

### Update
```graphql
mutation UpdateRecord($payload: [ModelUpdatePayloadInput!]!) {
  updateModel(payload: $payload) {
    id
    field1
    field2
  }
}
```

Variables:
```jsonata
{
  "payload": [
    {
      "where": { "id": $.recordId },
      "update": {
        "field1": $.newValue,
        "processedAt": $moment()
      }
    }
  ]
}
```

### Upsert
```graphql
mutation UpsertRecord($payload: [ModelUpsertPayloadInput!]!) {
  upsertModel(payload: $payload) {
    id
    field1
  }
}
```

Variables:
```jsonata
{
  "payload": [
    {
      "where": { "id": $.recordId },
      "create": {
        "id": $.recordId,
        "field1": $.value
      },
      "update": {
        "field1": $.value,
        "updatedAt": $moment()
      }
    }
  ]
}
```

### Delete
```graphql
mutation DeleteRecord($payload: [ModelDeletePayloadInput!]!) {
  deleteModel(payload: $payload) {
    id
  }
}
```

Variables:
```jsonata
{
  "payload": [
    {
      "where": { "id": $.recordId }
    }
  ]
}
```

### Batch Mutations

Multiple records in single mutation (array mapping):

```jsonata
{
  "payload": $.records.(
    {
      "create": {
        "workcenterId": workcenterId,
        "value": value,
        "occurAt": $moment()
      }
    }
  )
}
```

## Query Node Configuration

```json
{
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetData($where: ModelWhereInput) { model(where: $where) { edges { node { id field } } } }",
    "variablesTransform": "{ \"where\": { \"id\": { \"_eq\": $.id } } }",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**variablesTransform options:**
- `{}` - No variables (static query)
- `$` - Pass entire payload as variables
- JSONata expression - Transform payload to variables

### variablesTransform Best Practices

- **Always add fallbacks for required fields** — use `$.field ? $.field : defaultValue` to avoid missing field errors
- **Capture $state.context values at top** — `$val := $state.context.v` for cleaner reuse
- **Capture $moment() once** — `$now := $moment()` to ensure consistent timestamps across fields
- **Use $cuid() for ID generation** — standard Fuuz binding for generating unique record IDs

## Mutate Node Configuration

```json
{
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation CreateRecord($payload: [ModelCreatePayloadInput!]!) { createModel(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": [{ \"create\": { \"field\": $.value } }] }",
    "nextNodes": ["next-node-uuid"]
  }
}
```

## Settings Access

Tenant settings like timezone:

```graphql
query GetSettings {
  setting(where: { id: { "_eq": "TimeZone" } }) {
    edges {
      node {
        id
        defaultValue
      }
    }
  }
}
```

Common settings:
- `TimeZone` - Default timezone
- `Locale` - Language/region settings

## Delete Mutation Patterns (Critical)

Delete mutations use a `payload` array with individual `where` wrappers per record. They do **NOT** support a top-level `where` argument with `_in` arrays.

```graphql
mutation DeleteRecords($payload: [ModelNameDeletePayloadInput!]!) {
  deleteModelName(payload: $payload) {
    id
  }
}
```

**variablesTransform (JSONata):**
```jsonata
{
  "payload": $.modelName.edges.node.{
    "where": { "id": id }
  }
}
```

**Critical rules:**
- Type name: `{ModelName}DeletePayloadInput` (PascalCase model name)
- Each item wraps the id: `"where": { "id": id }`
- Use `.{}` mapping (no square brackets `[]`) — JSONata produces the array naturally
- Must return subfields (at minimum `{ id }`)
- `where: { id: { _in: [...] } }` does **NOT** work at the mutation level

## Filter Pattern: No `_nin` Support

Fuuz does not support `_nin` (not in) operator. Use `_not` with `_in` instead:

```jsonata
{ "_not": { "assetId": { "_in": ["id1", "id2"] } } }
```

## OrderBy Variable Syntax

```jsonata
{
  "orderBy": [
    { "field": "fieldName", "direction": "desc" }
  ]
}
```

Always use `"field"` and `"direction"` as explicit string keys (not shorthand). Always wrap in an array.

> **Note:** The `orderBy` shorthand syntax `{ "occurAt": "DESC" }` works in inline GraphQL strings. The `{ "field": ..., "direction": ... }` format is for variablesTransform JSONata.

## Best Practices

1. **Always use edges.node pattern** - Results are always wrapped in connection structure
2. **Specify required fields only** - Don't query unnecessary fields
3. **Use first parameter** - Limit results to prevent large payloads: `first: 1000`
4. **Filter in where clause** - Don't retrieve then filter in JSONata
5. **Use aggregations** - Let database aggregate rather than fetching all records
6. **Batch mutations** - Send multiple records in single mutation array
7. **Even single mutations need arrays** - `{ "payload": [{ "create": {...} }] }`
8. **Use aliases for multi-query** - Combine related queries into single request
9. **orderBy for deterministic results** - Important when using pagination
10. **Use _isNull for optional dates** - Combine with _or for open-ended ranges
