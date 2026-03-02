# JSONata Reference for Data Flows

Flow-specific JSONata reference covering evaluation context, operators, common patterns, and platform functions available in data flow nodes.

---

## Data Flow Evaluation Context

In data flow nodes (Transform, Set Context, Merge Context, Conditional, etc.), JSONata expressions execute server-side with the following context.

### Root Input (`$`)

The current node's payload — `state.payload`. This is the data flowing from the previous node.

**Scope shifting:** In JSONata, `$` refers to the current scope. At the root level this is the input payload, but inside a nested expression (e.g., `$.items.( $.value )`) it shifts to the nested object. Use `$$` to escape back to the root input payload from within a nested scope.

```jsonata
$;  /* Entire payload at top level */

$.data.(
  $;   /* Each element in $.data array — scope shifted */
  $$;  /* ALWAYS the root payload (never shifts) */
);

/* Capture before chaining */
( $payload := $; $someFunction().chain($payload) )
```

### `$state` — Flow Execution State

The full accumulated execution state. Available as a binding in all data flow node evaluations.

| Path | Description |
|------|-------------|
| `$state.payload` | Current payload (same as `$`) |
| `$state.context` | User-defined context set by Set Context / Merge Context nodes |
| `$state.claims` | Auth claims of the triggering user |
| `$state.batches` | Batch tracking array (Fork/Broadcast/Combine) |
| `$state.messageId` | Unique message ID for this flow execution |
| `$state.metadata` | Flow metadata: `{tenantId, enterpriseId, flowId}` |
| `$state.depth` | Current execution depth counter |
| `$state.nextNodes` | Array of next node IDs to execute |
| `$state.trace` | Trace entries (when tracing enabled) |
| `$state.lastError` | Error from last caught error (with catchErrorNode): `{name, message, stack, info, node}` |
| `$state.catchErrorNode` | ID of the error handler node |

#### `$state.context`

Free-form object that accumulates data across the flow:
- **Set Context** nodes replace the entire context
- **Merge Context** nodes deep-merge into the existing context
- Access as `$state.context.anyPath`
- Primary mechanism for passing data between nodes outside the payload
- Context values set via `setContext` DO survive through `broadcast` fan-out nodes

#### `$state.claims`

Authentication claims of the user or system that triggered the flow. Available for security/authorization logic.

#### `$state.lastError`

When using Try-Catch error handling: `{name, message, stack, info, node}` from the last failed node.

### Node Types and JSONata Results

| Node Type | What happens with JSONata result |
|-----------|--------------------------------|
| JSONata (transform) | Result becomes new `payload` |
| If Else | Result (boolean) determines output port |
| Route | Result determines which branches to route to |
| Accept / Reject | Result (boolean) determines whether to continue |
| Set Context | Result replaces the entire `$state.context` |
| Merge Context | Result is deep-merged into `$state.context` |
| Remove From Context | Result (array of paths) removes keys from context |
| Response | Result becomes the response payload |
| Log | Result is logged as the message |

---

## Critical Gotchas

1. **No `!` operator** — Use `$not(expr)` function instead. `$not($exists(x))` not `!$exists(x)`
2. **Dot-before-brace matters** — `Account.Order.{"p": Product}` constructs per-item; `Account.{"o": Order}` constructs once
3. **Ternary vs Elvis vs Null Coalescing** — `expr ? truthy : falsy` (ternary), `expr ?: default` (elvis — default when falsy), `expr ?? default` (null coalescing — default when null/undefined only)
4. **`$round` uses banker's rounding** — Rounds to nearest even on .5 boundary. `$round(2.5)` = 2, `$round(3.5)` = 4
5. **`$now()` and `$millis()` are frozen** — Return same value for entire evaluation (consistent timestamps)
6. **No explicit return** — Last expression evaluated is the result
7. **`undefined` propagates** — Accessing a missing field returns `undefined`, which silently drops from results rather than erroring
8. **String concatenation uses `&`** — Not `+`. `"hello" & " " & "world"`
9. **Comparison `=` not `==`** — Single equals for comparison. No `===`.
10. **`in` operator for membership** — `x in [1,2,3]` not `[1,2,3].includes(x)`
11. **Singleton array equivalence** — `[42]` and `42` are interchangeable in path expressions. `Phone[0]` on a non-array returns the value itself.
12. **`$[].{}` vs `$.{}`** — `$[].{ "a": fieldA }` produces an array of objects (CORRECT). `$.{ "a": fieldA }` collects values into arrays per field (WRONG).

---

## Operators Reference

### Path Operators

| Operator | Name | Description |
|----------|------|-------------|
| `.` | Map | Navigate object fields. On arrays, maps over each element. `Account.Order.Product` |
| `[ ]` | Filter/Index | Array index (`[0]`, `[-1]`) or predicate filter (`[price > 10]`). |
| `^( )` | Order-by | Sort array. `Account.Order^(>Price)` descending. `<` = ascending (default), `>` = descending. |
| `{ }` | Reduce | Group and aggregate. `Account.Order.Product.{Description: $sum(Price)}` |
| `*` | Wildcard | All fields at current level. `Address.*` |
| `**` | Descendants | All fields at any depth. `Account.**[id=123]` |
| `%` | Parent | Access parent context in nested filter. `Account.Order.Product[%.OrderID = "order1"]` |
| `#$var` | Index bind | Bind array index to variable. `library.books#$i[genre="fiction"]{title: $i}` |
| `@$var` | Focus bind | Bind current focus to variable. `library.loans@$l.books[isbn=$l.isbn]` |

### Chaining Operator (`~>`)

Pipe operator passes the left result as the first argument of the right function:

```jsonata
Account.Order.Product ~>
  $filter(function($p) { $p.Price > 50 }) ~>
  $map(function($p) { $p.Description }) ~>
  $sort() ~>
  $join(", ")
```

### Transform Operator

Creates a modified copy (non-mutating). Syntax: `| source | update_expr | delete_keys |`

```jsonata
/* Update fields */
$ ~> | Account | {"Status": "Active"} |

/* Delete fields */
$ ~> | Account.Order | {} | ["InternalId"] |

/* Multi-step transform via chaining */
$ ~> | Account | {"Name": $uppercase(Name)} |
  ~> | Account.Order | {"Total": Price * Quantity} |
```

- Returns a deep copy (does not mutate original)
- Can target nested paths
- `update_expr` can reference fields of the matched object
- `delete_keys` is optional

### Conditional Operators

| Operator | Name | Description |
|----------|------|-------------|
| `? :` | Ternary | `expr ? truthy_result : falsy_result`. Falsy: `false`, `null`, `undefined`, `0`, `""`. |
| `?:` | Elvis | `expr ?: default`. Returns `expr` if truthy, else `default`. No space before `:`. |
| `??` | Null coalescing | `expr ?? default`. Returns `expr` if not `null`/`undefined`, else `default`. Unlike `?:`, keeps `0`, `false`, `""`. |

### Other Operators

| Operator | Description |
|----------|-------------|
| `+` `-` `*` `/` `%` | Arithmetic (% is modulo in numeric context) |
| `=` `!=` `<` `<=` `>` `>=` | Comparison (single `=`, not `==`) |
| `and` `or` | Boolean (no `not` — use `$not()`) |
| `&` | String concatenation |
| `:=` | Variable binding (`$x := 42`) |
| `..` | Range (`[1..5]` produces `[1,2,3,4,5]`) |
| `in` | Membership test (`x in [1,2,3]`) |

---

## Programming Constructs

### Variable Binding and Block Expressions

```jsonata
(
  $price := Product.Price;
  $tax := $price * 0.1;
  $price + $tax
)
```

Block expressions use `( )` with `;` separating statements. The last expression is the result.

### Context References

| Symbol | Meaning |
|--------|---------|
| `$` | Current input/scope (changes as you navigate into objects) |
| `$$` | Root input — always refers to the top-level input, regardless of navigation depth |

### Lambda Functions

```jsonata
/* Named lambda */
$fn := function($x, $y) { $x + $y };

/* Inline in higher-order functions */
$map(data, function($item) { $item.name })

/* Partial application with ? placeholder */
$trim := $substringBefore(?, "@");
emails ~> $map($trim)
```

### Regular Expressions

```jsonata
$contains(name, /^[A-Z]/)           /* Starts with uppercase */
$match(text, /(\d+)-(\w+)/g)        /* All matches with groups */
$replace(str, /\s+/g, "-")          /* Replace whitespace */
```

---

## Common Flow Patterns

### Null-Safe Navigation

```jsonata
Account.Order.Product.Price          /* undefined if any step missing */
$exists(Account.Order) ? Account.Order.Product : "N/A"
(Account.Name ?? "Unknown")          /* Null coalescing */
(Account.Name ?: "Unknown")          /* Elvis (also covers empty string) */
```

### Conditional Object Construction

```jsonata
{
  "name": Name,
  "email": Email,
  "phone": $exists(Phone) ? Phone : undefined
  /* undefined fields are omitted from result */
}
```

### Array Aggregation with Grouping

```jsonata
Account.Order.Product.{
  Description: {
    "total": $sum(Price),
    "count": $count(Price)
  }
}
```

### Building GraphQL Variables from Payload

```jsonata
/* Build where clause */
{
  "where": {
    "id": { "_eq": $.recordId },
    "occurAt": { "_gte": $.startTime, "_lt": $.endTime }
  }
}

/* Array mapping for mutation payloads */
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
$.model.edges[0].node.field          /* Single value */
$.model.edges.node.field             /* Map to array */
$.model.edges.node.{                 /* Map with transformation */
  "id": id,
  "displayName": name & " (" & code & ")"
}
$.model.edges[node.status = "Active"].node.id  /* Filter then extract */
```

### Using Context in Transforms

```jsonata
{
  "data": $,
  "config": $state.context.oeeConfig,
  "calculatedValue": $.rawValue * $state.context.multiplier
}
```

### Filtering and Aggregation

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

### Dynamic Field Names

```jsonata
$fieldName := "status";
{ $fieldName: "active" }    /* {"status": "active"} */
```

---

## Platform Functions — Data Flow Available

All custom bindings registered by the platform that are available in data flow transform nodes. Organized by category.

### Core — General Utility

| Function | Description |
|----------|-------------|
| `$cuid()` | Generate a CUID (collision-resistant unique identifier). |
| `$uuid()` | Generate a V4 UUID. |
| `$throw(message, info?)` | Throw a `ThrowBindingError`. `info` object attached to error's data property. |
| `$coalesce(array)` | Return first non-null/undefined value from array. |
| `$identity(value)` | Return value unchanged (identity function). |
| `$tryCatch(fn, catchFn)` | Wrap `fn` in try/catch; `catchFn(error, ...originalArgs)` called on error. |
| `$nullIf(value, compare)` | Return `null` if value equals compare; otherwise return value. |
| `$wait(ms)` | Pause execution for `ms` milliseconds. |
| `$retry(fn, options?, failedFn?)` | Return function that retries `fn` up to `retries` times (default 5) with `delay` ms (default 1000). |
| `$cronConverter(cronString, timezone?)` | Parse cron string, return cron-converter object. Default timezone `"Etc/UTC"`. |

### Core — Type Predicates

| Function | Description |
|----------|-------------|
| `$isArray(value)` | True if value is an array. |
| `$isString(value)` | True if value is a string. |
| `$isBoolean(value)` | True if value is a boolean. |
| `$isInteger(value)` | True if value is a safe integer. |
| `$isFloat(value)` | True if value is a finite float (integers count). |
| `$isNumber(value)` | True if value is a finite number. |
| `$isObject(value)` | True if value is a plain object. |
| `$isUndefined(value)` | True if value is undefined. |
| `$isNil(value)` | True if value is null or undefined. |
| `$isNaN(value)` | True if value is NaN. |
| `$isEmpty(value)` | True if empty array, object, or string. |
| `$isNilOrEmpty(value)` | True if undefined, null, or empty array/object/string. |
| `$isNotNilOrEmpty(value)` | Logical NOT of `$isNilOrEmpty`. |
| `$pathSatisfies(object, path, fn)` | True if predicate `fn` returns truthy for value at dot-path. |

### Core — Conversion

| Function | Description |
|----------|-------------|
| `$arrayToCsv(array, options?)` | Convert array of objects to CSV string. |
| `$csvToArray(string, options?)` | Convert CSV string to array of objects. |
| `$epcToUrn(epc, options?)` | Convert hex RFID EPC to URN object. Default GRAI96. |
| `$numeral(value)` | Create numeral.js object for number formatting/parsing. |
| `$parseInt(string, base?)` | Parse string to integer with radix (default 10). |
| `$parseNumber(value)` | Parse value to number via numeral.js. |
| `$decimalToString(array, encoding?)` | Decode byte array to string. Default `"utf-8"`. |
| `$numberToWords(number, locale?, options?)` | Number to written words. Supports currency options. |
| `$jsonToJsonSchema(json, options?)` | Generate JSON Schema from JSON. |
| `$jsonToMarkdown(array)` | Convert JSON array to markdown via json2md format. |

### Core — String

| Function | Description |
|----------|-------------|
| `$startsWith(string, pattern)` | True if string starts with pattern. |
| `$endsWith(string, pattern)` | True if string ends with pattern. |
| `$camelCase(string)` | Convert to lowerCamelCase. |
| `$snakeCase(string)` | Convert to snake_case. |
| `$kebabCase(string)` | Convert to kebab-case. |
| `$startCase(string)` | Convert to Start Case. |
| `$lowerFirst(string)` | Lowercase first character. |
| `$upperFirst(string)` | Uppercase first character. |
| `$pluralize(word, locale)` | Pluralize word using grammatical rules. |
| `$singularize(word, locale)` | Singularize word using grammatical rules. |
| `$htmlEscape(string)` | Escape `&<>"'` to HTML entities. |
| `$htmlUnescape(string)` | Convert HTML entities back to characters. |
| `$uriEscape(string)` | URI encode (preserves `?=/&`). |
| `$uriUnescape(string)` | URI decode. |
| `$uriEscapeComponent(string)` | Full URI component encode. |
| `$uriUnescapeComponent(string)` | Full URI component decode. |
| `$convertCharacterSet(string, target, source?)` | Re-encode between character encodings. Default source `"utf8"`. |
| `$parseUrl(string)` | Parse URL to object `{protocol, host, pathname, query, ...}`. |
| `$urlStringify(params, options?)` | Object to querystring. |
| `$urlJoin(array)` | Join URL path segments, normalizing slashes. |
| `$removeLeadingValues(string, value)` | Remove all leading occurrences of value. |
| `$replaceLeadingValues(string, value, replacement)` | Replace all leading occurrences. |
| `$evalHandlebarsTemplate(template, fields, options?)` | Evaluate Handlebars template with field values. |
| `$parseExpression(string)` | Parse JSONata expression to AST. |
| `$parseGraphQLStatement(string)` | Parse GraphQL statement to AST. |

### Core — Rich Text / Markdown

| Function | Description |
|----------|-------------|
| `$markdownToHtml(string)` | Convert markdown to HTML. |
| `$richTextToMarkdown(draftContent)` | Convert RawDraftContent to markdown. |
| `$markdownToRichText(string)` | Convert markdown to RawDraftContent. |
| `$plainTextToRichText(string)` | Convert plain text to RawDraftContent. |
| `$richTextToPlainText(draftContent)` | Convert RawDraftContent to plain text. |
| `$richTextToHTML(draftContent)` | Convert RawDraftContent to HTML. |

### Core — Object

| Function | Description |
|----------|-------------|
| `$path(object, path)` | Get value at dot-path or path array. |
| `$omit(object, keys)` | Return object without specified keys. |
| `$pick(object, keys)` | Return object with only specified keys. |
| `$values(object)` | Return array of all values. |
| `$has(object, property)` | True if property exists on object. |
| `$toPairs(object)` | Convert to `[[key, value], ...]` pairs. |
| `$fromPairs(pairs)` | Convert `[[key, value], ...]` to object. |
| `$mapValues(object, fn)` | Map over values. `fn(value, key, obj)`. |
| `$mapKeys(object, fn)` | Map over keys. `fn(value, key, obj)`. |
| `$mergeDeep(array)` | Recursive deep merge of objects array. Arrays at same path concatenated. |
| `$jsonParse(string)` | Parse JSON string to value. |
| `$jsonStringify(value, options?)` | Serialize to JSON string. Options: `{space}`. |
| `$flattenObject(object, options?)` | Flatten nested object to dot-keyed single level. Options: `{delimiter, safe, maxDepth}`. |
| `$unflattenObject(object, options?)` | Reverse flatten. Options: `{delimiter, object, overwrite}`. |
| `$objectDiff(before, after, options?)` | Compare two objects. `{detailed: true}` returns `{added, deleted, updated}`. |

### Core — Array

| Function | Description |
|----------|-------------|
| `$chunk(array, size)` | Split into groups of `size`. |
| `$uniq(array)` | Remove duplicates (reference equality for objects). |
| `$flatten(array, depth?)` | Flatten nested arrays to `depth` (default 1). |
| `$first(array)` | First element. |
| `$last(array)` | Last element. |
| `$takeFirst(array, n?)` | First n elements. |
| `$takeLast(array, n?)` | Last n elements. |
| `$dropFirst(array, n)` | All but first n elements. |
| `$dropLast(array, n)` | All but last n elements. |
| `$partition(array, predFn)` | Split into `[matches, nonMatches]`. |
| `$groupBy(array, keyFn)` | Group by key function result. Returns `{key: [items]}`. |
| `$indexBy(array, keyFn)` | Like `$groupBy` but keeps only last item per key. |
| `$uniqBy(array, keyFn)` | Remove duplicates by key function. Keeps first occurrence. |
| `$findFirst(array, predFn)` | First element where predFn returns true. |
| `$findLast(array, predFn)` | Last element where predFn returns true. |
| `$findFirstIndex(array, predFn)` | Index of first match. |
| `$findLastIndex(array, predFn)` | Index of last match. |
| `$flatMap(array, fn)` | Map then flatten one level. |
| `$partitionBetween(array, beginFn, endFn, options?)` | Partition into groups bounded by begin/end predicates. |
| `$all(array, predFn)` | True if ALL elements satisfy predicate. |
| `$any(array, predFn)` | True if ANY element satisfies predicate. |

### Core — Parallel Execution

For async operations. Default parallelism is 8.

| Function | Description |
|----------|-------------|
| `$mapParallel(array, mapFn, parallelism?)` | Map with concurrent execution. |
| `$mapValuesParallel(object, mapFn, parallelism?)` | Map object values with concurrent execution. |
| `$filterParallel(array, filterFn, parallelism?)` | Filter with concurrent predicate evaluation. |
| `$groupByParallel(array, keyFn, parallelism?)` | Group by key with concurrent key-function calls. |
| `$timesParallel(count, fn, parallelism?)` | Execute function N times in parallel (functional for-loop). |

### Core — Predicate Filters

| Function | Description |
|----------|-------------|
| `$predicateFilter(payload, predicates, options?)` | Filter using GraphQL-style operators (`_eq`, `_in`, `_gt`, `_lt`, `_contains`, etc.). Default case-insensitive. |

### Core — Schema Validation

| Function | Description |
|----------|-------------|
| `$validateSchema(value)` | True if value is a valid JSON Schema. |
| `$validateJson(data, schema)` | Validate data against JSON Schema. Returns data if valid; throws on invalid. |
| `$isValidJson(data, schema)` | True if data matches schema; false otherwise. |

### Core — Tree

| Function | Description |
|----------|-------------|
| `$visitPreorder(treeOrForest, visitor)` | Preorder traversal of nested objects. `visitor(node, branchName, parentNode, parentBranchName)`. |

### MFGx Application Functions

Primary data flow functions for querying, mutating, and orchestrating.

| Function | Description |
|----------|-------------|
| `$query(input)` | Execute GraphQL query. Input: `{api?, statement, variables}`. API defaults to APPLICATION. Returns `data` property. |
| `$mutate(input)` | Execute GraphQL mutation. Same interface as `$query`. |
| `$integrate(data)` | Post data to integration connection API. Returns response body. |
| `$document(payload, options?)` | Render document via `renderDocument` mutation. Options: `{returnContent}`. |
| `$executeFlow(flowId, payload?)` | Execute orchestration flow by ID with optional payload. |
| `$executeTransform(savedTransformId, payload, bindings?)` | Execute saved transform with payload and optional bindings. |
| `$executeDataMapping(dataMappingId, payload)` | Execute data mapping by ID. |
| `$executeSavedQuery(savedQueryId, parameters?)` | Execute saved query by ID with parameters. |
| `$getCalendar(queryInput)` | Fetch calendar by ID or name. |
| `$addNotificationMessage(title, options?)` | Show UI notification. Options: `{message, data, severity, snackbar}`. Severity: `"info"\|"success"\|"warning"\|"error"`. |
| `$executeDeviceFunction(payload)` | Execute device function. Returns `{requestId, deviceGatewayId, response, error}`. |
| `$executeDeviceGatewayFunction(payload)` | Execute device gateway function. Returns `{requestId, response, error}`. |

**Common patterns:**

```jsonata
/* Query data */
$query({
  "statement": "query($filter: JSON) { WorkOrder(filter: $filter) { edges { id name status } } }",
  "variables": { "filter": { "status": { "_eq": "Active" } } }
}).WorkOrder.edges

/* Mutate data */
$mutate({
  "statement": "mutation($input: UpdateWorkOrderInput!) { updateWorkOrder(input: $input) { workOrder { id } } }",
  "variables": { "input": { "id": id, "status": "Complete" } }
})

/* Execute flow */
$executeFlow("flow-id-here", { "orderId": id })
```

**IMPORTANT: Always prefer Query/Mutate NODES over `$query()`/`$mutate()` functions.** The node is more performant because it splits variable transformation and API request into separate orchestration steps. If you must use `$query()` in JavaScript, it returns a promise and requires `await`.

### Joins

SQL-style joins between arrays. Accessors can be a string (field name), array of strings (composite key), or function.

| Function | Description |
|----------|-------------|
| `$crossJoin(left, right, joinedKey, options?)` | Every left paired with every right. Options: `{grouped}`. |
| `$innerJoin(left, right, leftAccessor, rightAccessor, joinedKey, options?)` | Only rows with matches in both sides. |
| `$leftOuterJoin(left, right, leftAccessor, rightAccessor, joinedKey, options?)` | All left rows; right matches where available. |
| `$leftSemiJoin(left, right, leftAccessor, rightAccessor)` | Left rows that have a right match (no right data merged). |
| `$leftAntiJoin(left, right, leftAccessor, rightAccessor)` | Left rows that have NO right match. |

### Moment.js

Full moment.js API available on returned objects (`.format()`, `.add()`, `.diff()`, `.isBefore()`, etc.).

| Function | Description |
|----------|-------------|
| `$moment(value?, format?, localeOrStrict?, strict?)` | Create moment instance. Accepts date strings, arrays, objects. |
| `$momentDuration(value?, unitOfTime?)` | Create duration instance. Supports ISO 8601 (`"PT8H"`), numbers, objects. |
| `$momentTz(date?, format?, timezone?)` | Create moment with specific timezone. |
| `$momentTzTimezones()` | List all available IANA timezone names. |
| `$momentTzGuess(ignoreCache?)` | Guess user's timezone. Returns `"UTC"` on backend. |
| `$momentMax(values, ignoreInvalidDates?)` | Most future moment from array. |
| `$momentMin(values, ignoreInvalidDates?)` | Most past moment from array. |

**Common patterns:**

```jsonata
$moment().format("YYYY-MM-DD")                           /* Current date */
$moment(dateStr).add(7, "days").toISOString()             /* Add 7 days */
$moment(end).diff($moment(start), "hours")                /* Hours between */
$momentDuration("PT1H30M").humanize()                     /* "2 hours" */
$momentTz(dateStr, "YYYY-MM-DD", "America/New_York")      /* Parse in timezone */
```

### XML

| Function | Description |
|----------|-------------|
| `$jsonToXml(data, options?)` | Convert JSON to XML string. Options per xml2js Builder. |
| `$xmlToJson(xmlString, options?)` | Parse XML to JSON. Options per xml2js parser. |

### EDI

| Function | Description |
|----------|-------------|
| `$parseX12(string)` | Parse X12 EDI string to JSON object. |
| `$formatX12(array)` | Format parsed X12 array to structured JSON. |
| `$readEDI(format, data, options)` | Read EDI documents. Format: `"x12"` or `"edifact"`. |
| `$writeEDI(format, data, options)` | Write EDI documents. |
| `$validateEDI(format, data, options)` | Validate EDI documents. |
| `$ackEDI(format, data, options)` | Generate EDI acknowledgments. |

### Encryption

| Function | Description |
|----------|-------------|
| `$encryptHmac(algorithm, key, string, encoding?)` | HMAC digest. Algorithm: OpenSSL method (e.g., `"sha256"`). Default encoding `"base64"`. |
| `$hash(algorithm, string, encoding?)` | Hash string. Default encoding `"base64"`. |

### Network

| Function | Description |
|----------|-------------|
| `$http(method, env, body?)` | HTTP request. Method: `"get"\|"post"\|"put"\|"patch"\|"delete"\|"head"\|"options"`. Returns base64-encoded response body. |

### Calendars

| Function | Description |
|----------|-------------|
| `$eventsBetween(start, end, calendar, options?)` | Events between two moments. Options: `{includeStart, includeEnd, assignOccurrenceIds}`. |
| `$getEventEnd(event, after?)` | End of first (or next after `after`) occurrence. |
| `$getEventsAt(moment, calendar)` | Events occurring at a specific moment. |
| `$getAvailabilityBetween(start, end, calendar, options?)` | Events categorized by availability. Options: `{exact}`. |
| `$availableAt(moment, calendar)` | True if any available event at the moment. |
| `$getTimeline(start, end, calendar, options?)` | Timeline for a time range on calendar. |

### Scheduling

| Function | Description |
|----------|-------------|
| `$scheduleGroup(id)` | Get schedule group. Methods: `.getCurrentSchedules()`, `.getTimeline()`, `.getEventsIntersections()`, `.getAvailabilityBetween()`, `.getCalendarIntervals()` |
| `$schedule(id)` | Get individual schedule. Methods: `.getAvailabilityBetween()`, `.getTimeline()`, `.getEventsIntersections()`, `.getEventsBetween()`, `.getExceptionsBetween()`, `.getCalendarIntervals()` |

### Unit Conversion

| Function | Description |
|----------|-------------|
| `$convertUnit(fromUnitId, toUnitId, value)` | Convert between units of same type. |
| `$getConversionFactor(fromUnitId, toUnitId)` | Get conversion factor between units. |
| `$getUnit(identifier)` | Retrieve Unit record by `{id}`, `{code}`, or `{name}`. |

### Semver (28 functions)

Semantic versioning via the `semver` npm package. All functions accept optional `loose?` boolean.

**Parsing:** `$semverParse(v)`, `$semverValid(v)`, `$semverClean(v)`, `$semverCoerce(v, opts)`

**Components:** `$semverMajor(v)`, `$semverMinor(v)`, `$semverPatch(v)`, `$semverPrerelease(v)`

**Mutation:** `$semverInc(v, release, loose?, id?)` — increment by `major|minor|patch|premajor|preminor|prepatch|prerelease`

**Comparison:** `$semverGt(v1, v2)`, `$semverLt(v1, v2)`, `$semverEq(v1, v2)`, `$semverNeq(v1, v2)`, `$semverGte(v1, v2)`, `$semverLte(v1, v2)`, `$semverCmp(v1, op, v2)`, `$semverCompare(v1, v2)`, `$semverDiff(v1, v2)`

**Sorting:** `$semverSort(versions)`, `$semverRsort(versions)`

**Ranges:** `$semverSatisfies(v, range)`, `$semverMaxSatisfying(versions, range)`, `$semverMinSatisfying(versions, range)`, `$semverValidRange(range)`, `$semverIntersects(r1, r2)`

---

## Built-in JSONata Functions (Quick Reference)

All 65 standard JSONata functions are available. Key categories:

**String (21):** `$string`, `$length`, `$substring`, `$substringBefore`, `$substringAfter`, `$uppercase`, `$lowercase`, `$trim`, `$pad`, `$contains`, `$split`, `$join`, `$match`, `$replace`, `$eval`, `$base64encode`, `$base64decode`, `$encodeUrlComponent`, `$decodeUrlComponent`, `$encodeUrl`, `$decodeUrl`

**Numeric (12):** `$number`, `$abs`, `$floor`, `$ceil`, `$round` (banker's rounding), `$power`, `$sqrt`, `$random`, `$formatNumber`, `$formatBase`, `$formatInteger`, `$parseInteger`

**Aggregation (4):** `$sum`, `$max`, `$min`, `$average`

**Boolean (3):** `$boolean`, `$not` (only way to negate), `$exists`

**Array (7):** `$count`, `$append`, `$sort`, `$reverse`, `$shuffle`, `$distinct`, `$zip`

**Object (9):** `$keys`, `$lookup`, `$spread`, `$merge`, `$sift`, `$each`, `$error`, `$assert`, `$type`

**Date/Time (4):** `$now` (frozen), `$millis` (frozen), `$fromMillis`, `$toMillis`

**Higher-Order (5):** `$map`, `$filter`, `$single`, `$reduce`, `$sift`

---

## Using Custom Bindings in JavaScript

All Fuuz custom bindings are available in JavaScript transform nodes with the same syntax, since Fuuz runs on Node.js.

```javascript
'use strict';

var timezone = $state.context.timezone;
var logs = $.productionLogs;
var results = [];

for (var i = 0; i < logs.length; i++) {
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

Note: `$query()` returns a promise in JavaScript and must be called with `await`.

---

## Debug Tips

1. **Use debugSource node** to test expressions with sample payloads
2. **Use log node** to inspect intermediate values: `messageTransform: "$"` logs entire payload
3. **Echo node** passes payload through unchanged — useful for breakpoints
4. **Check execution logs** for JSONata evaluation errors
5. **Test expressions incrementally** — build complex expressions step by step

---

## Performance Considerations

1. **Avoid nested loops** — JSONata is performant but deeply nested iterations can slow down
2. **Filter early** — Use predicates `[condition]` to reduce data before processing
3. **Use $distinct carefully** — Can be expensive on large arrays
4. **Pre-calculate in JavaScript** — Complex algorithms are faster in JavaScript transform nodes
5. **Leverage query filters** — Filter in GraphQL where clause rather than after retrieval
6. **Prefer Query/Mutate nodes** over `$query()`/`$mutate()` functions for better orchestration performance
