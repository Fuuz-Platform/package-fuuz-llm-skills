---
name: fuuz-mcp-tools
description: Guide for building MCP (Model Context Protocol) tools in Fuuz. Covers MCP tool flow patterns, context window optimization through data compression, and integration standards. Use when requests mention "MCP tool", "MCP server", "LLM tool", "agent tool", "context window optimization", "data compression for LLM", "Model Context Protocol", "MCP flow", "MCP configuration", "inputSchema", "outputSchema", "readOnly tool", "destructive tool", or building data flows that are exposed as tools for LLM agents. This skill is the authoritative reference for turning Fuuz data flows into MCP-compliant tools with optimized output for AI consumption.
---

# Fuuz MCP Tools Skill

**Version 1.2.0** | Last Updated: 2026-02-26

## Purpose

Build Fuuz data flows that are exposed as MCP (Model Context Protocol) tools for LLM agents. This document covers MCP tool flow architecture, the critical context window optimization pattern for read operations, and the read/write asymmetry rule that ensures data integrity when writing back to Fuuz.

---

## Golden Rules

1. **Every MCP tool is a data flow with a Request/Response pattern** -- the flow must have a `request` node as entry and a `response` node as exit.
2. **MCP configuration lives on the DataFlow record** -- the `mcpToolConfiguration` field on a DataFlow contains `enabled`, `readOnly`, `destructive`, `idempotent`, `inputSchema`, and `outputSchema`.
3. **READ tools MUST compress output for LLM consumption** -- use JSONata or JavaScript transforms to strip unnecessary fields, flatten structures, and produce compact markdown or summary JSON. This is the single most important pattern in this skill.
4. **WRITE tools MUST use exact Fuuz JSON structures** -- never compress or simplify mutation payloads. Follow the standards in fuuz-schema, fuuz-flows, fuuz-screens, and fuuz-packages for all write operations.
5. **One tool = one flow = one purpose** -- each MCP tool should do exactly one thing. Do not create multi-purpose tools with mode-switching payloads.
6. **inputSchema and outputSchema must be valid JSON Schema** -- these schemas tell the LLM what to send and what to expect back. Be precise with types, required fields, and descriptions.
7. **Always include error handling** -- use `tryCatch` nodes to catch errors and return structured error responses via the `response` node, not unhandled exceptions.
8. **Name MCP tools descriptively** -- the flow name IS the tool name the LLM sees. Use clear, action-oriented names: `list_data_models`, `get_model_details`, `create_work_order`, `query_inventory`.
9. **Set MCP flags accurately** -- `readOnly: true` for query-only tools; `destructive: true` only for tools that delete or irreversibly modify data; `idempotent: true` for tools that produce the same result when called multiple times with the same input.
10. **Compress metadata extractions to markdown format** -- when extracting schema, screen, flow, or document definitions, compress them into the concise markdown format demonstrated by the WMS data schema pattern (see Section 3).
11. **Keep tool output under 4,000 tokens when possible** -- LLM context windows are limited. Compressed output preserves room for the agent's reasoning and subsequent tool calls.
12. **Use summary metrics at the top of compressed output** -- start with a count table (total models, total fields, total relationships) so the LLM can quickly assess scope before reading details.
13. **Omit IDs when names suffice** -- in compressed read output, use human-readable names instead of internal CUIDs. The LLM does not need `id: "clx7abc123"` when `name: "WorkOrder"` conveys the same meaning.
14. **Include relationship summaries as tree diagrams** -- use ASCII tree format to show parent-child relationships in compressed output. This is far more compact than listing individual FK fields.
15. **Reference other Fuuz skills for write operation formats** -- this skill does NOT redefine how to build GraphQL mutations, screen JSON, or package files. Defer to the authoritative skill for each domain.
16. **Use `$query()` and `$mutate()` bindings for dynamic GraphQL** -- when the GraphQL statement must be built at runtime (dynamic query/mutate tools), execute via `$query({statement, variables})` or `$mutate({statement, variables})` inside `transform` nodes instead of using `query`/`mutate` nodes.
17. **All mutations use the payload array pattern** -- Fuuz mutations wrap operations in a `payload` array: `[{ "create": data }]`, `[{ "where": { "id": id }, "update": data }]`, or `[{ "where": { "id": id } }]` for delete. The `where` in mutations uses a direct id value, NOT `{ "_eq": id }` like query filters.

---

## 1. Overview

### What Are Fuuz MCP Tools?

Fuuz data flows can be configured as MCP (Model Context Protocol) tools, making them callable by LLM agents (Claude, GPT, etc.) through the Fuuz MCP server. When an LLM agent needs to interact with a Fuuz application -- querying data models, reading screen configurations, creating records, or executing business logic -- it calls these tools.

A data flow becomes an MCP tool when:
1. The flow uses a **Request** node as its entry point and a **Response** node as its exit
2. The flow's `mcpToolConfiguration` is set with `enabled: true`
3. The flow has valid `inputSchema` and `outputSchema` defining its contract

The MCP server matches LLM agent requests to available tools based on the tool name (flow name), description (flow description), and input/output schemas.

### When to Use This Skill

- Building new data flows intended for LLM agent consumption
- Converting existing request/response flows into MCP tools
- Optimizing MCP tool output for context window efficiency
- Designing input/output schemas for MCP tools
- Understanding the read vs write asymmetry rule

### When NOT to Use This Skill

- Building standard backend flows (schedules, data changes) -- use **fuuz-flows**
- Designing data models -- use **fuuz-schema**
- Building screen JSON -- use **fuuz-screens**
- Creating packages -- use **fuuz-packages**
- General platform guidance -- use **fuuz-platform**

---

## 2. MCP Tool Flow Architecture

### Flow Structure

Every MCP tool follows this fundamental pattern:

```
Request Node --> TryCatch
                  Try path:
                    --> Validate (JSON Schema) --> Query / Process --> Transform (JSONata) --> Response Node
                  Catch path:
                    --> Error Response (uses $state.lastError)
```

### Node Configuration

**Request Node** (entry point):
```json
{
  "id": "request-1",
  "type": "request",
  "data": {
    "nextNodes": ["validate-1"]
  }
}
```

The request node receives the payload from the LLM agent. The payload shape is defined by the `inputSchema` in the MCP configuration.

**Response Node** (exit point):
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

The `responseTransform` is a JSONata expression that shapes the final response. Use `"$"` to pass through the current payload as-is, or provide a transform to reshape it.

### Standard MCP Tool Flow Pattern

```
debugSource-1 (DebugSource - test payload for development)
  --> request-1 (Request)
    --> tryCatch-1 (TryCatch)
          tryNextNodes:
            --> validate-1 (Validate - JSON Schema validation)
              --> query-1 (Query - fetch data from Fuuz GraphQL)
                --> transform-1 (Transform - JSONata compression/formatting)
                  --> response-1 (Response - return to LLM)
          catchNextNodes:
            --> error-response-1 (Response - return error using $state.lastError)
```

> **Note:** Simple tools (e.g., a single external API call) may omit TryCatch and Validate nodes entirely. See the `http` node pattern for minimal flows.

### Error Handling Pattern

Always wrap MCP tool logic in a `tryCatch` node. The TryCatch uses `tryNextNodes` for the happy path and `catchNextNodes` for the error path. The catch path goes directly to a `response` node that formats the error using `$state.lastError`:

```json
{
  "id": "tryCatch-1",
  "type": "tryCatch",
  "data": {
    "tryNextNodes": ["validate-1"],
    "catchNextNodes": ["error-response-1"],
    "nextNodes": []
  }
}
```

```json
{
  "id": "error-response-1",
  "type": "response",
  "data": {
    "responseTransform": "{\n  \"error\": true,\n  \"errorType\": \"EXECUTION_ERROR\",\n  \"errorMessage\": $state.lastError.message ? $state.lastError.message : \"execution failed\",\n  \"errorDetails\": $state.lastError\n}",
    "nextNodes": []
  }
}
```

Key details:
- `$state.lastError` contains the caught error object
- `$state.lastError.message` contains the error message string
- Use `error: true` (not `success: false`) and `errorType`/`errorMessage`/`errorDetails` fields
- No separate error transform node is needed -- the `responseTransform` on the response node handles formatting

This ensures the LLM always receives a parseable response, even when errors occur.

---

## 3. Context Window Optimization (CRITICAL)

This is the most important section of this skill. LLM context windows are finite (typically 100K-200K tokens). Every token of MCP tool output consumes context that could be used for reasoning, planning, or additional tool calls. Compressing tool output is not optional -- it is a core design requirement for effective MCP tools.

### The Compression Pattern

```
Raw Data (verbose JSON from GraphQL)
  --> Transform (JSONata or JavaScript)
    --> Compressed Output (compact markdown or minimal JSON)
```

**Before compression** (raw GraphQL response for a single model):
```json
{
  "data": {
    "dataModel": {
      "id": "clx7abc123",
      "name": "Inventory",
      "header": { "id": "inventory", "name": "Inventory", "description": "Core inventory model", "dataModelKindId": "reference" },
      "version": { "id": "inventory_v1", "number": "1", "dataModelId": "inventory", "modelDefinition": { ... } },
      "fields": [
        { "id": "f1", "name": "id", "type": "ID!", "metadata": { "create": { "include": true }, "update": { "include": false }, ... } },
        { "id": "f2", "name": "serialNumber", "type": "String!", "metadata": { "unique": true, "create": { "include": true }, "update": { "include": true }, ... } },
        ...50 more fields with full metadata...
      ]
    }
  }
}
```

**After compression** (optimized for LLM consumption):
```markdown
## Inventory
Core inventory model. **Data changes tracked for 25 years**.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `serialNumber` | String! | unique |
| `active` | Boolean! | |
| `quantity` | Measure! | required |
| `product` | Product | relationship |
| `productId` | ID | |
| `lot` | Lot | relationship |
| `lotId` | ID | |
...
```

The compressed version is **5-10x smaller** while retaining all information the LLM needs for reasoning and code generation.

### Compression Techniques

#### 1. Summary Metrics at the Top

Always start compressed output with a summary table:

```markdown
| Metric | Value |
|--------|-------|
| **Total Models** | 37 |
| **Total Relationships** | 124 |
| **Total Fields** | 556 |
| **Custom Models** | 36 |
```

This lets the LLM quickly assess scope without reading every detail.

#### 2. Compact Field Tables

Strip metadata to essential columns only:

| Column | Include? | Why |
|--------|----------|-----|
| Field name | Yes | Essential for reference |
| Type | Yes | Essential for query/mutation building |
| Constraints (unique, required) | Yes | Critical for validation |
| Create/update include flags | No | Verbose, rarely needed by LLM |
| Internal IDs | No | LLM uses names, not CUIDs |
| Full metadata objects | No | Too verbose, extract key constraints instead |

#### 3. Relationship Summaries as Trees

Replace verbose FK field listings with ASCII tree diagrams:

```
Area
  --> StorageZone (1:N)
        --> StorageUnit (1:N)
              --> Inventory (1:N)

Product
  |-- ProductCategory (N:1)
  |-- Lot (1:N)
  |-- Inventory (1:N)
  +-- OrderLine (1:N)
```

#### 4. Group by Functional Module

Organize models by module rather than alphabetically. This gives the LLM functional context:

```markdown
### Inventory Tracking
- Inventory (core stock tracking, 25yr retention)
- InventoryTrace (transaction audit trail)
- Lot (batch/heat groupings)

### Order Processing
- Order (order headers)
- OrderLine (line items)
- OrderLineRelease (fulfillment schedules)
```

#### 5. Omit IDs When Names Suffice

```markdown
-- BAD (wastes tokens):
Model: Inventory (id: clx7abc123, dataModelId: inventory, versionId: inventory_v1)

-- GOOD (compact):
Model: Inventory
```

#### 6. Summarize Common Patterns Once

Instead of repeating `customData: JSON, integrationData: JSON` on every model, note it once:

```markdown
> **Standard extension fields** (present on all models): `customData` (JSON), `integrationData` (JSON)
```

### What to Compress

| Fuuz Artifact | Compress For LLM? | Approach |
|---------------|--------------------|----------|
| Data model definitions | Yes | Field tables, relationship trees, module groupings |
| Screen configurations | Yes | Element hierarchy, data bindings summary, action lists |
| Flow definitions | Yes | Node pipeline diagram, key transforms, trigger/output summary |
| Document designs | Yes | Section layout, data field mappings, conditional logic summary |
| Query results (records) | Sometimes | Depends on volume -- summarize large result sets, pass through small ones |
| Enum/lookup values | Minimal | List code + description, omit metadata |

### The WMS Schema Example

The file `fuuz-wms-data-schema.md` demonstrates the gold standard for compressed data model output. Key elements:

1. **Auto-generated header** with source attribution
2. **Summary metrics table** (37 models, 124 relationships, 556 fields)
3. **Module listing** with one-line descriptions
4. **Models grouped by module** with compact field tables
5. **Relationship tree diagram** showing key hierarchies
6. **Data retention notes** for audit-critical models
7. **Common field patterns** documented once (identifiers, extension fields, status flags, measurement types)

When building MCP tools that extract application metadata, use this format as your template.

### JSONata Compression Transform Example

This JSONata expression compresses a raw data model query into the compact markdown format:

```jsonata
(
  $models := $.data.dataModels;
  $totalFields := $sum($models.fields.$count($));
  $totalRelations := $sum($models.fields[$contains(type, "[") or $contains(type, "!")  and $not($contains(type, "String")) and $not($contains(type, "Boolean")) and $not($contains(type, "Int")) and $not($contains(type, "Float")) and $not($contains(type, "ID")) and $not($contains(type, "DateTime")) and $not($contains(type, "JSON"))].($count($)));

  "# Data Schema Summary\n\n" &
  "| Metric | Value |\n|--------|-------|\n" &
  "| Total Models | " & $string($count($models)) & " |\n" &
  "| Total Fields | " & $string($totalFields) & " |\n\n" &

  $join($models.(
    "## " & name & "\n" &
    (description ? description & "\n\n" : "\n") &
    "| Field | Type | Constraints |\n|-------|------|-------------|\n" &
    $join(fields.(
      "| `" & name & "` | " & type & " | " &
      ($contains(metadata, "unique") ? "unique " : "") &
      ($contains(type, "!") ? "required " : "") &
      " |\n"
    )) & "\n"
  ))
)
```

### JavaScript Compression Transform Example

For more complex compression logic, use a JavaScript transform node:

```javascript
var models = $.data.dataModels;
var output = "# Data Schema Summary\n\n";

// Summary metrics
output += "| Metric | Value |\n|--------|-------|\n";
output += "| Total Models | " + models.length + " |\n";

var totalFields = 0;
for (var i = 0; i < models.length; i++) {
  totalFields += models[i].fields.length;
}
output += "| Total Fields | " + totalFields + " |\n\n";

// Model details
for (var i = 0; i < models.length; i++) {
  var model = models[i];
  output += "## " + model.name + "\n";
  if (model.description) output += model.description + "\n\n";

  output += "| Field | Type | Constraints |\n";
  output += "|-------|------|-------------|\n";

  for (var j = 0; j < model.fields.length; j++) {
    var field = model.fields[j];
    var constraints = [];
    if (field.metadata && field.metadata.unique) constraints.push("unique");
    if (field.type && field.type.indexOf("!") > -1) constraints.push("required");
    if (field.type && (field.type.indexOf("[") > -1 || (field.type.indexOf("!") === -1 && field.type !== "String" && field.type !== "Boolean" && field.type !== "Int" && field.type !== "Float" && field.type !== "ID" && field.type !== "DateTime" && field.type !== "JSON"))) {
      constraints.push("relationship");
    }
    output += "| `" + field.name + "` | " + field.type + " | " + constraints.join(", ") + " |\n";
  }
  output += "\n";
}

return output;
```

---

## 4. Read vs Write Asymmetry (CRITICAL RULE)

This is a fundamental design principle for all Fuuz MCP tools:

### READ Operations: Compress Output

When a tool **reads** data from Fuuz (queries, listings, metadata extraction):
- Compress the output using the techniques in Section 3
- Strip internal IDs, verbose metadata, audit fields
- Use markdown format for structured data
- Summarize where possible
- The goal is minimal token usage while preserving information content

### WRITE Operations: Use Exact JSON Structures

When a tool **writes** data to Fuuz (mutations, upserts, creates):
- The LLM MUST push JSON objects back to Fuuz following **strictly** the design standards outlined in the other Fuuz skills
- Never compress, simplify, or abbreviate mutation payloads
- Use the exact field names, types, and structures that the Fuuz GraphQL API expects
- Reference these skills for write formats:
  - **fuuz-schema** -- data model field types, naming conventions, relationship structures
  - **fuuz-flows** -- query/mutate node configuration, GraphQL query/mutation syntax
  - **fuuz-screens** -- screen element JSON structure, action configurations
  - **fuuz-packages** -- package-data.json structure, manifest.json, definition.json

### Why This Asymmetry Exists

The LLM is the **consumer** of read output -- it needs compact, readable data to reason about.

The Fuuz GraphQL API is the **consumer** of write output -- it needs exact JSON matching its strict schema validation.

Compressing writes would cause mutations to fail. Leaving reads verbose would waste context window space.

### Example: Read vs Write for the Same Model

**READ tool output** (compressed for LLM):
```markdown
## WorkOrder
Production work order tracking. Transactional, 10yr retention.

| Field | Type | Constraints |
|-------|------|-------------|
| `id` | ID! | unique |
| `number` | String! | unique, sequence |
| `product` | Product! | relationship |
| `productId` | ID! | required |
| `status` | WorkOrderStatus! | relationship |
| `statusId` | ID! | required |
| `quantity` | Measure! | required |
| `scheduledStartAt` | DateTime | |
| `completedAt` | DateTime | |
```

**WRITE tool input** (exact Fuuz JSON for creating a work order):
```json
{
  "query": "mutation CreateWorkOrder($input: WorkOrderCreateInput!) { createWorkOrder(input: $input) { id number } }",
  "variables": {
    "input": {
      "productId": "clx8prod456",
      "statusId": "NEW",
      "quantity": { "value": 100, "unitId": "EA" },
      "scheduledStartAt": "2026-03-01T08:00:00Z"
    }
  }
}
```

---

## 5. MCP Tool Configuration

### DataFlowMCPToolConfiguration Schema

The MCP configuration is stored on the DataFlow record in the system schema:

```graphql
type DataFlowMCPToolConfiguration {
  enabled: Boolean!            # Whether this flow is exposed as an MCP tool
  inputSchema: JSONObject      # JSON Schema defining the tool's input
  outputSchema: JSONObject     # JSON Schema defining the tool's output
  readOnly: Boolean!           # If true, the tool does not modify its environment
  destructive: Boolean!        # If true, the tool may perform destructive updates
  idempotent: Boolean!         # If true, repeated calls with same args have no additional effect
}
```

### Configuration Fields

| Field | Type | Description |
|-------|------|-------------|
| `enabled` | Boolean! | Set to `true` to expose the flow as an MCP tool. The MCP server only registers enabled tools. |
| `inputSchema` | JSONObject | A JSON Schema object defining what the LLM must send. Used by the agent to format input and select the correct tool. |
| `outputSchema` | JSONObject | A JSON Schema object defining what the tool returns. The flow output is validated against this before returning to the agent. |
| `readOnly` | Boolean! | `true` = tool only reads data, no side effects. `false` = tool modifies data. |
| `destructive` | Boolean! | `true` = tool may delete or irreversibly modify data. Only meaningful when `readOnly` is `false`. |
| `idempotent` | Boolean! | `true` = calling the tool N times with the same input has the same effect as calling it once. Only meaningful when `readOnly` is `false`. |

### Flag Combinations

| Tool Type | readOnly | destructive | idempotent | Example |
|-----------|----------|-------------|------------|---------|
| Query/List | `true` | `false` | `true` | List all data models |
| Get Details | `true` | `false` | `true` | Get model fields |
| Create Record | `false` | `false` | `false` | Create work order |
| Update Record | `false` | `false` | `true` | Update order status |
| Upsert Record | `false` | `false` | `true` | Upsert product |
| Delete Record | `false` | `true` | `true` | Delete draft record |

### Naming Conventions

MCP tool names are derived from the flow name. Use these conventions:

| Pattern | Convention | Examples |
|---------|-----------|----------|
| List/query tools | `list_{entity}` or `query_{entity}` | `list_data_models`, `query_inventory` |
| Detail tools | `get_{entity}_details` | `get_model_details`, `get_screen_config` |
| Create tools | `create_{entity}` | `create_work_order`, `create_product` |
| Update tools | `update_{entity}` | `update_order_status`, `update_recipe` |
| Delete tools | `delete_{entity}` | `delete_draft_batch` |
| Action tools | `{verb}_{entity}` | `execute_quality_check`, `calculate_oee` |
| Schema tools | `describe_{entity}` | `describe_data_model`, `describe_flow` |

### Input Schema Best Practices

```json
{
  "type": "object",
  "properties": {
    "modelName": {
      "type": "string",
      "description": "The PascalCase name of the data model to query (e.g., 'WorkOrder', 'Inventory')"
    },
    "includeRelationships": {
      "type": "boolean",
      "description": "Whether to include relationship fields in the output. Defaults to true.",
      "default": true
    },
    "limit": {
      "type": "integer",
      "description": "Maximum number of records to return. Defaults to 50.",
      "default": 50,
      "minimum": 1,
      "maximum": 500
    }
  },
  "required": ["modelName"]
}
```

Key principles:
- Use `description` on every property -- the LLM reads these to understand how to call the tool
- Provide `default` values for optional parameters
- Use `enum` for constrained choices
- Mark truly required fields in the `required` array
- Keep the schema as simple as possible -- complex nested objects make it harder for the LLM to construct correct input

### Output Schema Best Practices

```json
{
  "type": "object",
  "properties": {
    "data": {
      "type": "string",
      "description": "Compressed markdown representation of the query results"
    },
    "error": {
      "type": "boolean",
      "description": "True if the operation failed"
    },
    "errorType": {
      "type": "string",
      "description": "Error category (e.g., EXECUTION_ERROR, VALIDATION_ERROR)"
    },
    "errorMessage": {
      "type": "string",
      "description": "Human-readable error message if the operation failed"
    },
    "errorDetails": {
      "type": "object",
      "description": "Full error object for debugging"
    }
  }
}
```

For read tools that return compressed markdown, the output schema can simply declare a string `data` field. The LLM will parse the markdown content naturally. Error fields (`error`, `errorType`, `errorMessage`, `errorDetails`) are populated only on failure via the `$state.lastError` pattern in the catch path.

---

## 6. Standard MCP Tool Patterns

### Pattern 1: Model Listing Tool

**Purpose:** List all data models in the application with compact summaries.

**MCP Configuration:**
- `readOnly: true`, `destructive: false`, `idempotent: true`
- Input: optional filter parameters (module name, search string)
- Output: compressed markdown with model list

**Flow:**
```
request-1 --> tryCatch-1
                tryNextNodes: query-1 (fetch all models) --> transform-1 (compress to markdown) --> response-1
                catchNextNodes: error-response-1 (uses $state.lastError)
```

**Query node** -- fetch models from System API:
```json
{
  "id": "query-1",
  "type": "query",
  "data": {
    "api": "system",
    "query": "query($where: ModelDefinitionWhereInput, $first: Int) { modelDefinitions(where: $where, first: $first) { total edges { node { id name description kind } } } }",
    "variablesTransform": "{ \"first\": 100, \"where\": $isNilOrEmpty(payload.module) ? undefined : { \"module\": { \"_eq\": payload.module } } }",
    "nextNodes": ["transform-1"]
  }
}
```

**Transform node** -- compress to markdown:
```json
{
  "id": "transform-1",
  "type": "transform",
  "data": {
    "transform": "( $models := data.modelDefinitions.edges.node; { \"data\": \"# Data Models (\" & $string($count($models)) & \")\\n\\n| Model | Description |\\n|-------|-------------|\\n\" & $join($models.(\"| \" & name & \" | \" & (description ? description : \"-\") & \" |\\n\")) } )",
    "nextNodes": ["response-1"]
  }
}
```

### Pattern 2: Model Details Tool

**Purpose:** Get detailed field information for a specific model.

**MCP Configuration:**
- `readOnly: true`, `destructive: false`, `idempotent: true`
- Input: `modelName` (string, required)
- Output: compressed markdown with field table and relationships

**Flow:**
```
request-1 --> tryCatch-1
                tryNextNodes: validate-1 (require modelName) --> query-1 (fetch model by name with fields)
                  --> transform-1 (compress fields to markdown table) --> response-1
                catchNextNodes: error-response-1 (uses $state.lastError)
```

**Transform node** -- compress model details:
```json
{
  "id": "transform-1",
  "type": "transform",
  "data": {
    "transform": "( $model := data.modelDefinitions.edges.node[0]; $fields := $model.fields; { \"data\": \"## \" & $model.name & \"\\n\" & ($model.description ? $model.description & \"\\n\\n\" : \"\\n\") & \"| Field | Type | Constraints |\\n|-------|------|-------------|\\n\" & $join($fields.(\"| `\" & name & \"` | \" & type & \" | \" & (required ? \"required \" : \"\") & \" |\\n\")) } )",
    "nextNodes": ["response-1"]
  }
}
```

### Pattern 3: Dynamic Query Tool

**Purpose:** Execute a parameterized query against any model with runtime field selection, filtering, and pagination. Fetches the model schema first, validates fields, then builds and executes a GraphQL query dynamically.

**MCP Configuration:**
- `readOnly: true`, `destructive: false`, `idempotent: true`
- Input: `modelName`, `fields` (array), `filter` (object), `orderBy` (object), `limit` (integer), `after` (cursor string)
- Output: query results with pagination metadata and optional field warnings

**Flow (multi-step transform pipeline -- uses `$query()` binding, NO query node):**
```
request-1 --> tryCatch-1
                tryNextNodes: validate-1
                  --> transform-fetchSchema (fetch model schema via $query())
                    --> transform-buildFields (validate requested fields against schema)
                      --> transform-buildQuery (construct dynamic GraphQL string)
                        --> transform-execute (run query via $query())
                          --> transform-formatResponse (format output with pagination)
                            --> response-1
                catchNextNodes: error-response-1 (with "failedAt" diagnostic)
```

The key here is the **multi-step transform pipeline** pattern. Each `transform` node adds underscore-prefixed state via `$merge([$, {...}])`, progressively building up `_model`, `_schema`, `_fields`, `_query`, `_variables`, and `_result`. The `$query()` binding executes GraphQL inline within transforms, allowing the query string to be constructed dynamically at runtime.

See **Example F** in the patterns reference for the complete node-by-node implementation.

### Pattern 4: Dynamic Mutation Tool

**Purpose:** Execute create/update/delete mutations on any data model with schema validation and optional dryRun mode.

**MCP Configuration:**
- `readOnly: false`, `destructive: true`, `idempotent: false`
- Input: `modelName`, `operation` (create/update/delete), `data` (object), `id` (for update/delete), `returnFields` (array), `dryRun` (boolean)
- Output: mutation result or dryRun validation report

**CRITICAL:** All mutations use the **payload array pattern**: `[{ "create": data }]`, `[{ "where": { "id": id }, "update": data }]`, or `[{ "where": { "id": id } }]`. The mutation `where` clause uses a direct id value (string), NOT `{ "_eq": id }` like query filters.

**Flow (multi-step transform pipeline -- uses `$mutate()` binding, NO mutate node):**
```
request-1 --> tryCatch-1
                tryNextNodes: validate-1
                  --> transform-fetchSchema (fetch model schema via $query())
                    --> transform-validateOp (validate operation against schema)
                      --> transform-buildMutation (construct dynamic mutation string)
                        --> transform-execute ($mutate() or dryRun introspection)
                          --> transform-formatResponse (format result)
                            --> response-1
                catchNextNodes: error-response-1 (with "failedAt" diagnostic)
```

When `dryRun: true`, the execute step uses GraphQL introspection (`__type`) to validate the mutation input type exists and check required fields -- without actually executing the mutation. This is critical for safe LLM exploration.

See **Example G** in the patterns reference for the complete implementation.

### Pattern 5: External API Integration Tool

**Purpose:** Call an external API via the Fuuz HTTP connector and return formatted results.

**MCP Configuration:**
- `readOnly: true` (for GET operations), `destructive: false`, `idempotent: true`
- Input: API-specific parameters
- Output: compressed response

**Flow (simple -- no error handling needed for basic GET):**
```
request-1 --> http-1 (http node - call external API) --> response-1
```

**Flow (advanced -- with error handling and compression):**
```
request-1 --> tryCatch-1
                tryNextNodes: validate-1 --> http-1 (integrateV2 node) --> transform-1 (compress) --> response-1
                catchNextNodes: error-response-1
```

Three approaches are available for external APIs:
- **`http` node** -- simplest, uses `connectionName`, `httpMethod`, `httpPathTransform`. Best for straightforward single calls.
- **`integrateV2` node** -- more control, uses `connectorId`, `method`, `path`, `headers`, `queryParams`, `body`. Best for complex integrations.
- **`$integrate()` binding** -- most flexible, called from within `transform` nodes. Supports batched requests (payload array), parallel execution, and all 42 Fuuz connectors. See `integration-service-reference.md` in fuuz-platform for per-connector payload schemas.

### Pattern 6: Application Metadata Extraction Tool

**Purpose:** Extract and compress the full application schema, screen list, flow list, or document designs for LLM consumption.

This is the pattern demonstrated by `fuuz-wms-data-schema.md`. The tool queries ALL models (or screens, flows, etc.) and compresses them into the standardized markdown format.

**MCP Configuration:**
- `readOnly: true`, `destructive: false`, `idempotent: true`
- Input: optional `module` filter, optional `format` (markdown/json)
- Output: compressed markdown following the WMS schema format

**Flow:**
```
request-1 --> tryCatch-1
                tryNextNodes: query-1 (fetch all models with full field details)
                  --> compress-1 (JavaScript - comprehensive compression)
                    --> response-1
                catchNextNodes: error-response-1 (uses $state.lastError)
```

Use a **JavaScript transform** (not JSONata) for this pattern because the compression logic is complex:
- Group models by module
- Count metrics (models, fields, relationships)
- Build compact field tables
- Generate relationship tree diagrams
- Summarize data retention policies
- Note common field patterns

See `references/mcp-tool-patterns.md` for the complete JavaScript compression function.

---

## 7. Packaging MCP Tools

MCP tool flows are packaged and distributed like any other Fuuz data flow. Refer to the **fuuz-packages** skill for the complete guide to creating `.fuuz` packages.

Key notes for MCP tool packages:

1. **Flow definitions go in `package-data.json` under `dataFlows`** -- each MCP tool flow is a standard data flow definition
2. **MCP configuration is part of the flow metadata** -- the `mcpToolConfiguration` is stored on the DataFlow record, not in the flow's node graph
3. **Input/output schemas should be documented** -- include the JSON Schema definitions in the flow description or in package documentation
4. **Test tools with the MCP server before packaging** -- ensure the tool works end-to-end with an LLM agent before creating the package
5. **Version MCP tool packages independently** -- MCP tools change more frequently than data models; keep them in separate packages per the fuuz-packages skill's scope strategy

### Package Scope for MCP Tools

| Package | Contains |
|---------|----------|
| Data model package | `dataModels` + `data` (installed first) |
| MCP tools package | `dataFlows` with MCP configuration (installed after models) |

---

## 8. Related Skills

| Skill | Relationship to MCP Tools |
|-------|--------------------------|
| **fuuz-flows** | Foundation for all MCP tools -- covers flow node types, JSONata/JavaScript transforms, query/mutate patterns, error handling. Read this skill first. |
| **fuuz-schema** | Defines data model structure that MCP tools query and mutate. Write tools must follow schema naming conventions and field types exactly. |
| **fuuz-screens** | Screen JSON structure that MCP tools may need to read/describe. Screen extraction tools should compress screen configs using Section 3 patterns. |
| **fuuz-packages** | Package format for distributing MCP tool flows. MCP tools are packaged as standard data flows. |
| **fuuz-platform** | General platform guidance including MCP enablement. MCP must be enabled for the tenant before tools are accessible. |
| **fuuz-industrial-ops** | Domain-specific patterns (OEE, telemetry, production) that MCP tools often need to implement. |

---

## Appendix: Quick Reference

### MCP Tool Checklist

Before deploying an MCP tool, verify:

- [ ] Flow has a `request` node as entry point
- [ ] Flow has a `response` node as exit point (including error paths)
- [ ] Flow is wrapped in a `tryCatch` node (using `tryNextNodes`/`catchNextNodes`) for error handling
- [ ] Flow includes a `debugSource` node for testing with sample payloads
- [ ] Input validation uses a `validate` node with JSON Schema (not JSONata `$error()`)
- [ ] `mcpToolConfiguration.enabled` is `true`
- [ ] `inputSchema` is valid JSON Schema with descriptive field descriptions
- [ ] `outputSchema` is valid JSON Schema
- [ ] `readOnly`, `destructive`, and `idempotent` flags are set correctly
- [ ] Flow name is descriptive and follows naming conventions
- [ ] Flow description clearly explains what the tool does
- [ ] READ tools compress output (markdown format, summary metrics, compact tables)
- [ ] WRITE tools pass through exact Fuuz JSON without compression
- [ ] Query nodes use `variablesTransform` (JSONata) not `variables` (static string)
- [ ] Error responses use `$state.lastError` pattern with `error: true`, `errorType`, `errorMessage`, `errorDetails`
- [ ] Dynamic query/mutate tools use `$query()`/`$mutate()` bindings in transform nodes (not query/mutate nodes)
- [ ] Write tools use the payload array pattern: `[{ "create": data }]`, `[{ "where": { "id": id }, "update": data }]`, or `[{ "where": { "id": id } }]`
- [ ] Multi-step transform pipelines use `$merge([$, {...}])` with underscore-prefixed keys for progressive state accumulation
- [ ] Tool has been tested end-to-end with an LLM agent (use `debugSource` node for flow designer testing)
- [ ] Output stays under ~4,000 tokens for typical inputs

### Token Budget Guidelines

| Output Type | Target Tokens | Technique |
|-------------|---------------|-----------|
| Model list (10 models) | 200-400 | Name + description table |
| Model details (1 model, 30 fields) | 400-800 | Compact field table |
| Full schema (30 models) | 2,000-4,000 | Grouped markdown with trees |
| Query results (50 records) | 500-2,000 | Tabular with key fields only |
| Error response | 50-100 | Structured error JSON |
