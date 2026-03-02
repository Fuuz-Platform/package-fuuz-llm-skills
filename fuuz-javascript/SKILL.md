---
name: fuuz-javascript
description: "Write JavaScript code for Fuuz Industrial Operations Platform flow nodes and custom scripts. Trigger when requests mention Fuuz JavaScript, script node, JavaScript transform, Fuuz code node, flow scripting, custom logic in flows, or JavaScript limitations in Fuuz. Covers the restricted sandbox environment (no let/const, no arrow functions, no template literals, var-only scoping, use strict required, no .toFixed()), available APIs, script node types (Script, External Script, Function, RFC Call, RFC Handler), and patterns for data transformation, error handling, and integration logic within Fuuz data flows."
---

# Fuuz JavaScript Runtime

> **Purpose:** Guide Claude in writing JavaScript code for Fuuz data flow script nodes. Fuuz provides a restricted JavaScript sandbox for cases where JSONata is insufficient — complex algorithmic logic, stateful transformations, or specific API integrations.

## 1. When to Use JavaScript vs JSONata

| Use JSONata When | Use JavaScript When |
|-----------------|-------------------|
| Data mapping and transformation | Complex algorithms (sorting, recursion) |
| Simple conditionals and filtering | Stateful multi-step processing |
| Array/object manipulation | String parsing with regex complexity |
| Date formatting | Binary data manipulation |
| Query variable construction | Try/catch error handling patterns |
| Most flow transform operations | Custom mathematical operations |

**Default to JSONata** — it's the primary expression language in Fuuz. Use JavaScript only when JSONata cannot express the required logic.

## 2. Sandbox Restrictions

The Fuuz JavaScript runtime is a **restricted sandbox** with significant limitations:

### Prohibited Syntax
```javascript
// WRONG — These will fail in Fuuz
let x = 1;              // No let
const y = 2;            // No const
const fn = () => {};    // No arrow functions
`template ${v}`;        // No template literals
num.toFixed(2);         // No .toFixed()

// CORRECT — Use these instead
'use strict';
var x = 1;              // var only
var y = 2;              // var only
var fn = function() {}; // function expressions
'value: ' + v;          // String concatenation
Math.round(num * 100) / 100;  // Manual rounding
```

### Required Practices
- **Always start with `'use strict';`**
- **Use `var` for all variable declarations**
- **Use `function` keyword for all function definitions**
- **Use string concatenation (`+`) instead of template literals**
- **Use `Math.round()` instead of `.toFixed()`**

## 3. Script Node Types

Fuuz provides 5 script-related flow nodes:

| Node | Purpose |
|------|---------|
| **Script** | Inline JavaScript execution within a flow |
| **External Script** | Reference to a shared/reusable script asset |
| **Function** | Reusable function definition callable from other nodes |
| **RFC Call** | Remote Function Call — invoke a function in another flow |
| **RFC Handler** | Handle incoming RFC requests from other flows |

### Script Node Structure
```javascript
'use strict';

// Access flow state
var payload = $state.payload;
var context = $state.context;

// Your logic here
var result = processData(payload);

// Return the new payload
return result;

function processData(data) {
  var output = [];
  for (var i = 0; i < data.length; i++) {
    output.push({
      id: data[i].id,
      value: Math.round(data[i].raw * 100) / 100
    });
  }
  return output;
}
```

## 4. Available APIs

### Flow State Access
```javascript
$state.payload    // Current flow payload
$state.context    // User-defined context
$state.claims     // Auth claims
$state.metadata   // Flow metadata {tenantId, enterpriseId, flowId}
```

### Utility Functions
```javascript
// Logging
$log(value, 'label');

// UUID generation
var id = $generateUUID();

// JSON operations
var obj = JSON.parse(jsonString);
var str = JSON.stringify(obj);
```

### Async Operations
```javascript
// HTTP requests (when available in flow context)
var response = $http({
  method: 'GET',
  url: 'https://api.example.com/data',
  headers: { 'Authorization': 'Bearer ' + token }
});
```

## 5. Common Patterns

### Data Transformation
```javascript
'use strict';

var items = $state.payload;
var result = [];

for (var i = 0; i < items.length; i++) {
  var item = items[i];
  if (item.status === 'Active' && item.quantity > 0) {
    result.push({
      id: item.id,
      name: item.name,
      total: Math.round(item.quantity * item.unitPrice * 100) / 100
    });
  }
}

return result;
```

### Error Handling
```javascript
'use strict';

var payload = $state.payload;

try {
  var parsed = JSON.parse(payload.rawData);
  return { success: true, data: parsed };
} catch (e) {
  $log('Parse error: ' + e.message, 'ERROR');
  return { success: false, error: e.message, raw: payload.rawData };
}
```

### Batch Processing
```javascript
'use strict';

var records = $state.payload;
var batchSize = 100;
var batches = [];

for (var i = 0; i < records.length; i += batchSize) {
  var batch = [];
  for (var j = i; j < Math.min(i + batchSize, records.length); j++) {
    batch.push(records[j]);
  }
  batches.push(batch);
}

return batches;
```

### Lookup Map Construction
```javascript
'use strict';

var records = $state.payload;
var lookup = {};

for (var i = 0; i < records.length; i++) {
  var key = records[i].partNumber;
  if (!lookup[key]) {
    lookup[key] = [];
  }
  lookup[key].push(records[i]);
}

return lookup;
```

## 6. Anti-Patterns

- **Don't use JavaScript for simple mappings** — JSONata handles these more cleanly
- **Don't use ES6+ syntax** — The sandbox doesn't support it
- **Don't forget `'use strict'`** — Always include it
- **Don't use `.toFixed()`** — Use `Math.round(n * Math.pow(10, d)) / Math.pow(10, d)`
- **Don't declare variables without `var`** — Strict mode will throw errors
- **Don't assume Node.js APIs** — The sandbox is restricted, not a full Node runtime
- **Don't use `for...of` or `for...in`** — Use traditional `for` loops with index

## 7. Cross-References

- **fuuz-flows** — How script nodes fit into the overall flow architecture and node configuration
- **fuuz-jsonata** — The preferred expression language; use JavaScript only when JSONata is insufficient
- **fuuz-graphql** — How to construct queries/mutations referenced from script nodes
