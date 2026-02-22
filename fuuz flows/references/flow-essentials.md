---
name: fuuz-data-flows
description: >
  Design, build, analyze, document, and troubleshoot data flows in the Fuuz Industrial Operations Platform (fuuz.com).
  Trigger this skill whenever the user mentions Fuuz flows, data flows, flow nodes, flow designer, workflow automation,
  integration flows, scheduled flows, webhook flows, trigger nodes, query nodes, mutation nodes, transform nodes,
  if/else logic in flows, try/catch error handling in flows, or any visual flow-based automation within Fuuz.
  Also trigger when the user uploads or pastes Fuuz flow JSON for analysis, documentation, or modification,
  or when building manufacturing automation like OEE calculations, inventory movements, production logging,
  or asset tracking flows.
---

# Fuuz Data Flows Skill

> **Purpose:** Guide Claude in designing, building, analyzing, and documenting data flows for the Fuuz Industrial Operations Platform. Data flows are Fuuz's visual programming environment for automation, integration, and business logic — similar to Node-RED but with Fuuz-specific node types and industrial manufacturing patterns.

## 1. What Are Fuuz Data Flows?

Data flows are visual, node-based programs that automate data movement, transformation, and business logic. They follow an **Input → Process → Output** paradigm where data flows through connected nodes on a canvas.

### When to Use Data Flows
- Scheduled data processing (OEE calculations, report generation)
- Event-driven automation (production logging, quality alerts)
- System integration (ERP sync, equipment data collection)
- Data transformation (payload reshaping between systems)
- Business rule enforcement (inventory validation, work order routing)

### Flow Execution Model
- Flows execute left-to-right through connected nodes
- Each node receives a **payload** (the data), processes it, and passes output to the next node
- Nodes can also access **context** — persistent state across flow execution
- Flows can be triggered by schedules, webhooks, data changes, or manual execution

## 2. Node Categories

### Trigger Nodes (Flow Entry Points)

| Node Type | Purpose | Configuration |
|-----------|---------|---------------|
| `schedule` | Time-based execution | Cron expression or interval (e.g., every 30 min) |
| `webhook` | HTTP POST endpoint | Path, value schema, authentication |
| `dataChange` | Fires on data model changes | Model name, field filters, change types |
| `debugSource` | Manual test trigger | Static test payload for development |
| `manual` | User-initiated execution | Triggered from UI or API |

### Data Query Nodes

| Node Type | Purpose | Key Config |
|-----------|---------|-----------|
| `query` | GraphQL query against Fuuz data models | API (Application/System), query name, variables, field selection |
| `savedQuery` | Execute a pre-saved reusable query | Query reference, dynamic variables |
| `httpRequest` | Call external REST API | URL, method, headers, body, authentication |

**Query Node Example Configuration:**
```json
{
  "type": "query",
  "api": "Application",
  "operation": "query",
  "name": "productionHistories",
  "variables": {
    "where": {
      "createdAt": {
        "gte": "$moment().subtract(1, 'day').toISOString()"
      }
    },
    "orderBy": [{ "createdAt": "desc" }],
    "take": 1000
  },
  "fields": ["id", "quantity", "workcenterId", "workcenter.name", "asset.id"]
}
```

### Data Mutation Nodes

| Node Type | Purpose | Key Config |
|-----------|---------|-----------|
| `mutation` | GraphQL create/update/delete | API, mutation name, variables (from payload or static) |
| `batchMutation` | Bulk create/update/delete | Array of mutation payloads |

**Mutation Payload Format:**
```json
{
  "payload": [
    {
      "where": { "id": "record-id-123" },
      "data": { "status": "Complete", "completedAt": "2025-01-15T10:30:00.000" }
    }
  ]
}
```

### Transform & Logic Nodes

| Node Type | Purpose | Key Config |
|-----------|---------|-----------|
| `transform` | JSONata transformation | JSONata expression body |
| `script` | JavaScript execution | JS function body (Node.js 20, ES2023+) |
| `ifElse` | Conditional routing | JSONata condition expression |
| `switch` | Multi-path routing | Multiple condition/route pairs |
| `loop` | Iterate over array items | Input array path, body nodes |
| `merge` | Combine multiple inputs | Join strategy |

### Error Handling Nodes

| Node Type | Purpose | Key Config |
|-----------|---------|-----------|
| `tryCatch` | Wrap nodes with error handling | Try path, Catch path |
| `throwError` | Propagate error up chain | Error message, error code |
| `log` | Write to execution log | Level (info/warning/error), include context/payload |

### Output & Action Nodes

| Node Type | Purpose |
|-----------|---------|
| `notification` | Send email, SMS, or in-app notification |
| `mqttPublish` | Publish to MQTT topic (Fuuz UNS) |
| `httpResponse` | Return response to webhook caller |
| `setContext` | Update flow-level persistent context |

## 3. Flow Design Patterns

### Pattern 1: Scheduled Data Processing (OEE Calculation)
```
[Schedule: every 30 min]
  → [Query: GET production logs last 30 min]
  → [IfElse: Any records?]
    → YES: [Transform: Calculate OEE metrics]
      → [Query: GET existing OEE records for this period]
      → [Script: Merge new data with existing]
      → [Mutation: Upsert OEE records]
    → NO: [Log: No production data for period]
```

### Pattern 2: Event-Driven Validation (Inventory Move)
```
[Webhook: /inventory/move]
  → [TryCatch]
    → TRY:
      → [Transform: Validate request fields]
      → [Query: GET inventory record]
      → [Query: GET destination location]
      → [IfElse: Inventory exists and active?]
        → YES: [IfElse: Not loaded in workcenter?]
          → YES: [IfElse: Status allows movement?]
            → YES: [IfElse: Different location?]
              → YES: [Mutation: Update inventory location]
                → [Mutation: Create inventory transaction]
                → [HTTPResponse: 200 Success]
              → NO: [HTTPResponse: 400 "Same location"]
            → NO: [HTTPResponse: 400 "Status restricted"]
          → NO: [HTTPResponse: 400 "Loaded to workcenter"]
        → NO: [HTTPResponse: 404 "Inventory not found"]
    → CATCH:
      → [Log: ERROR with payload]
      → [HTTPResponse: 500 "Internal error"]
```

### Pattern 3: Data Enrichment (Asset Lifecycle Tracking)
```
[Schedule: daily]
  → [Query: GET production history last 24h]
  → [Transform: Group by asset, sum cycles/hours]
  → [Query: GET current asset records]
  → [Script: Calculate delta (new cycles - existing)]
  → [IfElse: Any updates needed?]
    → YES: [Mutation: Update asset currentCycles, lifetimeCycles]
    → NO: [Log: No asset updates needed]
```

### Pattern 4: Fan-Out Integration
```
[DataChange: Production record created]
  → [Transform: Normalize payload]
  → [Parallel]
    → [Mutation: Update WIP inventory]
    → [MQTTPublish: production/events topic]
    → [HTTPRequest: POST to ERP system]
    → [Notification: Alert quality team if scrap]
```

### Pattern 5: Planned Schedule Generation
```
[Schedule: nightly at 11 PM]
  → [Query: GET all workcenters with scheduleGroupId]
  → [Loop: For each schedule group]
    → [Script: Calculate tomorrow's date boundaries in tenant timezone]
    → [Transform: Get schedule timeline data]
    → [Script: Generate 24 hourly records per workcenter]
      → [Set shift hours, day hours for each hour slot]
      → [Handle overnight shifts spanning day boundary]
    → [Mutation: Batch create planned schedule records]
```

## 4. Webhook Configuration

Webhooks create inbound HTTP endpoints for external system integration:

```
URL: https://api.{environment}.fuuz.app/webhook/post/{path}
Method: POST
Content-Type: application/json
```

### Configuration Properties
- **Name**: Human-readable identifier
- **Path**: URL path segment (e.g., `inbound.test` → `/webhook/post/inbound.test`)
- **Value Schema**: JSON schema for payload validation
- **Description**: Documentation for the endpoint

### Using Webhooks as Flow Triggers
The webhook node receives the POST body as its payload, which flows into subsequent nodes for processing.

## 5. Timezone Handling in Flows

This is critical for manufacturing — shifts, production logs, and schedules must align with plant-local time.

### Rules
1. **Business timestamps** (occurAt, shiftStart, scheduledDate): Use tenant timezone WITHOUT offset — format: `YYYY-MM-DDTHH:mm:ss.SSS`
2. **System timestamps** (createdAt, updatedAt): Remain in UTC
3. **Access tenant timezone** from context: `$state.context.tenantTimezone.setting.edges.node.defaultValue`
4. **Use $moment with timezone**: `$moment().tz(tenantTz).format("YYYY-MM-DDTHH:mm:ss.SSS")`
5. **Overnight shifts**: A 10 PM – 6 AM shift spans two calendar days — calculate `dayHour` correctly

### Example: Timezone-Aware Window Calculation
```javascript
const tenantTz = $state.context.tenantTimezone.setting.edges.node.defaultValue;
const now = $moment().tz(tenantTz);
const windowEnd = now.clone();
const windowStart = now.clone().subtract(30, "minutes");

// Format for Fuuz business fields
const occurAt = windowEnd.format("YYYY-MM-DDTHH:mm:ss.SSS");
```

## 6. Flow JSON Structure

Flows are stored as JSON. When analyzing or generating flow JSON, follow this structure:

```json
{
  "name": "Asset Cycles from Production History",
  "description": "Updates asset cycle/hour counts from production data",
  "nodes": [
    {
      "id": "unique-uuid",
      "type": "schedule",
      "label": "Every 30 minutes",
      "position": { "x": 100, "y": 200 },
      "config": { /* node-specific configuration */ },
      "outputs": ["next-node-id"]
    },
    {
      "id": "next-node-id",
      "type": "query",
      "label": "GET PH data for last interval",
      "config": {
        "api": "Application",
        "operation": "query",
        "name": "productionHistories",
        "variables": {},
        "fields": []
      },
      "outputs": ["transform-node-id"]
    }
  ],
  "connections": [
    { "source": "unique-uuid", "target": "next-node-id", "port": "output" }
  ]
}
```

## 7. Error Handling Best Practices

1. **Always wrap critical paths in TryCatch** — especially mutations and external API calls
2. **Log with context** — Include payload and flow state in error logs for debugging
3. **Use specific error messages** — "Inventory not found" not just "Error"
4. **Return meaningful HTTP responses** from webhook flows — status codes + error details
5. **Disable debug breakpoints** before deploying to production
6. **Handle empty query results** — Always check with IfElse before processing

## 8. Performance Considerations

- **Batch mutations** when updating many records — don't loop individual mutations
- **Limit query results** with `take` parameter — don't fetch entire tables
- **Use data change triggers carefully** — high-frequency changes can overwhelm flows
- **Separate enrichment from logging** — Use separate flows triggered by data changes to prevent bottlenecks in the primary write path
- **Use interval-based queries** (last 30 min) rather than full-day queries for frequent schedules

## 9. Flow Documentation Standard

When documenting a flow, include:
1. **Flow Name & Purpose** — What does this flow do and why
2. **Trigger Type** — Schedule, webhook, data change, manual
3. **Node-by-Node Description** — ID, type, purpose, key configuration
4. **Data Dependencies** — Which models/tables are queried and mutated
5. **Error Handling** — What happens when things fail
6. **Timezone Considerations** — How timestamps are handled
7. **Debug/Test Notes** — Debug sources, breakpoints, test payloads
