# Fuuz Data Flow Skill — Context & Reference

> **Purpose:** Use this document as context when building a Claude Skill for developing Data Flows on the Fuuz Industrial Operations platform (fuuz.com). Everything below was extracted from a working application (`MachineTelemetryApp v0.0.2`, platform version 2026.2.0). This application was vibe-coded with Claude — Claude generated most of the flows, queries, and screens using examples of Fuuz patterns as context.

---

## 1. What Is a Fuuz Data Flow?

A Data Flow is a server-side automation pipeline. It is a directed graph of **nodes** connected by `nextNodes` references (UUIDs). Each node receives the output of the previous node as its input (`$` in JSONata, or the function parameter in JavaScript). Flows are triggered by schedules, data-change events, or API requests.

### Flow JSON Structure (top level)

```json
{
  "header": {
    "id": "calculateOeeHourly",
    "name": "calculateOeeHourly",
    "description": "..."
  },
  "version": {
    "id": "...",
    "number": "4",
    "description": "...",
    "dataFlowId": "calculateOeeHourly",
    "flow": {
      "nodes": [ ...array of node objects... ]
    },
    "diagram": { ... visual layout metadata ... }
  }
}
```

### Node JSON Structure

Every node has this shape:

```json
{
  "id": "uuid-here",
  "name": "Human-readable name",
  "type": "nodeType",
  "data": {
    "nextNodes": ["uuid-of-next-node"],
    ...type-specific properties...
  },
  "debug": {
    "breakpointEnabled": false,
    "publishSubscribeClient": false,
    "publishToMFGx": false
  },
  "logging": {
    "executionMetrics": false,
    "executionStarted": false,
    "executionSucceeded": false,
    "executionFailed": true,
    "enableTraceLogging": false
  }
}
```

Nodes are connected via `data.nextNodes` — an array of node UUIDs. Some nodes have conditional exits (`onTrueNextNodes`, `onFalseNextNodes`, `onSubmitNextNodes`, `onCancelNextNodes`, or `branches`).

---

## 2. Node Type Catalog (21 types found in production)

### 2.1 Trigger Nodes (flow entry points)

#### `schedule`
Starts a flow on a cron schedule. The schedule is configured externally via DataFlowSchedule/DataFlowScheduleFrequency records and linked to the flow.

```json
{
  "type": "schedule",
  "data": {
    "configure": "Open Schedule Editor",
    "nextNodes": ["uuid"]
  }
}
```

#### `dataChanges`
Triggers on Create/Update/Delete mutations of a data model. This is event-driven / reactive.

```json
{
  "type": "dataChanges",
  "data": {
    "api": "Application",
    "type": "ProductionLog",
    "operations": ["Create"],
    "nextNodes": ["uuid"]
  }
}
```

The payload injected into the flow is:
```json
{
  "value": {
    "before": { ...previous record state or null... },
    "after": { ...new record state... }
  }
}
```

#### `request` / `response`
Entry/exit for sub-flows and web flows invoked via API. `request` is the entry point, `response` returns data.

```json
// Request node
{ "type": "request", "data": { "nextNodes": ["uuid"] } }

// Response node
{ "type": "response", "data": { "responseTransform": "$", "nextNodes": [] } }
```

### 2.2 Data Nodes

#### `query`
Executes a GraphQL query against the Application API.

```json
{
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetData($where: ModelWhereInput) { model(where: $where) { edges { node { id field1 field2 } } } }",
    "variablesTransform": "{ \"where\": { \"id\": { \"_eq\": $.someId } } }",
    "nextNodes": ["uuid"]
  }
}
```

**Key patterns for `variablesTransform`:**
- `{}` — no variables (static query)
- `$` — pass entire current payload as variables
- JSONata expression — transform current payload into variables object

#### `mutate`
Executes a GraphQL mutation (create, update, upsert, delete).

```json
{
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation CreateRecord($payload: [ModelCreatePayloadInput!]!) { createModel(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": [{ \"create\": { \"field1\": $.value1 } }] }",
    "nextNodes": ["uuid"]
  }
}
```

**Mutation patterns observed:**

Create:
```graphql
mutation CreateModel($payload: [ModelCreatePayloadInput!]!) {
  createModel(payload: $payload) { id }
}
```
Variables: `{ "payload": [{ "create": { "field": "value" } }] }`

Update:
```graphql
mutation UpdateModel($payload: [ModelUpdatePayloadInput!]!) {
  updateModel(payload: $payload) { id }
}
```
Variables: `{ "payload": [{ "where": { "id": "x" }, "update": { "field": "value" } }] }`

Upsert:
```graphql
mutation UpsertModel($payload: [ModelUpsertPayloadInput!]!) {
  upsertModel(payload: $payload) { id }
}
```
Variables: `{ "payload": [{ "where": { "id": "x" }, "create": { ... }, "update": { ... } }] }`

### 2.3 Transform Nodes

#### `transform` (JSONata)
Evaluates a JSONata expression. Has access to `$`, `$state.context`, `$moment()`, `$momentTzGuess()`, `$coalesce()`, `$scheduleGroup()`, `$isNilOrEmpty()`, `$isNotNilOrEmpty()`, and other built-in functions.

```json
{
  "type": "transform",
  "data": {
    "transform": "( $timezone := $.setting.edges[0].node.defaultValue; ... )",
    "nextNodes": ["uuid"]
  }
}
```

**JSONata examples from production:**

Time window calculation:
```jsonata
(
  $prevHourStart := $moment().startOf('hour').subtract(1, 'hour');
  $prevHourEnd := $moment($prevHourStart).add(1, 'hour');
  {
    "periodStart": $moment($prevHourStart),
    "periodEnd": $moment($prevHourEnd),
    "periodAt": $moment($prevHourStart)
  }
)
```

Timezone-aware date handling:
```jsonata
(
  $timezone := $.setting.edges[0].node.defaultValue;
  $nowLocal := $moment().tz($timezone);
  $hourStartLocal := $nowLocal.startOf('hour').subtract(1, 'hour');
  $hourEndLocal := $moment($hourStartLocal).add(1, 'hour');
  /* $moment() already returns properly formatted dates — no .toISOString() needed */
  ...
)
```

Schedule group lookup:
```jsonata
(
  $now := $moment();
  $scheduleGroupIds := $distinct($workcenters.scheduleGroupId);
  $currentScheduleByGroup := $merge($scheduleGroupIds.(
    $groupId := $;
    $currentSchedules := $scheduleGroup($groupId).getCurrentSchedules($now, $now, { "inclusive": true });
    $activeSchedule := $currentSchedules[0];
    { ($groupId): $activeSchedule.scheduleId }
  ));
  ...
)
```

Simple data extraction:
```jsonata
$.workcenter
```

Reducing edges to flat array:
```jsonata
$.workcenter.edges.node
```

#### `javascriptTransform`
Executes JavaScript (ES6 strict mode). Has access to `$` (current payload), `$state.context` (flow state), `$moment()` (moment-timezone), and `console.log()`. Must `return` the output.

```json
{
  "type": "javascriptTransform",
  "data": {
    "transform": "'use strict';\n\nvar result = ...;\nreturn result;",
    "nextNodes": ["uuid"]
  }
}
```

**JavaScript patterns from production:**

Simple window calculation:
```javascript
'use strict';

var config = { frequencySeconds: 300, anomaly: { enabled: true, probability: 0.02 } };
// Use $moment() which already returns properly formatted dates
var windowEnd = $moment();
var windowStart = $moment().subtract(config.frequencySeconds, 'seconds');

return {
  floatDataPoints: $.floatDataPoints,
  window: { start: windowStart, end: windowEnd }
};
```

Accessing flow state:
```javascript
'use strict';
var oeeConfig = $state.context.oeeConfig;
var boundaries = oeeConfig.boundaries;
// ... calculations ...
return { payload: results };
```

Duration handling:
```javascript
function toMs(val) {
  if (!val) return 0;
  if (typeof val === 'number') return val;
  if (typeof val === 'string') {
    try { return moment.duration(val).asMilliseconds(); }
    catch (e) { return 0; }
  }
  return 0;
}
```

#### `savedTransformV2`
References a reusable, named transform (stored separately). Used for shared logic across flows.

```json
{
  "type": "savedTransformV2",
  "data": {
    "transformScriptLanguage": "JavaScript",
    "transformId": { "id": "oeeTimeBoundaryScript", "label": "OEE Time Boundary Script" },
    "requestTransform": "$",
    "responseTransform": "$",
    "nextNodes": ["uuid"]
  }
}
```

### 2.4 State Management Nodes

#### `setContext`
Stores data in the flow's state context (`$state.context`). Subsequent nodes can access these values.

```json
{
  "type": "setContext",
  "data": {
    "transform": "{ \"oeeConfig\": $ }",
    "nextNodes": ["uuid"]
  }
}
```

**Context patterns:**
- `{ "timeWindow": $ }` — store entire payload
- `{ "afterData": $.value.after }` — extract and store specific data
- `{ "config": { "avgOrdersPerWorkcenter": 5, ... } }` — set static configuration
- `{ "workcenterId": $.workcenterId, "historyId": $.historyId }` — cherry-pick fields

#### `mergeContext`
Like `setContext` but merges into existing context without overwriting.

```json
{
  "type": "mergeContext",
  "data": {
    "transform": "{ \"setting\": $.setting.edges.node.defaultValue, \"productCycleTime\": $.product.edges.node.standardCycleTime }",
    "nextNodes": ["uuid"]
  }
}
```

### 2.5 Control Flow Nodes

#### `when` (Accept — conditional gate, passes through if true)
```json
{
  "type": "when",
  "data": {
    "transform": "($.value.after.qualityCode = \"16\" or $.value.after.qualityCode = \"64\" or $.value.after.quality = \"Bad\")",
    "nextNodes": ["uuid"]
  }
}
```

#### `unless` (Reject — conditional gate, passes through if condition is false)
```json
{
  "type": "unless",
  "data": {
    "transform": "$.value.after.processedInOeeHourlyAt != null",
    "nextNodes": ["uuid"]
  }
}
```

#### `ifElse`
Branches into true/false paths.
```json
{
  "type": "ifElse",
  "data": {
    "transform": "$state.context.copyDataPoints = true",
    "onTrueNextNodes": ["uuid-true-path"],
    "onFalseNextNodes": ["uuid-false-path"]
  }
}
```

#### `switch` (Route)
Multi-way branch.
```json
{
  "type": "switch",
  "data": {
    "branches": [
      { "name": "Has Data", "nextNodes": ["uuid"], "transform": "$isNotNilOrEmpty($)" },
      { "name": "No Data", "nextNodes": ["uuid"], "transform": "$isNilOrEmpty($)" }
    ]
  }
}
```

#### `filterArray`
Filters an array using JSONata.
```json
{
  "type": "filterArray",
  "data": {
    "inputTransform": "$.productionRecords",
    "filterTransform": "$",
    "outputTransform": "$",
    "enableAdvancedConfiguration": true,
    "nextNodes": ["uuid"]
  }
}
```

#### `broadcast`
Fan-out: sends the same payload to the next node for each item in an array. Used when processing batches.
```json
{
  "type": "broadcast",
  "data": { "nextNodes": ["uuid"] }
}
```

### 2.6 Concurrency Nodes

#### `mutexLock`
```json
{
  "type": "mutexLock",
  "data": {
    "lockTTLms": 30000,
    "lockRetries": -1,
    "throwErrorWhenLockNotAcquired": false,
    "resourceIdTransform": "$.updateOeeHourly.id",
    "nextNodes": ["uuid"]
  }
}
```

#### `mutexUnlock`
```json
{
  "type": "mutexUnlock",
  "data": { "nextNodes": ["uuid"] }
}
```

**Mutex pattern:** Always wrap critical sections (multiple mutations to the same record) with lock -> operations -> unlock. Use `resourceIdTransform` to lock on a specific record ID.

### 2.7 UI Interaction Nodes (screen-invoked flows only)

#### `formDialog`
Shows a modal form dialog to the user.
```json
{
  "type": "formDialog",
  "data": {
    "titleTransform": "\"Duplicate Asset\"",
    "formInputs": [...form field definitions...],
    "dataTransform": "$.formData ~> |$|{...}|",
    "onSubmitNextNodes": ["uuid"],
    "onCancelNextNodes": ["uuid"],
    "submitIconTransform": "\"floppy-disk\""
  }
}
```

#### `snackbar`
Shows a toast notification.
```json
{
  "type": "snackbar",
  "data": {
    "severityTransform": "\"warning\"",
    "titleTransform": "\"Action Canceled\"",
    "messageTransform": "\"No changes were made\"",
    "nextNodes": []
  }
}
```

#### `searchTable`
Refreshes a table on the screen.
```json
{
  "type": "searchTable",
  "data": {
    "screenElementTransform": "\"AssetTable\"",
    "nextNodes": ["uuid"]
  }
}
```

---

## 3. GraphQL API Patterns

### 3.1 Query Structure

All queries follow a Relay-style connection pattern with `edges.node`:

```graphql
query GetModel($where: ModelWhereInput, $orderBy: [ModelOrderByInput!], $first: Int, $after: String) {
  model(where: $where, orderBy: $orderBy, first: $first, after: $after) {
    edges {
      node {
        id
        field1
        field2
        relatedModel {
          id
          name
        }
      }
    }
  }
}
```

### 3.2 Where Clause Operators

```json
{ "field": { "_eq": "value" } }
{ "field": { "_in": ["a", "b"] } }
{ "field": { "_gte": "2026-01-01", "_lt": "2026-01-02" } }
{ "field": { "_isNull": true } }
{ "_and": [ { "field1": { "_eq": "x" } }, { "field2": { "_gte": "y" } } ] }
{ "_or": [ { "endAt": { "_gte": "..." } }, { "endAt": { "_isNull": true } } ] }
```

### 3.3 Aggregation Queries

Fuuz supports inline aggregations via `_aggregate` on relationship fields:

```graphql
query AggregateRawTelemetry($where: TelemetryRawWhereInput) {
  telemetryRaw(where: $where, first: 100000) {
    edges {
      node {
        dataPointId
        _aggregate {
          value {
            count
            minimum
            maximum
            average
            sum
            first
            last
            standardDeviation
            median
            uniqueCount
            percentile(percentiles: [5, 25, 50, 75, 95])
          }
        }
      }
    }
  }
}
```

### 3.4 Duration Fields

Duration fields support a `format` argument:
```graphql
actualCycleTime(format: milliseconds)
idealCycleTime(format: milliseconds)
```

### 3.5 Multi-Query Pattern

Combine multiple queries in a single GraphQL request with aliases:

```graphql
query GetDataPointsByType {
  floatDataPoints: dataPoint(where: { dataType: { _eq: "float" } }) {
    edges { node { id frequency setPoint } }
  }
  booleanDataPoints: dataPoint(where: { dataType: { _eq: "boolean" } }) {
    edges { node { id frequency } }
  }
}
```

### 3.6 Settings Access

Tenant settings (timezone, locale, etc.) are fetched via:
```graphql
setting(where: { id: { _eq: "TimeZone" } }) {
  edges { node { defaultValue } }
}
```

---

## 4. Common Flow Patterns

### 4.1 Scheduled ETL (Extract -> Transform -> Load)

```
Schedule -> Query (get data) -> Transform/JavaScript (process) -> Mutate (write results)
```

Example: Hourly telemetry aggregation
```
Schedule
  -> JSONata: Calculate time window (previous hour)
  -> SetContext: Store time window
  -> Query: Get all data points where historize=true
  -> JavaScript: Batch data points into groups
  -> Broadcast: Fan out to parallel processing
    -> Query: Aggregate raw telemetry (with _aggregate)
    -> Mutate: Create TelemetryHourly records
    -> Response
```

### 4.2 Data Change -> Reactive Update

```
DataChanges (trigger) -> When/Unless (filter) -> SetContext -> Query (get related data) -> JavaScript (calculate) -> MutexLock -> Mutate -> MutexUnlock
```

Example: Production log -> OEE update
```
DataChanges: ProductionLog Create
  -> Unless: Skip if already processed (processedInOeeHourlyAt != null)
  -> SetContext: Store trigger data
  -> Query: Get timezone, product data, matching OEE hourly record
  -> MergeContext: Add product info to state
  -> JavaScript: Calculate OEE impact
  -> Unless: Skip if no matching OEE record
  -> MutexLock: Lock on OEE hourly record ID
  -> Mutate: Update OeeHourly
  -> MutexUnlock
  -> Mutate: Mark ProductionLog as processed
```

### 4.3 Web Flow (Dashboard API)

```
Request -> JSONata (extract params) -> SavedTransform (shared logic) -> SetContext -> Query -> JavaScript (calculate) -> JSONata (format) -> Response
```

Web flows read parameters from:
```jsonata
$metadata.querystring.wcId          // URL query params
$components.Screen.context.xxx      // Screen context
$metadata.settings.TimeZone         // Tenant settings
```

And push results back to the screen:
```jsonata
($data := $; $components.Screen.fn.setContext($data))
```

### 4.4 Interactive Screen Flow

```
FormDialog (user input) -> Request -> Query (get source data) -> JavaScript (generate form) -> FormDialog (confirm options) -> SetContext -> Mutate (create) -> Conditional (ifElse) -> Mutate (related) -> SearchTable (refresh) -> Snackbar (notify)
```

### 4.5 Simulation / Generator Pattern

```
Schedule -> Query (get config & active records) -> SetContext -> JavaScript (generate data with randomization) -> FilterArray (split by type) -> Broadcast -> Mutate (batch insert)
```

### 4.6 Multi-Step State Change with Mutex

```
Schedule/DataChange -> Query (all relevant data)
  -> JavaScript (process state engine)
  -> FilterArray (separate creates vs updates)
  -> Switch (route based on data presence)
    -> MutexLock -> Mutate (update existing) -> MutexUnlock
    -> MutexLock -> Mutate (create new) -> Mutate (update parent ref) -> MutexUnlock
```

---

## 5. Data Model Reference (key models)

### Hierarchy: Site -> Area -> Line -> Cell -> Asset -> DataPoint

### Telemetry Pipeline:
- `TelemetryRaw` (Float: value, recorded_at, dataPointId, quality, qualityCode)
- `TelemetryRawBool` (valueBool instead of value)
- `TelemetryRawString` (value:String instead of Float)
- `TelemetryHourly` (avg, min, max, sum, std, p05/p25/p50/p75/p95, count, periodAt)
- `TelemetryDaily` (same stats as hourly, periodAt:Date)

### OEE Pipeline (ISO 22400):
- `Workcenter` (production execution point, has scheduleGroupId, currentWorkcenterHistoryId)
- `WorkcenterHistory` (state changes: occurAt, endAt, modeId, stateId, eventId)
- `Mode` (8 values: PROD, SETUP, MAINT, DOWN, IDLE, CLEAN, ENG, NOSCHED — drives availability)
- `State` (14 values: RUN, SLOW, STOP, FLT, etc. — categorizes operational state)
- `OeeHourly` (availability, performance, quality, oee, run/downtime minutes, units)
- `OeeDaily` (daily aggregation of hourly)

### Production:
- `Product` (standardCycleTime, targetYield, unitId)
- `WorkOrder` (code, status, quantityTarget/Completed/Scrap, productId, workcenterId)
- `ProductionLog` (quantityGood, quantityScrap, occurAt, productId, workcenterId)

### Alarm:
- `Alarm` (triggerValue, limitValue, limitType, deviation, triggeredAt, alarmStateId, dataPointId)
- `AlarmState` (ACTIVE, ACKNOWLEDGED, ACTIVE_ACK, CLEARED_ACK, CLEARED_UNACK, etc.)

### Downtime:
- `EventCategory` (12 categories: Mechanical, Electrical, Changeover, etc.)
- `Event` (72 specific reasons within categories)

---

## 6. JSONata Built-in Functions Available in Fuuz

Based on production usage:

| Function | Usage |
|---|---|
| `$moment()` | Current moment (moment-timezone) |
| `$moment(value)` | Parse a value |
| `$moment().tz(timezone)` | Convert to timezone |
| `$moment().startOf('hour')` | Truncate to hour |
| `$moment().subtract(1, 'hour')` | Subtract time |
| `$moment().add(1, 'day')` | Add time |
| `$moment().format('YYYY-MM-DD')` | Format output (note: `$moment()` already returns properly formatted ISO dates — `.toISOString()` is not needed) |
| `$momentTzGuess()` | Guess current timezone |
| `$coalesce([a, b, c])` | First non-null value |
| `$scheduleGroup(id).getCurrentSchedules(start, end, opts)` | Get active schedules |
| `$isNilOrEmpty($)` | Check for null/empty |
| `$isNotNilOrEmpty($)` | Check for not null/empty |
| `$state.context.xxx` | Access flow state |
| `$now()` | Current ISO timestamp |
| `$count(array)` | Array length |
| `$distinct(array)` | Unique values |
| `$map(array, function)` | Map transformation |
| `$filter(array, function)` | Filter array |
| `$merge(array)` | Merge objects |
| `$keys(object)` | Object keys |
| `$lookup(object, key)` | Object value lookup |

### JSONata in `variablesTransform`:

The `variablesTransform` field on query/mutate nodes uses JSONata to transform the current payload (`$`) into the GraphQL variables object. Common patterns:

```jsonata
{}                                              // No variables
$                                               // Pass entire payload
{ "where": { "id": { "_eq": $.someId } } }     // Build where clause from payload
{ "payload": [$.{ "create": { "field": field } }] }  // Map array to create payloads
$count($) > 0 ? { "payload": ... } : { "payload": [] }  // Conditional payload
```

---

## 7. JavaScript Runtime in Fuuz Flows

- **Mode:** `'use strict'` recommended
- **Access:** `$` (current payload), `$state.context` (flow state), `$moment` (moment-timezone library), `moment` (also available)
- **Output:** Must `return` the value to pass to the next node
- **Logging:** `console.log()` available for debugging
- **Error handling:** `throw new Error('message')` to halt flow
- **No async/await** — synchronous execution only
- **Duration parsing:** Use `moment.duration(isoString).asMilliseconds()`

---

## 8. Complete Flow Examples (Connection Graphs)

### Scheduled OEE Hourly Calculation
```
Schedule -> Get Tenant Timezone [query]
  -> Calculate Hour Boundaries [JSONata transform]
  -> Set Context [setContext]
  -> ProductionLog & WC History Aggregate Query [query]
  -> Calculate OEE Metrics [JavaScript]
  -> Update OEE Hourly [mutate upsert] -> END
```

### Event-Driven: Production Log -> OEE Update
```
Data Changes (ProductionLog Create)
  -> Reject (unless: already processed)
  -> Set Context (store trigger data)
  -> Get Timezone & Product Data [query]
  -> Merge Context (product info)
  -> Generate OEE Details [JavaScript]
  -> Reject - If no id (unless: no matching OEE record)
  -> Mutex Lock (on OEE hourly record)
  -> Update OeeHourly [mutate]
  -> Mutex Unlock
  -> Update ProductionLog processedAt [mutate] -> END
```

### Scheduled Telemetry Aggregation (Hourly)
```
Schedule -> Calculate Time Window [JSONata]
  -> Set Context
  -> Get DataPoints where historize=true [query]
  -> Batch DataPoints [JavaScript]
  -> Broadcast (fan-out per batch)
    -> Aggregate Numeric Telemetry [query with _aggregate]
    -> Create Telemetry Hourly [mutate]
    -> Response -> END
```

### Event-Driven: Raw Telemetry -> Alarm Creation
```
Data Changes (TelemetryRaw Create)
  -> Accept (when: qualityCode is "16" or "64" or quality is "Bad")
  -> Set Context (store raw data)
  -> Query DataPoint (get alarm config)
  -> JavaScript (evaluate alarm conditions)
  -> Create Alarm [mutate]
  -> Response -> END
```

### Web Flow: Dashboard Real-Time API
```
Request (browser invokes)
  -> Get Dashboard Runtime Properties [JSONata: read $metadata.querystring, $components.Screen.context]
  -> OEE Time Boundaries [savedTransformV2: shared JS]
  -> Set Context
  -> Query (get OEE + workcenter data)
  -> JavaScript (calculate real-time metrics)
  -> JSONata (push to screen: $components.Screen.fn.setContext($data))
  -> Response -> END
```

---

## 9. Tips for Claude When Generating Flows

1. **Always use UUIDs** for node IDs and nextNode references. Generate them fresh for each flow.
2. **JSONata vs JavaScript:** Use JSONata for simple transforms, restructuring, and time calculations. Use JavaScript for complex business logic, loops with side effects, or heavy calculation.
3. **SetContext early** — after querying configuration/settings, store them in context so all downstream nodes can access via `$state.context`.
4. **MergeContext** when adding to context without overwriting (e.g., adding product data to existing OEE config).
5. **Mutex pattern** is essential when multiple concurrent triggers could update the same record (e.g., OEE hourly). Lock on the specific record ID.
6. **Broadcast + Response** for fan-out patterns — broadcast sends each array item individually, response marks the end of that parallel path.
7. **Unless node** for guard clauses — condition is the rejection condition (passes through when false).
8. **When node** for acceptance gates — condition is the acceptance condition (passes through when true).
9. **Variables transforms** on queries should build the `$where` input from `$` or `$state.context`.
10. **All dates in UTC** for queries, use `$moment().tz(timezone)` for local display. `$moment()` always returns properly formatted ISO dates — `.toISOString()` is not needed.
11. **GraphQL result shape** is always `$.modelName.edges[*].node.field` — remember to traverse the edges/node structure.
12. **Mutation payload arrays** — even for single records, wrap in array: `{ "payload": [{ "create": { ... } }] }`.
13. **Schedule configuration** is external — the schedule node just links to a DataFlowSchedule record.
14. **Web flows** read screen context from `$metadata` and `$components` and write back via `$components.Screen.fn.setContext()`.