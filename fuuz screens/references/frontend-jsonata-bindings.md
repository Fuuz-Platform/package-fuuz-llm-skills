# Fuuz Frontend JSONata Bindings for Screens

> Curated subset of the 294 custom JSONata bindings available in screen expressions.
> Covers navigation, data access, form operations, formatting, and component functions.

---

## Frontend JSONata Bindings for Screens

This section covers the JSONata bindings most relevant to screen design. All screen expressions use JSONata — JavaScript is only available in web flows. For the full 294-binding reference, consult the `fuuz_jsonata_bindings_skill.md` project knowledge file.

### Binding Context Rules

1. All custom bindings use the `$` prefix (e.g., `$uuid()`, `$moment()`).
2. **Frontend bindings** — Only available in Screen Designer expressions (action chains, `__transform`, event handlers).
3. **Shared bindings** — Available in both Screen Designer and Data Flows.
4. **Backend bindings** — Only available in server-side Data Flows (not in screens).
5. Standard JSONata from [docs.jsonata.org](https://docs.jsonata.org/) is fully supported alongside custom bindings.
6. Component bindings use `$components.{ComponentName}.fn.{method}()` — replace with the actual component `elementName`.

### Component Bindings (Frontend Only)

These are the primary bindings for screen interactivity. Replace `{Table}`, `{Form}`, `{Container}`, `{ActionButton}` with actual component element names.

**Table Operations:**

| Binding | Purpose |
|---|---|
| `$components.{Table}.fn.search({rowsPerPage: 500})` | Execute table search using filter form values. `rowsPerPage` optional. |
| `$components.{Table}.fn.loadData({filter: {field: {_eq: value}}, rowLimit: 500})` | Load data with explicit filter (bypasses filter form). |
| `$components.{Table}.fn.setData([{id, ...fields}])` | Overwrite entire table data with custom array. `id` required per row. |
| `$components.{Table}.fn.addRows([{id, ...fields}])` | Append rows to end of table. |
| `$components.{Table}.fn.updateRows([{id, ...fields}])` | Match by `id` and update existing rows. |
| `$components.{Table}.fn.upsertRows([{id, ...fields}])` | Add new rows and update existing by `id`. |
| `$components.{Table}.fn.deleteRows([id1, id2])` | Delete rows by ID array. |
| `$components.{Table}.fn.selectRow([id1, id2])` | Select specific rows by ID. |
| `$components.{Table}.fn.deselectRow([id1, id2])` | Deselect specific rows. |
| `$components.{Table}.fn.toggleRowSelection([id1, id2])` | Toggle selection state. |
| `$components.{Table}.fn.selectAllRows()` | Select all rows regardless of filtering/grouping. |
| `$components.{Table}.fn.deselectAllRows()` | Clear all selections. |
| `$components.{Table}.fn.selectAllFiltered()` | Select only filtered rows. |
| `$components.{Table}.fn.deselectAllFiltered()` | Clear filtered selections. |
| `$components.{Table}.fn.setSelectedRows([id1, id2])` | Set exact selection by IDs. |

**Form Operations:**

| Binding | Purpose |
|---|---|
| `$components.{Form}.fn.setValue(field, data)` | Set a form field value. `field` is the data path. |
| `$components.{Form}.fn.focus(field)` | Set cursor focus to a field. |
| `$components.{Form}.fn.blur(field)` | Remove cursor focus from a field. |
| `$components.{Form}.fn.loadData()` | Reload form data from the bound data model. |
| `$components.{Form}.fn.getUpdateMutation()` | Generate GraphQL update mutation (requires data model). |
| `$components.{Form}.fn.getUpdatePayload()` | Get the mutation payload for form changes. |
| `$components.{Form}.fn.validate()` | Validate form and return error array. |
| `$components.{Form}.fn.save(isSaveAll?)` | Save form. Default saves modified fields only; pass `true` to save all. |
| `$components.{Form}.fn.getVariables()` | Get all form variables. |
| `$components.{Form}.fn.delete()` | Delete the form record. |

**Container Operations:**

| Binding | Purpose |
|---|---|
| `$components.{Container}.fn.hide()` | Hide a container. |
| `$components.{Container}.fn.show()` | Show a container. |
| `$components.{Container}.fn.toggle()` | Toggle container visibility. |
| `$components.{Container}.fn.resetHidden()` | Reset all hidden fields in container. |

**Cross-Component Execution:**

| Binding | Purpose |
|---|---|
| `$components.{ActionButton}.fn.execute({field: value})` | Execute another action button's action chain. Optional parameters passed as object. |

### Navigation & Dialog Bindings (Frontend Only)

| Binding | Purpose |
|---|---|
| `$navigateTo(pathname, options)` | Navigate to a page. Options: `{newWindow: true, replace: true}` |
| `$navigateBack()` | Navigate back one page. |
| `$navigateReload()` | Reload the current page. |
| `$addNotificationMessage(title, options)` | Show notification snackbar. Options: `{severity: "success"\|"error"\|"warning"\|"info"}` |
| `$showAlertDialog(message, options)` | Show alert dialog. Options: `{title, icon, buttonColor}` |
| `$showConfirmDialog(message, options)` | Show confirm dialog. Options: `{title, delayTimeInSeconds, icon, buttonColor}` |
| `$showFormDialog(object)` | Show form dialog from JSON definition. |
| `$writeToClipboard(data)` | Write data to clipboard. |
| `$readFromClipboard()` | Read data from clipboard. |

### Data & Integration Bindings (Shared — Usable in Screens)

These are available in both screens and flows, commonly used in action chain transformations and dynamic data props.

**GraphQL Operations (used with Fuuz GraphQL syntax):**

| Binding | Example |
|---|---|
| `$query(object)` | `$query({"statement": "query { workcenter { edges { node { id name } } } }", "variables": {}})` |
| `$mutate(object)` | `$mutate({"statement": "mutation CreateWC($payload: [WorkcenterCreatePayloadInput!]!) { createWorkcenter(payload: $payload) { id } }", "variables": {"payload": [{"create": {"name": "WC-1"}}]}})` |

**Flow & Transform Execution:**

| Binding | Example |
|---|---|
| `$executeFlow(flowId, payload)` | `$executeFlow("flow-uuid", {"workcenterId": id})` — Call a request/response flow and return result. |
| `$executeTransform(transformId, payload)` | `$executeTransform("transform-uuid", data)` — Execute a saved transform. |

**Device Communication:**

| Binding | Example |
|---|---|
| `$executeDeviceFunction(object)` | `$executeDeviceFunction({"deviceId": "dev-1", "functionId": "readTag", "request": {}})` |
| `$executeDeviceGatewayFunction(object)` | `$executeDeviceGatewayFunction({"deviceGatewayId": "gw-1", "functionId": "writeTag", "request": {}})` |

**External Integration:**

| Binding | Example |
|---|---|
| `$integrate(object)` | `$integrate({"Request": {"connectionName": "SAP", "payload": []}})` |

### Commonly Used Shared Utilities in Screen Expressions

| Binding | Purpose |
|---|---|
| `$uuid()` | Generate a V4 UUID (for new record IDs). |
| `$cuid()` | Generate a CUID. |
| `$moment(string?, format?, timezone?)` | Create Moment.js object. Chain `.format()`, `.add()`, `.subtract()`, `.diff()`, `.startOf()`, `.endOf()`. |
| `$momentTz(string?, format?, timezone?)` | Create Moment.js with explicit timezone: `$momentTz("2024-01-01", "YYYY-MM-DD", "America/Detroit")`. |
| `$momentTzGuess()` | Guess user's timezone from browser (frontend only — returns UTC in backend). |
| `$momentMax(array)` | Return most distant future from array of moment objects. |
| `$momentMin(array)` | Return most distant past from array of moment objects. |
| `$now()` | Current UTC timestamp in ISO 8601 string. |
| `$log(message, meta?, level?)` | Log to console. Levels: error, warn, info, verbose, debug, silly. |
| `$throw(message, object?)` | Halt transform execution with error. |
| `$coalesce(array)` | Return first non-null/undefined value. |
| `$wait(ms)` | Pause execution for milliseconds. |
| `$jsonParse(string)` | Parse JSON string to object. |
| `$jsonStringify(value, options?)` | Serialize to JSON string. Options: `{space: 2}`. |
| `$numeral(value)` | Numeral.js object for formatting: `$numeral(0.85).format("0.0%")`. |
| `$markdownToHtml(string)` | Convert markdown to HTML (for RichText content). |
| `$richTextToPlainText(object)` | Convert RawDraftContent to plain text. |
| `$getCalendar(object)` | Get production calendar by `{id}` or `{name}`. |
| `$document(object, options?)` | Generate PDF: `$document({"documentDesignId": id, "payload": {}}, {"returnContent": true})`. |

**Type Checking (use in conditional transforms):**

| Binding | Purpose |
|---|---|
| `$isNilOrEmpty(value)` | Check if null, undefined, or empty string/array/object. |
| `$isNil(value)` | Check if null or undefined. |
| `$isEmpty(value)` | Check if empty array, object, or string. |
| `$isArray(value)` | Check if value is an array. |
| `$isString(value)` | Check if value is a string. |
| `$isNumber(value)` | Check if value is a number. |
| `$isObject(value)` | Check if value is an object. |
| `$isBoolean(value)` | Check if value is a boolean. |

**Object Manipulation (use in action chain pre-transforms):**

| Binding | Purpose |
|---|---|
| `$path(object, string)` | Get value at nested path: `$path(obj, "parent.child")`. |
| `$omit(object, keys)` | Remove properties from object. |
| `$pick(object, keys)` | Keep only specified properties. |
| `$mergeDeep(arrayOfObjects)` | Deep merge multiple objects. |
| `$has(object, path)` | Check if property exists at path. |
| `$values(object)` | Get array of object values. |
| `$toPairs(object)` | Convert to `[[key, value], ...]` array. |
| `$fromPairs(array)` | Convert `[[key, value], ...]` to object. |
| `$flattenObject(object, options?)` | Flatten nested object to single level. Options: `{delimiter: ".", safe: true}`. |
| `$objectDiff(obj1, obj2, options?)` | Compare two objects, return differences. Options: `{detailed: true}`. |

**Array Manipulation (use in table data transforms and mapping):**

| Binding | Purpose |
|---|---|
| `$groupBy(array, function)` | Group array by key function. |
| `$indexBy(array, function)` | Like groupBy but keeps only last match per key. |
| `$uniq(array)` | Remove duplicates (by reference for objects). |
| `$uniqBy(array, function)` | Remove duplicates by custom key function. |
| `$chunk(array, size)` | Split array into chunks. |
| `$flatten(array, depth?)` | Flatten nested arrays. |
| `$partition(array, function)` | Split into two arrays: [truthy, falsy]. |
| `$first(array)` | Get first element. |
| `$last(array)` | Get last element. |
| `$findFirst(array, function)` | Find first element matching predicate. |
| `$findLast(array, function)` | Find last element matching predicate. |
| `$all(array, function)` | True if all elements satisfy predicate. |
| `$any(array, function)` | True if any element satisfies predicate. |
| `$flatMap(array, function)` | Map then flatten result. |

**String Utilities (use in display transforms):**

| Binding | Purpose |
|---|---|
| `$startCase(string)` | Convert to Start Case. |
| `$camelCase(string)` | Convert to camelCase. |
| `$upperFirst(string)` | Capitalize first character. |
| `$startsWith(string, pattern)` | Check if string starts with pattern. |
| `$endsWith(string, pattern)` | Check if string ends with pattern. |
| `$pluralize(string, locale)` | Pluralize a word. |
| `$singularize(string, locale)` | Singularize a word. |
| `$htmlEscape(string)` | Escape HTML special characters. |

**CSV & Data (use in export/import actions):**

| Binding | Purpose |
|---|---|
| `$arrayToCsv(array, options?)` | Convert array of objects to CSV string. |
| `$csvToArray(string, options?)` | Parse CSV string to array of objects. |

**SQL-Style Joins (use in complex table data assembly):**

| Binding | Purpose |
|---|---|
| `$innerJoin(left, right, leftKey, rightKey, joinedKey, options?)` | Inner join two arrays. |
| `$leftOuterJoin(left, right, leftKey, rightKey, joinedKey, options?)` | Left outer join. |
| `$leftAntiJoin(left, right, leftKey, rightKey)` | Keep left items with no right match. |
| `$leftSemiJoin(left, right, leftKey, rightKey)` | Keep left items that have a right match. |
| `$crossJoin(left, right, joinedKey, options?)` | Cross join all combinations. |

**Calendar & Scheduling (use in production screen expressions):**

| Binding | Purpose |
|---|---|
| `$eventsBetween(moment1, moment2, calendar)` | Get calendar events between two dates. |
| `$getEventsAt(moment, calendar)` | Get events occurring at a specific time. |
| `$availableAt(moment, calendar)` | Check if any available event exists at the moment. |
| `$getAvailabilityBetween(moment1, moment2, calendar)` | Get events categorized by availability. |

### Fuuz JSONata Platform-Specific Constraints

Several JSONata patterns that work in standard JSONata do **not** work in the Fuuz runtime. Follow these rules to avoid platform errors.

**Working patterns in Fuuz:**
```jsonata
/* Block expressions with variable assignment */
(
  $var := expression;
  $result
)

/* Function definitions */
$fn := function($s) {(
  $r := $replace($s, /pattern/, "replacement");
  $uppercase($substring($r, 0, 1)) & $substring($r, 1)
)};

/* Direct .{} mapping (final expression) */
$metadata.userTenants.{
  "id": id,
  "field": value
}
```

**Broken patterns in Fuuz — AVOID:**
```jsonata
/* Bare ternary with .{} mapping — FAILS */
$count(arr) > 0 ? arr.{ "id": id } : [{ "id": "none" }]

/* $map with function — may cause "map is not a function" */
$map($metadata.userRoles, function($r) { ... })

/* $append for array coercion — unreliable in Cards transform */
$append([], $metadata.userRoles)
```

**Rules for Fuuz Cards/Table/List transforms:**
1. Always wrap the entire expression in `()` block expression
2. Use `.{}` mapping syntax, NOT `$map()`
3. Wrap mapping result in `[]` array literal to guarantee array output
4. Avoid bare ternary + mapping combinations
5. Handle empty arrays via container `hidden` property with `$isNilOrEmpty()`, not inline conditionals

**Correct Cards transform pattern:**
```jsonata
(
  [$metadata.userTenants.{
    "id": id,
    "tenantName": tenant.name,
    "accessType": accessTypeId
  }]
)
```

### Screen Expression Patterns

**Dynamic text with state context:**
```
$state.context.oee ? $numeral($state.context.oee).format("0.0%") : "--"
```

**Conditional container background (status color):**
```
$state.context.status = "Running" ? "#4CAF50" : $state.context.status = "Down" ? "#F44336" : "#9E9E9E"
```

**Table search after action (in action chain transformation step):**
```
$components.Table1.fn.search()
```

**Navigate to form screen with route parameter:**
```
$navigateTo("/app/workcenter/" & id)
```

**Show notification after save:**
```
$addNotificationMessage("Record saved successfully", {"severity": "success"})
```

**Query data inline (for select options or lookups):**
```
$query({"statement": "query { workcenter(where: {isActive: {_eq: true}}) { edges { node { id name } } } }"}).workcenter.edges.node
```

**Execute flow and use result:**
```
$executeFlow("oee-calc-flow-id", {"workcenterId": $state.context.workcenterId})
```

**Disabled button when no rows selected:**
```
$count($components.Table1.selectedRows) = 0
```

**Dynamic delete confirmation message:**
```
"Delete " & $count($components.Table1.selectedRows) & " records?"
```

**Format date/time for display (user timezone):**
```
$moment(data.createdAt).format("MM/DD/YYYY hh:mm A")
```

**Calculate time elapsed (duration display):**
```
$moment().diff($moment(data.startAt), "minutes") & " min"
```

**Conditional icon color based on threshold:**
```
data.value >= 85 ? "#4CAF50" : data.value >= 60 ? "#FFC107" : "#F44336"
```

**Build filter payload from form values (action chain pre-transform):**
```jsonata
{
  "payload": {
    "create": $mergeDeep([
      data,
      {"createdAt": $now(), "id": $uuid()}
    ])
  }
}
```

**Transform table data for chart consumption:**
```jsonata
$components.Table1.data.{
  "label": name,
  "value": $string($round(oee * 100, 1))
}
```

**Aggregate selected rows for bulk action:**
```jsonata
{
  "payload": $components.Table1.selectedRows.{
    "where": {"id": id},
    "update": {"status": "Completed", "completedAt": $now()}
  }
}
```

**Join two datasets in a transform (e.g., enrich table data):**
```jsonata
$innerJoin(
  $state.context.workOrders,
  $state.context.products,
  "productId",
  "id",
  "product"
)
```

**Calendar-aware expression (check if workcenter is scheduled):**
```jsonata
$availableAt($moment(), $getCalendar({"id": data.calendarId}))
  ? "Scheduled" : "Off-Schedule"
```

---
