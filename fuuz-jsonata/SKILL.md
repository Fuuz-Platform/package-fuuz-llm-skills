---
name: fuuz-jsonata
description: "Write and debug JSONata expressions for the Fuuz Industrial Operations Platform. Trigger when requests mention JSONata, Fuuz expressions, bindings, $state, $query, $mutate, data transformation, flow expressions, screen bindings, JSONata context, moment.js in Fuuz, $map, $filter, $reduce, custom bindings, or expression debugging. Covers JSONata syntax (no ! operator, use $not(); dot-before-brace; & for concatenation; = not ==), 182+ platform bindings (core, type, conversion, string, array, object, parallel, MFGx app, moment, XML, EDI, encryption, network, calendar, scheduling, unit conversion), evaluation context ($state.payload, $state.context, $state.claims, $state.metadata), and integration with flows and screens."
---

# Fuuz JSONata Expressions

> **Purpose:** Guide Claude in writing JSONata expressions for the Fuuz Industrial Operations Platform. JSONata is the primary expression language used throughout Fuuz — in data flow transform nodes, screen element bindings, calculated fields, and query variable construction.

## 1. JSONata in Fuuz

JSONata is used in two main contexts within Fuuz:

### Data Flow Transform Nodes
- **Transform** nodes evaluate JSONata expressions against the current flow state
- **Set Context** and **Merge Context** nodes use JSONata to build context objects
- **Query variable** construction uses JSONata for dynamic values
- Access via `$state.payload`, `$state.context`, `$state.claims`, `$state.metadata`

### Screen Element Bindings
- Any screen element property can be bound to a JSONata expression
- Screen context includes `$components`, `$appConfig`, root `input`, and `metadata`
- Frontend-only functions like `$query()`, `$mutate()`, `$executeSavedQuery()` are available

## 2. Critical Syntax Rules

| Rule | Wrong | Right |
|------|-------|-------|
| No `!` operator | `!value` | `$not(value)` |
| String concat | `"a" + "b"` | `"a" & "b"` |
| Equality | `a == b` | `a = b` |
| Dot-before-brace | `items{...}` | `items.{...}` |
| Null check | `value != null` | `$exists(value)` |
| Banker's rounding | `$round(2.5)` → `2` | Use `$ceil`/`$floor` for predictable rounding |
| `$now()` is frozen | Changes per-flow-run | Use `$millis()` for elapsed time within a run |

## 3. Evaluation Context

### In Data Flows
```jsonata
$state.payload    /* Current payload (same as $) */
$state.context    /* User-defined context from Set/Merge Context nodes */
$state.claims     /* Auth claims of the triggering user */
$state.metadata   /* Flow metadata: {tenantId, enterpriseId, flowId} */
```

### In Screens
```jsonata
input             /* Root data bound to the screen/component */
metadata          /* Screen metadata */
$components       /* Access sibling component values */
$appConfig        /* Application configuration */
```

## 4. Platform Bindings Overview

Fuuz extends standard JSONata with **182+ custom bindings** organized by category:

| Category | Key Bindings | Purpose |
|----------|-------------|---------|
| **Core Utility** | `$log()`, `$generateUUID()`, `$hash()`, `$base64encode/decode()` | Debugging, IDs, encoding |
| **Type Predicates** | `$isString()`, `$isNumber()`, `$isArray()`, `$isObject()`, `$isNull()` | Type checking |
| **Conversion** | `$toNumber()`, `$toString()`, `$toBoolean()`, `$toArray()` | Type conversion |
| **String** | `$padStart()`, `$padEnd()`, `$regexMatch()`, `$camelCase()` | String manipulation |
| **Array** | `$groupBy()`, `$orderBy()`, `$uniqBy()`, `$chunk()`, `$flatten()` | Array operations |
| **Object** | `$pick()`, `$omit()`, `$deepMerge()`, `$set()`, `$get()` | Object manipulation |
| **Parallel Execution** | `$parallel()` | Run multiple async operations concurrently |
| **MFGx Application** | `$query()`, `$mutate()`, `$executeSavedQuery()`, `$executeFlow()` | Platform API calls |
| **Moment.js** | `$moment()`, `$utc()`, `$duration()` | Date/time operations |
| **XML** | `$xmlParse()`, `$xmlBuild()` | XML processing |
| **EDI** | `$ediParse()`, `$ediBuild()` | EDI document handling |
| **Encryption** | `$encrypt()`, `$decrypt()`, `$jwtDecode()` | Security operations |
| **Network** | `$http()`, `$ftp()`, `$smtp()` | External integrations |
| **Calendar/Scheduling** | `$getSchedule()`, `$getScheduleGroup()`, `$isWorkingTime()` | Production scheduling |
| **Unit Conversion** | `$convert()` | Engineering unit conversion |

> **See** `references/jsonata-bindings.md` for complete binding signatures and examples.
> **See** `references/time-schedule-bindings.md` for moment.js and scheduling binding details.

## 5. Common Patterns

### Safe Property Access
```jsonata
/* Null-safe traversal */
$exists(order.product) ? order.product.name : "No Product"

/* Default values */
($v := item.quantity; $exists($v) ? $v : 0)
```

### Array Transformations
```jsonata
/* Group and aggregate */
$groupBy(records, "category").{
  "category": $keys($)[0],
  "total": $sum($.quantity),
  "count": $count($)
}

/* Filter and map */
items[$$.status = "Active"].{
  "id": id,
  "label": name & " (" & partNumber & ")"
}
```

### Date/Time Operations
```jsonata
/* Current shift start */
$moment().startOf('day').add(6, 'hours').toISOString()

/* Date range for last 24 hours */
{
  "start": $moment().subtract(24, 'hours').toISOString(),
  "end": $moment().toISOString()
}
```

### Dynamic Query Variables
```jsonata
/* Build where clause dynamically */
{
  "where": $merge([
    {"status": {"in": ["Active", "In Progress"]}},
    $exists($selectedWorkcenter) ? {"workcenterId": {"equals": $selectedWorkcenter}} : {}
  ])
}
```

## 6. Debugging

- Use `$log(value, "label")` to output intermediate values to flow logs
- Use `$type(value)` to check what type a value is at runtime
- Remember `$now()` returns the same value throughout a single flow execution
- Check for `undefined` vs `null` — JSONata treats missing properties as `undefined`

## 7. Cross-References

- **fuuz-flows** — How JSONata expressions are used within flow transform nodes and context management
- **fuuz-screens** — How JSONata bindings work in screen element properties
- **fuuz-graphql** — How to construct GraphQL queries/mutations that JSONata expressions reference via `$query()` and `$mutate()`
