---
name: fuuz-flows-v1
description: Build data flows for the Fuuz Industrial Operations platform (fuuz.com). Use when requests mention "fuuz flow", "data flow", "flow", "web flow", "backend flow", "gateway flow", "rfc", "remote function call", "fuuz api", JSONata expressions, or industrial automation workflows (OEE calculation, warehouse logic, external API integration, telemetry aggregation, production tracking). Covers JSONata and JavaScript transforms, GraphQL queries/mutations, flow architecture, node configuration, and common patterns for event-driven and scheduled automation.
---

# Fuuz Data Flow Builder

**Version 1.4** | Last Updated: 2026-02-26

Build comprehensive, production-ready data flows for the Fuuz Industrial Operations platform.

## Flow Types

Fuuz supports three flow execution contexts:

**Backend Flows** (most common) - Server-side processing triggered by schedules or data changes. Use for ETL, calculations, reactive updates, batch processing.

**Web Flows** - Invoked from front-end applications via request/response pattern. Use for dashboard APIs, screen interactions, real-time queries. Can interact with UI via formDialog, snackbar, searchTable nodes.

**Gateway Flows** - Run on Fuuz Gateway on-premise. Use for edge device interactions, local printing, device subscriptions.

## State Model

The workflow state passed between nodes contains:
- **payload** — current data being processed (accessible as `$` in JSONata)
- **context** — persistent key-value store (set via `setContext`, deep-merged via `mergeContext`, read via `$state.context`)
- **claims** — authentication claims from the initiating user/system (`$state.claims`)
- **batches** — tracking info for broadcast/fork parallel execution (`$state.batches`)

The full state object is accessible via the `$state` binding in all data flow JSONata evaluations. See `references/jsonata-bindings.md` for the complete `$state` path reference.

## Common Property Patterns

**Standard Output Port** — Most transition nodes have a `nextNodes` property: `{ "title": "Output", "type": "array", "format": "port" }`. When a node has only this standard port, it is noted as "Standard output port" rather than listed in the properties table.

**JSONata Transform** — `format: "jsonata"` indicates a JSONata expression. In JSONata, `$` refers to the current scope — at the root level this is the input payload, but inside a nested expression (e.g., `$.items.( $.value )`) it shifts to the nested object. Use `$$` to escape back to the root input payload from within a nested scope. The `$state` binding provides access to the full workflow state (e.g., `$state.context`, `$state.claims`).

**Connection** — Integration nodes typically have a `connectionName` or `connectionNameTransform` property referencing a named integration connection (HTTP, ODBC, FTP, etc.).

**Return Errors** — `returnErrors` (boolean): when true, failed requests return `{ error: { statusCode, message, info } }` as payload instead of aborting the flow.

**Degree of Parallelism** — `degreeOfParallelism` (integer, default: 10): when payload is an array, how many parallel requests to execute simultaneously.

**Combination Strategies** — Used by Combine and Collect nodes:
- `index` — results ordered by branch index (default for payload)
- `first` (labeled "Last") — uses the last received result (default for context)
- `merge` — deep merges all results

## Format Glossary

| Format | Meaning |
|--------|---------|
| `jsonata` | JSONata expression |
| `javascript` | JavaScript code block |
| `graphql` | GraphQL query/mutation string |
| `port` | Output port connecting to downstream nodes |
| `sql` | SQL query (used in inputProps mode) |

## Validation Rules

NodeTypes may specify these validation rules:
- `requireChangedName` — node must be renamed from default
- `requireInputNode` — must have at least one input connection
- `requireOutputNode` — must have at least one output connection
- `requireWalkthrough` — must have a completed walkthrough/note
- `minimumNoteLength` (integer) — minimum character length for the node note

## Flow Design Standards

**Single-Purpose Flow Design** — Each flow should represent ONE process, not multiple processes triggered by payload variations. Avoid multiple request nodes in a single flow. Flows with multiple entry points are hard to maintain and are better split into smaller, simpler flows.

**Data-First Design Philosophy** — Fuuz centers on data-first design. Start with the data model, then build flows around it, then design screens. This is a critical distinction for Fuuz development and applies to every project.

**Ship Iteratively** — Build, test, refine. Avoid building everything perfect upfront.

## Fuuz API Architecture

Fuuz provides **two GraphQL APIs**:

**Application API** - App-specific data models (production logs, OEE, telemetry, work orders, assets, data points)
**System API** - Platform models (tenants, enterprises, users, roles, permissions, groups)

Specify which API in query/mutate nodes: `"api": "application"` or `"api": "system"`

## JSONata vs JavaScript Decision

Use **JSONata** for:
- Simple data transformations and restructuring
- Extracting fields from payloads
- Time/date calculations with `$moment()`
- Filtering and mapping arrays
- Conditional logic (when/unless nodes)
- Building GraphQL variables

Use **JavaScript** for:
- Complex business logic with loops and side effects
- Heavy calculations or algorithms
- State management requiring multiple variables
- Simulation or data generation
- When you need standard JavaScript libraries

**Important**: All Fuuz JSONata custom bindings are also accessible in JavaScript transforms since Fuuz runs on Node.js. Use the same syntax: `$moment()`, `$schedule()`, `$numeral()` etc.

### JavaScript Runtime Limitations (CRITICAL)

The Fuuz JS runtime (`javascriptTransform` nodes) is a restricted sandbox with hard failures:

**`.toFixed()` Is Not Supported** — throws `cannot read properties of toFixed`. Always use `Math.round()`:
```javascript
// ❌ FAILS
var rounded = someNumber.toFixed(4);
// ✅ WORKS
var rounded = Math.round(someNumber * 10000) / 10000;
```

**Variable Scoping Unreliable in Loops** — Variables declared with `var` inside loop bodies may silently fail. Inline expressions directly:
```javascript
// ❌ UNRELIABLE
for (var i = 0; i < results.length; i++) {
  var roundedR = Math.round(results[i].r * 10000) / 10000;
  insights.push({ value: roundedR });
}
// ✅ RELIABLE
for (var i = 0; i < results.length; i++) {
  insights.push({ value: Math.round(results[i].r * 10000) / 10000 });
}
```

**`'use strict';` should always be at the top of JS scripts.**

**Works:** `'use strict'`, `var`, `function` declarations, `Math.*`, `JSON.stringify/parse`, `Object.keys()`, `Array.isArray()`, `.push()`, `.slice()`, `parseFloat()`, `parseInt()`, `new Date().toISOString()`, `for/while/if/else/switch`

**Does NOT work:** `.toFixed()`, `let`/`const`, arrow functions, template literals, destructuring, spread operator, `Promise`/`async/await`, `require()`

## Critical JSONata Context Behavior

In JSONata, `$` refers to the **current context** which changes in chained operations:

```jsonata
$; /* Root payload at top level */

$.data.(
  $;   /* NOW refers to each element in $.data array */
  $$;  /* ALWAYS refers to root payload */
);

$moment($);      /* $ is root payload */
$moment().add($); /* BAD! $ is now undefined due to context shift */
$moment().add($$); /* GOOD! $$ is always root */

$components.Screen.fn.setContext($); /* BAD! $ shifted */
( $data := $; $components.Screen.fn.setContext($data) ); /* GOOD! */
```

**Rule**: When calling functions that chain with `.`, capture the payload first: `$data := $` then use `$data` or use `$$` for root.

### Critical JSONata Iteration Rule

**`$[].{}` produces an array of objects (CORRECT). `$.{}` collects values into arrays per field (WRONG).**

```jsonata
/* CORRECT — produces [{a:1,b:2}, {a:3,b:4}] */
$[].{ "a": fieldA, "b": fieldB }

/* WRONG — produces { "a": [1,3], "b": [2,4] } */
$.{ "a": fieldA, "b": fieldB }
```

Always use `$[].{}` when iterating over arrays to produce arrays of objects.

### Duration Storage Rule

**Always store durations as milliseconds (Int), NOT ISO 8601 duration strings.**

Duration fields in GraphQL responses may come back in mixed formats:
- Objects: `{ text: "PT0S", milliseconds: 0 }`
- Plain strings: `"PT0M"`
- Arrays: `[{ text: "...", milliseconds: 0 }]`

Use this robust parsing function:
```javascript
function getDurationMs(val) {
  if (val === null || val === undefined) return 0;
  if (typeof val === 'number') return val;
  if (Array.isArray(val)) return getDurationMs(val[0]);
  if (typeof val === 'object' && val.milliseconds !== undefined) return val.milliseconds;
  if (typeof val === 'string') return $moment.duration(val).asMilliseconds();
  return 0;
}
```

### Memory Wrapping for Array Nodes

When using Array nodes, downstream mutations may receive `undefined` if the payload isn't wrapped:
```javascript
// CORRECT — wrap output for Array node safety
return [$.payload];

// WRONG — may produce undefined in downstream mutations
return $.payload;
```

**See `references/common-pitfalls.md` for more critical patterns including:**
- Data access patterns ($ vs $state.context)
- Duration field handling with mixed formats
- Inline transform parsing errors
- Complete debugging strategies

## Flow Execution Context & Timeouts

**External Flows** (10 minute timeout):
- Web flows (invoked from frontend)
- Request/Response flows from external APIs (via api.__.fuuz.app)

**Internal Flows** (20 minute effective timeout):
- Schedule-triggered flows
- Data change subscriptions
- Webhooks
- Topic subscriptions
- Note: Node execution timeout is 30 minutes, but service request timeout is 20 minutes

**Data Changes node** functions like topic/webhook triggers - same queue mechanism, just different event source.

## Naming Conventions & Documentation Requirements

**CRITICAL: Always provide proper naming and descriptions for every flow generated.**

### Flow Naming

Use the format: `{Primary Fuuz Object} {direction/verb} {External Platform} {External Object} {Event Type}`

**Examples:**
- `Locations From NetSuite Bins` — standard integration naming
- `Locations To NetSuite Bins on Data Change` — event-driven integration naming
- `WorkOrder Process Defaults From Fuuz Default Product Strategy Processes on Data Change`
- For internal flows without external platforms, use descriptive action-oriented names: `calculateOeeHourly`, `processQualityFailures`, `getDashboardMetrics`

> **Note**: Renaming a flow doesn't change its ID. To rename, export contents into an empty flow with the new name.

### Flow Descriptions
- Clear, concise explanation of what the flow does and when it runs
- Include trigger type (schedule, data change, request)
- Mention key data models or systems involved
- Example: "Calculates hourly OEE metrics for all active workcenters. Runs every hour via schedule. Aggregates production logs, workcenter history, and applies ISO 22400 calculations."

### Node Naming
- Use **descriptive, human-readable names** for every node (not just "Transform" or "Query")
- Indicate the node's purpose: "Get Timezone Setting", "Calculate Time Window", "Lock OEE Record"
- Help future developers understand flow logic by reading node names
- Examples: "Query Active Workcenters", "Batch DataPoints by 10", "Update OEE with Production Data"

### Node Descriptions (Optional but Recommended)
- The `name` field is required, but nodes can also have detailed descriptions
- Useful for complex logic or business rules
- If user requests descriptions, **suggest exporting the flow JSON** so Claude can analyze the complete flow structure and generate accurate, contextual descriptions for each node

### Description Development Workflow
When user needs descriptions added:
1. User exports flow JSON from Fuuz
2. User shares JSON with Claude
3. Claude analyzes the flow structure, data flow, and node purposes
4. Claude generates comprehensive descriptions for flow and each node
5. User imports updated JSON back to Fuuz

### Documentation Requirements

- Every flow **must** have a meaningful description including connections used and overall summary
- Every version **must** have a description of changes being made
- Every node **must** have a meaningful description — no exceptions
- Every flow **must** have a walkthrough configured in logical order
- All nodes must have a unique and meaningful name (error logs must be useful)
- Nodes cannot use the default name even if unique
- All nodes with a connection on the left side must be connected to another node
- Debug node descriptions should highlight specific test cases

## Flow Development Workflow

### 1. Understand Requirements
Identify the trigger (schedule, data change, request), required data sources, transformations needed, and output destination.

### 2. Choose Entry Node
- **schedule** - Cron-based timing (configured externally) - **Internal, 20min timeout**
- **dataChanges** - React to Create/Update/Delete on a data model - **Internal, 20min timeout**
- **request** - Entry point for web flows (paired with response) - **External, 10min timeout**
- **webhook** - External HTTP trigger - **Internal, 20min timeout**

### 3. Build Processing Pipeline
Standard pattern: Trigger → Query (get data) → SetContext (store config) → Transform (calculate) → Mutate (write)

**IMPORTANT: Always use the Query NODE over $query() JSONata function.** The Query node is more performant because it splits transformation and API request into separate steps:
- Query node: Orchestration → Transformation → Orchestration → Application → Orchestration
- $query() function: Orchestration → Transformation → Application → Transformation → Orchestration

If you must use $query() in JavaScript, remember it returns a promise and requires `await`.

Common nodes in sequence:
1. Entry node (schedule/dataChanges/request)
2. **when/unless** - Filter early to avoid unnecessary processing
3. **setContext** - Store configuration, timezone, or reference data
4. **query** - Fetch data from Application API
5. **mergeContext** - Add query results to context
6. **transform/javascriptTransform** - Calculate, format, generate
7. **mutexLock** - (if updating shared resources)
8. **mutate** - Write results
9. **mutexUnlock**
10. **response** - (for web flows)

### 4. Handle Large Datasets
For operations that may approach timeout limits:
- **External flows (10 min)**: Use master flow + child flow architecture for datasets >5000 records
- **Internal flows (20 min)**: Can handle larger datasets, but still consider master/child pattern for >50,000 records
- Use Broadcast node to fan out array processing
- Batch data in JavaScript before broadcasting
- FilterArray to split by operation type

**Pattern**: Master flow queries IDs only → Batch IDs → Broadcast → ExecuteFlow (child) processes each batch

### 5. Add Concurrency Protection
Always wrap critical sections (updating same record from multiple triggers) with mutexLock/mutexUnlock. Use `resourceIdTransform` to lock on specific record ID.

### 6. Use Saved Transforms for Reusable Logic

**Best Practice: Create saved versions of repetitive tasks for easier maintenance.**

**When to create saved transforms:**
- Complex calculations used across multiple flows
- Standard data transformations (e.g., OEE calculations, time boundaries)
- Business logic that may change over time
- API integrations with consistent formatting

**Saved Transform Types:**
- **savedTransformV2** - Reusable JavaScript or JSONata logic
- **Saved Queries** - Standard query patterns
- **Saved Mutations** - Common mutation operations
- **Saved Visualizations** - Fusion Charts configurations with saved scripts

**Benefits:**
- Update logic once, applies to all flows using it
- Consistent calculations across system
- Easier testing and debugging
- Self-documenting through naming

**Example Usage:**
```json
{
  "type": "savedTransformV2",
  "name": "Calculate OEE Time Boundaries",
  "data": {
    "transformScriptLanguage": "JavaScript",
    "transformId": { "id": "oeeTimeBoundaries", "label": "OEE Time Boundaries" },
    "requestTransform": "$",
    "responseTransform": "$",
    "nextNodes": ["next-node-uuid"]
  }
}
```

### 7. Fusion Charts Visualizations

**For complex visualizations, use Saved Visualizations:**
- Saved Visualizations require a Saved Script
- Can include full Fusion Charts configuration in the script
- Can also manage chart configurations via Visualizations screen in Fuuz
- See `references/visualization-library.md` for complete configuration options

**Pattern:**
1. Create Saved Script with chart logic and optional config
2. Create Saved Visualization referencing the script
3. Use visualization in flows or screens
4. Update configuration centrally when needed

## Common Flow Patterns

**Pattern 1: Scheduled ETL**
```
Schedule → JSONata (time window) → SetContext → Query (data) → JavaScript (process) → Mutate (write)
```

**Pattern 2: Data Change Reactive Update**
```
DataChanges → Unless (guard) → SetContext → Query (related) → JavaScript (calculate) → MutexLock → Mutate → MutexUnlock
```

**Pattern 3: Web Flow Dashboard API**
```
Request → JSONata (params) → SetContext → Query → JavaScript (calculate) → JSONata (format + push to screen) → Response
```

**Pattern 4: Batch Processing with Fan-out**
```
Schedule → Query (all records) → JavaScript (batch into groups) → Broadcast → Query (aggregate per batch) → Mutate → Response
```

### Hybrid Multi-Flow Design

Complex systems benefit from separating real-time and batch processing:

| Flow Type | Trigger | Use Case |
|---|---|---|
| **Real-time** | `dataChanges` on model Create | Per-value analysis — O(1) per record |
| **Scheduled batch** | `schedule` (hourly/daily) | Cross-record analysis — O(n) correlation, aggregation |
| **Scheduled projection** | `schedule` (hourly) | Forward-looking calculations using batch results |

**Key principle:** Real-time flows should be O(1) per incoming record. Any calculation requiring multiple records belongs in a scheduled batch flow.

### Configuration via Application Settings

Store ALL operational parameters in application Settings, not hardcoded in flows.

**Query pattern (System API):**
```graphql
query GetSettings {
  setting(where: { code: { _in: ["ml.prefix.key1", "ml.prefix.key2"] } }) {
    edges { node { id code value } }
  }
}
```

**Parse with defaults in setContext (JSONata):**
```jsonata
(
  $settings := $.setting.edges.node;
  $lookup := function($code, $default) {
    $val := $settings[code = $code].value;
    $val ? $number($val) : $default
  };
  { "config": { "key1": $lookup('ml.prefix.key1', 60) } }
)
```

The `$appConfig` binding provides access to shared application configuration across all Fuuz components — Data Flows, Saved Scripts, Screens, and Data Model Triggers.

See `references/flow-patterns.md` for complete examples with node configurations.

### Flow Control Rules

- All flows with a **Request** node must have at least one **Response** node
- **Data Changes** can trigger many flows — follow immediately with a conditional to reduce unnecessary executions
- **Topics, publishing, and subscribing** are preferred for pagination of large data sets

### Broadcast Warning

**Broadcasts are dangerous.** The number of requests grows exponentially: `(Number of Records) × (Number of Nodes after Broadcast) = Total Requests`. 10 parts × 3 nodes = 30 requests. 1000 parts × 3 nodes = 3,000 requests. A script-based approach keeps it linear: 3 requests regardless of record count. In most cases, there is another way to solve the problem. Use script nodes that format requests in batch instead.

**Performance Note: Broadcast + Combine** — The Broadcast + Combine pattern is acceptable for small datasets but performs poorly over large datasets. When processing large arrays, prefer using nodes with built-in `degreeOfParallelism` support or handle iteration within a JSONata/JavaScript transform instead.

### Collect Node

Always use **both** Batch Count and Batch Time together. If you only set Count to 5 and only 3 messages arrive, the node will never emit — it waits indefinitely for the remaining 2. Setting a Batch Time ensures the node flushes partial batches after the timeout.

### Mutex Lock/Unlock

Always add **Mutex Unlock on ALL terminating routes**. If any branch exits without unlocking, the lock persists until its TTL expires, blocking all other transactions on that resource.

### Try/Catch

Never loop back into the flow from the catch port — this can create infinite loops. Access error details via `$state.lastError`.

### Throw Error Node

Use the Throw Error node to provide meaningful context when a flow fails. Build descriptive error messages that include relevant identifiers:
```jsonata
"PO " & $state.context.purchaseOrder.number & " failed creation"
```
This makes error logs actionable — operators can immediately identify which record failed and why.

### Debug Node Standards

- Descriptions are important — use them to highlight specific test cases being validated
- Test cases are **required** to prove testing was performed prior to deployment

### Conditional Best Practices

- Be descriptive in conditionals
- Avoid double negatives (e.g., `$not(false)`)
- Use **If/Else** in most situations, even if the else goes nowhere
- Data Changes can trigger many flows — follow immediately with a conditional statement to reduce executions
- If If/Else results in a double negative for the true path, consider using the else path instead

### Integration Node Standards

- Node name must indicate what it is calling, e.g., `Calling Plex DS - POs_Get (key:12345)`
- Node description must include parameters and intent, e.g., `Pulling POs with status of Open`
- **$integrate() is highly discouraged** in transforms — it will likely be deprecated
- Use native connector nodes over HTTP nodes whenever possible

## GraphQL API Usage

All queries use Relay-style connections:
```graphql
query GetData($where: ModelWhereInput) {
  model(where: $where) {
    edges {
      node {
        id field1 field2
      }
    }
  }
}
```

Result traversal: `$.model.edges.node` or `$.model.edges[*].node`

Mutations require payload arrays:
```jsonata
{ "payload": [{ "create": { "field": "value" } }] }  /* Create */
{ "payload": [{ "where": { "id": "x" }, "update": { "field": "value" } }] }  /* Update */
```

See `references/graphql-patterns.md` for operators, aggregations, duration fields, multi-query patterns.

### GraphQL Mutation Patterns

**Delete Mutations** use `payload` array with individual `where` wrappers:
```graphql
mutation DeleteRecords($payload: [ModelNameDeletePayloadInput!]!) {
  deleteModelName(payload: $payload) { id }
}
```
JSONata: `{ "payload": $.modelName.edges.node.{ "where": { "id": id } } }`

Use `.{}` mapping (no square brackets) — JSONata produces the array naturally from the node set.

**Create Mutations** wrap each record in `"create"`:
```jsonata
{ "payload": $.arrayOfResults.{ "create": $ } }
```

**Upsert Mutations** need `where`, `create`, and `update`:
```jsonata
{ "payload": $.records.{ "where": { "uniqueField": uniqueField }, "create": $, "update": $ } }
```

**No `_nin` support** — use `_not` with `_in`: `{ "_not": { "assetId": { "_in": ["id1"] } } }`

**`where: { id: { _in: [...] } }` does NOT work at the mutation level.** Use individual `where` wrappers inside the `payload` array instead (see Delete Mutations above).

**OrderBy** always uses `"field"` and `"direction"` as explicit keys, always wrapped in an array:
```jsonata
{ "orderBy": [{ "field": "createdAt", "direction": "DESC" }] }
```

**`$state.context` survives through broadcast fan-out nodes.** Safe to rely on context for timestamps, config values, and other shared data across broadcast branches.

## Node Reference

Read `references/node-catalog.md` for complete node types and configurations.

### Node Categories

| Category | Count | Description |
|----------|-------|-------------|
| Conditionals | 4 | Conditional routing and filtering |
| Context | 3 | Workflow context manipulation |
| Debugging | 2 | Logging and echo for development |
| Device Gateway | 5 | Device function execution and printing |
| Events | 6 | Event sources and pub/sub |
| Flow Control | 10 | Parallel execution, delays, error handling |
| Fuuz | 9 | Platform queries, mutations, documents |
| Integration | 27 | External system connectors |
| Notification | 2 | Push notification send/receive |
| Scripts | 5 | Custom transforms (JSONata, JS, saved) |
| Transformation | 8 | Data format conversion and array operations |
| Validation | 1 | Schema validation |

### Key Node Types by Function

- **Triggers**: schedule, dataChanges, request, webhook
- **Data**: query, mutate, queryFile
- **Transform**: transform (JSONata), javascriptTransform, savedTransformV2
- **State**: setContext, mergeContext
- **Control**: when, unless, ifElse, switch, filterArray, broadcast
- **Concurrency**: mutexLock, mutexUnlock
- **UI** (web flows): formDialog, snackbar, searchTable, alertDialog
- **Gateway**: devicePrintFile, deviceSubscription, executeDeviceFunction
- **Integration**: integrateV2, awsLambda, various ERP/external systems

## JSONata Custom Bindings

Fuuz extends JSONata with hundreds of custom bindings. Key functions:
- `$moment()`, `$momentTz()` - Date/time with timezone support
- `$schedule(id)`, `$scheduleGroup(id)` - Production scheduling
- `$coalesce([...])` - First non-null value
- `$isNilOrEmpty($)`, `$isNotNilOrEmpty($)` - Null checks
- `$state.context` - Access flow state
- Duration, aggregation, and data model helpers

See `references/jsonata-bindings.md` for complete binding reference and `references/time-schedule-bindings.md` for schedule-specific functions.

### Context Management Best Practices

- **Always** label context with a wrapping variable: use `{"plexResponse": $}` instead of just `$`
- Never set or merge context with a transform that is just `$`
- If context has unnecessary data, prune it before storage
- If context is large and no longer needed, remove it
- **Warning**: Context is shared with each downstream node. Forks and broadcasts multiply memory usage by the number of concurrent transactions. Keep context clean.
- `$state.context` values set via `setContext` nodes DO survive through `broadcast` fan-out nodes
- Use `mergeContext` when you need data from a query available alongside pipe data from another query. `setContext` replaces context; `mergeContext` adds to it.

## Best Practices

0. **Single-purpose flows** — Each flow should do ONE thing well. Don't combine multiple triggers, domains, or audiences in one flow. If a flow grows beyond ~15 nodes, consider splitting into master + child flows.
1. **Always provide descriptive names** for flows and nodes - future maintainability depends on it
2. **Include flow descriptions** - explain purpose, trigger, and key models
3. **Use Query node over $query()** - better performance with split orchestration
4. **UUID Generation**: Always use fresh UUIDs for node IDs and nextNodes references
5. **Early Filtering**: Use when/unless nodes early to exit unnecessary paths
6. **Context Storage**: Store config/settings in context after querying, before heavy processing
7. **Mutex Pattern**: Lock on specific record ID for concurrent write protection
8. **Broadcast + Response**: Fan-out pattern requires response node to mark end of parallel path
9. **Guard Clauses**: unless node passes when condition is false (rejection condition)
10. **Variables Transform**: Build GraphQL variables from `$` or `$state.context`
11. **Date Handling**: Use `$moment()` for UTC dates, `.tz(timezone)` for local display
12. **Result Traversal**: Always traverse `edges.node` structure in GraphQL results
13. **Payload Arrays**: Even single mutations require array wrap
14. **For adding descriptions**: Export flow JSON and provide to Claude for contextual description generation
15. **Store config in Settings, not hardcoded** — Query application Settings via System API and parse with defaults. Enables runtime tuning without redeploying.
16. **JS Runtime Restrictions** — No `.toFixed()` (use `Math.round()`), no `let`/`const` (use `var`), no arrow functions, no template literals. See `common-pitfalls.md` for full list.
17. **Inline expressions in loops** — Variable scoping is unreliable inside `for` loops in the Fuuz JS runtime. Inline single-use values instead of assigning to variables.
18. **setContext vs mergeContext** — `setContext` replaces context entirely; `mergeContext` adds to it. Use `mergeContext` when you need data from a prior query alongside new pipe data.
19. **Guard empty mutations** — Always add `unless` guard nodes before mutations: `$count($.model.edges.node) = 0`. Prevents empty payload errors.

## Resources

### references/
- `node-catalog.md` - Complete node types with configuration examples
- `jsonata-bindings.md` - Flow-specific JSONata reference: evaluation context ($state, node result behavior), operators (path, chaining, transform), critical gotchas, common flow patterns, and all platform functions (182+ custom bindings for queries, mutations, joins, moments, EDI, XML, encryption, scheduling, unit conversion)
- `graphql-patterns.md` - Query/mutation patterns, operators, aggregations
- `flow-patterns.md` - Flow design philosophy, single-purpose principle, hybrid multi-flow design, flow control patterns, complete flow examples with node-by-node configurations
- `time-schedule-bindings.md` - Schedule and moment timezone functions
- `common-pitfalls.md` - **CRITICAL** - Data access patterns, duration handling, JS runtime limitations (.toFixed(), scoping), JSONata error handling, context management, debugging solutions
- ~~`system-schema.json`~~ - **Moved to standalone `fuuz-system-schema` skill** (158 system models, 2,676 fields). Load that skill when you need system model field definitions.
- ~~`system-seeded-values.md`~~ - **Located in `fuuz-platform` skill** (`fuuz-platform/references/system-seeded-values.md`). Load fuuz-platform for seeded values.
- ~~`visualization-library.md`~~ - **Located in `fuuz-platform` skill** (`fuuz-platform/references/visualization-library.md`). Load fuuz-platform for chart configuration.

### Additional References
- `references/flow-design-patterns-advanced.md` — Advanced flow patterns: array iteration rules, duration storage, memory wrapping, pre-population, broadcast optimization, structured logging, validation tracking, OEE calculation patterns, telemetry aggregation
- `references/flow-context-reference.md` — Flow JSON envelope structure, 21 node types with production examples, dataChanges payload structure, `$scheduleGroup()` pattern, production JSONata transforms
- `references/flow-essentials.md` — Quick reference: node categories, webhook URL format and configuration, 5 manufacturing flow patterns (OEE calculation, inventory move webhook, asset lifecycle, fan-out integration, planned schedule generation), timezone handling rules, flow JSON structure, flow documentation standard

### Companion Skills
- **fuuz-schema** (`fuuz schema/SKILL.md`) — Data model design and Fuuz package generation. Consult when building flows that query or mutate custom data models — the schema skill defines the models, field types, and relationships your flows will interact with.
- **fuuz-screens** (`fuuz screens/SKILL.md`) — Screen creation and UI design. Consult when building web flows (Screen type) that support dashboards, HMIs, or custom screen actions via `$executeFlow()`.
- **fuuz-platform** (`fuuz-platform/SKILL.md`) — Shared platform reference. Consult for connector configurations, device driver details, system seeded values, visualization library, platform glossary, and cross-skill task routing.
- **fuuz-system-schema** (`fuuz-system-schema/SKILL.md`) — Complete System API GraphQL schema (158 system models, 2,676 fields). Load when you need exact field names/types for system models.

### Application Schema (Customer-Specific)

**IMPORTANT:** Application API schemas are customer/application-specific and NOT included in this skill.

**When users need Application API schema information:**
1. Ask: "Can you provide your Application API schema or data model documentation?"
2. User exports schema from Fuuz or provides model documentation
3. Schema should include: data models, field types, relationships, custom fields
4. Use provided schema to generate accurate queries/mutations for their specific app

**System API schema** is available in the companion `fuuz-system-schema` skill — load it when you need system model definitions.

---

## Version History

### Version 1.0 (February 8, 2026)

**Initial Release** - Comprehensive Fuuz data flow development skill

**Core Features:**
- Complete node catalog (50+ node types with configurations)
- JSONata custom bindings reference (294+ functions)
- GraphQL patterns (System API & Application API)
- 5 complete flow pattern examples
- Time/schedule binding reference
- Critical pitfalls and debugging guide

**Reference Documentation:**
- System API schema (now in fuuz-system-schema skill — 158 models)
- System seeded values reference (now in fuuz-platform — 2k+ lines)
- Fusion Charts visualization library reference (now in fuuz-platform)
- Common pitfalls with solutions

**Platform Knowledge:**
- Dual API architecture (System vs Application)
- Timeout contexts (10min external, 20min internal)
- Query node vs $query() performance patterns
- Saved transforms for reusability
- Proper naming conventions and documentation requirements
- Duration field mixed format handling
- Data access patterns ($ vs $state.context)

**Maintainer:** Fuuz Development Team
**Distribution:** fuuz-flows-v1.skill

### Version 1.1 (February 2026)

**Training Data Update** - Integrated new standards from TeamChatTrainingDocs and ML Script Training

**Flow Design Standards (new top-level section):**
- Promoted Flow Design Philosophy to standalone section
- Emphasized single-purpose flow design and data-first design philosophy

**Naming Conventions:**
- Consolidated flow naming into single section with `{Primary Fuuz Object} {direction/verb} {External Platform} {External Object} {Event Type}` format
- Removed conflicting camelCase-only guidance; camelCase retained for internal flows

**Flow Control:**
- Added Flow Control Rules subsection (Request/Response pairing, Data Changes conditional guard, Topics for pagination)
- Enhanced Broadcast Warning with batch script alternative recommendation
- Added Throw Error Node pattern for contextual error messages

**JavaScript Runtime:**
- Added `'use strict';` requirement at top of all JS scripts
- Reorganized Works/Does NOT work lists for clarity

**GraphQL Patterns:**
- Added `_in` mutation-level limitation warning
- Added OrderBy syntax pattern with explicit `field`/`direction` keys

**Existing sections verified and confirmed current:**
- Documentation Requirements (all items present)
- Context Management Best Practices (wrapping, pruning, mergeContext vs setContext)
- Conditional Best Practices (double negatives, If/Else guidance)
- Integration Node Standards ($integrate() deprecation warning, naming)
- Collect Node (Batch Count + Batch Time requirement)
- Try/Catch (no catch-port looping)
- Settings-Driven Configuration pattern

### Version 1.4 (February 26, 2026)

**Training Data Integration** - Incorporated authoritative data-flow-nodes and JSONata training data

**New SKILL.md Sections:**
- **State Model** — Documented payload, context, claims, and batches as the four components of workflow state passed between nodes
- **Common Property Patterns** — Standard output port, JSONata transform `$` vs `$$` scope, connection, returnErrors, degreeOfParallelism, combination strategies (index, first, merge)
- **Format Glossary** — Reference table for property format values (jsonata, javascript, graphql, port, sql)
- **Validation Rules** — NodeType validation rules (requireChangedName, requireInputNode, requireOutputNode, requireWalkthrough, minimumNoteLength)
- **Node Category Count Table** — 12 categories with counts totaling 82 node types
- **Broadcast+Combine Performance Note** — Guidance on when to use Broadcast+Combine vs degreeOfParallelism or in-transform iteration

**jsonata-bindings.md Replaced:**
- Replaced with flow-specific JSONata reference built from authoritative training data
- Added Data Flow evaluation context documentation ($state paths, node types and JSONata results table)
- Added critical gotchas section (12 items including singleton array equivalence, `$[].{}` vs `$.{}`)
- Added complete operators reference (path operators, chaining `~>`, transform operator `| |`, conditional operators)
- Added programming constructs (variable binding, lambdas, partial application, regex)
- Added common flow patterns (null-safe navigation, conditional object construction, array aggregation, GraphQL variable building, time windows)
- Added all 182+ platform functions organized by category (Core, MFGx App, Joins, Moment, XML, EDI, Encryption, Network, Calendars, Scheduling, Unit Conversion, Semver)
- Removed screen-specific content ($components, metadata, frontend functions) — flow-only reference
- Retained custom bindings in JavaScript usage, debug tips, and performance considerations

### Version 1.3 (February 22, 2026)

**New Reference File:**
- `references/flow-essentials.md` — Flow quick reference with node categories, webhook URL format (`https://api.{environment}.fuuz.app/webhook/post/{path}`), 5 manufacturing patterns (scheduled OEE, inventory move webhook with validation cascade, asset lifecycle tracking, fan-out integration, planned schedule generation), timezone handling rules, flow JSON structure overview, flow documentation standard

### Version 1.2 (February 21, 2026)

**Reference Documentation Update** - Baked advanced flow design patterns and flow context reference into skill

**New Reference Files:**
- `references/flow-design-patterns-advanced.md` — Comprehensive flow design patterns covering array iteration rules, duration storage as milliseconds, memory wrapping for Array nodes, pre-population strategy, broadcast optimization, structured logging, validation tracking, OEE calculation patterns (ISO 22400), telemetry aggregation pipelines, workcenter state management, and quality code handling
- `references/flow-context-reference.md` — Complete flow JSON envelope structure, 21 node types with production-extracted examples, dataChanges payload structure (`value.before`/`value.after`), `$scheduleGroup()` pattern, production JSONata transforms, web flow parameter access (`$metadata`, `$components`), and full connection graph examples for scheduled OEE, reactive OEE, telemetry aggregation, alarm creation, and dashboard APIs

**New Critical Rules Added to SKILL.md:**
- Critical JSONata Iteration Rule: `$[].{}` vs `$.{}` distinction with examples
- Duration Storage Rule: Always store as milliseconds (Int) with robust parsing function for mixed GraphQL response formats
- Memory Wrapping for Array Nodes: `return [$.payload]` pattern to prevent undefined in downstream mutations

