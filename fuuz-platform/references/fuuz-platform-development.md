# Fuuz Platform Development Skill

## Overview

This skill covers development patterns for the Fuuz Industrial Operations platform, including data flows, GraphQL queries, JavaScript/JSONata scripting, screen building, and debugging.

---

## Node Flow Architecture

Fuuz uses multi-node flows for data processing. A typical pattern:

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Node 1    │───▶│   Node 2    │───▶│   Node 3    │───▶│   Node 4    │
│  JS Script  │    │   GraphQL   │    │  JS Script  │    │   JSONata   │
│  Setup/     │    │   Query     │    │ Calculations│    │ Set Context │
│  Boundaries │    │             │    │             │    │             │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

**Node Types:**
- **JavaScript Saved Script** — Complex logic, loops, calculations
- **GraphQL Query** — Data fetching with variables from prior nodes
- **JSONata Transform** — Simple data mapping, context setting

---

## Data Access Patterns

### Critical: Know Where Your Data Comes From

| Context | Access Pattern |
|---------|----------------|
| JavaScript Saved Script - Query Results | `$` directly (e.g., `$.queryAlias`) |
| JavaScript Saved Script - Prior Context | `$state.context.nodeName.*` |
| JSONata Transform - Query Results | `$` directly |
| JSONata Transform - Prior Context | `$state.context.*` |

### Common Mistakes

```javascript
// WRONG - these patterns don't work in saved scripts
const data = $.queryData.myQuery;     // No queryData wrapper
const data = input.myQuery;           // No input object
const data = $state.myQuery;          // Query results aren't in state

// CORRECT - query results are at $ directly
const data = $.myQuery;

// CORRECT - context from prior nodes uses $state.context
const workcenterId = $state.context.boundary.workcenterId;
const boundaries = $state.context.boundary.boundaries;
```

### Extracting Nodes from GraphQL Response

```javascript
// Standard pattern for extracting nodes from edges
const records = $.myQuery?.edges?.map(e => e.node) || [];
```

---

## JavaScript vs JSONata

### Use JavaScript Saved Script When:
- Complex calculations with loops
- Mutable variables needed
- Array manipulation (map, reduce, filter, forEach)
- Conditional logic branches
- Object building/transformation
- Any code longer than a few lines

### Use JSONata Transform When:
- Simple data mapping
- Setting context for UI binding
- Passing data between nodes
- Basic field selection/renaming

### Critical Pitfall: Inline Transform Parsing

**Fuuz parses inline transforms as JSONata, not JavaScript.**

```javascript
// This FAILS in inline transform (parsed as JSONata)
const n = (v) => v || 0;
// Error: ">" cannot be used as unary operator

// The "=>" arrow function syntax is JavaScript-only
// JSONata sees ">" and tries to parse it as a comparison operator
```

**Solution:** Use a JavaScript Saved Script for any code with:
- Arrow functions (`=>`)
- `const`, `let`, `var` declarations
- Standard JavaScript methods

---

## GraphQL Query Patterns

### Variable Passing from Prior Nodes

Use mustache syntax to pass values from prior node output:

```json
{
  "wcId": "{{workcenterId}}",
  "startTime": "{{boundaries.windowStart}}",
  "endTime": "{{boundaries.now}}"
}
```

The values come from the prior node's return object.

### Aggregation Queries

Fuuz GraphQL supports `_aggregate` for server-side aggregation:

```graphql
workcenterHistory(
  where: {
    _and: [
      { workcenterId: { _eq: $wcId } }
      { occurAt: { _gte: $startTime } }
      { occurAt: { _lt: $endTime } }
    ]
  }
) {
  edges {
    node {
      eventId
      _aggregate {
        runTime { sum }
        id { count }
      }
    }
  }
}
```

### Query Aliasing for Multiple Time Windows

Use aliases to query the same entity with different filters:

```graphql
query MultiWindow($wcId: String!, $start24h: DateTime!, $start7d: DateTime!, $now: DateTime!) {

  # 24-hour window
  data24h: workcenterHistory(
    where: {
      _and: [
        { workcenterId: { _eq: $wcId } }
        { occurAt: { _gte: $start24h } }
        { occurAt: { _lt: $now } }
      ]
    }
  ) {
    edges { node { ... } }
  }

  # 7-day window
  data7d: workcenterHistory(
    where: {
      _and: [
        { workcenterId: { _eq: $wcId } }
        { occurAt: { _gte: $start7d } }
        { occurAt: { _lt: $now } }
      ]
    }
  ) {
    edges { node { ... } }
  }
}
```

---

## Duration Field Handling

### Mixed Format Problem

Duration fields may return different formats depending on the value:

```javascript
// Format 1: Object with milliseconds (when non-zero)
{ text: "PT1H30M", milliseconds: 5400000 }

// Format 2: Plain string (commonly when zero)
"PT0M"

// Format 3: Array of values (aggregated data)
[{ text: "PT1H", milliseconds: 3600000 }, { text: "PT30M", milliseconds: 1800000 }]
```

### Robust Parsing Function

```javascript
const getDurationMs = (values) => {
  if (!values || values.length === 0) return 0;

  let totalMs = 0;
  values.forEach(v => {
    // Only process objects with milliseconds property
    if (typeof v === 'object' && v !== null && v.milliseconds !== undefined) {
      totalMs += v.milliseconds || 0;
    }
    // Plain strings like "PT0M" are skipped (they represent zero)
  });
  return totalMs;
};
```

### Usage in Aggregation

```javascript
const sumAggregates = (nodes) => {
  return nodes.reduce((acc, node) => {
    const agg = node._aggregate || {};
    return {
      runTimeMs: acc.runTimeMs + getDurationMs(agg.runTime?.values),
      downtimeMs: acc.downtimeMs + getDurationMs(agg.downtime?.values),
      count: acc.count + (agg.id?.count || 0)
    };
  }, { runTimeMs: 0, downtimeMs: 0, count: 0 });
};
```

---

## Time Boundary Setup Pattern

### Node 1: Calculate Time Windows

```javascript
const now = $moment().toISOString();
const currentHourStart = $moment().startOf('hour').toISOString();
const currentHourEnd = $moment().endOf('hour').toISOString();
const dayStart = $moment().startOf('day').toISOString();
const dayEnd = $moment().endOf('day').toISOString();
const window24hStart = $moment().subtract(24, 'hours').toISOString();
const window7dStart = $moment().subtract(7, 'days').toISOString();
const window30dStart = $moment().subtract(30, 'days').toISOString();

return {
  workcenterId: $state.context.workcenterId,
  timezone: $state.context.timezone,
  boundaries: {
    now,
    currentHourStart,
    currentHourEnd,
    dayStart,
    dayEnd,
    window24hStart,
    window7dStart,
    window30dStart
  }
};
```

### Available $moment() Methods

```javascript
$moment()                           // Current time
$moment().toISOString()             // ISO format string
$moment().startOf('hour')           // Start of current hour
$moment().endOf('day')              // End of current day
$moment().subtract(24, 'hours')     // 24 hours ago
$moment().subtract(7, 'days')       // 7 days ago
$moment().valueOf()                 // Unix timestamp in milliseconds
$moment(isoString).valueOf()        // Parse ISO string to timestamp
```

---

## Screen Component Patterns

### Component Hierarchy

Fuuz screens follow strict parent-child chains:

```
Screen
└── Card
    └── Form
        └── Container
            └── DisplayText / Input / Button / etc.
```

**Key Rules:**
- Use `DisplayText` (not `Typography`) for data display
- All containers need full props specified
- Parent-child relationships must be explicit

### Data Binding Format

```json
{
  "__transform": "$state.context.myData.value",
  "__cacheKey": "uniqueKey",
  "__dynamicFields": ["value"]
}
```

### linkedNodes Configuration

Screens require `linkedNodes` to connect components:

```json
{
  "linkedNodes": {
    "card-content": "form-123",
    "form-content": "container-456"
  }
}
```

---

## Context and State Management

### Setting Context (Final Node Pattern)

Use JSONata to set context for UI binding:

```jsonata
(
  $components.Screen.fn.setContext("myData", $);
  $
)
```

With explicit object shaping:

```jsonata
(
  $result := {
    "metrics": $.metrics,
    "status": $.currentStatus,
    "lastRefresh": $.lastRefresh
  };
  $components.Screen.fn.setContext("dashboard", $result);
  $result
)
```

### Accessing Context in UI Bindings

```
$state.context.dashboard.metrics.value
$state.context.dashboard.status.code
$state.context.dashboard.lastRefresh
```

---

## Platform Built-ins

### Available Functions

| Function | Purpose |
|----------|---------|
| `$moment()` | Date/time manipulation (Moment.js API) |
| `$query()` | Execute GraphQL queries |
| `$merge()` | Deep merge objects |
| `$sum()` | Sum array values |
| `$count()` | Count array items |
| `$reduce()` | Array reduce operation |
| `$map()` | Array map operation |
| `$components.Screen.fn.setContext()` | Set screen context for UI binding |

### JSONata Syntax Quick Reference

```jsonata
/* Assignment */
$variable := value

/* Function definition */
$myFunc := function($param) { $param * 2 }

/* String concatenation (use & not +) */
firstName & " " & lastName

/* Ternary */
condition ? trueValue : falseValue

/* Array operations */
$count(items)
$sum(items.value)
$map(items, function($i) { $i.name })
$reduce(items, function($acc, $i) { $acc + $i }, 0)

/* Null coalescing */
value ? value : defaultValue
```

---

## Debugging Patterns

### Common Errors and Solutions

| Error | Cause | Solution |
|-------|-------|----------|
| `">" cannot be used as unary operator` | JavaScript arrow function in JSONata context | Use Saved Script instead of inline transform |
| `Cannot read properties of undefined (reading 'xxx')` | Wrong data access pattern | Check `$` vs `$state.context` access |
| `Variable "$xxx" was not provided` | Missing GraphQL variable | Ensure prior node outputs the variable, check mustache syntax |
| Duration/time values are 0 | Wrong field or mixed format | Use robust getDurationMs(), check for milliseconds property |
| Query returns empty | Filter mismatch | Verify date formats, check field names in where clause |

### Data Exploration Strategy

When calculations produce unexpected results:

1. **Log the raw query response**
   ```javascript
   console.log(JSON.stringify($.myQuery, null, 2));
   ```

2. **Check field availability**
   ```javascript
   const sample = $.myQuery?.edges?.[0]?.node;
   console.log('Fields:', Object.keys(sample || {}));
   console.log('Aggregate fields:', Object.keys(sample?._aggregate || {}));
   ```

3. **Verify data access pattern**
   ```javascript
   console.log('Direct $:', $.myQuery);
   console.log('State context:', $state.context);
   ```

4. **Check intermediate values**
   ```javascript
   console.log('Raw duration values:', agg.runTime?.values);
   console.log('Parsed ms:', getDurationMs(agg.runTime?.values));
   ```

### Iterative Field Discovery

When documentation is limited, explore actual data:

```javascript
// Get a sample record to understand structure
const edges = $.workcenterHistory?.edges || [];
if (edges.length > 0) {
  const sample = edges[0].node;
  console.log('Sample record:', JSON.stringify(sample, null, 2));

  // Check what aggregate fields are available
  if (sample._aggregate) {
    console.log('Aggregate keys:', Object.keys(sample._aggregate));
  }
}
```

---

## Best Practices

1. **Separate concerns across nodes** — Boundaries/setup in Node 1, queries in Node 2, calculations in Node 3, context setting in Node 4

2. **Use saved scripts for any complexity** — Loops, conditionals, multi-step logic all belong in JavaScript saved scripts, not inline transforms

3. **Validate data before accessing nested properties**
   ```javascript
   const nodes = $.myQuery?.edges?.map(e => e.node) || [];
   ```

4. **Create both raw and formatted values**
   ```javascript
   return {
     valueMs: 3600000,
     valueFormatted: "1h 0m"
   };
   ```

5. **Handle empty/null states gracefully**
   ```javascript
   const result = count > 0 ? total / count : null;
   const display = result != null ? result.toFixed(2) : "N/A";
   ```

6. **Match query aliases to variable names**
   ```javascript
   // Query uses alias: data24h: workcenterHistory(...)
   // Access matches: const data24h = $.data24h?.edges?.map(e => e.node) || [];
   ```

7. **Document return object structure**
   ```javascript
   // Return shape for UI binding:
   // {
   //   metrics: { value, valuePct, ... },
   //   status: { code, duration, ... },
   //   lastRefresh: ISO string
   // }
   return { metrics, status, lastRefresh };
   ```