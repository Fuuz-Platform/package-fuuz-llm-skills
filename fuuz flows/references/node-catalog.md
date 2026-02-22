# Flow Node Catalog

Complete reference of all Fuuz flow node types with configuration examples.

## Execution Context & Timeouts

Flows execute in different contexts with different timeout limits:

**External Context (10 minute timeout):**
- Web flows invoked from frontend
- Request/Response flows from external APIs (via api.__.fuuz.app)

**Internal Context (20 minute effective timeout):**
- Schedule-triggered flows
- Data change subscriptions (dataChanges node)
- Webhooks
- Topic subscriptions

Note: Internal flows have 30-minute node execution timeout, but service request timeout is 20 minutes.

## Node Structure

Every node has this basic structure:

```json
{
  "id": "unique-uuid",
  "name": "Human-readable name",
  "type": "nodeType",
  "data": {
    "nextNodes": ["uuid-of-next-node"],
    ...type-specific properties...
  },
  "description": "Optional detailed description of node purpose and logic",
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

**Required fields:**
- `id` - Unique UUID for the node
- `name` - Human-readable, descriptive name (e.g., "Get Active Workcenters", "Calculate Hourly Boundaries")
- `type` - Node type (see categories below)
- `data` - Node configuration including nextNodes array

**Optional fields:**
- `description` - Detailed explanation of node purpose, logic, or business rules

**Naming Best Practice:** Use descriptive names that indicate purpose, not generic names like "Transform" or "Query". Good examples: "Query Active Work Orders", "Filter Unprocessed Logs", "Lock OEE Hourly Record".

Nodes connect via `data.nextNodes` array of UUIDs. Some nodes have conditional exits.

---

## Trigger Nodes (Flow Entry Points)

### schedule
Starts flow on cron schedule. Schedule configured externally via DataFlowSchedule records.

```json
{
  "type": "schedule",
  "data": {
    "configure": "Open Schedule Editor",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Timeout: Internal (20 minutes effective)**

**Use for:** Hourly aggregations, daily reports, periodic cleanup, batch processing

### dataChanges
Triggers on Create/Update/Delete mutations of a data model. Functions like topic/webhook triggers - same queue-based mechanism.

```json
{
  "type": "dataChanges",
  "data": {
    "api": "Application",
    "type": "ProductionLog",
    "operations": ["Create"],
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Payload injected:**
```json
{
  "value": {
    "before": { ...previous state or null... },
    "after": { ...new state... }
  }
}
```

**Timeout: Internal (20 minutes effective)**

**Use for:** Reactive updates, cascading calculations, data validation, audit trails

### request
Entry point for sub-flows and web flows invoked via API.

```json
{
  "type": "request",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Timeout: External (10 minutes)** - applies to web flows and external API calls (via api.__.fuuz.app)

**Use for:** Web flows, remote function calls, sub-flow invocations

### response
Exit point for sub-flows and web flows. Returns data to caller.

```json
{
  "type": "response",
  "data": {
    "responseTransform": "$",
    "nextNodes": []
  }
}
```

**responseTransform:** JSONata expression to shape response payload

### webhook
External HTTP trigger.

```json
{
  "type": "webhook",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Timeout: Internal (20 minutes effective)**

---

## Data Nodes

### query
Executes GraphQL query against Application or System API.

```json
{
  "type": "query",
  "data": {
    "api": "application",
    "query": "query GetData($where: ModelWhereInput) { model(where: $where) { edges { node { id field } } } }",
    "variablesTransform": "{ \"where\": { \"id\": { \"_eq\": $.id } } }",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**API Selection:**
- `"api": "application"` - For app data models (production, OEE, telemetry, assets)
- `"api": "system"` - For platform models (tenants, enterprises, users, roles)

**variablesTransform patterns:**
- `{}` - No variables
- `$` - Pass entire payload
- JSONata expression - Transform to variables object

### mutate
Executes GraphQL mutation (create/update/upsert/delete) against Application or System API.

```json
{
  "type": "mutate",
  "data": {
    "api": "Application",
    "mutation": "mutation CreateModel($payload: [ModelCreatePayloadInput!]!) { createModel(payload: $payload) { id } }",
    "variablesTransform": "{ \"payload\": [{ \"create\": { \"field\": $.value } }] }",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**API Selection:**
- `"api": "Application"` - For app data models (note: capital A in mutations)
- `"api": "System"` - For platform models (note: capital S in mutations)

**Common mutations:** createModel, updateModel, upsertModel, deleteModel

### queryFile
Reads file content (for file-based integrations).

```json
{
  "type": "queryFile",
  "data": {
    "fileIdTransform": "$.fileId",
    "encoding": "utf8",
    "nextNodes": ["next-node-uuid"]
  }
}
```

---

## Transform Nodes

### transform
Evaluates JSONata expression.

```json
{
  "type": "transform",
  "data": {
    "transform": "( $timezone := $.setting.edges[0].node.defaultValue; { \"timezone\": $timezone } )",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Use for:** Data restructuring, calculations, extraction, formatting

### javascriptTransform
Executes JavaScript (ES6 strict mode).

```json
{
  "type": "javascriptTransform",
  "data": {
    "transform": "'use strict';\n\nvar config = { frequency: 300 };\nvar result = $.data * config.frequency;\nreturn { value: result };",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Available in JavaScript transforms:**
- `$` - Current payload
- `$state.context` - Flow state
- `$moment()` - moment-timezone (also available as `moment`)
- `$numeral()` - Number formatting and parsing
- `$schedule()`, `$scheduleGroup()` - Schedule functions
- **All Fuuz JSONata custom bindings** - Since Fuuz runs on Node.js
- `console.log()` - Logging

**Must return value**

**Use for:** Complex business logic, loops, algorithms, state management

### savedTransformV2
References reusable named transform.

```json
{
  "type": "savedTransformV2",
  "data": {
    "transformScriptLanguage": "JavaScript",
    "transformId": { "id": "oeeScript", "label": "OEE Calculation" },
    "requestTransform": "$",
    "responseTransform": "$",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Use for:** Shared logic, complex calculations used across flows

---

## State Management Nodes

### setContext
Stores data in flow state context.

```json
{
  "type": "setContext",
  "data": {
    "transform": "{ \"oeeConfig\": $, \"timestamp\": $moment() }",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Access later:** `$state.context.oeeConfig`

**Use for:** Configuration storage, reference data, intermediate calculations

### mergeContext
Merges into existing context without overwriting.

```json
{
  "type": "mergeContext",
  "data": {
    "transform": "{ \"productInfo\": $.product.edges[0].node }",
    "nextNodes": ["next-node-uuid"]
  }
}
```

---

## Control Flow Nodes

### when (Accept)
Conditional gate - passes through if condition is TRUE.

```json
{
  "type": "when",
  "data": {
    "transform": "$.value.after.quality = \"Bad\"",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Use for:** Acceptance filtering, routing good data

### unless (Reject)
Conditional gate - passes through if condition is FALSE.

```json
{
  "type": "unless",
  "data": {
    "transform": "$.value.after.processedAt != null",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Use for:** Rejection filtering, guard clauses, skip processed records

### ifElse
Branches into true/false paths.

```json
{
  "type": "ifElse",
  "data": {
    "transform": "$state.context.copyData = true",
    "onTrueNextNodes": ["true-path-uuid"],
    "onFalseNextNodes": ["false-path-uuid"]
  }
}
```

### switch (Route)
Multi-way branch.

```json
{
  "type": "switch",
  "data": {
    "branches": [
      {
        "name": "Has Data",
        "nextNodes": ["has-data-uuid"],
        "transform": "$isNotNilOrEmpty($)"
      },
      {
        "name": "No Data",
        "nextNodes": ["no-data-uuid"],
        "transform": "$isNilOrEmpty($)"
      }
    ]
  }
}
```

### filterArray
Filters array using JSONata.

```json
{
  "type": "filterArray",
  "data": {
    "inputTransform": "$.productionRecords",
    "filterTransform": "status = \"Complete\"",
    "outputTransform": "$",
    "enableAdvancedConfiguration": true,
    "nextNodes": ["next-node-uuid"]
  }
}
```

### broadcast
Fan-out: sends payload to next node for each array item.

```json
{
  "type": "broadcast",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Pattern:** Query (returns array) → Broadcast → Process each item → Response

---

## Concurrency Nodes

### mutexLock
Acquires lock for critical section.

```json
{
  "type": "mutexLock",
  "data": {
    "lockTTLms": 30000,
    "lockRetries": -1,
    "throwErrorWhenLockNotAcquired": false,
    "resourceIdTransform": "$.oeeHourlyId",
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Use resourceIdTransform** to lock on specific record ID

### mutexUnlock
Releases lock.

```json
{
  "type": "mutexUnlock",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Pattern:** MutexLock → Mutate → MutexUnlock

---

## UI Interaction Nodes (Web Flows Only)

### formDialog
Shows modal form to user.

```json
{
  "type": "formDialog",
  "data": {
    "titleTransform": "\"Duplicate Asset\"",
    "formInputs": [...field definitions...],
    "dataTransform": "$.formData",
    "onSubmitNextNodes": ["submit-uuid"],
    "onCancelNextNodes": ["cancel-uuid"],
    "submitIconTransform": "\"floppy-disk\""
  }
}
```

### snackbar
Shows toast notification.

```json
{
  "type": "snackbar",
  "data": {
    "severityTransform": "\"success\"",
    "titleTransform": "\"Operation Complete\"",
    "messageTransform": "\"Record saved successfully\"",
    "nextNodes": []
  }
}
```

**Severity:** "success", "warning", "error", "info"

### searchTable
Refreshes table on screen.

```json
{
  "type": "searchTable",
  "data": {
    "screenElementTransform": "\"AssetTable\"",
    "nextNodes": ["next-node-uuid"]
  }
}
```

### alertDialog
Shows alert dialog.

```json
{
  "type": "alertDialog",
  "data": {
    "titleTransform": "\"Attention\"",
    "messageTransform": "\"Operation completed\"",
    "nextNodes": ["next-node-uuid"]
  }
}
```

### confirmDialog
Shows confirmation dialog.

```json
{
  "type": "confirmDialog",
  "data": {
    "titleTransform": "\"Confirm Delete\"",
    "messageTransform": "\"Are you sure?\"",
    "onConfirmNextNodes": ["confirm-uuid"],
    "onCancelNextNodes": ["cancel-uuid"]
  }
}
```

### navigateScreen
Navigates to different screen.

```json
{
  "type": "navigateScreen",
  "data": {
    "screenTransform": "\"DashboardScreen\"",
    "nextNodes": []
  }
}
```

---

## Gateway Nodes (Gateway Flows Only)

### devicePrintFile
Prints file to edge device.

```json
{
  "type": "devicePrintFile",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

### devicePrintDocument
Prints structured document.

```json
{
  "type": "devicePrintDocument",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

### devicePrintRaw
Sends raw print commands.

```json
{
  "type": "devicePrintRaw",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

### deviceSubscription
Subscribes to edge device messages.

```json
{
  "type": "deviceSubscription",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

### executeDeviceFunction V2
Executes function on edge device.

```json
{
  "type": "executeDeviceFunction V2",
  "data": {
    "requestTransform": "$",
    "nextNodes": ["next-node-uuid"]
  }
}
```

---

## Integration Nodes

### integrateV2
Generic external integration.

```json
{
  "type": "integrateV2",
  "data": {
    "requestTransform": "$",
    "responseTransform": "$",
    "returnErrors": false,
    "degreeOfParallelism": 10,
    "nextNodes": ["next-node-uuid"]
  }
}
```

### awsLambda
Invokes AWS Lambda function.

```json
{
  "type": "awsLambda",
  "data": {
    "regionTransform": "\"us-east-1\"",
    "bodyTransform": "$",
    "responseTransform": "$",
    "returnErrors": false,
    "degreeOfParallelism": 10,
    "nextNodes": ["next-node-uuid"]
  }
}
```

### openaiChat
OpenAI chat completion.

```json
{
  "type": "openaiChat",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Other integration nodes available:** ADP, ADP Vista, Amazon, Google Drive API, Success Factors, UPS, TecCom

---

## Utility Nodes

### log
Logs message for debugging.

```json
{
  "type": "log",
  "data": {
    "messageTransform": "\"Processing: \" & $.id",
    "level": "Debug",
    "includeContext": false,
    "includePayload": true,
    "nextNodes": ["next-node-uuid"]
  }
}
```

**Levels:** "Debug", "Info", "Warning", "Error"

### echo
Passes payload through unchanged (useful for breakpoints).

```json
{
  "type": "echo",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

### validate
Validates payload against schema.

```json
{
  "type": "validate",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

### executeFlow
Invokes another flow (sub-flow pattern).

```json
{
  "type": "executeFlow",
  "data": {
    "dataFlowTransform": "\"childFlowId\"",
    "requestTransform": "$",
    "responseTransform": "$",
    "nextNodes": ["next-node-uuid"]
  }
}
```

### loop
Iterates over array.

```json
{
  "type": "loop",
  "data": {
    "itemsTransform": "$.array",
    "bodyNextNodes": ["loop-body-uuid"],
    "afterNextNodes": ["after-loop-uuid"]
  }
}
```

---

## Debug Nodes (Testing Only)

### debugSource
Manual flow trigger with sample payload.

```json
{
  "type": "debugSource",
  "data": {
    "payload": { "testData": "value" },
    "context": {},
    "nextNodes": ["next-node-uuid"]
  }
}
```

### debugFile
File source for testing.

```json
{
  "type": "debugFile",
  "data": {
    "encode": "base64",
    "nextNodes": ["next-node-uuid"]
  }
}
```

### debugDataChange
Simulates data change event.

```json
{
  "type": "debugDataChange",
  "data": {
    "api": "Application",
    "beforeData": null,
    "afterData": { "id": "test", "field": "value" },
    "nextNodes": ["next-node-uuid"]
  }
}
```

### previewFile
Opens file for preview.

```json
{
  "type": "previewFile",
  "data": {
    "contentTransform": "$",
    "encoding": "base64",
    "autoOpen": false
  }
}
```

---

## Notification Nodes

### sendNotification
Sends platform notification.

```json
{
  "type": "sendNotification",
  "data": {
    "transform": "{ \"message\": \"Task complete\", \"userId\": $.assignedTo }",
    "nextNodes": ["next-node-uuid"]
  }
}
```

### receiveNotification
Receives platform notification (trigger).

```json
{
  "type": "receiveNotification",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

---

## Email Nodes

### emailRead
Reads emails from inbox.

```json
{
  "type": "emailRead",
  "data": {
    "nextNodes": ["next-node-uuid"]
  }
}
```

### emailSend
Sends email.

```json
{
  "type": "emailSend",
  "data": {
    "toTransform": "$.recipientEmail",
    "subjectTransform": "\"Alert: \" & $.subject",
    "bodyTransform": "$.emailBody",
    "nextNodes": ["next-node-uuid"]
  }
}
```

---

## Best Practices

1. **Name nodes descriptively** - Use clear, purposeful names like "Query Pending Orders", not generic "Query" or "Transform"
2. **Always generate fresh UUIDs** for node IDs and nextNodes
3. **Provide flow descriptions** - Explain what the flow does, when it runs, and key models involved
4. **Enable failed logging** by default - `executionFailed: true`
5. **Use when/unless early** - filter before expensive operations
6. **Store config in context** - setContext after queries, before transforms
7. **Wrap mutations with mutex** - when concurrent access possible
8. **Use broadcast for arrays** - better performance than loop for parallel processing
9. **Transform payloads incrementally** - easier to debug than complex single transform
10. **Log intermediate values** - use log nodes during development
11. **End web flows with response** - always provide response node
12. **For description generation** - export flow JSON and let Claude analyze structure to create contextual descriptions
