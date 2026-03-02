# Common Flow Patterns

Complete flow examples with node-by-node configurations for common Fuuz automation scenarios.

**IMPORTANT:** All examples show proper node naming in the `name` field. In production flows:
- Always provide descriptive node names (shown in examples below)
- Generate fresh UUIDs for all `id` fields (examples use simplified IDs for readability)
- Include flow-level descriptions explaining purpose and trigger
- For adding descriptions to existing flows, export JSON and provide to Claude for analysis

## Flow Design Philosophy

### Single-Purpose Flow Principle

**Every flow should do ONE thing well.** If a flow needs to handle multiple concerns, break it into separate flows that communicate via executeFlow or shared data.

- **One trigger** → one flow. Don't combine schedule + data change triggers.
- **One domain** → one flow. Don't mix OEE calculations with alarm processing.
- **One audience** → one flow. Don't combine admin logic with operator logic.

### Flow Complexity Guidelines

| Flow Complexity | Recommended Max Nodes | Strategy |
|---|---|---|
| Simple (CRUD, data change) | 5-8 nodes | Single flow |
| Moderate (ETL, calculations) | 8-15 nodes | Single flow with clear sections |
| Complex (multi-model, multi-query) | 15-25 nodes | Split into master + child flows |
| Very Complex (cross-system integration) | 25+ nodes | Decompose into multiple coordinated flows |

### Flow Control Patterns

| Pattern | Use Case | Node Types |
|---|---|---|
| **Broadcast** | Fan-out array processing (process each item independently) | `broadcast` → process → `response` |
| **Fork** | Run multiple independent paths from one trigger | Multiple `nextNodes` from a single node |
| **Combine / Collect** | Aggregate results from parallel paths | `mergeContext` to combine results |
| **Mutex** | Protect concurrent writes to same record | `mutexLock` → mutate → `mutexUnlock` |
| **Try-Catch** | Handle errors gracefully | `when`/`unless` guards with error response paths |
| **Guard** | Skip processing when conditions not met | `unless` with rejection condition |

### Hybrid Multi-Flow Design

Complex systems should separate real-time and batch processing:

| Flow Type | Trigger | Timeout | Use Case |
|---|---|---|---|
| **Real-time** | `dataChanges` on Create | Internal (20 min) | Per-value analysis — O(1) per record |
| **Scheduled batch** | `schedule` (hourly/daily) | Internal (20 min) | Cross-record analysis — O(n) correlation, aggregation |
| **Scheduled projection** | `schedule` (hourly) | Internal (20 min) | Forward-looking calculations using batch results |

**Key principle:** Real-time flows should be O(1) per incoming record. Any calculation requiring multiple records belongs in a scheduled batch flow.

### Configuration via Application Settings

Store ALL operational parameters in application Settings, not hardcoded in flows:

```graphql
query GetSettings {
  setting(where: { code: { _in: ["prefix.key1", "prefix.key2"] } }) {
    edges { node { id code value } }
  }
}
```

Parse with defaults in `setContext`:
```jsonata
(
  $settings := $.setting.edges.node;
  $lookup := function($code, $default) {
    $val := $settings[code = $code].value;
    $val ? $number($val) : $default
  };
  {
    "config": {
      "key1": $lookup('prefix.key1', 60),
      "key2": $lookup('prefix.key2', 2.0)
    }
  }
)
```

### Context Survival Through Broadcast

`$state.context` values set via `setContext` nodes **DO survive** through `broadcast` fan-out nodes. Safe to rely on context for timestamps, config values, etc. across broadcast branches.

---

## Pattern 1: Scheduled Hourly ETL (Telemetry Aggregation)

**Use Case:** Aggregate raw telemetry data every hour

**Flow Description:** "Aggregates raw telemetry data into hourly statistical summaries for all historized float data points. Runs every hour on schedule. Calculates min/max/avg/std/percentiles using GraphQL aggregation and creates TelemetryHourly records."

**Flow:**
```
Schedule → Calculate Time Window → Set Context → Query DataPoints → 
Batch DataPoints → Broadcast → Aggregate Telemetry → Create Hourly → Response
```

### Nodes:

**1. Schedule Node**
```json
{
  "id": "schedule-1",
  "name": "Hourly Schedule Trigger",
  "type": "schedule",
  "data": {
    "configure": "Open Schedule Editor",
    "nextNodes": ["transform-1"]
  }
}
```

**2. Calculate Time Window (Transform)**
```json
{
  "id": "transform-1",
  "name": "Calculate Previous Hour Window",
  "type": "transform",
  "data": {
    "transform": "( $hourStart := $moment().startOf('hour').subtract(1, 'hour'); $hourEnd := $moment($hourStart).add(1, 'hour'); { \"periodStart\": $hourStart, \"periodEnd\": $hourEnd, \"periodAt\": $hourStart } )",
    "nextNodes": ["setcontext-1"]
  }
}
```

**3. Set Context**
```json
{
  "id": "setcontext-1",
  "name": "Store Time Window in Context",
  "type": "setContext",
  "data": {
    "transform": "$",
    "nextNodes": ["query-1"]
  }
}
```

**4. Query DataPoints**
```json
{
  "id": "query-1",
  "name": "Query Historized Float DataPoints",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetDataPoints { dataPoint(where: { historize: { _eq: true }, dataType: { _eq: \"float\" } }) { edges { node { id frequency } } } }",
    "variablesTransform": "{}",
    "nextNodes": ["js-1"]
  }
}
```

**5. Batch DataPoints (JavaScript)**
```json
{
  "id": "js-1",
  "name": "Batch DataPoints by 10",
  "type": "javascriptTransform",
  "data": {
    "transform": "'use strict';\n\nvar dataPoints = $.dataPoint.edges.node;\nvar batchSize = 10;\nvar batches = [];\n\nfor (var i = 0; i < dataPoints.length; i += batchSize) {\n  batches.push(dataPoints.slice(i, i + batchSize));\n}\n\nreturn { batches: batches, timeWindow: $state.context };",
    "nextNodes": ["broadcast-1"]
  }
}
```

**6. Broadcast**
```json
{
  "id": "broadcast-1",
  "name": "Fan Out to Process Batches",
  "type": "broadcast",
  "data": {
    "nextNodes": ["query-2"]
  }
}
```

**7. Aggregate Telemetry**
```json
{
  "id": "query-2",
  "name": "Aggregate Raw Telemetry with Statistics",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query AggregateRaw($where: TelemetryRawWhereInput) { telemetryRaw(where: $where, first: 100000) { edges { node { dataPointId _aggregate { value { count minimum maximum average sum standardDeviation median percentile(percentiles: [5, 25, 50, 75, 95]) } } } } } }",
    "variablesTransform": "{ \"where\": { \"dataPointId\": { \"_in\": $.map(function($v) { $v.id }) }, \"recordedAt\": { \"_gte\": $state.context.periodStart, \"_lt\": $state.context.periodEnd } } }",
    "nextNodes": ["mutate-1"]
  }
}
```

**8. Create Hourly Records**
```json
{
  "id": "mutate-1",
  "name": "Create Telemetry Hourly Records",
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation CreateHourly($payload: [TelemetryHourlyCreatePayloadInput!]!) { createTelemetryHourly(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": $.telemetryRaw.edges.node.( { \"create\": { \"dataPointId\": dataPointId, \"periodAt\": $state.context.periodAt, \"avg\": _aggregate.value.average, \"min\": _aggregate.value.minimum, \"max\": _aggregate.value.maximum, \"sum\": _aggregate.value.sum, \"std\": _aggregate.value.standardDeviation, \"median\": _aggregate.value.median, \"p05\": _aggregate.value.percentile[0], \"p25\": _aggregate.value.percentile[1], \"p50\": _aggregate.value.percentile[2], \"p75\": _aggregate.value.percentile[3], \"p95\": _aggregate.value.percentile[4], \"count\": _aggregate.value.count } } ) }",
    "nextNodes": ["response-1"]
  }
}
```

**9. Response**
```json
{
  "id": "response-1",
  "type": "response",
  "data": {
    "responseTransform": "$",
    "nextNodes": []
  }
}
```

---

## Pattern 2: Event-Driven Reactive Update (Production Log → OEE)

**Use Case:** When production log created, update OEE hourly record

**Flow:**
```
DataChanges → Reject if Processed → Set Trigger Data → Query Config → 
Merge Context → Calculate OEE → Reject if No Match → MutexLock → 
Update OEE → MutexUnlock → Mark Processed
```

### Nodes:

**1. DataChanges Trigger**
```json
{
  "id": "datachanges-1",
  "type": "dataChanges",
  "data": {
    "api": "Application",
    "type": "ProductionLog",
    "operations": ["Create"],
    "nextNodes": ["unless-1"]
  }
}
```

**2. Reject if Already Processed**
```json
{
  "id": "unless-1",
  "type": "unless",
  "data": {
    "transform": "$.value.after.processedInOeeHourlyAt != null",
    "nextNodes": ["setcontext-1"]
  }
}
```

**3. Store Trigger Data**
```json
{
  "id": "setcontext-1",
  "type": "setContext",
  "data": {
    "transform": "{ \"afterData\": $.value.after }",
    "nextNodes": ["query-1"]
  }
}
```

**4. Query Timezone and Product**
```json
{
  "id": "query-1",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetConfig($productId: UUID!) { setting(where: { id: { _eq: \"TimeZone\" } }) { edges { node { defaultValue } } } product(where: { id: { _eq: $productId } }) { edges { node { standardCycleTime targetYield } } } }",
    "variablesTransform": "{ \"productId\": $.value.after.productId }",
    "nextNodes": ["mergecontext-1"]
  }
}
```

**5. Merge Product Info**
```json
{
  "id": "mergecontext-1",
  "type": "mergeContext",
  "data": {
    "transform": "{ \"timezone\": $.setting.edges[0].node.defaultValue, \"standardCycleTime\": $.product.edges[0].node.standardCycleTime, \"targetYield\": $.product.edges[0].node.targetYield }",
    "nextNodes": ["js-1"]
  }
}
```

**6. Calculate OEE Impact (JavaScript)**
```json
{
  "id": "js-1",
  "type": "javascriptTransform",
  "data": {
    "transform": "'use strict';\n\nvar after = $state.context.afterData;\nvar cycleTime = $state.context.standardCycleTime;\nvar yield = $state.context.targetYield;\nvar timezone = $state.context.timezone;\n\nvar occurAt = moment(after.occurAt).tz(timezone);\nvar hourStart = occurAt.clone().startOf('hour');\n\nvar goodQty = after.quantityGood || 0;\nvar scrapQty = after.quantityScrap || 0;\nvar totalQty = goodQty + scrapQty;\n\nvar idealMinutes = (totalQty * cycleTime) / 60000;\nvar quality = totalQty > 0 ? (goodQty / totalQty) : 1;\n\nreturn { workcenterId: after.workcenterId, periodAt: hourStart, goodQty: goodQty, scrapQty: scrapQty, idealMinutes: idealMinutes, quality: quality, logId: after.id };",
    "nextNodes": ["query-2"]
  }
}
```

**7. Find Matching OEE Record**
```json
{
  "id": "query-2",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetOeeHourly($where: OeeHourlyWhereInput) { oeeHourly(where: $where, first: 1) { edges { node { id unitsGood unitsScrap quality } } } }",
    "variablesTransform": "{ \"where\": { \"workcenterId\": { \"_eq\": $.workcenterId }, \"periodAt\": { \"_eq\": $.periodAt } } }",
    "nextNodes": ["unless-2"]
  }
}
```

**8. Reject if No Match**
```json
{
  "id": "unless-2",
  "type": "unless",
  "data": {
    "transform": "$count($.oeeHourly.edges) = 0",
    "nextNodes": ["transform-1"]
  }
}
```

**9. Prepare Update**
```json
{
  "id": "transform-1",
  "type": "transform",
  "data": {
    "transform": "{ \"id\": $.oeeHourly.edges[0].node.id, \"newGood\": $.oeeHourly.edges[0].node.unitsGood + $.goodQty, \"newScrap\": $.oeeHourly.edges[0].node.unitsScrap + $.scrapQty, \"newQuality\": ($.oeeHourly.edges[0].node.unitsGood + $.goodQty) / ($.oeeHourly.edges[0].node.unitsGood + $.goodQty + $.oeeHourly.edges[0].node.unitsScrap + $.scrapQty), \"logId\": $.logId }",
    "nextNodes": ["mutex-1"]
  }
}
```

**10. Lock OEE Record**
```json
{
  "id": "mutex-1",
  "type": "mutexLock",
  "data": {
    "lockTTLms": 30000,
    "lockRetries": -1,
    "throwErrorWhenLockNotAcquired": false,
    "resourceIdTransform": "$.id",
    "nextNodes": ["mutate-1"]
  }
}
```

**11. Update OEE**
```json
{
  "id": "mutate-1",
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation UpdateOee($payload: [OeeHourlyUpdatePayloadInput!]!) { updateOeeHourly(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": [{ \"where\": { \"id\": $.id }, \"update\": { \"unitsGood\": $.newGood, \"unitsScrap\": $.newScrap, \"quality\": $.newQuality } }] }",
    "nextNodes": ["mutex-2"]
  }
}
```

**12. Unlock**
```json
{
  "id": "mutex-2",
  "type": "mutexUnlock",
  "data": {
    "nextNodes": ["mutate-2"]
  }
}
```

**13. Mark Log Processed**
```json
{
  "id": "mutate-2",
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation MarkProcessed($payload: [ProductionLogUpdatePayloadInput!]!) { updateProductionLog(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": [{ \"where\": { \"id\": $.logId }, \"update\": { \"processedInOeeHourlyAt\": $moment() } }] }",
    "nextNodes": []
  }
}
```

---

## Pattern 3: Web Flow Dashboard API

**Use Case:** Screen requests real-time OEE data

**Flow:**
```
Request → Extract Params → Get Time Boundaries → Set Context → 
Query OEE → Calculate Metrics → Push to Screen → Response
```

### Nodes:

**1. Request**
```json
{
  "id": "request-1",
  "type": "request",
  "data": {
    "nextNodes": ["transform-1"]
  }
}
```

**2. Extract Parameters**
```json
{
  "id": "transform-1",
  "type": "transform",
  "data": {
    "transform": "{ \"workcenterId\": $metadata.querystring.wcId, \"timezone\": $metadata.settings.TimeZone }",
    "nextNodes": ["savedtransform-1"]
  }
}
```

**3. OEE Time Boundaries (Saved Transform)**
```json
{
  "id": "savedtransform-1",
  "type": "savedTransformV2",
  "data": {
    "transformScriptLanguage": "JavaScript",
    "transformId": { "id": "oeeTimeBoundaries", "label": "OEE Time Boundaries" },
    "requestTransform": "$",
    "responseTransform": "$",
    "nextNodes": ["setcontext-1"]
  }
}
```

**4. Set Context**
```json
{
  "id": "setcontext-1",
  "type": "setContext",
  "data": {
    "transform": "$",
    "nextNodes": ["query-1"]
  }
}
```

**5. Query OEE + Workcenter**
```json
{
  "id": "query-1",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetOeeData($wcId: UUID!, $start: DateTime!, $end: DateTime!) { oeeHourly(where: { workcenterId: { _eq: $wcId }, periodAt: { _gte: $start, _lt: $end } }) { edges { node { availability performance quality oee runMinutes downMinutes } } } workcenter(where: { id: { _eq: $wcId } }) { edges { node { name currentMode currentState } } } }",
    "variablesTransform": "{ \"wcId\": $.workcenterId, \"start\": $.periodStart, \"end\": $.periodEnd }",
    "nextNodes": ["js-1"]
  }
}
```

**6. Calculate Real-Time Metrics**
```json
{
  "id": "js-1",
  "type": "javascriptTransform",
  "data": {
    "transform": "'use strict';\n\nvar oeeRecords = $.oeeHourly.edges.node;\nvar workcenter = $.workcenter.edges[0].node;\n\nvar totalRun = 0;\nvar totalDown = 0;\nvar avgOee = 0;\n\nfor (var i = 0; i < oeeRecords.length; i++) {\n  totalRun += oeeRecords[i].runMinutes || 0;\n  totalDown += oeeRecords[i].downMinutes || 0;\n  avgOee += oeeRecords[i].oee || 0;\n}\n\navgOee = oeeRecords.length > 0 ? avgOee / oeeRecords.length : 0;\n\nreturn { workcenterName: workcenter.name, currentMode: workcenter.currentMode, currentState: workcenter.currentState, totalRunMinutes: totalRun, totalDownMinutes: totalDown, averageOee: avgOee, periodStart: $state.context.periodStart, periodEnd: $state.context.periodEnd };",
    "nextNodes": ["transform-2"]
  }
}
```

**7. Push to Screen Context**
```json
{
  "id": "transform-2",
  "type": "transform",
  "data": {
    "transform": "( $data := $; $components.Screen.fn.setContext($data); $data )",
    "nextNodes": ["response-1"]
  }
}
```

**8. Response**
```json
{
  "id": "response-1",
  "type": "response",
  "data": {
    "responseTransform": "$",
    "nextNodes": []
  }
}
```

---

## Pattern 4: Interactive Screen Flow (Duplicate Record)

**Use Case:** User clicks "Duplicate" button, fills form, creates copy

**Flow:**
```
FormDialog (get record ID) → Request → Query Source → Generate Form Data → 
FormDialog (confirm) → SetContext → Mutate (create) → IfElse (relations?) → 
Mutate (copy relations) → SearchTable (refresh) → Snackbar
```

### Nodes:

**1. Initial Form Dialog**
```json
{
  "id": "form-1",
  "type": "formDialog",
  "data": {
    "titleTransform": "\"Duplicate Asset\"",
    "formInputs": [
      {
        "id": "assetId",
        "label": "Asset to Duplicate",
        "type": "asset-lookup",
        "required": true
      }
    ],
    "dataTransform": "$.formData",
    "onSubmitNextNodes": ["request-1"],
    "onCancelNextNodes": ["snackbar-2"]
  }
}
```

**2. Request (Flow Start)**
```json
{
  "id": "request-1",
  "type": "request",
  "data": {
    "nextNodes": ["query-1"]
  }
}
```

**3. Query Source Asset**
```json
{
  "id": "query-1",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetAsset($id: UUID!) { asset(where: { id: { _eq: $id } }) { edges { node { id name description siteId areaId serialNumber } } } }",
    "variablesTransform": "{ \"id\": $.assetId }",
    "nextNodes": ["js-1"]
  }
}
```

**4. Generate Form Data**
```json
{
  "id": "js-1",
  "type": "javascriptTransform",
  "data": {
    "transform": "'use strict';\n\nvar asset = $.asset.edges[0].node;\n\nreturn { name: asset.name + ' (Copy)', description: asset.description, siteId: asset.siteId, areaId: asset.areaId, serialNumber: '', sourceId: asset.id };",
    "nextNodes": ["form-2"]
  }
}
```

**5. Confirmation Form**
```json
{
  "id": "form-2",
  "type": "formDialog",
  "data": {
    "titleTransform": "\"Confirm Duplicate\"",
    "formInputs": [
      { "id": "name", "label": "Name", "type": "text", "required": true },
      { "id": "description", "label": "Description", "type": "textarea" },
      { "id": "serialNumber", "label": "Serial Number", "type": "text" }
    ],
    "dataTransform": "$.formData ~> |$|{'siteId': $.siteId, 'areaId': $.areaId, 'sourceId': $.sourceId}|",
    "onSubmitNextNodes": ["setcontext-1"],
    "onCancelNextNodes": ["snackbar-2"]
  }
}
```

**6. Store Form Data**
```json
{
  "id": "setcontext-1",
  "type": "setContext",
  "data": {
    "transform": "$",
    "nextNodes": ["mutate-1"]
  }
}
```

**7. Create Duplicate**
```json
{
  "id": "mutate-1",
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation CreateAsset($payload: [AssetCreatePayloadInput!]!) { createAsset(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": [{ \"create\": { \"name\": $.name, \"description\": $.description, \"siteId\": $.siteId, \"areaId\": $.areaId, \"serialNumber\": $.serialNumber } }] }",
    "nextNodes": ["query-2"]
  }
}
```

**8. Query Relations**
```json
{
  "id": "query-2",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetRelations($sourceId: UUID!) { dataPoint(where: { assetId: { _eq: $sourceId } }) { edges { node { name dataType frequency assetId } } } }",
    "variablesTransform": "{ \"sourceId\": $state.context.sourceId }",
    "nextNodes": ["ifelse-1"]
  }
}
```

**9. Check if Relations Exist**
```json
{
  "id": "ifelse-1",
  "type": "ifElse",
  "data": {
    "transform": "$count($.dataPoint.edges) > 0",
    "onTrueNextNodes": ["mutate-2"],
    "onFalseNextNodes": ["search-1"]
  }
}
```

**10. Copy Relations**
```json
{
  "id": "mutate-2",
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation CreateDataPoints($payload: [DataPointCreatePayloadInput!]!) { createDataPoint(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": $.dataPoint.edges.node.( { \"create\": { \"name\": name, \"dataType\": dataType, \"frequency\": frequency, \"assetId\": $state.context.createAsset.id } } ) }",
    "nextNodes": ["search-1"]
  }
}
```

**11. Refresh Table**
```json
{
  "id": "search-1",
  "type": "searchTable",
  "data": {
    "screenElementTransform": "\"AssetTable\"",
    "nextNodes": ["snackbar-1"]
  }
}
```

**12. Success Notification**
```json
{
  "id": "snackbar-1",
  "type": "snackbar",
  "data": {
    "severityTransform": "\"success\"",
    "titleTransform": "\"Asset Duplicated\"",
    "messageTransform": "\"Successfully created \" & $state.context.name",
    "nextNodes": ["response-1"]
  }
}
```

**13. Cancel Notification**
```json
{
  "id": "snackbar-2",
  "type": "snackbar",
  "data": {
    "severityTransform": "\"warning\"",
    "titleTransform": "\"Action Canceled\"",
    "messageTransform": "\"No changes were made\"",
    "nextNodes": ["response-1"]
  }
}
```

**14. Response**
```json
{
  "id": "response-1",
  "type": "response",
  "data": {
    "responseTransform": "$",
    "nextNodes": []
  }
}
```

---

## Pattern 5: Master-Child Flow for Large Datasets

**Use Case:** Process 10,000+ records efficiently within timeout constraints

**External flows (10 min):** Use master-child for datasets >5000 records
**Internal flows (20 min):** Use master-child for datasets >50,000 records

**Master Flow:**
```
Schedule → Query (get all IDs) → Batch IDs → Broadcast → ExecuteFlow (child) → Response
```

**Child Flow:**
```
Request → Query (batch of records) → Transform → Mutate → Response
```

### Master Flow Nodes:

**1. Schedule**
```json
{
  "id": "schedule-1",
  "type": "schedule",
  "data": {
    "nextNodes": ["query-1"]
  }
}
```

**2. Query IDs Only**
```json
{
  "id": "query-1",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetAllIds { productionLog(where: { processedAt: { _isNull: true } }, first: 10000) { edges { node { id } } } }",
    "variablesTransform": "{}",
    "nextNodes": ["js-1"]
  }
}
```

**3. Batch into Groups**
```json
{
  "id": "js-1",
  "type": "javascriptTransform",
  "data": {
    "transform": "'use strict';\n\nvar ids = $.productionLog.edges.node.map(function(n) { return n.id; });\nvar batchSize = 100;\nvar batches = [];\n\nfor (var i = 0; i < ids.length; i += batchSize) {\n  batches.push({ ids: ids.slice(i, i + batchSize) });\n}\n\nreturn batches;",
    "nextNodes": ["broadcast-1"]
  }
}
```

**4. Broadcast**
```json
{
  "id": "broadcast-1",
  "type": "broadcast",
  "data": {
    "nextNodes": ["executeflow-1"]
  }
}
```

**5. Execute Child Flow**
```json
{
  "id": "executeflow-1",
  "type": "executeFlow",
  "data": {
    "dataFlowTransform": "\"processProductionLogBatch\"",
    "requestTransform": "$",
    "responseTransform": "$",
    "nextNodes": ["response-1"]
  }
}
```

**6. Response**
```json
{
  "id": "response-1",
  "type": "response",
  "data": {
    "responseTransform": "$",
    "nextNodes": []
  }
}
```

### Child Flow Nodes:

**1. Request**
```json
{
  "id": "request-1",
  "type": "request",
  "data": {
    "nextNodes": ["query-1"]
  }
}
```

**2. Query Batch Records**
```json
{
  "id": "query-1",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetBatch($ids: [UUID!]!) { productionLog(where: { id: { _in: $ids } }) { edges { node { id workcenterId productId quantityGood quantityScrap occurAt } } } }",
    "variablesTransform": "{ \"ids\": $.ids }",
    "nextNodes": ["transform-1"]
  }
}
```

**3. Process Batch**
```json
{
  "id": "transform-1",
  "type": "transform",
  "data": {
    "transform": "{ \"processed\": $.productionLog.edges.node.{ \"id\": id, \"total\": quantityGood + quantityScrap } }",
    "nextNodes": ["mutate-1"]
  }
}
```

**4. Mark Processed**
```json
{
  "id": "mutate-1",
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation MarkProcessed($payload: [ProductionLogUpdatePayloadInput!]!) { updateProductionLog(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": $.processed.( { \"where\": { \"id\": id }, \"update\": { \"processedAt\": $moment() } } ) }",
    "nextNodes": ["response-1"]
  }
}
```

**5. Response**
```json
{
  "id": "response-1",
  "type": "response",
  "data": {
    "responseTransform": "$",
    "nextNodes": []
  }
}
```

---

## Pattern 6: Multi-Model Mutation with Type Routing

**Use Case:** Store telemetry in one of multiple type-specific models (Float, Boolean, String) based on a dataType field, using a single mutation node.

**Pattern:** Define separate GraphQL variables per model. The JSONata variablesTransform conditionally builds the payload for the matching type and passes `[]` (empty array) to the others. GraphQL accepts empty payload arrays without error.

```graphql
mutation CreateTelemetryRaw(
  $floatPayload: [TelemetryRawCreatePayloadInput!]!
  $boolPayload: [TelemetryRawBoolCreatePayloadInput!]!
  $stringPayload: [TelemetryRawStringCreatePayloadInput!]!
) {
  createTelemetryRaw(payload: $floatPayload) { id }
  createTelemetryRawBool(payload: $boolPayload) { id }
  createTelemetryRawString(payload: $stringPayload) { id }
}
```

```jsonata
(
  $dataType := $.dataPoint.edges[0].node.dataType;
  {
    "floatPayload": $dataType = "Float" ? [{ "create": { ... }}] : [],
    "boolPayload": $dataType = "Boolean" ? [{ "create": { ... }}] : [],
    "stringPayload": $dataType = "String" ? [{ "create": { ... }}] : []
  }
)
```

**Key Insight:** `[]` (empty array) is valid for required payload inputs — GraphQL processes it as a no-op for that mutation.

---

## Pattern 7: UNS Publish with Hierarchy Topic

**Use Case:** Publish contextualized telemetry back to the UNS with a topic path built from the asset hierarchy.

```jsonata
(
  $dp := $.dataPoint.edges[0].node;
  $asset := $dp.asset;
  $cell := $asset.cell;
  $line := $cell.line;
  $area := $line.area;
  $site := $area.site;

  [
    {
      "topic": $join([
        "fuuz",
        $site.code,
        $area.code,
        $line.code,
        $cell.code,
        $asset.code,
        $dp.code
      ], "/"),
      "payload": $string({
        "elementId": $dp.id,
        "displayName": $dp.code,
        "typeId": $dp.dataType,
        "parentId": $asset.id,
        "hasChildren": false,
        "value": {
          "value": $msg.value,
          "quality": $msg.quality,
          "recordedAt": $msg.recordedAt
        },
        "timestamp": $moment(),
        "quality": "Good"
      }),
      "encoding": "UTF8"
    }
  ]
)
```

**Key Insight:** `$string()` wraps the payload object when the publish node expects a string. Use `$join()` with `"/"` separator to build ISA-95 topic paths.

---

## Pattern 8: Telemetry Ingest Flow (DataBroker → Validate → Store → Publish)

**Use Case:** Receive raw telemetry from a UNS, validate against dataPoint limits, store by type, and republish contextualized data.

**Flow:**
```
DataChanges → [JS: Validate Value & Check Limits] → [When: Skip if not historized or no dataPoint] → [Query: Get DataPoint with Hierarchy] → [Mutate: Store by DataType] → [Transform: Build UNS Publish] → [Publish]
```

**JavaScript Validate node key logic:**

- Guard for empty edges (no matching dataPoint)
- Check `historize` flag — skip if false
- Parse value to number with `parseFloat()` for limit comparison
- Compare against `upperLimit` / `lowerLimit`
- Set quality to `"Bad"` / qualityCode to `"0"` if out of tolerance
- Return `{ skip: true }` for guard conditions

**Mutate node key logic:**

- Single mutation with 3 model variables (Float, Bool, String) — see Pattern 6
- Route by `dataType` using ternary, `[]` for non-matching
- `$number()` for Float, boolean coercion for Bool, `$string()` for String
- Fallbacks on all required fields

---

## Key Patterns Summary

1. **Early Filtering:** Use when/unless nodes immediately after triggers
2. **Context Storage:** Store config/reference data early, access via $state.context
3. **Mutex Protection:** Wrap concurrent writes with lock/unlock
4. **Broadcast for Parallel:** Better than loop for independent array processing
5. **Master-Child for Scale:** Break large datasets into batches with sub-flows
6. **Web Flow Structure:** Request → Process → Push to Screen → Response
7. **Multi-Stage Queries:** Query config → Merge → Query data → Process
8. **Batch Mutations:** Use array mapping in variablesTransform
9. **Conditional Branching:** ifElse for two paths, switch for multiple
10. **UI Feedback:** Always end web flows with snackbar + searchTable + response
11. **Type Routing:** Use `[]` empty arrays for non-matching model types in multi-model mutations
12. **UNS Publish:** Build ISA-95 topic paths with `$join()` and serialize payloads with `$string()`
13. **Telemetry Ingest:** DataChanges → Validate → Query hierarchy → Store by type → Republish
