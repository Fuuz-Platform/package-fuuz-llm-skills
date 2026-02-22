# JSONata Custom Bindings and Patterns

Fuuz extends standard JSONata with hundreds of custom bindings for industrial data processing, scheduling, and data model access.

**CRITICAL: All Fuuz custom JSONata bindings are also available in JavaScript transforms.** Since Fuuz runs on Node.js, you can use `$moment()`, `$schedule()`, `$numeral()`, `$state.context`, and all other custom bindings directly in JavaScript transform nodes with the same syntax.

## Critical Context Behavior

### $ Context Shifting (Most Common Gotcha)

In JSONata, `$` refers to the **current context value** which changes during chained operations:

```jsonata
/* At top level, $ is the entire payload */
$;  /* → entire payload */

/* Inside array iteration, $ becomes each element */
$.data.(
  $;   /* → each element in $.data array */
  $$;  /* → ALWAYS the root payload (never shifts) */
);

/* Function chaining shifts context */
$moment($);           /* CORRECT - $ is still root */
$moment().add($);     /* WRONG - $ is now undefined */
$moment().add($$);    /* CORRECT - $$ is always root */

/* Chained property access shifts context */
$components.Screen.fn.setContext($);  /* WRONG - $ shifted to fn's value */
( $data := $; $components.Screen.fn.setContext($data) );  /* CORRECT */
```

**Golden Rule**: When you see `.` chaining, especially in function calls, capture the value first or use `$$`:

```jsonata
/* Pattern 1: Capture first */
( $payload := $; $someFunction().chain($payload) )

/* Pattern 2: Use $$ for root */
$someFunction().chain($$)

/* Pattern 3: Don't reference $ after dot-chains */
$.data | { "processed": true }  /* GOOD */
$.data.( $ | { "id": id } )     /* BAD - $ shifted */
$.data.( $$ | { "id": id } )    /* GOOD - $$ is root */
```

## Core JSONata Functions

### Null/Empty Checking
```jsonata
$isNilOrEmpty($)         /* Returns true if null, undefined, empty string, or empty array */
$isNotNilOrEmpty($)      /* Opposite of above */
$coalesce([a, b, c])     /* Returns first non-null value */
```

### State Management
```jsonata
$state.context           /* Access flow context set by setContext/mergeContext nodes */
$state.context.configKey /* Access specific context values */
```

### Query Functions

**IMPORTANT: Always use the Query NODE instead of $query() function.** The Query node is more performant because it splits variable transformation and API request into separate orchestration steps.

If you must use $query() in JavaScript:
```javascript
// $query() returns a promise - must use await
const result = await $query('application', 'query GetData { model { edges { node { id } } } }', {});
```

**Performance comparison:**
- Query node: Orchestration → Transform vars → Orchestration → API → Orchestration
- $query() function: Orchestration → Transform → API → Transform → Orchestration (slower)

### Time Functions
See `time-schedule-bindings.md` for complete reference. Quick access:
```jsonata
$moment()                /* Current time */
$momentTzGuess()         /* Current timezone */
$now()                   /* Current ISO timestamp */
```

### Number Formatting Functions
```jsonata
$numeral(value)          /* Format numbers, currencies, bytes */
$numeral('1KB').value()  /* Parse byte sizes → 1024 */
$numeral(1000).format('0,0') /* Format with thousands separator → "1,000" */
$numeral(0.974).format('0.0%') /* Format as percentage → "97.4%" */
```

**numeral.js capabilities:**
- Parse byte sizes: `$numeral('1KB').value()` → 1024, `$numeral('5MB').value()` → 5242880
- Format numbers: `$numeral(1000000).format('0.0a')` → "1.0m"
- Format currency: `$numeral(1000.12).format('$0,0.00')` → "$1,000.12"
- Format percentages: `$numeral(0.5).format('0%')` → "50%"
- Parse formatted numbers: `$numeral('$10,000.00').value()` → 10000

### Standard JSONata Functions (Available in Fuuz)
```jsonata
$count(array)            /* Array length */
$distinct(array)         /* Unique values */
$map(array, function)    /* Transform each element */
$filter(array, function) /* Filter array */
$merge(arrayOfObjects)   /* Merge objects into one */
$keys(object)            /* Get object keys */
$lookup(object, "key")   /* Get value by key */
$exists(value)           /* Check if value exists */
$not(boolean)            /* Logical NOT */
$append(array, value)    /* Add to array */
$sort(array)             /* Sort array */
$reverse(array)          /* Reverse array */
$join(array, separator)  /* Join array to string */
$split(string, separator) /* Split string to array */
$substring(string, start, length?) /* Extract substring */
$uppercase(string)       /* Convert to uppercase */
$lowercase(string)       /* Convert to lowercase */
$trim(string)            /* Remove whitespace */
$number(string)          /* Parse to number */
$string(value)           /* Convert to string */
$boolean(value)          /* Convert to boolean */
```

## Web Flow Specific Bindings

### Screen Context Access
```jsonata
$metadata.querystring.paramName     /* URL query parameters */
$metadata.settings.TimeZone         /* Tenant settings */
$components.Screen.context.key      /* Screen component context */
```

### Screen Context Writing
```jsonata
/* WRONG - context shifts */
$components.Screen.fn.setContext($)

/* CORRECT - capture first */
( $data := $; $components.Screen.fn.setContext($data) )

/* Common pattern for web flow response */
(
  $result := {
    "data": $.calculatedValues,
    "timestamp": $moment()
  };
  $components.Screen.fn.setContext($result);
  $result
)
```

## Common Patterns

### Building GraphQL Variables from Payload
```jsonata
/* No variables */
{}

/* Pass entire payload */
$

/* Build where clause */
{
  "where": {
    "id": { "_eq": $.recordId },
    "occurAt": { "_gte": $.startTime, "_lt": $.endTime }
  }
}

/* Conditional variables */
$count($.items) > 0 ? { "payload": $.items } : { "payload": [] }

/* Array mapping to create payloads */
$.records.(
  {
    "create": {
      "field1": field1,
      "field2": field2,
      "occurAt": $moment()
    }
  }
)
```

### Time Window Calculations
```jsonata
(
  $hourStart := $moment().startOf('hour').subtract(1, 'hour');
  $hourEnd := $moment($hourStart).add(1, 'hour');
  {
    "start": $hourStart,
    "end": $hourEnd,
    "periodAt": $hourStart
  }
)
```

### Data Extraction from GraphQL Results
```jsonata
/* Extract single field */
$.model.edges[0].node.field

/* Map to array of values */
$.model.edges.node.field

/* Map with transformation */
$.model.edges.node.{
  "id": id,
  "displayName": name & " (" & code & ")"
}

/* Filter then map */
$.model.edges[node.status = "Active"].node.id
```

### Conditional Data Shaping
```jsonata
{
  "baseData": $.input,
  "conditionalField": $.condition ? "valueIfTrue" : "valueIfFalse",
  "optionalArray": $.hasData ? $.items : []
}
```

### Merging Multiple Data Sources
```jsonata
$merge([
  { "source1": $.data1.edges.node },
  { "source2": $.data2.edges.node },
  { "calculated": $.value1 + $.value2 }
])
```

### Array Filtering and Aggregation
```jsonata
(
  $filtered := $.items[status = "Complete"];
  $total := $sum($filtered.quantity);
  {
    "items": $filtered,
    "totalQuantity": $total,
    "averageQuantity": $total / $count($filtered)
  }
)
```

## Transform Node Usage

The `transform` node evaluates JSONata expressions. The result becomes the payload for the next node.

**Simple extraction:**
```jsonata
$.workcenter
```

**Restructuring:**
```jsonata
{
  "workcenterId": $.value.after.workcenterId,
  "timestamp": $.value.after.occurAt,
  "quantity": $.value.after.quantityGood + $.value.after.quantityScrap
}
```

**Using context:**
```jsonata
{
  "data": $,
  "config": $state.context.oeeConfig,
  "calculatedValue": $.rawValue * $state.context.multiplier
}
```

## VariablesTransform Node Usage

The `variablesTransform` field on query/mutate nodes transforms the current payload into GraphQL variables:

```jsonata
/* For queries */
{
  "where": {
    "workcenterId": { "_eq": $.workcenterId },
    "occurAt": { "_gte": $.periodStart, "_lt": $.periodEnd }
  },
  "orderBy": [{ "occurAt": "ASC" }],
  "first": 1000
}

/* For mutations - create */
{
  "payload": [
    {
      "create": {
        "workcenterId": $.workcenterId,
        "occurAt": $moment(),
        "quantity": $.quantity
      }
    }
  ]
}

/* For mutations - update */
{
  "payload": [
    {
      "where": { "id": $.recordId },
      "update": {
        "processedAt": $moment(),
        "status": "Complete"
      }
    }
  ]
}

/* For mutations - array mapping */
{
  "payload": $.items.(
    {
      "create": {
        "dataPointId": dataPointId,
        "value": value,
        "recordedAt": $moment()
      }
    }
  )
}
```

## Debug Tips

1. **Use debugSource node** to test expressions with sample payloads
2. **Use log node** to inspect intermediate values: `messageTransform: "$"` logs entire payload
3. **Echo node** passes payload through unchanged - useful for breakpoints
4. **Check execution logs** for JSONata evaluation errors
5. **Test expressions incrementally** - build complex expressions step by step

## Using Custom Bindings in JavaScript

Since all Fuuz custom bindings are available in JavaScript transforms, you can combine JavaScript logic with Fuuz functions:

**Example: Calculate byte sizes in JavaScript**
```javascript
'use strict';

var dataPoints = $.dataPoints;
var totalBytes = 0;

for (var i = 0; i < dataPoints.length; i++) {
  // Use $numeral() in JavaScript
  var bytes = $numeral(dataPoints[i].dataSize).value();
  totalBytes += bytes;
}

var formattedTotal = $numeral(totalBytes).format('0.00b');

return {
  totalBytes: totalBytes,
  formattedTotal: formattedTotal,
  averageBytes: totalBytes / dataPoints.length
};
```

**Example: Complex time calculations in JavaScript**
```javascript
'use strict';

var timezone = $state.context.timezone;
var logs = $.productionLogs;
var results = [];

for (var i = 0; i < logs.length; i++) {
  // Use $moment() in JavaScript
  var occurAt = $moment(logs[i].occurAt).tz(timezone);
  var hourStart = occurAt.clone().startOf('hour');
  
  results.push({
    logId: logs[i].id,
    hourStart: hourStart,
    dayOfWeek: occurAt.format('dddd')
  });
}

return { processedLogs: results };
```

## Debug Tips

## Fuuz Custom JSONata Library — Extended Reference

The following functions are Fuuz-specific custom bindings grouped by category. These are available in **both JSONata transforms and JavaScript transforms**.

### Frontend Navigation Functions (Web Flows Only)

| Function | Description |
|---|---|
| `$app.fn.getModuleId()` | Get current module ID |
| `$app.fn.getModuleGroupId()` | Get current module group ID |
| `$app.fn.navigate(route)` | Navigate to a route |
| `$app.fn.openScreenDialog(screenId, options)` | Open a screen in a dialog |
| `$app.fn.closeScreenDialog()` | Close the current screen dialog |
| `$app.fn.refreshScreen()` | Refresh the current screen |

### Table Functions (Web Flows Only)

| Function | Description |
|---|---|
| `$components.TableName.fn.search()` | Refresh/reload a table |
| `$components.TableName.fn.getSelectedRows()` | Get currently selected rows |
| `$components.TableName.fn.clearSelection()` | Clear table selection |
| `$components.TableName.fn.exportCsv()` | Export table data as CSV |

### Form Functions (Web Flows Only)

| Function | Description |
|---|---|
| `$components.FormName.fn.submit()` | Submit a form programmatically |
| `$components.FormName.fn.reset()` | Reset form to initial state |
| `$components.FormName.fn.validate()` | Trigger form validation |
| `$components.FormName.fn.getData()` | Get current form data |
| `$components.FormName.fn.setData(data)` | Set form data programmatically |

### Screen Context Functions (Web Flows Only)

| Function | Description |
|---|---|
| `$components.Screen.fn.setContext(data)` | Set screen-level context (replaces) |
| `$components.Screen.fn.mergeContext(data)` | Merge into screen context |
| `$components.Screen.context.keyName` | Read screen context value |

### Shared Utility Functions

| Function | Description |
|---|---|
| `$cuid()` | Generate a unique CUID (collision-resistant unique ID) |
| `$uuid()` | Generate a UUID v4 |
| `$coalesce([a, b, c])` | Return first non-null/non-undefined value |
| `$isNilOrEmpty(val)` | Returns `true` if null, undefined, empty string, or empty array |
| `$isNotNilOrEmpty(val)` | Opposite of `$isNilOrEmpty()` |
| `$jsonStringify(obj)` | Stringify object to JSON string |
| `$jsonParse(str)` | Parse JSON string to object |
| `$hash(value)` | Generate hash of a value |
| `$base64Encode(str)` | Encode string to Base64 |
| `$base64Decode(str)` | Decode Base64 string |

### Array/Object Operations

| Function | Description |
|---|---|
| `$flatten(array)` | Flatten nested arrays to single level |
| `$zip(arrays)` | Combine arrays element-by-element |
| `$unique(array)` | Remove duplicate values from array |
| `$groupBy(array, key)` | Group array elements by a key |
| `$orderBy(array, key, direction)` | Sort array by key |
| `$pick(object, keys)` | Select specific keys from an object |
| `$omit(object, keys)` | Remove specific keys from an object |
| `$deepMerge(obj1, obj2)` | Deep merge two objects |

### Date/Time (Moment.js Extensions)

| Function | Description |
|---|---|
| `$moment()` | Current UTC time (moment object) |
| `$moment(dateStr)` | Parse date string to moment |
| `$moment().tz(timezone)` | Convert to timezone |
| `$moment().startOf('hour')` | Truncate to start of hour/day/week/month |
| `$moment().endOf('hour')` | End of hour/day/week/month |
| `$moment().add(n, 'hours')` | Add time units |
| `$moment().subtract(n, 'days')` | Subtract time units |
| `$moment().diff(other, 'minutes')` | Difference between moments |
| `$moment().format('YYYY-MM-DD')` | Format as string |
| `$moment().toISOString()` | Output as ISO 8601 string |
| `$moment().valueOf()` | Output as Unix epoch milliseconds |
| `$momentTz(dateStr, timezone)` | Parse with specific timezone |
| `$momentTzGuess()` | Guess browser timezone |
| `$momentDuration(iso8601)` | Parse ISO 8601 duration |
| `$momentDuration(value, unit)` | Create duration from value and unit |
| `$now()` | Current ISO timestamp string |

### Type Checking

| Function | Description |
|---|---|
| `$isArray(val)` | Check if value is an array |
| `$isObject(val)` | Check if value is a plain object |
| `$isString(val)` | Check if value is a string |
| `$isNumber(val)` | Check if value is a number |
| `$isBoolean(val)` | Check if value is a boolean |
| `$isNull(val)` | Check if value is null |
| `$isUndefined(val)` | Check if value is undefined |

### String Utilities

| Function | Description |
|---|---|
| `$padStart(str, length, char)` | Pad string start |
| `$padEnd(str, length, char)` | Pad string end |
| `$camelCase(str)` | Convert to camelCase |
| `$snakeCase(str)` | Convert to snake_case |
| `$kebabCase(str)` | Convert to kebab-case |
| `$capitalize(str)` | Capitalize first letter |
| `$truncate(str, length)` | Truncate with ellipsis |
| `$slugify(str)` | Convert to URL-safe slug |

---

## Performance Considerations

1. **Avoid nested loops** - JSONata is performant but deeply nested iterations can slow down
2. **Filter early** - Use predicates `[condition]` to reduce data before processing
3. **Use $distinct carefully** - Can be expensive on large arrays
4. **Pre-calculate in JavaScript** - Complex algorithms are faster in JavaScript transform node
5. **Leverage query filters** - Filter in GraphQL where clause rather than after retrieval
