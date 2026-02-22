---
name: fuuz-jsonata
description: >
  Write, debug, and optimize JSONata expressions and JavaScript for the Fuuz Industrial Operations Platform (fuuz.com).
  Trigger this skill whenever the user mentions JSONata in Fuuz, Fuuz transformations, Fuuz bindings, data transforms,
  expression syntax, $moment, $query, $mutate, Fuuz Script Editor, payload access patterns, context variables,
  or any JSONata/JS work within Fuuz screens, data flows, or form components. Also trigger when the user references
  Fuuz-specific functions like $executeSavedQuery, $urlJoin, $isNotNilOrEmpty, or needs help with the $ and
  $variableName access patterns unique to the Fuuz runtime.
---

# Fuuz JSONata & JavaScript Skill

> **Purpose:** Guide Claude in writing JSONata expressions and JavaScript within the Fuuz Industrial Operations Platform. Fuuz uses JSONata extensively in screen transforms, data flow nodes, and form components. It also has a Script Editor with a unique JavaScript runtime.

## 1. JSONata in Fuuz — Where It's Used

JSONata expressions appear in multiple Fuuz contexts:

| Context | Syntax | Example Use |
|---------|--------|-------------|
| Screen DisplayText transforms | `{expression}` | `$formatNumber(oee, '#0.0') & '%'` |
| Data flow transform nodes | Full JSONata body | Payload reshaping between nodes |
| Form computed fields | JSONata expression | Calculated values from other fields |
| Table column transforms | JSONata expression | Color coding, formatting |
| Action pipeline variables | JSONata expression | Dynamic mutation parameters |
| Query variable binding | JSONata expression | Filter values from context |

## 2. JSONata Basics Quick Reference

### Navigation
```jsonata
product.name                    /* dot notation */
orders[0]                       /* array index */
orders[-1]                      /* last element */
orders.items.price              /* nested traversal */
```

### Filtering (Predicates)
```jsonata
orders[status = "Active"]                     /* equality */
inventory[quantity > reorderPoint]            /* comparison */
equipment[type = "CNC" and status = "Running"] /* compound */
```

### Constructing Output
```jsonata
{
  "summary": product.name,
  "totalQty": $sum(orders.quantity),
  "activeCount": $count(orders[status = "Active"])
}
```

### String Functions
```jsonata
$uppercase(status)              /* "ACTIVE" */
$lowercase(name)                /* "motor xl-500" */
$trim(description)              /* remove whitespace */
$substring(code, 0, 3)         /* first 3 chars */
$join(tags, ", ")               /* array to string */
$split(csv, ",")                /* string to array */
$replace(text, "old", "new")   /* substitution */
$pad(id, 6, "0")               /* zero-pad: "000042" */
```

### Numeric Functions
```jsonata
$sum(items.cost)                /* total */
$average(readings.value)        /* mean */
$max(temperatures)              /* highest */
$min(temperatures)              /* lowest */
$round(price, 2)                /* 2 decimal places */
$abs(delta)                     /* absolute value */
$floor(quantity)                /* round down */
$ceil(quantity)                 /* round up */
$formatNumber(oee, "#0.0")     /* formatted string */
$power(base, exponent)          /* exponentiation */
```

### Array Functions
```jsonata
$count(items)                   /* length */
$sort(items, function($a,$b){ $a.name > $b.name })
$reverse(items)
$distinct(items.category)       /* unique values */
$append(arrayA, arrayB)         /* concatenate */
$map(items, function($v){ $v.name })
$filter(items, function($v){ $v.active })
$reduce(items, function($acc,$v){ $acc + $v.qty }, 0)
```

### Conditional Logic
```jsonata
status = "Active" ? "Running" : "Stopped"                 /* ternary */
$exists(errorCode) ? "Error: " & errorCode : "OK"        /* existence check */
oee < 60 ? "red" : oee < 85 ? "orange" : "green"         /* nested ternary (common for KPIs) */
```

### Grouping (Powerful for Manufacturing Summaries)
```jsonata
/* Group inventory by status */
inventory{
  status: {
    "status": status,
    "totalQty": $sum(quantity),
    "lotCount": $count(lot_number),
    "products": $sort($distinct(product_id))
  }
}
```

### Variables & Functions
```jsonata
(
  $threshold := 85;
  $calcOEE := function($a, $p, $q) { $a * $p * $q * 100 };
  $oee := $calcOEE(availability, performance, quality);
  $oee < $threshold ? "Below Target" : "On Target"
)
```

## 3. Fuuz-Specific JSONata Bindings

These are custom functions available ONLY in the Fuuz runtime:

### Date/Time — `$moment()`
Fuuz uses a moment.js-based binding for all date operations:
```jsonata
$moment()                                          /* current timestamp */
$moment().format("YYYY-MM-DD")                     /* format */
$moment().tz("America/Detroit")                    /* timezone conversion */
$moment(dateField).diff($moment(otherDate), "hours") /* difference */
$moment().subtract(7, "days").toISOString()        /* relative dates */
$moment(timestamp).startOf("day")                  /* day boundary */
$moment(timestamp).endOf("shift")                  /* shift boundary */
$momentTzTimezones()                               /* all timezone names */
```

### GraphQL Data Access
```jsonata
$query("workOrders", { "where": { "status": { "equals": "Active" } } })
$mutate("updateWorkOrder", { "where": { "id": $id }, "data": { "status": "Complete" } })
$executeSavedQuery("dailyOEEReport", { "date": $moment().format("YYYY-MM-DD") })
```

### URL Utilities
```jsonata
$urlJoin("api", "v1", "workorders", $id)          /* path joining */
$urlStringify({ "status": "active", "page": 1 })  /* query string */
```

### Validation
```jsonata
$isNotNilOrEmpty(fieldValue)                       /* true if non-null and non-empty */
```

### ID Generation & Type Checking (Core Utilities)
Fuuz provides ~145 core utility bindings including:
- ID generation functions
- Type checking (isString, isNumber, isArray, isObject, etc.)
- String manipulation beyond standard JSONata
- Collection helpers

## 4. Fuuz Script Editor (JavaScript)

The Fuuz Script Editor runs JavaScript in a Node.js 20 / V8 11.3 environment with ES2023+ support. It has a **unique variable access pattern** that differs from standard JavaScript.

### Critical: Payload & Context Access

**Payload data** (the input to the script) is accessed via the `$` object:
```javascript
// ✅ CORRECT — access payload fields via $
const records = $.production_records;
const facility = $.facility;
const timestamp = $.timestamp;

// ❌ WRONG — these variables don't exist
const records = payload.production_records;   // "payload is not defined"
const records = input.production_records;     // "input is not defined"
const records = data.production_records;      // "data is not defined"
```

**Context data** (additional reference data) is accessed via `$variableName`:
```javascript
// ✅ CORRECT — context variables are prefixed with $
const products = $products;
const workOrders = $work_orders;
const settings = $settings;

// ❌ WRONG
const products = context.products;            // "context is not defined"
```

### Available Helpers in Script Editor
```javascript
$moment          // moment.js with timezone support
$moment.tz       // timezone-aware constructor
$                // payload data object
$variableName    // context variables (each is a top-level $-prefixed var)
```

### Diagnostic Script (for debugging what's available)
```javascript
// Run this to discover available variables and structure
const diagnostics = {
  payloadKeys: Object.keys($),
  payloadSample: JSON.stringify($).substring(0, 500),
  hasMoment: typeof $moment !== 'undefined',
};

// Check for specific context variables
try { diagnostics.hasProducts = typeof $products !== 'undefined'; } catch(e) {}
try { diagnostics.hasWorkOrders = typeof $work_orders !== 'undefined'; } catch(e) {}

diagnostics;
```

### Timezone Handling Rules
Business fields like `occurAt` should use **tenant timezone without UTC offset**:
```javascript
// Format: YYYY-MM-DDTHH:mm:ss.SSS (no Z, no offset)
const tenantTz = $state.context.tenantTimezone.setting.edges.node.defaultValue;
const localNow = $moment().tz(tenantTz).format("YYYY-MM-DDTHH:mm:ss.SSS");
```

System fields like `createdAt` and `updatedAt` remain in UTC.

### JavaScript Standards
- Use ES2023+ features (optional chaining `?.`, nullish coalescing `??`, `Array.at()`)
- Arrow functions preferred
- Template literals for string construction
- Destructuring for clean data access
- `const` by default, `let` when reassignment needed, never `var`

## 5. Common Manufacturing Patterns

### OEE Color Coding (Screen Transform)
```jsonata
$value < 60 ? "#ef4444" : $value < 85 ? "#f59e0b" : "#22c55e"
```

### Duration Formatting (PT format to readable)
```jsonata
(
  $seconds := $number($replace($replace(duration, "PT", ""), "S", ""));
  $hours := $floor($seconds / 3600);
  $mins := $floor(($seconds - $hours * 3600) / 60);
  $secs := $seconds - $hours * 3600 - $mins * 60;
  ($hours > 0 ? $string($hours) & "h " : "") &
  ($mins > 0 ? $string($mins) & "m " : "") &
  $string($round($secs, 0)) & "s"
)
```

### Inventory Grouping by Status
```jsonata
inventory_snapshot{
  status: {
    "status": status,
    "total_quantity": $sum(quantity),
    "lot_count": $count(lot_number),
    "products": $sort($distinct(product_id)),
    "locations": $sort($distinct(location))
  }
}
```

### Production History Summary
```jsonata
{
  "totalGood": $sum(records[result = "Good"].quantity),
  "totalScrap": $sum(records[result = "Scrap"].quantity),
  "quality": $round($sum(records[result = "Good"].quantity) /
             $sum(records.quantity) * 100, 1) & "%",
  "byWorkcenter": records{
    workcenterId: {
      "name": workcenterName,
      "output": $sum(quantity),
      "oee": $round($average(oee) * 100, 1)
    }
  }
}
```

### Shift-Aware Time Calculations
```jsonata
(
  $now := $moment();
  $shiftStart := $moment().startOf("day").add(6, "hours");
  $shiftEnd := $moment().startOf("day").add(14, "hours");
  $inShift := $now.isAfter($shiftStart) and $now.isBefore($shiftEnd);
  $inShift ? "Day Shift" : "Off Shift"
)
```

## 6. Common Anti-Patterns

- **Never use `$sort()` chained** — Use `$sort($distinct(values))` not `$distinct(values).$sort()`
- **Never assume `payload` or `context` exist** in Script Editor — Use `$` and `$variableName`
- **Never hardcode timezone offsets** — Always use `$moment().tz(tenantTimezone)`
- **Never use `var`** — Use `const` or `let`
- **Never skip null checks** — Use `$exists(field)` or optional chaining in JS
- **Never mix UTC and local timestamps** — Business fields are tenant-local, system fields are UTC
- **Never assume `$moment.tz()` is available** without checking — some Fuuz contexts may only have `$moment()`

## 7. Testing & Debugging

### Fuuz Transformation Explorer
Use the built-in Transformation Explorer to test JSONata expressions with sample payloads:
1. Enter your payload JSON
2. Enter your JSONata expression
3. Optionally add context JSON
4. Execute and inspect output

### Script Editor Debugging
```javascript
// Log to flow execution log
console.log("Processing", $.production_records.length, "records");

// Return diagnostic info as output
return {
  inputKeys: Object.keys($),
  recordCount: $.production_records?.length ?? 0,
  firstRecord: $.production_records?.[0] ?? null
};
```
