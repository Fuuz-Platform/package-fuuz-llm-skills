# Flow Node Catalog

Complete reference of all Fuuz data flow node types, their configuration properties, and validation rules.

---

## Data Flow Concepts

A **Flow** defines an execution pipeline: `{ id, name, version, type, nodes[], metadata }`. Flow types: System, Document, Integration, Screen, Edge.

A **Node** is an instance in a flow: `{ id, name, type, note, data, debug, logging }`. The `type` references a NodeType name. The `data` object holds configuration conforming to the NodeType's `dataSchema`.

A **NodeType** defines a node's capabilities:
- `name` (string, required) — unique identifier
- `title` (string, required) — display name
- `category` (string) — grouping (e.g., "Flow Control", "Integration")
- `description` (string, required) — what the node does
- `responsibility` (string, required) — `source` (starts a flow) or `transition` (processes data)
- `parallel` (boolean) — whether the node can execute in parallel
- `dataSchema` (object, required) — JSON Schema defining the node's configurable properties
- `validation` (object) — flow validation rules

## State Model

The workflow state passed between nodes contains:
- **payload** — current data being processed
- **context** — persistent key-value store (set via setContext, `mergeContext`; read via `$state.context`)
- **claims** — authentication claims from the initiating user/system
- **batches** — tracking info for broadcast/fork parallel execution

## Common Property Patterns

**Standard Output Port** — Most transition nodes have a `nextNodes` property:
```json
{ "title": "Output", "type": "array", "format": "port" }
```
When a node has only this standard port, it is noted as "Standard output port" rather than listed in the properties table.

**JSONata Transform** — `format: "jsonata"` indicates a JSONata expression. In JSONata, `$` refers to the current scope — at the root level this is the input payload, but inside a nested expression (e.g., `$.test.( $.value )`) it shifts to the nested object. Use `$$` to escape back to the root input payload from within a nested scope. The `$state` binding provides access to the full workflow state (e.g., `$state.context`, `$state.claims`).

**Connection** — Integration nodes typically have a `connectionName` or `connectionNameTransform` property referencing a named integration connection (HTTP, ODBC, FTP, etc.).

**Return Errors** — `returnErrors` (boolean): when true, failed requests return `{ error: { statusCode, message, info } }` as payload instead of aborting the flow.

**Degree of Parallelism** — `degreeOfParallelism` (integer, default: 10): when payload is an array, how many parallel requests to execute simultaneously.

**Combination Strategies** — Used by Combine and Collect nodes:
- `index` — results ordered by branch index (default for payload)
- `first` (labeled "Last") — uses the last received result (default for context)
- `merge` — deep merges all results

**Performance Note: Broadcast/Combine** — The Broadcast + Combine pattern is acceptable for small datasets but performs poorly over large datasets. When processing large arrays, prefer using nodes with built-in `degreeOfParallelism` support or handle iteration within a JSONata/JavaScript transform instead.

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

## Categories

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

---

## Conditionals

Nodes for branching and filtering flow execution based on conditions.

---

### If Else (`ifElse`)
**Type:** transition

A node used to conditionally select one of two output ports based on the result of a single transform.

When the provided transform returns true, nodes linked to the True Port are executed; when the transform returns false, nodes linked to the False Port are executed.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the workflow state. This transform should return a boolean value. |
| `onTrueNextNodes` | array | port | Y | The nodes to route to if the transform evaluates to true. |
| `onFalseNextNodes` | array | port | Y | The nodes to route to if the transform evaluates to false. |

**Validation:** requireInputNode

---

### Route (`switch`)
**Type:** transition

A node used to conditionally route to one of many ports based on a list of transformations.

The branch transforms are executed in order, from top to bottom. For the first transform that returns true, the engine will route the payload to the nodes attached to that branch's port, and branch evaluation will stop.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `branches` | array | | Y | A list of conditional branches to test. At least one branch must be matched. Each branch has: `name` (string, required) — The name of this branch; `transform` (string jsonata, required) — A JSONata transform that should return a boolean value; `nextNodes` (array port, required) — The nodes to route to if the Transform evaluates to true. |

**Validation:** requireInputNode

---

### Accept (`when`)
**Type:** transition

Note: This node was previously called "Filter". A node used to conditionally accept messages from the flow. If the Transform returns true, the message will be passed to the next nodes; if the Transform returns false, the message will be dropped.

This node is the logical opposite of the Reject node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the workflow state. This transform should return a boolean value. True indicates this branch should proceed; false indicates the branch should terminate. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireOutputNode

---

### Reject (`unless`)
**Type:** transition

A node used to conditionally reject messages from the flow.

If the Transform returns true, the message will be dropped; if the Transform returns false, the message will be passed to the next nodes.

This node is the logical opposite of the Accept node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the workflow state. This transform should return a boolean value. True indicates this branch should terminate; false indicates the branch should proceed. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireOutputNode

---

## Context

Nodes for reading, writing, and removing data from the flow execution context.

---

### Merge Context (`mergeContext`)
**Type:** transition

A node used to merge data into the flow execution context. The input is deep-merged into the existing state, concatenating arrays if possible.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the data flow payload. The output is stored in the state context, which can be accessed with $state.context. |

Standard output port.

---

### Remove From Context (`removeFromContext`)
**Type:** transition

A node that takes in an array of key paths which will be removed from the state context.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the data flow state context. This should return an array of key paths that will be removed from the state context if a match is found. If the result is not an array, no changes to the context will be made. The state context can be accessed with $state.context. Default: `"[]"` |

Standard output port.

---

### Set Context (`setContext`)
**Type:** transition

A node used to set the flow execution context.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the data flow payload. The output is stored in the state context, which can be accessed with $state.context. |

Standard output port.

---

## Debugging

Nodes for logging and inspecting data during flow execution.

---

### Log (`log`)
**Type:** transition

A node used to create persistent logs at runtime.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `messageTransform` | string | jsonata | Y | A JSONata transform to run on the workflow state to transform it into a loggable string. Default: `""` |
| `level` | string | | Y | The logging level to send this message at. If you aren't seeing logs from this node, verify the Data Flow log level is set to the same Log Level or lower. Options: `Debug`, `Info`, `Warn`, `Error` |
| `includeContext` | boolean | | Y | When enabled, the logs will contain the current state context. Default: `false` |
| `includePayload` | boolean | | Y | When enabled, the logs will contain the payload passed into this node. Default: `false` |

Standard output port.

---

### Echo (`echo`)
**Type:** transition

A node which routes the unmodified payload to the next nodes.

Standard output port.

---

## Device Gateway

Nodes for interacting with devices through the MFGx Device Gateway.

---

### Execute Device Function (`executeDeviceFunction V2`)
**Type:** transition

A node used to execute a function on a device through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The device to execute the function on. |
| `functionIdTransform` | string | | Y | Which function to execute on the device. |
| `requestTransform` | object | | N | A series of transforms to produce the payload of the request to send to the device function. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

---

### Print File (`devicePrintFile`)
**Type:** transition

A node used to print an MFGx file through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The printer to send the document to. |
| `fileIdTransform` | string | | Y | The file to print. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

**Validation:** requireInputNode

---

### Print Data (`devicePrintDocument`)
**Type:** transition

A node used to print a document through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The printer to send the document to. |
| `dataTransform` | string | | Y | A transform producing the base-64 encoded document to send to the printer. |
| `filenameTransform` | string | | N | The name of the file to print. The extension on the filename determines how the gateway handles the document. If no filename is provided, a randomly generated ID is used, and the document is assumed to be a PDF. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

**Validation:** requireInputNode

---

### Print Raw (`devicePrintRaw`)
**Type:** transition

A node used to send raw commands (i.e. ZPL) to a printer through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The printer to send the raw commands to. |
| `dataTransform` | string | | Y | A transform producing the raw commands to send to the printer. |
| `filenameTransform` | string | | N | The name of the file to print. If no filename is provided, a randomly generated ID is used. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

**Validation:** requireInputNode

---

### Device Subscription (`deviceSubscription`)
**Type:** source

A node used to subscribe to device subscriptions. Any dropdowns left blank will act as a wildcard, allowing you to subscribe to multiple subscriptions with one node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceDriverType` | string | | N | The device driver type. |
| `driver` | string | | N | The device driver. |
| `device` | string | | N | The device. |
| `deviceGateway` | string | | N | The device gateway. |
| `deviceSubscriptionType` | string | | N | The device subscription type. |
| `deviceSubscription` | string | | N | The device subscription. |

Standard output port.

**Validation:** requireChangedName, requireOutputNode

---

## Events

Nodes for subscribing to and publishing messages through the event system.

---

### Data Changes (`dataChanges`)
**Type:** source

A node used to subscribe to data change events. This node will not process changes that are made by the flow where it's defined in an upcoming release.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `api` | string | | N | Which Fuuz API to subscribe to changes in. When not provided, the node will subscribe to data changes from all APIs. Default: `"Application"`. Options: `Application`, `System`. |
| `type` | string | | N | The GraphQL type to subscribe to changes in (e.g. ItemRevision). When not provided, the node will subscribe to data changes from all types. |
| `operations` | array | | N | The operations to filter data change events to. Options: `Create`, `Update`, `Delete`. |

Standard output port.

**Timeout: Internal (20 minutes effective)**

**Payload injected:**
```json
{
  "value": {
    "before": { ...previous state or null... },
    "after": { ...new state... }
  }
}
```

**Validation:** requireChangedName, requireOutputNode

---

### Topic (`topic`)
**Type:** source

A node used to subscribe to messages from a specific topic.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `topicName` | string | | Y | The topic to subscribe to. |

Standard output port.

**Validation:** requireChangedName, requireOutputNode

---

### Publish (`publish`)
**Type:** transition

A node used to publish a message to a topic.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `topicId` | string | | Y | The topic to publish to. |
| `bulkPublishing` | boolean | | N | When Bulk Publishing is enabled, an array input will be published as individual messages. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode

---

### Request (`request`)
**Type:** source

A node used to initiate the flow in Fuuz from an external request. You must have a Response node to respond to the request with a payload.

_No configurable properties._

Standard output port.

**Timeout: External (10 minutes)** — applies to web flows and external API calls (via api.__.fuuz.app)

**Validation:** requireOutputNode

---

### Response (`response`)
**Type:** transition

A node used to send a response to a flow execution initiated through a Request node. When the flow wasn't initiated through a Request node, the response node does nothing. The node always outputs the payload it received.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `responseTransform` | string | jsonata | N | A transform on the input for the response to be sent back to the requestor. Default: `"$"`. |

Standard output port.

**Validation:** requireInputNode

---

### Webhook (`webhook`)
**Type:** source

A node used to subscribe to messages from a specific topic that is webhook accessible.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `topicName` | string | | Y | The topic that is webhook accessible to listen on requests from. |

Standard output port.

**Timeout: Internal (20 minutes effective)**

**Validation:** requireChangedName, requireOutputNode

---

## Flow Control

Nodes for controlling the execution path and timing of a flow — broadcasting, forking, combining, collecting, delaying, scheduling, error handling, and mutex locking.

---

### Broadcast (`broadcast`)
**Type:** transition

A node which outputs a stream of individual messages based on an input array or object. The Broadcast node can be used to implement "for-each" or "map" style logic, with subsequent nodes operating on each individual element of an array or object rather than on the array or object as a whole. If a non-object value is passed to the node, the value is wrapped into an array with one element.

The individual streamed messages can be recombined into a single value using a Combine node.

No configurable properties beyond the standard output port.

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Fork (`fork`)
**Type:** transition

A node which creates batches of messages based on configurable branch outputs. Each branch is executed in parallel with the full payload passed to the Fork node. This allows flows to run different flow paths on the same payload in parallel.

The forked branches can be recombined into a single object using a Combine node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `branches` | array | | Y | A list of branches to fork to. Each branch will be executed in parallel. See sub-table below. |

**Branch items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `name` | string | | N | The name of this branch. When forked branches are recombined, the value of this branch will be keyed by this name. |
| `nextNodes` | array | port | Y | The branch of nodes to route a copy of the input payload to. |

**Validation:** requireInputNode, requireOutputNode

---

### Combine (`combine`)
**Type:** transition

A node which combines batches of messages created through Fork or Broadcast nodes back into a single message.

If the number of batches does not match the number of batches created by a Broadcast or Fork node, this node will throw an error.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `executionTimeout` | integer | | Y | The number of seconds to wait for the next message in a batch created by a Fork or Broadcast node. If the timeout expires, this node will throw an error. Default: `10`. Min: 1, Max: 900. |
| `payloadCombinationStrategy` | string | | N | Determines how payloads should be combined. Options: `Index`, `Last`, `Merge`. Default: `"index"`. See overview for details. |
| `contextCombinationStrategy` | string | | N | Determines how contexts should be combined. Options: `Index`, `Last`, `Merge`. Default: `"first"`. See overview for details. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Collect (`collect`)
**Type:** transition

A node which collects messages by time or count, emitting an array of collected messages when the time or count is hit. You must provide both a batch count and a timeout to ensure the messages will be emitted. This node generally shouldn't follow a broadcast or fork node, to combine the results of those nodes you generally want to use a Combine node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `batchCount` | integer | | N | The number of messages to collect before emitting a batch. If no value is provided, it will default to 1000 messages. Min: 2, Max: 1000. |
| `batchTimeMs` | integer | | N | The number of milliseconds to collect messages for before emitting a batch. If no value is provided, it will default to 5 minutes. Min: 1000, Max: 300000. |
| `payloadCombinationStrategy` | string | | N | Determines how payloads should be combined. Options: `Index`, `Last`, `Merge`. Default: `"index"`. See overview for details. |
| `contextCombinationStrategy` | string | | N | Determines how contexts should be combined. Options: `Index`, `Last`, `Merge`. Default: `"first"`. See overview for details. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Delay (`delay`)
**Type:** transition

A node used to delay execution of the next nodes by a specified amount of time.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `delay` | integer | | Y | The number of milliseconds to delay execution of the next nodes in the flow. Minimum: 0. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireOutputNode

---

### Mutex Lock (`mutexLock`)
**Type:** transition

Prevents downstream nodes from executing until this lock is released, ensuring only one message can process a shared resource at a time. The lock is released when a Mutex Unlock node executes or when the TTL expires. Locks are scoped to the specific flow version. They won't block execution in other flows or other versions of this flow even if using the same Resource ID. If the lock can't be acquired after all retry attempts, the node can be configured to either discard the message silently or throw an error.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `resourceIdTransform` | string | jsonata | Y | A unique identifier for the resource being locked. Messages with the same Resource ID will wait for each other. Use different IDs for independent operations. |
| `lockTTLms` | integer | | Y | The time in milliseconds the lock is held before automatically releasing. This is a safety limit. Ideally, your flow should release the lock explicitly with a Mutex Unlock node before this expires. Default: `30000`. Min: 30000, Max: 300000. |
| `lockRetries` | integer | | Y | The maximum number of attempts to acquire a lock. Set to -1 for infinite retries (use with caution). Default: `3`. Min: -1. |
| `throwErrorWhenLockNotAcquired` | boolean | | Y | Throws an error instead of silently discarding the message when the lock can't be acquired. Enable this if you need to handle lock failures explicitly (e.g., with a Try/Catch node) or want failed attempts logged. Default: `false`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Mutex Unlock (`mutexUnlock`)
**Type:** transition

A node which unlocks the last lock placed by a Mutex Lock node.

No configurable properties beyond the standard output port.

Standard output port.

**Validation:** requireInputNode

---

### Schedule (`schedule`)
**Type:** source

A node used to receive the payload from defined frequencies of schedules attached to the dataflow.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `configure` | display | | N | Follow the link to configure a schedule for this flow. |

Standard output port.

**Timeout: Internal (20 minutes effective)**

**Validation:** requireChangedName, requireOutputNode

---

### Try Catch (`tryCatch`)
**Type:** transition

A node used to catch errors that may happen downstream.

When an error occurs on the route linked to the Try port, that branch is stopped and nodes linked to the Catch Port are then executed. When no nodes are linked to the Catch Port, the current Try Catch handling is cleared for downstream nodes.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `tryNextNodes` | array | port | Y | The nodes to attempt to run during normal operation. |
| `catchNextNodes` | array | port | Y | The nodes to route to if an error occurs on the Try route. |

**Validation:** requireInputNode, requireOutputNode

---

### Throw Error (`throwError`)
**Type:** transition

A node used to throw a ThrowBindingError which halts execution. The error will use the provided Message and the data will include an object with an 'info' property holding the value of the Error Info parameter.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `messageTransform` | string | jsonata | Y | The error message. |
| `errorInfoTransform` | string | jsonata | N | A Transform on the input to create an object with properties that will be included with the error. Default: `"{}"`. |

No standard output port (execution halts on throw).

**Validation:** requireInputNode

---

## Fuuz

Nodes for interacting with the Fuuz platform — querying, mutating, aggregating data, executing flows, sending email, generating documents, and more.

---

### Aggregate (`aggregate`)
**Type:** transition

A node used to execute an aggregation pipeline against Fuuz.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `collectionTransform` | string | jsonata | Y | A JSONata expression to generate the collection to aggregate from. |
| `pipelineTransform` | string | jsonata | Y | A JSONata expression to generate the aggregation pipeline. |
| `explain` | boolean | | N | Enable this setting to get the execution plan for the aggregation pipeline. This is often helpful when performance tuning a pipeline. Default: `false`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Execute Flow (`executeFlow`)
**Type:** transition

A node used to execute a deployed flow in Fuuz. Flows will be executed starting from a Request node, and must have a Response node in the flow.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `flowId` | integer | combobox | Y | The flow to execute. If the flow is not deployed, this node will throw an error. |
| `payloadTransform` | string | jsonata | N | The transform to use to format the input before it is given as the payload to the flow's Request node. |

Standard output port.

**Validation:** requireInputNode

---

### Query (`query`)
**Type:** transition

A node used to run a query on data from the Fuuz GraphQL APIs.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `api` | string | | Y | Which Fuuz API to run the query. Options: `application`, `system`. Default: `"application"`. |
| `query` | string | graphql | Y | The query to execute. |
| `variablesTransform` | string | jsonata | Y | A transform producing the variables to pass into the query. Default: `"{}"`. |

Standard output port.

**API Selection:**
- `"api": "application"` - For app data models (production, OEE, telemetry, assets)
- `"api": "system"` - For platform models (tenants, enterprises, users, roles)

**variablesTransform patterns:**
- `{}` - No variables
- `$` - Pass entire payload
- JSONata expression - Transform to variables object

**Validation:** requireInputNode, requireOutputNode

---

### Mutate (`mutate`)
**Type:** transition

A node used to mutate data in the Fuuz GraphQL APIs.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `api` | string | | Y | Which Fuuz API to issue the mutation against. Options: `Application`, `System`. Default: `"Application"`. |
| `mutation` | string | graphql | Y | The mutation to execute. |
| `variablesTransform` | string | jsonata | N | A transform producing the variables to pass into the mutation. Default: `"{}"`. |

Standard output port.

**Common mutations:** createModel, updateModel, upsertModel, deleteModel

**Validation:** requireInputNode, requireOutputNode

---

### System Email (`systemEmail`)
**Type:** transition

A node used to send email via Fuuz.

Email fields can be provided plain (to@server.com) or formatted ("To Name" <to@server.com>), or as an object with 'name' and 'address' properties. Lists of addresses can be provided as comma-separated strings, or as arrays of strings or objects.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `emailToTransform` | string | jsonata | Y | The email addresses of the recipients. |
| `emailCCTransform` | string | jsonata | N | The email addresses of recipients to CC on the email. |
| `emailBCCTransform` | string | jsonata | N | The email addresses of recipients to BCC on the email. |
| `emailReplyToTransform` | string | jsonata | N | The email address to use for replies to the email. |
| `emailSubjectTransform` | string | jsonata | Y | The subject of the email. |
| `emailHtmlTransform` | string | jsonata | Y | The HTML body of the email. |
| `emailAttachments` | array | | N | A list of attachments to include in the email. See sub-table below. |

**Attachment items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `attachmentFilenameTransform` | string | jsonata | Y | The name of the attached file. |
| `attachmentContentTransform` | string | jsonata | Y | A transform outputting the encoded content of the attachment. |
| `attachmentEncoding` | string | combobox | N | The encoding of the content. Options: `ascii` (ASCII), `utf8` (UTF-8), `base64` (Base64), `hex` (Hexadecimal), `binary` (Binary). |

Standard output port.

**Validation:** requireInputNode

---

### Render Document (`generateDocument`)
**Type:** transition

A node used to generate a document in Fuuz.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `documentDesignTransform` | string | | Y | The document design to generate. |
| `payloadTransform` | string | jsonata | Y | A transform which returns the payload to pass to the document. Default: `"$"`. |
| `filenameTransform` | string | jsonata | N | The file name to use for the generated document. If no file name is supplied, one is auto-generated from the design name and current date and time. |
| `documentRenderFormatTransform` | string | | Y | The format to render the document in. Default: `"\"Pdf\""`. |
| `cultureTransform` | string | jsonata | N | The culture to apply to the rendered document. |
| `returnContent` | boolean | | N | When true, this will include the content of the document in the output. Default: `true`. |
| `saveRenderedFile` | boolean | | N | When true, the document will be saved. Default: `true`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Get Calendar (`getCalendar`)
**Type:** transition

A node used to get a Fuuz Calendar by id or name. The id will override the name field if a calendar is found for that id. If a calendar is not found for the id provided, the name will be used to lookup the calendar. If no calendar is found for the id or name provided an error will be thrown.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `calendarInput` | object | | N | The calendar to return. Accepts an id and/or name, or an array of such objects. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Saved Query (`savedQuery`)
**Type:** transition

A node used to execute a saved query in Fuuz.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `savedQueryId` | string | combobox | Y | The saved query to execute. |
| `variablesTransform` | string | jsonata | Y | A transform on the input payload to become the variables to pass into the query. Default: `"{}"`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### EDI Translation (`ediIntegration`)
**Type:** transition

Node for integrating with EDI Nation API to read, write, and acknowledge EDI messages. The EDI Nation API will convert X12 and EDIFACT documents to a JSON format providing an easier way to map documents with JSONata and Javascript. The EDI Nation system connection may not be enabled in your environment by default.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `action` | string | jsonata | Y | The action to perform. Options: `read`, `write`, `ack`, `validate`. Read converts raw EDI to JSON; Write converts JSON back to raw EDI; Ack generates an acknowledgement document; Validate checks document validity. |
| `format` | string | jsonata | Y | The document format. Options: `x12` (X12), `edifact` (EDIFACT). |
| `filename` | string | jsonata | N | The name of the file being translated. Visible when action is `read`. |
| `fileContents` | string | jsonata | N | The EDI data to be translated. |

Additional context-dependent Options properties appear based on the selected action and format combination. Each action/format pair (e.g., read+x12, write+edifact) exposes a dedicated Options field (jsonata) pre-populated with sensible defaults for API options such as `ignoreNullValues`, `continueOnError`, `charSet`, `preserveWhitespace`, `detectDuplicates`, `eancomS3`, and others.

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

## Integration

Nodes for connecting to external systems and services — HTTP, FTP, databases, ERP systems, cloud APIs, and third-party platforms. Most integration nodes share common properties: `connectionName` (or `connectionNameTransform`) to select a configured connection, `returnErrors` to handle failures gracefully, and `degreeOfParallelism` for batch requests. See overview for details on these shared patterns.

---

### ADP (`adp`)
**Type:** transition

A node used to make ADP requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The ADP connection to use for the request. |
| `method` | string | | Y | The method to use for the request. Options: `get`, `put`, `post`, `delete`. |
| `pathTransform` | string | jsonata | Y | The path to make the request against. |
| `paramsTransform` | string | jsonata | N | The transform to produce the URL parameters for the request. |
| `dataTransform` | string | jsonata | N | The transform to manipulate the payload input into the body for the request. |
| `masked` | boolean | | N | Whether or not to mask sensitive data that might be returned from ADP. Default: `true`. |
| `roleCode` | string | | N | The role code to use for this request to ADP. Options: `employee`, `manager`, `practitioner`, `administrator`, `supervisor`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### ADP Vista (`adpVista`)
**Type:** transition

A node used to make a request to the ADP Vista API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The ADP Vista connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the ADP Vista API endpoint. |
| `method` | string | jsonata | Y | The HTTP method to use for the request. Options: `POST`, `GET`. |
| `dataTransform` | string | jsonata | Y | A transform which produces the data to send to the API in the body of the request. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response from the ADP Vista API. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### Amazon (`amazon`)
**Type:** transition

A node used to make a request to the Amazon Selling Partner API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Amazon connection to use for the request. |
| `amazonAction` | string | | Y | The Amazon action to execute. Options: `callAPI`, `download`, `upload`. |
| `amazonAPITransform` | string | jsonata | N | A transform which returns the data to call the API with. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### AWS Lambda (`awsLambda`)
**Type:** transition

A node used to interact with AWS Lambda functions.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The AWS Lambda connection to use with the request. |
| `lambdaAction` | string | jsonata | Y | The Lambda command to execute. Options: `InvokeCommand` (Invoke Command), `ListFunctionsCommand` (List Functions). |
| `regionTransform` | string | jsonata | N | The region of the AWS data center in which your function is deployed and executed. Default: `"\"us-east-1\""`. |
| `functionNameTransform` | string | jsonata | N | The name of the Lambda function to be executed. Visible when action is `InvokeCommand`. |
| `bodyTransform` | string | jsonata | N | A transform which produces data to send to the API in the request body. Default: `"$"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the payload returned from the AWS Lambda request. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | N | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

---

### Email (`email`)
**Type:** transition

A node used to send email via SMTP.

Email fields can be provided plain (sender@server.com) or formatted ("Sender Name" <sender@server.com>), or as an object with 'name' and 'address' properties. Lists of addresses can be provided as comma-separated strings, or as arrays of strings or objects.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The SMTP connection to use for the request. |
| `emailFromTransform` | string | jsonata | Y | The email address of the sender. For most providers, this must match the account on the connection if provided. |
| `emailToTransform` | string | jsonata | Y | The email addresses of the recipients. |
| `emailSubjectTransform` | string | jsonata | Y | The subject of the email. |
| `emailHtmlTransform` | string | jsonata | Y | The HTML body of the email. |
| `emailAttachments` | array | | N | A list of attachments to include in the email. See sub-table below. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

**Attachment items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `attachmentFilenameTransform` | string | jsonata | Y | The name of the attached file. |
| `attachmentContentTransform` | string | jsonata | Y | The content of the attachment. |
| `attachmentEncoding` | string | | N | The encoding of the content. Options: `ascii` (ASCII), `utf8` (UTF-8), `base64` (Base64), `hex` (Hexadecimal), `binary` (Binary). |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### FTP (`ftp`)
**Type:** transition

A node used to make an FTP request.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The FTP connection to use for the request. |
| `ftpAction` | string | | Y | The FTP action to execute. Options: `get`, `post`, `delete`, `move`, `list`. |
| `ftpFileTransform` | string | jsonata | N | The filename to Get, Move, or Delete, or the name of the file to Post. Visible for get/move/delete/post actions. |
| `ftpDestinationTransform` | string | jsonata | N | The destination folder to Move the file to, or the folder to List files in. Visible for move/list actions. |
| `ftpDataTransform` | string | jsonata | N | A transform which returns the data to Post to the file. Visible for post action. |
| `ftpMatch` | string | | N | A regular expression to execute against Listed file names. Only files with a name matching the regular expression will be returned. Visible for list action. |
| `ftpFirst` | number | | N | The number of files to return from a List action, counting from the start of the list. Visible for list action. |
| `ftpLast` | number | | N | The number of files to return from a List action, counting from the end of the list. Visible for list action. |
| `encoding` | string | | N | The encoding to use for a file when pulling it from FTP. Options: `utf8`, `base64`, `binary`, `hex`, `ascii`. Defaults to utf8; for PDF files base64 encoding is needed. Visible for get/post/put actions. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Fuuz (`fuuz`)
**Type:** transition

A node used to make a request to an external Fuuz API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | jsonata | Y | The Fuuz connection to use with the request. |
| `action` | string | jsonata | Y | The type of request. Options: `query` (GraphQL Query), `mutation` (GraphQL Mutation), `publish` (Publish Topic Message), `flow` (Execute Flow), `savedQuery` (Run Saved Query), `savedScript` (Run Saved Script). |
| `api` | string | | N | Which Fuuz API to issue the query or mutation against. Options: `application`, `system`. Default: `"application"`. Visible for query/mutation actions. |
| `query` | string | graphql | N | The GraphQL query to execute. Visible for query action. |
| `mutation` | string | graphql | N | The GraphQL mutation to execute. Visible for mutation action. |
| `savedQueryId` | string | jsonata | N | The id of the saved query to execute. Visible for savedQuery action. |
| `variables` | string | jsonata | N | A transform producing the data to be passed through the selected API. Default: `"{}"`. Visible for query/mutation/savedQuery actions. |
| `savedScriptId` | string | jsonata | N | The id of the saved script to execute. Visible for savedScript action. |
| `flowId` | string | jsonata | N | The id of the flow to execute. Visible for flow action. |
| `topicName` | string | jsonata | N | The topic name to publish to. Visible for publish action. |
| `payloadTransform` | string | jsonata | N | A transform producing the data to be passed through the selected API. Default: `"{}"`. Visible for flow/savedScript actions. |
| `topicMessages` | string | jsonata | N | A transform producing an array of messages to pass to the selected Topic. Default: `"[]"`. Visible for publish action. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the response from the Fuuz API. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "errors" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Google BigQuery API (`googleBigQuery`)
**Type:** transition

A node used to make a request to Google BigQuery.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Google connection to use with the request. |
| `clientVersion` | string | jsonata | Y | The Google Client API Version. Options: `v2` (V2). |
| `clientAPIResource` | string | jsonata | Y | The Google API Resource to access. Options: `datasets`, `jobs`, `models`, `projects`, `routines`, `rowAccessPolicies`, `tabledata`, `tables`. |
| `clientAPIAction` | string | jsonata | Y | The Google API Action to execute. Available actions vary by selected resource (e.g., get, insert, delete, list, query, cancel, patch, update, undelete, getServiceAccount, getIamPolicy, setIamPolicy, testIamPermissions, insertAll, getQueryResults). |
| `projectIdTransform` | string | jsonata | N | The ID of the Google Cloud project. |
| `datasetIdTransform` | string | jsonata | N | The ID of the dataset. |
| `jobIdTransform` | string | jsonata | N | The ID of the BigQuery job. |
| `modelIdTransform` | string | jsonata | N | The ID of the BigQuery ML model. |
| `routineIdTransform` | string | jsonata | N | The ID of the routine. |
| `tableIdTransform` | string | jsonata | N | The ID of the table. |
| `resourceTransform` | string | jsonata | N | The full resource identifier for IAM policy actions. |
| `bodyTransform` | string | jsonata | N | Request body. Expects `{ "requestBody": { ... } }`. Default: `"$"`. |
| `optionsTransform` | string | jsonata | N | Optional parameters to pass with the request body. Default: `"{}"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

---

### Google Drive API (`googleDrive`)
**Type:** transition

A node used to make a request to Google Drive API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Google connection to use with the request. |
| `clientVersionTransform` | string | | Y | The Google Client API Version. Options: `v2`, `v3`. |
| `clientAPIResourceTransform` | string | | Y | The Google API Resource. Options: `about`, `changes`, `channels`, `comments`, `drives`, `files`, `permissions`, `replies`, `revisions`. |
| `clientAPIActionTransform` | string | | Y | The Google API Action. Options: `create`, `get`, `update`, `delete`, `copy`, `emptyTrash`, `export`, `generateIds`, `getStartPageToken`, `list`, `listLabels`, `modifyLabels`, `stop`, `watch`, `hide`, `unhide`. |
| `bodyTransform` | string | jsonata | N | Request body. Default: `"$"`. |
| `optionsTransform` | string | jsonata | N | Optional parameters. Default: `"{}"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

---

### HTTP (`http`)
**Type:** transition

A node used to make an HTTP request.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The HTTP connection to use for the request. |
| `httpMethod` | string | | Y | The HTTP method to use for the request. Options: `get`, `put`, `post`, `delete`. |
| `httpPathTransform` | string | jsonata | Y | The HTTP path to make the request against. |
| `httpHeadersTransform` | string | jsonata | N | A transform producing the HTTP headers to include with the request. |
| `httpDataTransform` | string | jsonata | N | The transform to manipulate the payload input into the data for the request. |
| `returnErrors` | boolean | | Y | When true, failed requests return an "error" property at the same level as a standard response. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Infor (`infor`)
**Type:** transition

A node used to make a request to the Infor (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Infor connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Infor API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `post`, `get`, `put`, `delete`. |
| `dataTransform` | string | jsonata | N | A transform which produces the data to send in the request body. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Integrate (`integrateV2`)
**Type:** transition

A node used to integrate with external systems. Input to the node can be a single payload, or an array of payloads to make many requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | Which integration connection to use for the request. |
| `requestTransform` | string | jsonata | Y | A transform to populate the integrate request. Default: `"$"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the payload returned from the integrate request. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads to the node. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Magento (`magento`)
**Type:** transition

A node used to make a request to the Magento (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Magento connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Magento API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `post`, `get`, `put`, `delete`. |
| `filtersTransform` | string | jsonata | N | A transform producing the filters for the request. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### SQL Server (`mssql`)
**Type:** transition

A node used to interact with Microsoft SQL Server.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The ODBC connection to use for the request. |
| `query` | string | | Y | The SQL query to execute. Default: `""`. |
| `parameters` | array | | N | A list of parameters to pass to the query. See sub-table below. |
| `procedure` | boolean | | Y | Whether or not you are executing a stored procedure. Default: `false`. |
| `maxRetries` | integer | | Y | The maximum number of times to retry a valid, but unsuccessful request. Default: `0`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

**Parameter items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `name` | string | | Y | The name of this parameter. |
| `dataType` | string | | Y | Options: `BigInt`, `Bit`, `Char`, `Date`, `DateTime`, `DateTime2`, `DateTimeOffset`, `Decimal`, `Float`, `Int`, `Money`, `NChar`, `NText`, `NVarChar`, `Numeric`, `Real`, `SmallDateTime`, `SmallInt`, `SmallMoney`, `Text`, `Time`, `TinyInt`, `UniqueIdentifier`, `VarChar`, `Xml`. |
| `valueTransform` | string | jsonata | N | A JSONata transform to determine the value for this parameter. |
| `length` | integer | | N | The length of the data type. |
| `scale` | integer | | N | The scale of the data type. |
| `precision` | integer | | N | The precision of the data type. |
| `output` | boolean | | N | Whether this is an output parameter. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Netsuite REST (`netsuiteRest`)
**Type:** transition

A node used to make a request to the Netsuite REST API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Netsuite REST connection to use for the request. |
| `netsuiteEndpoint` | string | | N | The Netsuite path to query. |
| `netsuiteRestAction` | string | | Y | The HTTP action. Options: `GET`, `POST`, `PATCH`, `PUT`, `DELETE`. |
| `headers` | string | jsonata | N | Optional request headers. |
| `netsuiteRestParameters` | string | jsonata | N | Additional request parameters. |
| `netsuiteRestBody` | string | jsonata | N | Body content sent with the request. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Netsuite SOAP (`netsuiteSoap`)
**Type:** transition

A node used to make a request to the Netsuite SOAP API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Netsuite SOAP connection to use for the request. |
| `netsuiteSoapAction` | string | | Y | The SOAP action to execute. Options: `login`, `ssoLogin`, `mapSso`, `changePassword`, `changeEmail`, `logout`, `add`, `delete`, `search`, `searchMore`, `searchMoreWithId`, `searchNext`, `update`, `upsert`, `addList`, `deleteList`, `updateList`, `upsertList`, `get`, `getList`, `getAll`, `getSavedSearch`, `getCustomizationId`, `initialize`, `initializeList`, `getSelectValue`, `getItemAvailability`, `getBudgetExchangeRate`, `getCurrencyRate`, `getDataCenterUrls`, `getPostingTransactionSummary`, `getServerTime`, `attach`, `detach`, `updateInviteeStatus`, `updateInviteeStatusList`, `asyncAddList`, `asyncUpdateList`, `asyncUpsertList`, `asyncDeleteList`, `asyncGetList`, `asyncInitializeList`, `asyncSearch`, `getAsyncResult`, `checkAsyncStatus`, `getDeleted`. |
| `netsuiteSoapTransform` | string | jsonata | Y | SOAP based XML body to send with the request. |
| `returnXML` | boolean | | Y | When true, successful responses will be returned as XML. Default: `false`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### ODBC (`odbcV2`)
**Type:** transition

A node used to make an ODBC query.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The ODBC connection to use for the request. |
| `query` | string | | Y | The SQL query to execute. Default: `""`. |
| `variableTransform` | string | jsonata | N | A transform producing an array of values to bind with the SQL query. Default: `"[]"`. |
| `maxRetries` | integer | | Y | The maximum number of times to retry. Default: `0`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "Errors" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### OpenAI Chat (`openaiChat`)
**Type:** transition

A node used to interact with OpenAI Chat GPT models.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | N | The OpenAI connection to use for the request. |
| `model` | string | | Y | The model to use. Refer to the OpenAI documentation for a list of models. |
| `messages` | string | jsonata | Y | A transform producing the messages array for chat completions. |
| `additionalOptions` | string | jsonata | N | Additional options to be applied to this operation. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Plex API (`plexApi`)
**Type:** transition

A node used to make Plex API requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Plex API connection. Supports Plex API and Plex IAM API connections. |
| `method` | string | | Y | The method. Options: `get`, `put`, `post`, `patch`, `delete`. |
| `pathTransform` | string | jsonata | Y | The path to make the request against. |
| `paramsTransform` | string | jsonata | N | URL parameters for the request. |
| `bodyTransform` | string | jsonata | N | The body for the request. |
| `tenantIdTransform` | string | jsonata | N | The Tenant ID. Does nothing when using Plex IAM API. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Plex Datasource (`plexDatasourceV2`)
**Type:** transition

A node used to call a Plex datasource.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | Y | The Plex integration connection. Supports Plex and Plex UX connections. |
| `datasourceKey` | string | | Y | The key of the datasource to execute. |
| `parameterTransform` | string | jsonata | Y | Parameters to send to the Plex datasource. Keys should align with datasource parameter names. Default: `"{}"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "Errors" property rather than aborting flow execution. Default: `false`. |
| `degreeOfParallelism` | integer | | Y | The number of requests to execute in parallel when providing an array of payloads. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Quickbooks (`quickbooks`)
**Type:** transition

A node used to make Quickbooks API requests.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Quickbooks connection to use for the request. |
| `method` | string | | Y | The method. Options: `get`, `put`, `post`, `patch`, `delete`. |
| `headersTransform` | string | jsonata | N | HTTP Headers for the request. |
| `pathTransform` | string | jsonata | Y | The path to make the request against. |
| `paramsTransform` | string | jsonata | N | URL parameters for the request. |
| `bodyTransform` | string | jsonata | N | The body for the request. |
| `returnErrors` | boolean | | Y | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Salesforce (`salesforce`)
**Type:** transition

A node used to make a request to the Salesforce REST API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Salesforce connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Salesforce API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `post`, `get`, `put`, `delete`, `patch`, `head`. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `paramsTransform` | string | jsonata | N | A transform producing the params for the request. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |
| `pagination` | boolean | | N | When true, automatically retrieves and merges paginated results. Default: `false`. |
| `degreeOfParallelism` | integer | | N | The number of parallel requests for array payloads. Default: `10`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

### Success Factors (`successFactors`)
**Type:** transition

A node used to make a request to the Success Factors (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The Success Factors connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the API endpoint. |
| `method` | string | jsonata | Y | The HTTP method. Options: `POST`, `GET`, `PUT`, `DELETE`, `PATCH`. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### TecCom File Upload (`tecComFileUpload`)
**Type:** transition

A node used to convert JSON to a CSV and send to TecCom.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The TecCom connection to use with the request. |
| `requestMode` | string | | Y | The request mode. Options: `REPLACE`, `MODIFY`. |
| `files` | array | | Y | The files to upload. See sub-table below. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

**File items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `fileName` | string | | N | Options: `article`, `assortment`, `article_data`, `sales_uom`, `buyer_grouping`, `availability`, `article_description`, `article_buyer`, `alternative`, `condition_group`, `price`, `alc`, `packaging`, `external_document`. |
| `data` | string | jsonata | N | The data to upload. Default: `"$"`. |

Standard output port.

---

### TecCom Web Service (`tecComWebService`)
**Type:** transition

A node used to make web service requests to TecCom.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The TecCom connection to use with the request. |
| `functionId` | string | | Y | The Id of the web service function. |
| `parameterTransform` | string | jsonata | Y | Parameters for the TecCom Web Service Function. Each element becomes another request. Default: `"{}"`. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### United Parcel Service (`ups`)
**Type:** transition

A node used to make a request to the UPS API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionNameTransform` | string | | N | The UPS connection to use with the request. |
| `path` | string | jsonata | N | The URL path for the UPS API endpoint. |
| `action` | string | jsonata | N | The HTTP method. Options: `POST`, `GET`, `PUT`, `DELETE`, `PATCH`. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `responseTransform` | string | jsonata | N | A transform to reformat the response. Default: `"$"`. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

---

### Woocommerce (`woocommerce`)
**Type:** transition

A node used to make a request to the Woocommerce (REST) API.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `connectionName` | string | | Y | The Woocommerce connection to use with the request. |
| `path` | string | jsonata | Y | The URL path for the Woocommerce API endpoint. |
| `method` | string | | Y | The HTTP method. Options: `POST`, `GET`, `PUT`, `DELETE`. |
| `version` | string | | N | The Version of Woocommerce. Options: `v2`, `v3`. |
| `parametersTransform` | string | jsonata | N | A transform producing the parameters for the request. |
| `dataTransform` | string | jsonata | N | A transform producing the data for the request body. |
| `returnErrors` | boolean | | N | When true, failed requests return errors in an "error" property rather than aborting flow execution. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireWalkthrough

---

## Notification

Nodes for sending and receiving messages through notification channels.

---

### Receive Notification (`receiveNotification`)
**Type:** source

A node used to receive notifications from a notification channel.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `notificationChannel` | string | | Y | The notification channel to subscribe to. |

Standard output port.

---

### Send Notification (`sendNotification`)
**Type:** transition

A node used send to messages to a notification channel.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `notificationChannelId` | string | | Y | The notification channel to send messages to. |
| `transform` | string | jsonata | N | A transform producing an array of notifications to send. Default: `"{}"` |

Standard output port.

---

## Scripts

Nodes for running transformation scripts against the workflow state payload.

---

### JSONata (`transform`)
**Type:** transition

A node used to run a script using the JSONata language.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata script to run on the workflow state. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### JavaScript (`javascriptTransform`)
**Type:** transition

A node used to run a script using the JavaScript language.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | javascript | Y | A JavaScript script to run on the workflow state. |

Standard output port.

**Available in JavaScript transforms:**
- `$` - Current payload
- `$state.context` - Flow state
- `$moment()` - moment-timezone (also available as `moment`)
- `$numeral()` - Number formatting and parsing
- `$schedule()`, `$scheduleGroup()` - Schedule functions
- **All Fuuz JSONata custom bindings** - Since Fuuz runs on Node.js
- `console.log()` - Logging

**Must return value**

---

### Saved Script (`savedTransformV2`)
**Type:** transition

A node used to transform the state payload with the given saved script.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transformScriptLanguage` | string | | Y | The language used by transform. Default: `"JSONata"`. |
| `transformId` | string | | Y | The saved script to run on the workflow state. |
| `requestTransform` | string | jsonata | Y | A transform to populate the integrate request. Default: `"$"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the payload returned from the integrate request. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.

---

### Data Mapping (`dataMapping`)
**Type:** transition

A node used to transform the state payload with the given data mapping.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `dataMappingId` | string | | Y | A data mapping to run on the payload. |
| `transformScriptLanguage` | string | | Y | The language used by the transform. Default: `"JSONata"`. |
| `requestTransform` | string | jsonata | Y | A transform to populate the data mapping request. Default: `"$"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the payload returned from the data mapping request. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | Enables configuration of the Script Language and the Request and Response transforms. Default: `false`. |

Standard output port.

---

### Object Diff (`objectDiff`)
**Type:** transition

A node that compares two objects and returns the differences.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `originalObj` | string | jsonata | Y | The first comparison object. |
| `updatedObj` | string | jsonata | Y | The second comparison object. |
| `ignore` | array | jsonata | N | An array of key paths to be excluded from comparison. |
| `detailed` | boolean | | Y | Shows a breakdown of which properties were added, removed, and changed. Default: `true`. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.

---

## Transformation

Nodes for converting data between formats and filtering or reshaping arrays.

---

### JSON To CSV (`jsonToCsv`)
**Type:** transition

A node used to convert an array of JSON objects to a CSV.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `fieldDelimiterTransform` | string | jsonata | Y | The character that separates fields. Options: `Comma` (`,`), `Pipe` (`\|`), `Semicolon` (`;`). |
| `fieldWrapTransform` | string | jsonata | Y | The character that field values are wrapped in. Options: `Double Quote` (`"`), `Single Quote` (`'`). |
| `endOfLineTransform` | string | | Y | The end of line delimiter. Default: `"\\n"`. Options: `Line Feed` (`\n`), `Carriage Return` (`\r`), `Tab` (`\t`), `Carriage Return and New Line` (`\r\n`). |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### CSV To JSON (`csvToJson`)
**Type:** transition

A node used to transform a CSV string to a JSON object.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | Y | A transform to reformat the input payload. Default: `"$"`. |
| `fieldDelimiterTransform` | string | jsonata | Y | The character that separates fields. Options: `Comma` (`,`), `Pipe` (`\|`), `Semicolon` (`;`). |
| `wrapDelimiterTransform` | string | jsonata | Y | The character that field values are wrapped in. Options: `Double Quote` (`"`), `Single Quote` (`'`). |
| `eolDelimiterTransform` | string | jsonata | Y | The end of line delimiter. Default: `"\\n"`. Options: `Line Feed` (`\n`), `Carriage Return` (`\r`), `Tab` (`\t`), `Carriage Return and New Line` (`\r\n`). |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### JSON To XML (`jsonToXml`)
**Type:** transition

A node used to transform the input from JSON to XML.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | Y | A transform to reformat the input payload. Default: `"$"`. |
| `attributeKeyTransform` | string | jsonata | Y | The prefix used to access the attributes. Options: `$`, `@`. |
| `charKeyTransform` | string | jsonata | Y | The prefix used to access the character content. Options: `Underscore` (`_`), `Hash` (`#`). |
| `rootNameTransform` | string | jsonata | Y | The name of the root element. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### XML To JSON (`xmlToJson`)
**Type:** transition

A node used to transform the input from XML to a JSON object.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | Y | A transform to reformat the input payload. Default: `"$"`. |
| `attributeKeyTransform` | string | jsonata | Y | The prefix used to access the attributes. Options: `$`, `@`. |
| `charKeyTransform` | string | jsonata | Y | The prefix used to access the character content. Options: `Underscore` (`_`), `Hash` (`#`). |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Unique Array (`uniqueArray`)
**Type:** transition

A node used to transform an array with the duplicate items removed by using the uniqBy function.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `uniqueTransform` | string | jsonata | Y | The transform to evaluate items in the input array one at a time, from first to last. If this transform returns a value that has not been seen before, that item will be added to the output array. However, if the same transform result was already seen, the item will be dropped. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When checked, the Input and Output Transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Filter Array (`filterArray`)
**Type:** transition

A node used to filter items in an array passed into the node or generated through the input transform.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `filterTransform` | string | jsonata | Y | The input array will be checked against the transform provided here. The filter transform will operate in the context of a single record in the array, from first to last. If the transform returns true, the data will be passed to the next nodes. If the transform returns false, it will be removed from the array. From within the filter transform you can access the index of the record through the `$i` binding and the overall array through the `$a` binding. If you need to reference the input payload you can escape the scope using `$$`. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | N | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Group Array (`groupBy`)
**Type:** transition

A node used to group data from the input into an object based on the provided Group By transform.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `groupingTransform` | string | jsonata | Y | The input will be checked against the condition given here. If it returns true, it will be added to an object in the output. If it returns false, it will be ignored. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input and Output transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Predicate Filter (`predicateFilter`)
**Type:** transition

A node used to filter a payload by given predicates.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `payloadType` | string | jsonata | Y | Determines if the payload is an array or an object. When set to array, the payload will be automatically converted into an array. Options: `Array` (`array`), `Object` (`object`). |
| `predicateFilterTransform` | string | jsonata | Y | Predicate to filter the payload by. |
| `caseSensitiveComparison` | boolean | | Y | Determines whether the filter should be case sensitive. Default: `false`. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

## Validation

Nodes for validating payloads against schemas.

---

### Validate (`validate`)
**Type:** transition

A node used to validate the payload with the provided schema. If the payload doesn't fit the schema, this node will throw an error.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `schema` | object | | Y | The JSON Schema to validate the payload against. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

## UI Interaction Nodes (Web Flows Only)

These nodes are available only in Screen (web) flows and provide user interaction capabilities.

---

### Form Dialog (`formDialog`)
**Type:** transition

Shows a modal form to the user for data input.

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

---

### Snackbar (`snackbar`)
**Type:** transition

Shows a toast notification to the user.

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

**Severity options:** `"success"`, `"warning"`, `"error"`, `"info"`

---

### Search Table (`searchTable`)
**Type:** transition

Refreshes a table element on a screen.

```json
{
  "type": "searchTable",
  "data": {
    "screenElementTransform": "\"AssetTable\"",
    "nextNodes": ["next-node-uuid"]
  }
}
```

---

### Alert Dialog (`alertDialog`)
**Type:** transition

Shows an alert dialog to the user.

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

---

### Confirm Dialog (`confirmDialog`)
**Type:** transition

Shows a confirmation dialog with confirm/cancel paths.

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

---

### Navigate Screen (`navigateScreen`)
**Type:** transition

Navigates to a different screen.

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

## Debug Nodes (Testing Only)

These nodes are used during development and testing. They are not used in deployed production flows.

---

### Debug Source (`debugSource`)
**Type:** source

Manual flow trigger with a sample payload for testing.

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

---

### Debug File (`debugFile`)
**Type:** source

File source for testing file-based flows.

```json
{
  "type": "debugFile",
  "data": {
    "encode": "base64",
    "nextNodes": ["next-node-uuid"]
  }
}
```

---

### Debug Data Change (`debugDataChange`)
**Type:** source

Simulates a data change event for testing dataChanges-triggered flows.

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

---

### Preview File (`previewFile`)
**Type:** transition

Opens a file for preview in the browser.

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

## Best Practices

1. **Name nodes descriptively** - Use clear, purposeful names like "Query Pending Orders", not generic "Query" or "Transform"
2. **Always generate fresh UUIDs** for node IDs and nextNodes
3. **Provide flow descriptions** - Explain what the flow does, when it runs, and key models involved
4. **Enable failed logging** by default - `executionFailed: true`
5. **Use when/unless early** - filter before expensive operations
6. **Store config in context** - setContext after queries, before transforms
7. **Wrap mutations with mutex** - when concurrent access is possible
8. **Use broadcast for arrays** - better performance than loop for parallel processing (but see performance note about large datasets)
9. **Transform payloads incrementally** - easier to debug than complex single transforms
10. **Log intermediate values** - use log nodes during development
11. **End web flows with response** - always provide a response node
12. **For description generation** - export flow JSON and let Claude analyze structure to create contextual descriptions
