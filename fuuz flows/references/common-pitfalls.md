# Common Pitfalls and Advanced Patterns

Critical knowledge for avoiding common mistakes when building Fuuz flows and scripts.

## Data Access Patterns

### Critical: Know Where Your Data Comes From

Understanding data access is essential to avoid the most common flow debugging issues.

| Context | Access Pattern | Example |
|---------|----------------|---------|
| **Transform nodes (JSONata)** | Direct `$` for query results | `$.myQuery.edges.node` |
| **Transform nodes (JSONata)** | `$state.context` for prior node data | `$state.context.boundary.workcenterId` |
| **JavaScript nodes** | Direct `$` for query results | `const data = $.myQuery;` |
| **JavaScript nodes** | `$state.context` for prior node context | `const wcId = $state.context.boundary.workcenterId;` |

### Common Data Access Mistakes

```javascript
// ❌ WRONG - these patterns don't work
const data = $.queryData.myQuery;     // No queryData wrapper exists
const data = input.myQuery;           // No input object exists
const data = $state.myQuery;          // Query results aren't in $state

// ✅ CORRECT - query results are at $ root
const data = $.myQuery;

// ✅ CORRECT - context from prior nodes uses $state.context
const workcenterId = $state.context.boundary.workcenterId;
const boundaries = $state.context.boundary.boundaries;
```

### Extracting Nodes from GraphQL Edges

Always use safe navigation and provide fallbacks:

```javascript
// ✅ Robust pattern with null safety
const records = $.myQuery?.edges?.map(e => e.node) || [];

// ✅ With type checking
const edges = $.myQuery?.edges || [];
const records = Array.isArray(edges) ? edges.map(e => e.node) : [];
```

## Inline Transform Parsing Pitfall

**CRITICAL: Fuuz parses inline transforms as JSONata, not JavaScript.**

This is one of the most confusing errors for developers coming from JavaScript backgrounds.

### The Problem

```javascript
// ❌ This FAILS in inline transform (parsed as JSONata)
const n = (v) => v || 0;
// Error: ">" cannot be used as unary operator

// The "=>" arrow function syntax is JavaScript-only
// JSONata sees ">" and tries to parse it as a comparison operator
```

### The Solution

Use a **JavaScript Saved Script** for any code containing:
- Arrow functions (`=>`)
- `const`, `let`, `var` declarations  
- Standard JavaScript methods
- Any multi-line logic

```javascript
// ✅ CORRECT - Use JavaScript Saved Script node
'use strict';

const n = (v) => v || 0;
const result = $.values.map(n);
return { processed: result };
```

### When to Use Each

**Inline JSONata Transform:**
```jsonata
/* Simple data mapping */
$.data.{ "id": id, "name": name }

/* Setting context */
( $result := $; $components.Screen.fn.setContext("data", $result); $result )

/* Basic field selection */
$.edges.node.quantity
```

**JavaScript Saved Script:**
```javascript
/* Complex calculations with loops */
/* Mutable variables */
/* Array manipulation with standard methods */
/* Conditional logic branches */
/* Object building/transformation */
```

## Duration Field Handling

Duration fields in Fuuz can return **mixed formats** depending on the value and context, leading to calculation errors if not handled properly.

### The Problem: Mixed Format Returns

```javascript
// Format 1: Object with milliseconds (when non-zero)
{ text: "PT1H30M", milliseconds: 5400000 }

// Format 2: Plain string (commonly when zero)
"PT0M"

// Format 3: Array of values (in aggregated data)
[
  { text: "PT1H", milliseconds: 3600000 },
  { text: "PT30M", milliseconds: 1800000 }
]
```

### Robust Parsing Function

Use this function to handle all duration formats safely:

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

### Usage in Aggregation Queries

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

// Usage
const nodes = $.workcenterHistory?.edges?.map(e => e.node) || [];
const totals = sumAggregates(nodes);
```

### Duration Format Conversion

```javascript
// Milliseconds to hours/minutes display
const msToDisplay = (ms) => {
  const hours = Math.floor(ms / 3600000);
  const minutes = Math.floor((ms % 3600000) / 60000);
  return hours > 0 ? `${hours}h ${minutes}m` : `${minutes}m`;
};

// Parse ISO 8601 duration string to milliseconds
const isoDurationToMs = (isoStr) => {
  // Use $momentDuration in JSONata/JavaScript
  return $momentDuration(isoStr).asMilliseconds();
};
```

## Variable Passing Between Nodes

### Using Mustache Syntax in GraphQL Variables

When passing values from a prior node's output to a GraphQL query:

```json
{
  "wcId": "{{workcenterId}}",
  "startTime": "{{boundaries.windowStart}}",
  "endTime": "{{boundaries.now}}"
}
```

The values come from the **prior node's return object**:

```javascript
// Prior node returns this:
return {
  workcenterId: "abc-123",
  boundaries: {
    windowStart: "2026-01-01T00:00:00Z",
    now: "2026-01-02T00:00:00Z"
  }
};

// Next node's GraphQL variables can access these with {{}}
```

## Debugging Patterns

### Data Exploration Strategy

When calculations produce unexpected results, follow this systematic approach:

**1. Log the raw query response**
```javascript
console.log('Raw query result:', JSON.stringify($.myQuery, null, 2));
```

**2. Check field availability**
```javascript
const sample = $.myQuery?.edges?.[0]?.node;
console.log('Available fields:', Object.keys(sample || {}));
console.log('Aggregate fields:', Object.keys(sample?._aggregate || {}));
```

**3. Verify data access pattern**
```javascript
console.log('Direct $ access:', $.myQuery ? 'SUCCESS' : 'FAILED');
console.log('State context:', $state.context);
```

**4. Check intermediate values**
```javascript
const agg = sample?._aggregate;
console.log('Raw duration values:', agg.runTime?.values);
console.log('Parsed ms:', getDurationMs(agg.runTime?.values));
```

### Iterative Field Discovery

When documentation is limited or unclear:

```javascript
'use strict';

// Get a sample record to understand structure
const edges = $.workcenterHistory?.edges || [];
console.log(`Total records: ${edges.length}`);

if (edges.length > 0) {
  const sample = edges[0].node;
  console.log('Sample record structure:', JSON.stringify(sample, null, 2));
  
  // Check what aggregate fields are available
  if (sample._aggregate) {
    console.log('Available aggregate fields:', Object.keys(sample._aggregate));
    
    // Check what's in each aggregate
    Object.keys(sample._aggregate).forEach(key => {
      console.log(`${key}:`, sample._aggregate[key]);
    });
  }
}

// Return actual data for flow to continue
return { explored: true, sampleData: edges[0]?.node };
```

### Common Error Messages and Solutions

| Error Message | Cause | Solution |
|---------------|-------|----------|
| `">" cannot be used as unary operator` | JavaScript arrow function in JSONata context | Use JavaScript Saved Script instead of inline transform |
| `Cannot read properties of undefined (reading 'xxx')` | Wrong data access pattern | Verify `$` vs `$state.context` access |
| `Cannot read properties of undefined (reading 'node')` | Query returned empty edges array | Add guard clause checking edges length before accessing `[0].node` |
| `Variable "$xxx" was not provided` | Missing GraphQL variable | Ensure prior node outputs the variable, check mustache `{{}}` syntax |
| Duration/time values are 0 | Wrong field or mixed format | Use robust `getDurationMs()`, check for milliseconds property |
| Query returns empty | Filter mismatch or date format issue | Verify date formats are ISO, check field names in where clause |
| `undefined is not a function` | Calling method on null/undefined | Add null checks with optional chaining `?.` |

## GraphQL Query Patterns

### Query Aliasing for Multiple Time Windows

Use aliases to query the same entity with different filters in a single request:

```graphql
query MultiWindow(
  $wcId: String!,
  $start24h: DateTime!,
  $start7d: DateTime!,
  $now: DateTime!
) {
  # 24-hour window
  data24h: workcenterHistory(
    where: {
      _and: [
        { workcenterId: { _eq: $wcId } }
        { occurAt: { _gte: $start24h, _lt: $now } }
      ]
    }
  ) {
    edges { node { id mode state duration } }
  }
  
  # 7-day window
  data7d: workcenterHistory(
    where: {
      _and: [
        { workcenterId: { _eq: $wcId } }
        { occurAt: { _gte: $start7d, _lt: $now } }
      ]
    }
  ) {
    edges { node { id mode state duration } }
  }
}
```

Access in JavaScript:
```javascript
const data24h = $.data24h?.edges?.map(e => e.node) || [];
const data7d = $.data7d?.edges?.map(e => e.node) || [];
```

## Nested Relation Where Clause Depth Limit

**Problem:** Fuuz where clauses on relation fields only support **one level of nesting**. Deep nested filtering like `site → area → line → cell → asset` will fail.

```jsonata
/* ❌ FAILS — too many nested levels */
{
  "where": {
    "areas": {
      "lines": {
        "cells": {
          "assets": { "id": { "_eq": "..." } }
        }
      }
    }
  }
}
```

**Error:** `Field "lines" is not defined by type "AreaRelationWhereInput"`

**Solution:** Traverse the hierarchy **upward** from the child record using relation fields instead of filtering **downward** from the parent.

```graphql
/* ✅ CORRECT — query from child, traverse up via relations */
query GetHierarchy($where: DataPointWhereInput) {
  dataPoint(where: $where, first: 1) {
    edges {
      node {
        id
        asset {
          id
          cell {
            line {
              area {
                site { id code }
              }
            }
          }
        }
      }
    }
  }
}
```

---

## Empty Edges Guard in JavaScript

**Problem:** Accessing `$.model.edges[0].node` when the query returns no results causes `Cannot read properties of undefined (reading 'node')`.

```javascript
/* ❌ FAILS when edges is empty */
var dp = $.dataPoint.edges[0].node;
```

**Solution:** Always guard for empty edges before accessing:

```javascript
/* ✅ CORRECT — guard clause */
var edges = $.dataPoint.edges;
if (!edges || edges.length === 0) {
  return { skip: true };
}
var dp = edges[0].node;
```

Pair with a downstream `when` node: `$.skip != true`

---

## Boolean Values from SCADA/PLC as 1/0

**Problem:** SCADA systems and PLCs commonly send boolean values as `1`/`0` (numeric) or `"1"`/`"0"` (string) rather than `true`/`false`. Storing these directly in a Boolean field will fail or produce unexpected results.

**Solution in JSONata variablesTransform:**

```jsonata
"valueBool": $val = 1 or $val = "1" or $val = true ? true : false
```

---

## String Values Needing Numeric Coercion

**Problem:** Values from UNS/dataBroker/MQTT often arrive as strings even when the dataPoint dataType is Float. Passing a string to a `Float!` GraphQL field causes a type error.

**Error:** `Field "value" of required type "Float!" was not provided` (the string value is silently dropped by JSONata when the GraphQL schema expects Float)

**Solution:** Use `$number()` in JSONata to coerce:

```jsonata
"value": $number($val) != null ? $number($val) : 0
```

---

## JavaScript Runtime Limitations (CRITICAL)

The Fuuz JS runtime (`javascriptTransform` nodes) is a restricted sandbox. These are hard failures that produce runtime errors, not warnings.

### `.toFixed()` Is Not Supported

`.toFixed()` on Number objects throws `cannot read properties of toFixed`. This applies everywhere in Fuuz JS — flow transforms, computed fields, etc.

**Always use `Math.round()` instead:**

```javascript
// ❌ FAILS — "cannot read properties of toFixed"
var rounded = someNumber.toFixed(4);

// ✅ WORKS — manual rounding
var rounded = Math.round(someNumber * 10000) / 10000;

// ✅ WORKS — reusable helper
function rd(val, dec) {
  var m = Math.pow(10, dec);
  return Math.round(val * m) / m;
}
```

### Variable Scoping Is Unreliable Inside Loops

Variables declared with `var` inside loop bodies may silently fail to resolve when referenced later in the **same iteration**.

```javascript
// ❌ UNRELIABLE — may produce "roundedR is not defined"
for (var i = 0; i < results.length; i++) {
  var roundedR = Math.round(results[i].r * 10000) / 10000;
  insights.push({ value: roundedR });
}

// ✅ RELIABLE — inline the expression directly
for (var i = 0; i < results.length; i++) {
  insights.push({
    value: Math.round(results[i].r * 10000) / 10000
  });
}
```

**Rule:** If a variable is only used once after assignment inside a loop body, inline it.

### General JS Runtime Capabilities

**Works:** `'use strict'`, `var` declarations, `Math.*`, `JSON.stringify()`/`JSON.parse()`, `Object.keys()`, `Array.isArray()`, `.push()`, `.slice()`, `.splice()`, `parseFloat()`, `parseInt()`, `new Date().toISOString()`, string concatenation with `+`, `for`/`while`/`if/else`/`switch`, `function` declarations

**Does NOT work:** `.toFixed()`, template literals (`` `${var}` ``), `let`/`const`, arrow functions (`=>`), destructuring, spread operator (`...`), `Promise`, `async/await`, external libraries/`require()`

---

## JSONata Error Handling Patterns

### Missing Closing Braces in $jsonStringify()

**Symptom:** `Expected "}", got ")"`

**Cause:** Incomplete object structure inside `$jsonStringify()`

```jsonata
// ❌ WRONG — missing closing braces
"payload": $jsonStringify({
  "value": {
    "nested": {
      "field": "value"
    }
  "encoding": "UTF8"
})

// ✅ CORRECT
"payload": $jsonStringify({
  "value": {
    "nested": {
      "field": "value"
    }
  },
  "timestamp": $moment()
}),
"encoding": "UTF8"
```

### Conditional Field Selection

```jsonata
// Handle optional hierarchy levels
$line := $wc.line ? $wc.line : $cell.line;
$parentId := $cell.id ? $cell.id : $line.id;

// Use in topic construction
$cell.code ? $cell.code : "nocell"
```

---

## Context Management Best Practices

- **Always label context** with a wrapping variable: use `{"plexResponse": $}` instead of just `$`
- **Never set or merge context** with a transform that is just `$` (use a wrapper object)
- **Prune unnecessary data** from context before storage
- **Remove large context** when no longer needed
- **Warning**: Context is shared with each downstream node. Forks and broadcasts multiply memory usage by the number of concurrent transactions.

---

## Best Practices Checklist

✅ **Validate data before accessing nested properties**
```javascript
const nodes = $.myQuery?.edges?.map(e => e.node) || [];
```

✅ **Create both raw and formatted values**
```javascript
return {
  valueMs: 3600000,
  valueFormatted: "1h 0m",
  valueHours: 1.0
};
```

✅ **Handle empty/null states gracefully**
```javascript
const result = count > 0 ? total / count : null;
const display = result != null ? result.toFixed(2) : "N/A";
```

✅ **Match query aliases to variable names**
```javascript
// Query uses alias: data24h: workcenterHistory(...)
// Access matches: const data24h = $.data24h?.edges || [];
```

✅ **Document return object structure**
```javascript
// Return shape for UI binding:
// {
//   metrics: { value, valuePct, trend },
//   status: { code, duration, color },
//   lastRefresh: ISO timestamp string
// }
return { metrics, status, lastRefresh };
```

✅ **Use saved scripts for repetitive logic**
- OEE calculations
- Time boundary generation
- Standard data transformations
- API response formatting

✅ **Test with edge cases**
- Empty result sets
- Null/undefined values
- Zero durations
- Single record vs multiple records
- Date boundary conditions
