# Fuuz GraphQL Patterns for Screen Data

> Query and mutation patterns specific to screen data binding, filters, and action chains.

---

## GraphQL Patterns for Screen Data

Screens interact with the Fuuz GraphQL API through Table/Form model bindings, action chain mutations, and `$query()`/`$mutate()` calls. For the full syntax reference, consult `Fuuz_GraphQL_Syntax___Patterns.md`. Key patterns for screen use:

### Edges/Node Pattern
All collection queries return `{ edges: [{ node: {...} }] }`. Tables handle this automatically when bound to a data model. In `$query()` calls, unwrap with: `$query(...).modelName.edges.node`.

### Table Model Query
When a Table component has `model: "workcenter"`, the platform generates a GraphQL query automatically. The `fields` array in `query` props controls which fields are fetched (including relationship paths like `site.name`). The `parameters` prop maps filter form values to `where` clause predicates.

### Where Clause Operators for Filters
```
_eq       — Equals
_gte/_lte — Greater/less than or equal
_gt/_lt   — Greater/less than
_in       — In list: ["Active", "InProgress"]
_isNull   — Null check (CRITICAL: not _is_null)
_and/_or  — Logical combinations
```

### Action Chain Mutations
Create and update mutations follow the Fuuz payload pattern:
```jsonata
/* Create — wrap with {create: {...}} */
{"payload": {"create": data}}

/* Update — wrap with {where: {...}, update: {...}} */
{"payload": {"where": {"id": id}, "update": data}}

/* Bulk update from selected rows */
{"payload": $components.Table1.selectedRows.{"where": {"id": id}}}
```

### Ordering
```graphql
orderBy: { field: createdAt, direction: desc }
```
CRITICAL: Field name is an enum — no quotes around field name.

### Aggregations (for dashboard data)
Available aggregate functions: `count`, `minimum`, `maximum`, `average`, `sum`, `standardDeviation`, `first`, `last`, `median`, `uniqueCount`, `percentile`.
CRITICAL: `minimum`, `maximum`, `first`, `last` return arrays — unwrap with `[0]`.

### Pagination
Use `first: N` to limit results. For cursor-based pagination, use `after: $cursor` with `pageInfo { hasNextPage endCursor }`.

### DateTime Format Specifiers
Some fields support format conversion in queries:
```graphql
scheduledStartAt(format: milliseconds)  /* Get as epoch ms */
scheduledEndAt(format: iso)             /* Get as ISO string (default) */
```

### Relationships & Nested Queries
Query related entities inline — no separate calls needed:
```graphql
workcenter {
  edges {
    node {
      id
      name
      site { id name timezone }           /* many-to-one */
      workOrders(where: { status: { _eq: "Active" } }) {  /* one-to-many filtered */
        edges { node { id code status } }
      }
    }
  }
}
```

### Screen-Specific GraphQL Patterns

**$query() for inline data fetching (action chains or transforms):**
```jsonata
(
  $result := $query({
    "statement": "query GetActive($where: WorkcenterWhereInput) { workcenter(where: $where, orderBy: {field: name, direction: asc}) { edges { node { id code name isActive } } } }",
    "variables": {"where": {"isActive": {"_eq": true}}}
  });
  $result.workcenter.edges.node
)
```

**$mutate() for action chain operations:**
```jsonata
$mutate({
  "statement": "mutation Update($payload: [WorkcenterUpdatePayloadInput!]!) { updateWorkcenter(payload: $payload) { id name } }",
  "variables": {
    "payload": [{
      "where": {"id": data.id},
      "update": {"name": data.name, "isActive": data.isActive}
    }]
  }
})
```

**Aggregation query for dashboard metrics:**
```jsonata
(
  $stats := $query({
    "statement": "query Stats($where: TelemetryRawWhereInput) { telemetryRaw(where: $where, groupBy: [dataPointId]) { edges { node { dataPointId _aggregate { value { count average minimum maximum } } } } } }",
    "variables": {"where": {"_and": [{"recordedAt": {"_gte": $state.context.startTime}}, {"recordedAt": {"_lte": $state.context.endTime}}]}}
  });
  $stats.telemetryRaw.edges.node.{
    "dataPointId": dataPointId,
    "count": _aggregate.value.count,
    "avg": _aggregate.value.average,
    "min": _aggregate.value.minimum[0],
    "max": _aggregate.value.maximum[0]
  }
)
```

---
