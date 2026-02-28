# MCP Tool Patterns Reference

Concrete examples for building Fuuz MCP tools. This file contains complete node configurations, input/output schemas, compression transforms, and error handling patterns. Updated with corrections from real production MCP packages (Example-MCPTools@0.0.1, MCP Tool Examples@0.0.2).

---

## 1. Flow Node Structures for MCP Tools

### Complete Request/Response Flow Skeleton

Every MCP tool flow starts with this skeleton. Copy and customize for each tool.

```json
{
  "nodes": [
    {
      "id": "debugSource-1",
      "name": "Debug Source",
      "type": "debugSource",
      "data": {
        "payload": "{ \"id\": \"test-123\", \"name\": \"Sample\" }",
        "nextNodes": ["request-1"]
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
    },
    {
      "id": "request-1",
      "name": "Receive Tool Input",
      "type": "request",
      "data": {
        "nextNodes": ["tryCatch-1"]
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
    },
    {
      "id": "tryCatch-1",
      "name": "Error Boundary",
      "type": "tryCatch",
      "data": {
        "tryNextNodes": ["validate-1"],
        "catchNextNodes": ["error-response-1"],
        "nextNodes": []
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
    },
    {
      "id": "validate-1",
      "name": "Validate Input",
      "type": "validate",
      "data": {
        "schema": {
          "type": "object",
          "additionalProperties": true,
          "required": ["payload"],
          "properties": {
            "payload": {
              "type": "object",
              "additionalProperties": true,
              "required": ["modelName"],
              "properties": {
                "modelName": {
                  "type": "string",
                  "description": "The name of the model to query",
                  "minLength": 1,
                  "example": "WorkOrder"
                },
                "limit": {
                  "type": "number",
                  "description": "Maximum records to return",
                  "default": 50
                }
              }
            }
          }
        },
        "nextNodes": ["query-1"]
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
    },
    {
      "id": "query-1",
      "name": "Query Fuuz Data",
      "type": "query",
      "data": {
        "api": "application",
        "query": "query($where: WorkOrderWhereInput, $first: Int) { workOrder(where: $where, first: $first) { total edges { node { id number status { code } product { name } quantity { value } } } } }",
        "variablesTransform": "{ \"where\": payload.filter, \"first\": payload.limit ? payload.limit : 50 }",
        "nextNodes": ["transform-1"]
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
    },
    {
      "id": "transform-1",
      "name": "Compress Output",
      "type": "transform",
      "data": {
        "transform": "( $records := data.workOrder.edges.node; { \"totalRecords\": data.workOrder.total, \"data\": \"# Work Orders (\" & $string($count($records)) & \")\\n\\n| Number | Product | Status | Qty |\\n|--------|---------|--------|-----|\\n\" & $join($records.(\"| \" & number & \" | \" & product.name & \" | \" & status.code & \" | \" & $string(quantity.value) & \" |\\n\")) } )",
        "nextNodes": ["response-1"]
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
    },
    {
      "id": "response-1",
      "name": "Return Result",
      "type": "response",
      "data": {
        "responseTransform": "$",
        "nextNodes": []
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
    },
    {
      "id": "error-response-1",
      "name": "Return Error",
      "type": "response",
      "data": {
        "responseTransform": "{\n  \"error\": true,\n  \"errorType\": \"EXECUTION_ERROR\",\n  \"errorMessage\": $state.lastError.message ? $state.lastError.message : \"execution failed\",\n  \"errorDetails\": $state.lastError\n}",
        "nextNodes": []
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
  ]
}
```

### Key Corrections from Production Flows

| What | Incorrect (old) | Correct (production) |
|------|-----------------|---------------------|
| TryCatch child fields | `tryNodes`, `catchNodes` | `tryNextNodes`, `catchNextNodes` |
| Validation | `jsonataTransform` with `$error()` | `validate` node with JSON Schema in `data.schema` |
| Error access | `$.error.message` | `$state.lastError.message` |
| Query variables | `variables` (static JSON string) | `variablesTransform` (JSONata expression) |
| Transform node type | `jsonataTransform` | `transform` with `data.transform` field |
| Error response shape | `{ success: false, error, code }` | `{ error: true, errorType, errorMessage, errorDetails }` |

### debugSource Node

Every real MCP tool flow includes a `debugSource` node for testing with sample payloads. This node feeds test data into the flow during development without needing an actual MCP client call.

```json
{
  "id": "debugSource-1",
  "name": "Debug Source",
  "type": "debugSource",
  "data": {
    "payload": "{ \"id\": \"test-connector-id\", \"name\": \"test\" }",
    "nextNodes": ["request-1"]
  }
}
```

The `payload` field is a JSON string representing the test input. It connects to the `request` node so the flow can be executed directly in the Fuuz flow designer.

### Node Debug and Logging Properties

Every node in production flows includes `debug` and `logging` sections. These control runtime behavior in the flow engine:

```json
{
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

Default convention: all flags `false` except `executionFailed: true` (log failures for debugging).

### Validate Node (JSON Schema)

The `validate` node is a dedicated node type that validates the current payload against a JSON Schema. It does NOT use JSONata `$error()` expressions.

```json
{
  "id": "validate-1",
  "name": "Validate Input",
  "type": "validate",
  "data": {
    "schema": {
      "type": "object",
      "additionalProperties": true,
      "required": ["payload"],
      "properties": {
        "payload": {
          "type": "object",
          "additionalProperties": true,
          "required": ["id"],
          "properties": {
            "id": {
              "type": "string",
              "description": "The ID of the record to fetch",
              "minLength": 1,
              "example": "abc-123"
            }
          }
        }
      }
    },
    "nextNodes": ["query-1"]
  }
}
```

The schema supports full JSON Schema keywords: `type`, `additionalProperties`, `required`, `properties`, `minLength`, `default`, `description`, `example`, `deprecated`, `enum`, etc.

Note: Even an empty schema `{}` is valid for tools that accept any input or have no required parameters.

### Transform Node

The correct node type for JSONata transforms is `"type": "transform"` with the expression in `data.transform`:

```json
{
  "id": "transform-1",
  "name": "Format Output",
  "type": "transform",
  "data": {
    "transform": "( $items := data.results.edges.node; { \"count\": $count($items), \"summary\": $join($items.name, \", \") } )",
    "nextNodes": ["response-1"]
  }
}
```

### Query Node -- Application API (Relay Pagination)

Real Fuuz queries use relay-style `edges.node` pagination:

```json
{
  "id": "query-app-1",
  "name": "Query Application Data",
  "type": "query",
  "data": {
    "api": "application",
    "query": "query($where: InventoryWhereInput, $first: Int) { inventory(where: $where, first: $first) { total edges { node { id serialNumber active quantity { value unit { code } } product { code name } storageUnit { code name } inventoryStatus { code } } } } }",
    "variablesTransform": "{ \"where\": payload.filter, \"first\": payload.limit ? payload.limit : 100 }",
    "nextNodes": ["transform-1"]
  }
}
```

### Query Node -- System API (for metadata extraction)

```json
{
  "id": "query-sys-1",
  "name": "Query Data Models",
  "type": "query",
  "data": {
    "api": "system",
    "query": "query($where: ModelDefinitionWhereInput) { modelDefinitions(where: $where) { total edges { node { id name description fields { name type description metadata } } } } }",
    "variablesTransform": "{}",
    "nextNodes": ["transform-1"]
  }
}
```

### Mutate Node (for Write Tools)

```json
{
  "id": "mutate-1",
  "name": "Create Record",
  "type": "mutate",
  "data": {
    "api": "application",
    "query": "mutation CreateWorkOrder($input: WorkOrderCreateInput!) { createWorkOrder(input: $input) { id number } }",
    "variablesTransform": "{ \"input\": payload.input }",
    "nextNodes": ["response-1"]
  }
}
```

**CRITICAL:** For write tools, the `variablesTransform` expression passes the LLM-provided `payload.input` directly to the mutation. Do NOT transform or compress the input.

### TryCatch Node

```json
{
  "id": "tryCatch-1",
  "name": "Error Boundary",
  "type": "tryCatch",
  "data": {
    "tryNextNodes": ["validate-1"],
    "catchNextNodes": ["error-response-1"],
    "nextNodes": []
  }
}
```

The `tryNextNodes` array contains the first node of the happy path. The `catchNextNodes` array contains the first node of the error path. In production flows, the catch path typically goes directly to a `response` node that uses `$state.lastError` in its `responseTransform`.

### HTTP Node (External API -- Simple)

For simple external API calls, use the `http` node type with `connectionName`:

```json
{
  "id": "http-1",
  "name": "Fetch External Data",
  "type": "http",
  "data": {
    "connectionName": "my-api-connection",
    "httpMethod": "GET",
    "httpPathTransform": "\"/api/v1/resources\"",
    "nextNodes": ["response-1"]
  }
}
```

The `http` node is simpler than `integrateV2` and suitable for straightforward external API calls. The `connectionName` references a pre-configured connection, and `httpPathTransform` is a JSONata expression for the URL path.

### HTTP Integration Node (External API -- Advanced)

For more complex external API integrations, use the `integrateV2` node:

```json
{
  "id": "http-1",
  "name": "Call External API",
  "type": "integrateV2",
  "data": {
    "connectorId": "connector-uuid",
    "method": "GET",
    "path": "/api/v1/resources",
    "headers": "{ \"Content-Type\": \"application/json\" }",
    "queryParams": "{ \"search\": payload.searchTerm }",
    "nextNodes": ["transform-1"]
  }
}
```

### Error Response Pattern (Production Standard)

In production flows, error handling does NOT use a separate transform node. Instead, the catch path goes directly to a `response` node with a `responseTransform` that accesses `$state.lastError`:

```json
{
  "id": "error-response-1",
  "name": "Return Error",
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
- The response includes `error: true` (not `success: false`)
- `errorType` categorizes the error (e.g., `"EXECUTION_ERROR"`, `"VALIDATION_ERROR"`)
- `errorDetails` passes through the full error object for debugging

---

## 2. Input/Output Schema Examples

### Model Listing Tool Schemas

**Input Schema:**
```json
{
  "type": "object",
  "properties": {
    "module": {
      "type": "string",
      "description": "Filter models by module name. Omit to list all models."
    },
    "includeFieldCounts": {
      "type": "boolean",
      "description": "Include field count per model in the output. Defaults to true.",
      "default": true
    }
  },
  "required": []
}
```

**Output Schema:**
```json
{
  "type": "object",
  "properties": {
    "data": {
      "type": "string",
      "description": "Markdown-formatted list of data models with names, descriptions, and field counts"
    },
    "error": {
      "type": "boolean",
      "description": "True if the operation failed"
    },
    "errorType": {
      "type": "string",
      "description": "Error category code if the operation failed"
    },
    "errorMessage": {
      "type": "string",
      "description": "Human-readable error message if the operation failed"
    }
  }
}
```

### Model Details Tool Schemas

**Input Schema:**
```json
{
  "type": "object",
  "properties": {
    "modelName": {
      "type": "string",
      "description": "The PascalCase name of the data model (e.g., 'Inventory', 'WorkOrder', 'Product')"
    },
    "includeRelationships": {
      "type": "boolean",
      "description": "Include relationship fields (FK and inverse relations) in the output. Defaults to true.",
      "default": true
    },
    "includeMetadata": {
      "type": "boolean",
      "description": "Include model metadata (retention, module, indices) in the output. Defaults to false.",
      "default": false
    }
  },
  "required": ["modelName"]
}
```

**Output Schema:**
```json
{
  "type": "object",
  "properties": {
    "data": {
      "type": "string",
      "description": "Markdown-formatted model details including field table, types, constraints, and optional relationship summary"
    },
    "error": {
      "type": "boolean"
    },
    "errorType": {
      "type": "string"
    },
    "errorMessage": {
      "type": "string",
      "description": "Error message if model was not found or query failed"
    }
  }
}
```

### Dynamic Query Tool Schemas

**Input Schema:**
```json
{
  "type": "object",
  "properties": {
    "modelName": {
      "type": "string",
      "description": "The PascalCase name of the model to query (e.g., 'Inventory', 'Order')"
    },
    "fields": {
      "type": "array",
      "items": { "type": "string" },
      "description": "List of field names to include in the query. Use dot notation for nested fields (e.g., 'product.name', 'status.code')."
    },
    "filter": {
      "type": "object",
      "description": "GraphQL filter object matching the model's WhereInput type. Example: { \"active\": { \"equals\": true } }"
    },
    "orderBy": {
      "type": "object",
      "description": "GraphQL ordering. Example: { \"createdAt\": \"desc\" }"
    },
    "limit": {
      "type": "integer",
      "description": "Maximum records to return. Defaults to 50.",
      "default": 50,
      "minimum": 1,
      "maximum": 500
    }
  },
  "required": ["modelName", "fields"]
}
```

**Output Schema:**
```json
{
  "type": "object",
  "properties": {
    "data": {
      "type": "string",
      "description": "Markdown table with queried records"
    },
    "recordCount": {
      "type": "integer",
      "description": "Number of records returned"
    },
    "error": {
      "type": "boolean"
    },
    "errorType": {
      "type": "string"
    },
    "errorMessage": {
      "type": "string"
    }
  }
}
```

### Create Record Tool Schemas

**Input Schema:**
```json
{
  "type": "object",
  "properties": {
    "modelName": {
      "type": "string",
      "description": "The PascalCase name of the model to create a record in (e.g., 'WorkOrder')"
    },
    "input": {
      "type": "object",
      "description": "The create input object matching the model's GraphQL CreateInput type. Must contain all required fields with correct types. Refer to fuuz-schema skill for field naming conventions."
    }
  },
  "required": ["modelName", "input"]
}
```

**Output Schema:**
```json
{
  "type": "object",
  "properties": {
    "data": {
      "type": "object",
      "properties": {
        "id": { "type": "string", "description": "The ID of the created record" },
        "summary": { "type": "string", "description": "Human-readable summary of what was created" }
      }
    },
    "error": {
      "type": "boolean"
    },
    "errorType": {
      "type": "string"
    },
    "errorMessage": {
      "type": "string"
    }
  }
}
```

### Full Schema Extraction Tool Schemas

**Input Schema:**
```json
{
  "type": "object",
  "properties": {
    "module": {
      "type": "string",
      "description": "Filter to a specific module name. Omit to extract the full application schema."
    },
    "includeRetention": {
      "type": "boolean",
      "description": "Include data retention policies in the output. Defaults to true.",
      "default": true
    },
    "includeRelationshipTree": {
      "type": "boolean",
      "description": "Include ASCII relationship tree diagram. Defaults to true.",
      "default": true
    }
  },
  "required": []
}
```

**Output Schema:**
```json
{
  "type": "object",
  "properties": {
    "data": {
      "type": "string",
      "description": "Complete compressed application schema in markdown format. Includes summary metrics, models grouped by module, compact field tables, relationship trees, and data retention notes."
    },
    "error": {
      "type": "boolean"
    },
    "errorType": {
      "type": "string"
    },
    "errorMessage": {
      "type": "string"
    }
  }
}
```

---

## 3. JSONata Transform Examples for Compression

### Platform Functions

Fuuz provides platform-specific JSONata functions. The most commonly used in MCP tools:

- **`$isNilOrEmpty(value)`** -- Returns true if a value is null, undefined, or an empty string. Used extensively for conditional variable construction with optional parameters:

```jsonata
{
  "connectorWhere": {
    "name": $isNilOrEmpty(name) ? undefined : {
      "_contains": name
    },
    "connectorType": $isNilOrEmpty(connectorType) ? undefined : {
      "_eq": connectorType
    }
  }
}
```

When `$isNilOrEmpty()` returns true and the field is set to `undefined`, the field is omitted from the resulting object, which means the GraphQL where clause will not filter on that field.

### `$query()` and `$mutate()` Bindings (Transform-Level GraphQL)

Fuuz provides `$query()` and `$mutate()` as JSONata bindings available inside `transform` nodes. These execute GraphQL operations inline during a transform, rather than using separate `query` or `mutate` nodes. This is essential for **dynamic query/mutation construction** where the GraphQL statement is built at runtime.

**`$query()` -- execute a GraphQL query from within a transform:**
```jsonata
(
  $result := $query({
    "statement": "query($where: DataModelWhereInput) { dataModel(where: $where, first: 1) { edges { node { name elements(first: 500) { edges { node { name type labelField relation } } } } } } }",
    "variables": { "where": { "name": { "_eq": $modelName } } }
  });
  $result.dataModel.edges[0].node
)
```

**`$mutate()` -- execute a GraphQL mutation from within a transform:**
```jsonata
(
  $result := $mutate({
    "statement": $._mutation,
    "variables": $._variables
  });
  $result
)
```

**When to use transform-level `$query()`/`$mutate()` vs dedicated nodes:**
- Use **`query` / `mutate` nodes** for static GraphQL where the statement is known at design time
- Use **`$query()` / `$mutate()` in transform nodes** when the GraphQL statement must be constructed dynamically at runtime (e.g., Dynamic Query, Dynamic Mutate patterns)
- Transform-level execution supports the **multi-step transform pipeline** pattern where results accumulate progressively

### Multi-Step Transform Pipeline (Progressive Accumulation)

For complex tools (Dynamic Query, Dynamic Mutate), chain multiple `transform` nodes where each step adds state using `$merge()`:

```jsonata
// Step 1 (Fetch Schema): adds _schema, _model, _modelLower
$merge([$, {
  "_model": payload.modelName,
  "_modelLower": $lowercase(payload.modelName),
  "_schema": $query({ "statement": "...", "variables": {...} })
}])

// Step 2 (Build Fields): adds _fields, _includes, _warnings
$merge([$, {
  "_fields": $._schema.elements.edges.node,
  "_warnings": [...],
  "_includes": [...]
}])

// Step 3 (Build Query): adds _query, _variables
$merge([$, {
  "_query": "query Dynamic" & $._model & "Query(...) { ... }",
  "_variables": { "where": ..., "first": ... }
}])

// Step 4 (Execute): adds _result
$merge([$, {
  "_result": $query({ "statement": $._query, "variables": $._variables })
}])

// Step 5 (Format Response): returns final output
{ "data": ..., "pagination": ..., "warnings": $._warnings }
```

Each transform merges new underscore-prefixed properties (`_model`, `_schema`, `_fields`, `_query`, `_result`) onto the existing state. The final transform produces the clean response.

### Additional Platform Bindings for Transform Nodes

- **`$error(message)`** -- Throw an explicit error from a transform node. Caught by the enclosing `tryCatch`:
```jsonata
$count($schema.elements.edges) = 0 ? $error("Model not found: " & $model) : $schema
```

- **`$lookup(object, key)`** -- Dynamic property access by variable name. Essential when the property name is computed:
```jsonata
$queryResult := $lookup($._result, $._modelLower)
```

### Mutation Payload Array Pattern (Write Operations)

All Fuuz GraphQL mutations use a `payload` array wrapping the operation. There are three operations:

**Create:**
```jsonata
{
  "payload": [{ "create": $data }]
}
```

**Update:**
```jsonata
{
  "payload": [{ "where": { "id": $recordId }, "update": $data }]
}
```
Note: The mutation `where` clause uses a direct `id` value (string), NOT `{ "_eq": ... }` like query filters.

**Delete:**
```jsonata
{
  "payload": [{ "where": { "id": $recordId } }]
}
```

**Dynamic mutation statement construction:**
```jsonata
$op = "create" ?
  "mutation Create" & $model & "($payload: [" & $model & "CreatePayloadInput!]!) { create" & $model & "(payload: $payload) { " & $returnFields & " } }" :
$op = "update" ?
  "mutation Update" & $model & "($payload: [" & $model & "UpdatePayloadInput!]!) { update" & $model & "(payload: $payload) { " & $returnFields & " } }" :
  "mutation Delete" & $model & "($payload: [" & $model & "DeletePayloadInput!]!) { delete" & $model & "(payload: $payload) { id } }"
```

### Compress Model List to Markdown Table

```jsonata
(
  $models := data.dataModels;
  {
    "data": "# Application Data Models (" & $string($count($models)) & ")\n\n" &
            "| Model | Description | Fields |\n" &
            "|-------|-------------|--------|\n" &
            $join($models.(
              "| " & name & " | " &
              (description ? $substring(description, 0, 60) : "-") &
              " | " & $string($count(version.modelDefinition.fields)) & " |\n"
            ))
  }
)
```

### Compress Single Model Details

```jsonata
(
  $model := data.dataModel;
  $def := $model.version.modelDefinition;
  $fields := $def.fields;
  $scalars := $fields[$not($contains(type, "["))];
  $relations := $fields[$contains(type, "[")];

  {
    "data": "## " & $model.name & "\n" &
            ($def.description ? $def.description & "\n\n" : "\n") &

            "### Fields (" & $string($count($scalars)) & " scalar, " &
            $string($count($relations)) & " relations)\n\n" &

            "| Field | Type | Constraints |\n" &
            "|-------|------|-------------|\n" &
            $join($scalars.(
              "| `" & name & "` | " & type & " | " &
              ($contains(type, "!") ? "required " : "") &
              " |\n"
            )) & "\n" &

            ($count($relations) > 0 ?
              "### Relationships\n\n" &
              $join($relations.(
                "- `" & name & "`: " & type & "\n"
              ))
              : ""
            )
  }
)
```

### Compress Relay-Style Query Results to Markdown Table

```jsonata
(
  $firstKey := $keys(data)[0];
  $result := $lookup(data, $firstKey);
  $items := $result.edges.node;
  $fieldNames := $items[0] ? $keys($items[0]) : [];

  {
    "recordCount": $count($items),
    "total": $result.total,
    "data": "# " & $firstKey & " (" & $string($count($items)) & " of " & $string($result.total) & " records)\n\n" &
            "| " & $join($fieldNames, " | ") & " |\n" &
            "| " & $join($fieldNames.("---"), " | ") & " |\n" &
            $join($items.(
              $item := $;
              "| " & $join($fieldNames.($string($lookup($item, $))), " | ") & " |\n"
            ))
  }
)
```

### Compress Enum/Lookup Values

```jsonata
(
  $values := data.*;
  $firstKey := $keys(data)[0];
  $items := $lookup(data, $firstKey);

  {
    "data": "## " & $firstKey & " (" & $string($count($items)) & " values)\n\n" &
            "| Code | Description | Active |\n" &
            "|------|-------------|--------|\n" &
            $join($items.(
              "| " & (code ? code : id) & " | " &
              (description ? description : "-") & " | " &
              ($string(usable ? usable : active)) & " |\n"
            ))
  }
)
```

### Build Dynamic GraphQL Query from Parameters

```jsonata
(
  $model := payload.modelName;
  $camel := $lowercase($substring($model, 0, 1)) & $substring($model, 1);
  $fields := $join(payload.fields, " ");
  $limit := payload.limit ? payload.limit : 50;

  {
    "query": "query($where: " & $model & "WhereInput, $first: Int) { " &
             $camel & "(where: $where, first: $first) { total edges { node { " & $fields & " } } } }",
    "variables": {
      "first": $limit,
      "where": payload.filter ? payload.filter : null
    }
  }
)
```

### Build Dynamic GraphQL Mutation from Parameters

```jsonata
(
  $model := payload.modelName;
  $op := payload.operation ? payload.operation : "create";
  $opCapital := $uppercase($substring($op, 0, 1)) & $substring($op, 1);
  $inputType := $model & $opCapital & "Input";

  {
    "query": "mutation " & $opCapital & $model & "($input: " & $inputType & "!) { " &
             $op & $model & "(input: $input) { id } }",
    "variables": {
      "input": payload.input
    }
  }
)
```

---

## 4. JavaScript Compression Examples

### Full Application Schema Compression

This JavaScript function compresses the complete data model export into the WMS-schema-style markdown format. Use this in a `javascriptTransform` (JavaScript) node.

```javascript
var models = $.data.dataModels || [];
var output = "# Application Data Schema\n\n";
output += "> Auto-generated by MCP tool\n\n";

// Summary metrics
var totalFields = 0;
var totalRelations = 0;
var moduleMap = {};

for (var i = 0; i < models.length; i++) {
  var m = models[i];
  var fields = (m.version && m.version.modelDefinition && m.version.modelDefinition.fields) || [];
  totalFields += fields.length;

  for (var j = 0; j < fields.length; j++) {
    if (fields[j].type && (fields[j].type.indexOf("[") > -1)) {
      totalRelations++;
    }
  }

  // Group by module
  var moduleName = "Uncategorized";
  if (m.version && m.version.modelDefinition && m.version.modelDefinition.metadata) {
    var meta = m.version.modelDefinition.metadata;
    if (meta.mfgx && meta.mfgx.module && meta.mfgx.module.id) {
      moduleName = meta.mfgx.module.id;
    }
  }
  if (!moduleMap[moduleName]) moduleMap[moduleName] = [];
  moduleMap[moduleName].push(m);
}

output += "## Summary\n\n";
output += "| Metric | Value |\n";
output += "|--------|-------|\n";
output += "| **Total Models** | " + models.length + " |\n";
output += "| **Total Fields** | " + totalFields + " |\n";
output += "| **Total Relationships** | " + totalRelations + " |\n\n";

// Models grouped by module
var moduleNames = Object.keys(moduleMap);
for (var mi = 0; mi < moduleNames.length; mi++) {
  var modName = moduleNames[mi];
  var modModels = moduleMap[modName];

  output += "## " + modName + "\n\n";

  for (var k = 0; k < modModels.length; k++) {
    var model = modModels[k];
    var def = model.version && model.version.modelDefinition;
    var modelFields = (def && def.fields) || [];

    output += "### " + model.name + "\n";
    if (def && def.description) {
      output += def.description + "\n\n";
    } else {
      output += "\n";
    }

    output += "| Field | Type | Constraints |\n";
    output += "|-------|------|-------------|\n";

    for (var f = 0; f < modelFields.length; f++) {
      var field = modelFields[f];
      var constraints = [];

      // Check for unique
      var metaStr = JSON.stringify(field.metadata || {});
      if (metaStr.indexOf('"unique":true') > -1 || metaStr.indexOf('"unique": true') > -1) {
        constraints.push("unique");
      }

      // Check for required (type ends with !)
      if (field.type && field.type.indexOf("!") > -1) {
        constraints.push("required");
      }

      // Check for relationship (array type or known model reference)
      if (field.type && field.type.indexOf("[") > -1) {
        constraints.push("relationship");
      }

      output += "| `" + field.name + "` | " + field.type + " | " + constraints.join(", ") + " |\n";
    }
    output += "\n";
  }
}

// Common field patterns section
output += "---\n\n";
output += "## Common Field Patterns\n\n";
output += "### Standard Identifiers\n";
output += "- `id`: Internal unique identifier (ID!)\n";
output += "- `code`: Business-readable unique code (String!)\n";
output += "- `externalId`: External system identifier (String, unique)\n\n";
output += "### Extension Fields\n";
output += "- `customData`: JSON for custom fields\n";
output += "- `integrationData`: JSON for integration metadata\n\n";
output += "### Status Flags\n";
output += "- `active`: Whether record is active\n";
output += "- `usable`: Whether record can be used in operations\n";
output += "- `system`: Whether record is system-managed\n";

return { "data": output };
```

### Compress Screen Configuration

```javascript
var screen = $.data.screen;
var output = "# Screen: " + screen.name + "\n\n";

output += "| Property | Value |\n";
output += "|----------|-------|\n";
output += "| Type | " + (screen.screenType || "standard") + " |\n";
output += "| Description | " + (screen.description || "-") + " |\n\n";

// Extract element tree (simplified)
var elements = screen.elements || [];
output += "## Elements (" + elements.length + ")\n\n";
output += "| Element | Type | Data Binding |\n";
output += "|---------|------|-------------|\n";

for (var i = 0; i < elements.length; i++) {
  var el = elements[i];
  var binding = el.dataPath || el.query || "-";
  output += "| " + (el.name || el.id) + " | " + el.type + " | " + binding + " |\n";
}

return { "data": output };
```

### Compress Flow Definition

```javascript
var flow = $.data.dataFlow;
var nodes = flow.version && flow.version.nodes ? flow.version.nodes : [];
var output = "# Flow: " + flow.name + "\n\n";

output += "| Property | Value |\n";
output += "|----------|-------|\n";
output += "| Type | " + (flow.dataFlowType ? flow.dataFlowType.name : "unknown") + " |\n";
output += "| Nodes | " + nodes.length + " |\n";
output += "| Description | " + (flow.description || "-") + " |\n\n";

// Node pipeline
output += "## Node Pipeline\n\n";
output += "```\n";
for (var i = 0; i < nodes.length; i++) {
  var node = nodes[i];
  var arrow = i < nodes.length - 1 ? " -->" : "";
  output += node.name + " (" + node.type + ")" + arrow + "\n";
}
output += "```\n\n";

// Key transforms
output += "## Key Transforms\n\n";
for (var i = 0; i < nodes.length; i++) {
  var node = nodes[i];
  if (node.type === "transform" || node.type === "javascriptTransform") {
    output += "### " + node.name + " (" + node.type + ")\n";
    if (node.data && node.data.transform) {
      output += "```\n" + node.data.transform.substring(0, 200) + "\n```\n\n";
    }
  }
}

return { "data": output };
```

---

## 5. Error Handling Patterns

### Standard Error Response Structure

All MCP tools should return errors in this consistent format (accessed via `$state.lastError` in the catch path):

```json
{
  "error": true,
  "errorType": "EXECUTION_ERROR",
  "errorMessage": "Human-readable error message",
  "errorDetails": { "...full error object..." }
}
```

### Error Type Conventions

| errorType | When to Use |
|-----------|-------------|
| `VALIDATION_ERROR` | Input fails schema validation or business rules |
| `NOT_FOUND` | Requested resource does not exist |
| `QUERY_ERROR` | GraphQL query execution failed |
| `MUTATION_ERROR` | GraphQL mutation execution failed |
| `PERMISSION_DENIED` | User lacks required permissions |
| `TIMEOUT` | Operation exceeded time limit |
| `EXECUTION_ERROR` | General execution failure (catch-all) |
| `EXTERNAL_API_ERROR` | External HTTP call failed |

### Production Error Response (Catch Path)

In real flows, the TryCatch catch path goes directly to a response node. No separate error transform node is needed:

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

### Validation via Validate Node

Instead of using JSONata `$error()` for validation, use the dedicated `validate` node with a JSON Schema. Validation failures are automatically caught by the TryCatch and routed to the catch path.

---

## 6. Connection Configuration Patterns

### System API Connection (for Metadata Tools)

When querying the System API for data model definitions, screen configurations, or flow definitions, use `"api": "system"` in the query node:

```json
{
  "type": "query",
  "data": {
    "api": "system",
    "query": "...",
    "variablesTransform": "..."
  }
}
```

### Application API Connection (for Data Tools)

When querying or mutating application data (inventory, orders, products, etc.), use `"api": "application"`:

```json
{
  "type": "query",
  "data": {
    "api": "application",
    "query": "...",
    "variablesTransform": "..."
  }
}
```

### External API Connection (HTTP Node -- Simple)

For simple external API calls, use the `http` node with a named connection:

```json
{
  "type": "http",
  "data": {
    "connectionName": "my-api-connection",
    "httpMethod": "GET",
    "httpPathTransform": "\"/api/endpoint\"",
    "nextNodes": ["response-1"]
  }
}
```

### External API Connection (integrateV2 -- Advanced)

For tools that call external APIs with more control, use the `integrateV2` node with a pre-configured connector:

```json
{
  "type": "integrateV2",
  "data": {
    "connectorId": "<connector-uuid>",
    "method": "GET",
    "path": "/api/endpoint",
    "headers": "{ \"Accept\": \"application/json\" }",
    "queryParams": "{ \"param1\": payload.value1 }",
    "body": "",
    "nextNodes": ["transform-1"]
  }
}
```

For POST/PUT methods with a body:

```json
{
  "type": "integrateV2",
  "data": {
    "connectorId": "<connector-uuid>",
    "method": "POST",
    "path": "/api/endpoint",
    "headers": "{ \"Content-Type\": \"application/json\" }",
    "body": "$string(payload.requestBody)",
    "nextNodes": ["transform-1"]
  }
}
```

**Note:** The connector must be pre-configured in Fuuz with the base URL, authentication, and any required headers. The `connectorId` references this configuration. Ask the developer for the connector ID.

### External API via `$integrate()` Binding (Transform-Level)

For dynamic external API calls from within a `transform` node (similar to `$query()`/`$mutate()`), use the `$integrate()` binding. This is the most flexible approach — it calls any of the 42 Fuuz connectors directly from JSONata:

```jsonata
$integrate({
  "connectionName": "My HTTP Connection",
  "options": {
    "degreeOfParallelism": 10,
    "returnErrors": true
  },
  "payload": [
    { "method": "get", "path": "/api/users" },
    { "method": "post", "path": "/api/orders", "data": { "item": "Widget", "qty": 5 } }
  ]
})
```

| Field | Type | Req | Description |
|-------|------|-----|-------------|
| `connectionName` | string | Y | Name of the configured connection (as shown in Fuuz UI) |
| `options.degreeOfParallelism` | integer | N | Concurrent requests. Default 10. |
| `options.returnErrors` | boolean | N | When `true`, failed requests return error objects instead of aborting. Default `false`. |
| `payload` | array | Y | Array of request items. Shape depends on connector type (see `integration-service-reference.md` in fuuz-platform). |

**When to use which:**
- **`http` node** — Simplest. One static external API call with a named connection. Best for minimal tools (like Get Dog Image).
- **`integrateV2` node** — More control (headers, queryParams, body). Still one call per node execution.
- **`$integrate()` in transform** — Most flexible. Send batched requests (payload array), dynamic connection selection, parallel execution. Best for complex integration tools.

The payload schema varies per connector type. The 42 connectors fall into 4 groups:
- **REST connectors (21)** — `{ method, path, data?, headers?, params? }` with per-connector variations in field names and casing
- **Plex connectors (4)** — `{ datasourceKey, parameters }` for SOAP/UX; standard REST for API/IAM
- **Database connectors (2)** — `{ query, parameters?, procedure? }` for MSSQL; `{ query, variables? }` for ODBC
- **Specialized connectors (15)** — Unique schemas per connector (FTP actions, SMTP emails, OpenAI chat, AWS Lambda, etc.)

See `integration-service-reference.md` in the fuuz-platform skill for complete per-connector inbound/outbound schemas.

---

## 7. Complete Example: Data Model Schema Extraction Tool

This is a complete MCP tool flow that extracts the entire application data schema and compresses it into the WMS-schema-style markdown format.

### Flow Metadata

```
Name: describe_application_schema
Description: Extracts and compresses the complete application data schema into a compact markdown format optimized for LLM consumption. Returns model names, descriptions, field tables, relationship summaries, and data retention policies.
Type: Backend (request/response)
```

### MCP Configuration

```json
{
  "enabled": true,
  "readOnly": true,
  "destructive": false,
  "idempotent": true,
  "inputSchema": {
    "type": "object",
    "properties": {
      "module": {
        "type": "string",
        "description": "Optional: filter to models in a specific module. Omit for full schema."
      }
    },
    "required": []
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "data": {
        "type": "string",
        "description": "Complete compressed application schema in markdown format"
      },
      "error": { "type": "boolean" },
      "errorType": { "type": "string" },
      "errorMessage": { "type": "string" }
    }
  }
}
```

### Flow Nodes

```json
{
  "nodes": [
    {
      "id": "debugSource-1",
      "name": "Debug Source",
      "type": "debugSource",
      "data": {
        "payload": "{ \"module\": \"inventory\" }",
        "nextNodes": ["request-1"]
      },
      "debug": { "breakpointEnabled": false, "publishSubscribeClient": false, "publishToMFGx": false },
      "logging": { "executionMetrics": false, "executionStarted": false, "executionSucceeded": false, "executionFailed": true, "enableTraceLogging": false }
    },
    {
      "id": "request-1",
      "name": "Receive Input",
      "type": "request",
      "data": { "nextNodes": ["tryCatch-1"] },
      "debug": { "breakpointEnabled": false, "publishSubscribeClient": false, "publishToMFGx": false },
      "logging": { "executionMetrics": false, "executionStarted": false, "executionSucceeded": false, "executionFailed": true, "enableTraceLogging": false }
    },
    {
      "id": "tryCatch-1",
      "name": "Error Boundary",
      "type": "tryCatch",
      "data": {
        "tryNextNodes": ["query-models-1"],
        "catchNextNodes": ["error-response-1"],
        "nextNodes": []
      },
      "debug": { "breakpointEnabled": false, "publishSubscribeClient": false, "publishToMFGx": false },
      "logging": { "executionMetrics": false, "executionStarted": false, "executionSucceeded": false, "executionFailed": true, "enableTraceLogging": false }
    },
    {
      "id": "query-models-1",
      "name": "Fetch All Models with Fields",
      "type": "query",
      "data": {
        "api": "system",
        "query": "query { dataModels { id name description version { modelDefinition { kind description metadata fields { name type description metadata } } } } }",
        "variablesTransform": "{}",
        "nextNodes": ["compress-1"]
      },
      "debug": { "breakpointEnabled": false, "publishSubscribeClient": false, "publishToMFGx": false },
      "logging": { "executionMetrics": false, "executionStarted": false, "executionSucceeded": false, "executionFailed": true, "enableTraceLogging": false }
    },
    {
      "id": "compress-1",
      "name": "Compress Schema to Markdown",
      "type": "javascriptTransform",
      "data": {
        "transform": "var models = $.data.dataModels || []; var moduleFilter = $.payload && $.payload.module ? $.payload.module : null; if (moduleFilter) { models = models.filter(function(m) { var meta = m.version && m.version.modelDefinition && m.version.modelDefinition.metadata; return meta && meta.mfgx && meta.mfgx.module && meta.mfgx.module.id === moduleFilter; }); } var output = '# Application Data Schema\\n\\n'; var totalFields = 0; for (var i = 0; i < models.length; i++) { var fields = (models[i].version && models[i].version.modelDefinition && models[i].version.modelDefinition.fields) || []; totalFields += fields.length; } output += '| Metric | Value |\\n|--------|-------|\\n'; output += '| Total Models | ' + models.length + ' |\\n'; output += '| Total Fields | ' + totalFields + ' |\\n\\n'; for (var i = 0; i < models.length; i++) { var model = models[i]; var def = model.version && model.version.modelDefinition; var modelFields = (def && def.fields) || []; output += '## ' + model.name + '\\n'; if (def && def.description) output += def.description + '\\n\\n'; else output += '\\n'; output += '| Field | Type | Constraints |\\n|-------|------|-------------|\\n'; for (var f = 0; f < modelFields.length; f++) { var field = modelFields[f]; var c = []; var ms = JSON.stringify(field.metadata || {}); if (ms.indexOf('unique') > -1) c.push('unique'); if (field.type && field.type.indexOf('!') > -1) c.push('required'); if (field.type && field.type.indexOf('[') > -1) c.push('relationship'); output += '| `' + field.name + '` | ' + field.type + ' | ' + c.join(', ') + ' |\\n'; } output += '\\n'; } return { data: output };",
        "nextNodes": ["response-1"]
      },
      "debug": { "breakpointEnabled": false, "publishSubscribeClient": false, "publishToMFGx": false },
      "logging": { "executionMetrics": false, "executionStarted": false, "executionSucceeded": false, "executionFailed": true, "enableTraceLogging": false }
    },
    {
      "id": "response-1",
      "name": "Return Compressed Schema",
      "type": "response",
      "data": { "responseTransform": "$", "nextNodes": [] },
      "debug": { "breakpointEnabled": false, "publishSubscribeClient": false, "publishToMFGx": false },
      "logging": { "executionMetrics": false, "executionStarted": false, "executionSucceeded": false, "executionFailed": true, "enableTraceLogging": false }
    },
    {
      "id": "error-response-1",
      "name": "Return Error",
      "type": "response",
      "data": {
        "responseTransform": "{\n  \"error\": true,\n  \"errorType\": \"EXECUTION_ERROR\",\n  \"errorMessage\": $state.lastError.message ? $state.lastError.message : \"Failed to extract schema\",\n  \"errorDetails\": $state.lastError\n}",
        "nextNodes": []
      },
      "debug": { "breakpointEnabled": false, "publishSubscribeClient": false, "publishToMFGx": false },
      "logging": { "executionMetrics": false, "executionStarted": false, "executionSucceeded": false, "executionFailed": true, "enableTraceLogging": false }
    }
  ]
}
```

---

## 8. Real Production Examples

These examples are distilled from actual deployed MCP tool packages (Example-MCPTools@0.0.1 with 12 production flows, MCP Tool Examples@0.0.2 with 3 simpler flows).

### Example A: Connector Details (Example) -- Single-Item Detail Tool

**Purpose:** Fetch detailed information about a specific connector or connection by ID.

**Flow:**
```
debugSource-1 --> request-1 --> tryCatch-1
    Try: validate-1 --> query-1 --> transform-1 --> response-1
    Catch: error-response-1
```

**Validate node** -- requires `id` field:
```json
{
  "type": "validate",
  "data": {
    "schema": {
      "type": "object",
      "additionalProperties": true,
      "required": ["payload"],
      "properties": {
        "payload": {
          "type": "object",
          "additionalProperties": true,
          "required": ["id"],
          "properties": {
            "id": {
              "type": "string",
              "description": "The ID of the connector to retrieve details for",
              "minLength": 1,
              "example": "clx7abc123"
            }
          }
        }
      }
    }
  }
}
```

**Query node** -- relay-style pagination with `variablesTransform`:
```json
{
  "type": "query",
  "data": {
    "api": "application",
    "query": "query($connectorWhere: ConnectorWhereInput, $connectionWhere: ConnectionWhereInput) { connector(where: $connectorWhere, first: 1) { total edges { node { id name description connectorType status schemas { name version fields { name type required } } audit { createdAt updatedAt createdBy { name } } } } } connection(where: $connectionWhere, first: 1) { total edges { node { id name description connectionType status metadata } } } }",
    "variablesTransform": "{ \"connectorWhere\": { \"id\": { \"_eq\": payload.id } }, \"connectionWhere\": { \"id\": { \"_eq\": payload.id } } }"
  }
}
```

**Transform node** -- conditional response based on which entity was found:
```json
{
  "type": "transform",
  "data": {
    "transform": "( $connector := data.connector.edges.node[0]; $connection := data.connection.edges.node[0]; $connector ? { \"type\": \"connector\", \"id\": $connector.id, \"name\": $connector.name, \"description\": $connector.description, \"connectorType\": $connector.connectorType, \"status\": $connector.status, \"schemas\": $connector.schemas, \"createdAt\": $connector.audit.createdAt, \"updatedAt\": $connector.audit.updatedAt, \"createdBy\": $connector.audit.createdBy.name } : $connection ? { \"type\": \"connection\", \"id\": $connection.id, \"name\": $connection.name, \"description\": $connection.description, \"connectionType\": $connection.connectionType, \"status\": $connection.status, \"metadata\": $connection.metadata } : { \"error\": true, \"errorMessage\": \"No connector or connection found with ID: \" & payload.id } )"
  }
}
```

**Key patterns:**
- Uses `variablesTransform` (JSONata) to construct the GraphQL where clause
- Queries two entity types in parallel (connector and connection)
- Conditional transform returns different response shapes based on which entity matched
- Relay-style `edges.node[0]` for single-item retrieval

### Example B: Connector To Graph (Example) -- List/Graph Tool

**Purpose:** Retrieve connectors and connections as a unified graph structure with nodes, edges, summary stats, and a hint for LLM consumption.

**Flow:**
```
debugSource-1 --> request-1 --> tryCatch-1
    Try: validate-1 --> query-1 --> transform-1 --> response-1
    Catch: error-response-1
```

**Key pattern -- `$isNilOrEmpty()` for optional filters in `variablesTransform`:**
```jsonata
{
  "connectorWhere": {
    "name": $isNilOrEmpty(payload.name) ? undefined : {
      "_contains": payload.name
    },
    "connectorType": $isNilOrEmpty(payload.connectorType) ? undefined : {
      "_eq": payload.connectorType
    }
  },
  "connectionWhere": {
    "name": $isNilOrEmpty(payload.name) ? undefined : {
      "_contains": payload.name
    }
  },
  "first": payload.limit ? payload.limit : 50
}
```

**Key pattern -- graph output structure:**
```jsonata
(
  $connectors := data.connector.edges.node;
  $connections := data.connection.edges.node;

  $connectorNodes := $connectors.{
    "id": id,
    "type": "connector",
    "name": name,
    "connectorType": connectorType,
    "status": status
  };

  $connectionNodes := $connections.{
    "id": id,
    "type": "connection",
    "name": name,
    "connectorId": connectorId
  };

  $edges := $connections.{
    "source": connectorId,
    "target": id,
    "relationship": "has_connection"
  };

  {
    "nodes": $append($connectorNodes, $connectionNodes),
    "edges": $edges,
    "summary": {
      "totalConnectors": $count($connectors),
      "totalConnections": $count($connections),
      "connectorTypes": $count($distinct($connectors.connectorType)),
      "activeConnectors": $count($filter($connectors, function($c) { $c.status = "active" }))
    },
    "hint": "Use connector IDs from nodes to get details. Edges show connector-to-connection relationships."
  }
)
```

**Key patterns:**
- `$isNilOrEmpty()` makes all filter parameters optional without complex ternary logic
- Returns a graph structure with `nodes` array, `edges` array, `summary` stats, and `hint`
- Summary uses `$count()`, `$distinct()`, and `$filter()` for aggregate statistics
- The `hint` field guides the LLM on how to use the graph data

### Example C: DataFlow Details (Example) -- Metadata Detail Tool

**Purpose:** Retrieve detailed metadata about a data flow including versions, deployments, and substitution relationships.

**Flow:**
```
debugSource-1 --> request-1 --> tryCatch-1
    Try: validate-1 --> query-1 --> transform-1 --> response-1
    Catch: error-response-1
```

**Key pattern -- navigating nested relay edges:**
```jsonata
(
  $flow := data.dataFlow.edges.node[0];
  $versions := $flow.versions.edges.node;
  $deployments := $flow.deployments.edges.node;
  $substitutions := $flow.substitutions.edges.node;

  {
    "id": $flow.id,
    "name": $flow.name,
    "description": $flow.description,
    "type": $flow.dataFlowType.name,
    "versions": $versions.{
      "id": id,
      "number": versionNumber,
      "status": status,
      "nodeCount": $count(nodes)
    },
    "deployments": $deployments.{
      "environment": environment.name,
      "status": status,
      "deployedAt": deployedAt
    },
    "substitutions": $substitutions.{
      "sourceFlow": sourceFlow.name,
      "targetFlow": targetFlow.name,
      "active": active
    }
  }
)
```

**Key patterns:**
- Multiple levels of relay edge navigation (`data.dataFlow.edges.node[0].versions.edges.node`)
- Flattening nested relay structures into simple arrays
- Extracting counts from nested data (`$count(nodes)`)

### Example D: Get Dog Image (MCP Tool Examples) -- Minimal External API Tool

**Purpose:** Fetch a random dog image from an external API. This is the simplest possible MCP tool pattern.

**Flow:**
```
debugSource-1 --> request-1 --> http-1 --> response-1
```

This tool demonstrates that simple tools do NOT need TryCatch or Validate nodes.

**HTTP node:**
```json
{
  "id": "http-1",
  "name": "Fetch Dog Image",
  "type": "http",
  "data": {
    "connectionName": "dog-api",
    "httpMethod": "GET",
    "httpPathTransform": "\"/api/breeds/image/random\"",
    "nextNodes": ["response-1"]
  }
}
```

**Key patterns:**
- Uses `http` node type (not `integrateV2`) for simple external calls
- `connectionName` references a pre-configured connection (base URL + auth)
- `httpPathTransform` is a JSONata expression (note the escaped quotes for a string literal)
- No validation or error handling needed for a simple GET
- Response passes through directly

### Example E: List All Models / Get Model Details (MCP Tool Examples) -- System API Tools

**Purpose:** Query system metadata (model definitions) with optional filtering.

**Flow (List All Models):**
```
debugSource-1 --> request-1 --> validate-1 --> query-1 --> response-1
```

**Validate node with minimal schema:**
```json
{
  "type": "validate",
  "data": {
    "schema": {}
  }
}
```

An empty schema `{}` validates any input -- useful for tools with no required parameters.

**Query node:**
```json
{
  "type": "query",
  "data": {
    "api": "system",
    "query": "query($where: ModelDefinitionWhereInput, $first: Int) { modelDefinitions(where: $where, first: $first) { total edges { node { id name description kind } } } }",
    "variablesTransform": "{ \"first\": payload.limit ? payload.limit : 100, \"where\": $isNilOrEmpty(payload.name) ? undefined : { \"name\": { \"_contains\": payload.name } } }"
  }
}
```

**Flow (Get Model Details):**
```
debugSource-1 --> request-1 --> validate-1 --> query-1 --> response-1
```

**Query node with `variablesTransform`:**
```json
{
  "type": "query",
  "data": {
    "api": "system",
    "query": "query($where: ModelDefinitionWhereInput) { modelDefinitions(where: $where, first: 1) { edges { node { id name description kind fields { name type description required defaultValue metadata } } } } }",
    "variablesTransform": "{ \"where\": { \"name\": { \"_eq\": payload.modelName } } }"
  }
}
```

**Key patterns:**
- System API uses `modelDefinitions` (not `dataModels` -- the latter is an older query shape)
- `variablesTransform` is always a JSONata expression, even for simple variable construction
- Relay pagination with `edges.node` pattern throughout
- `$isNilOrEmpty()` for optional filter parameters
- These simpler flows skip TryCatch for brevity but production flows should include it

### Example F: Dynamic Query (Example) -- Schema-Driven Dynamic Read Tool

**Purpose:** Execute dynamic read queries on ANY data model with runtime field selection, filtering, and pagination. Fetches the model schema first, validates requested fields, then builds and executes a GraphQL query dynamically.

**mcpToolConfiguration:** `readOnly: true`, `destructive: false`, `idempotent: true`

**Flow (multi-step transform pipeline -- NO query node):**
```
debugSource-1 --> request-1 --> tryCatch-1
                    tryNextNodes: validate-1 --> transform-fetchSchema --> transform-buildFields
                      --> transform-buildQuery --> transform-execute --> transform-formatResponse --> response-1
                    catchNextNodes: error-response-1
```

**Key architectural difference:** This flow uses **no `query` nodes**. All GraphQL is executed via `$query()` inside `transform` nodes because the query statement is built dynamically at runtime.

**Input schema:**
```json
{
  "type": "object",
  "required": ["modelName"],
  "properties": {
    "modelName": { "type": "string", "description": "PascalCase name of the model to query" },
    "fields": { "type": "array", "items": { "type": "string" }, "description": "Field names to return. Omit for all scalar fields." },
    "filter": { "type": "object", "description": "GraphQL WhereInput filter object or JSON string" },
    "orderBy": { "type": "object", "description": "GraphQL OrderByInput object" },
    "limit": { "type": "integer", "default": 50, "description": "Max records to return" },
    "after": { "type": "string", "description": "Cursor for pagination" }
  }
}
```

**Transform 1 -- Fetch Schema (uses `$query()` binding):**
```jsonata
(
  $model := payload.modelName;
  $schemaResult := $query({
    "statement": "query GetSchema($where: DataModelWhereInput) { dataModel(where: $where, first: 1) { edges { node { elements(first: 500) { edges { node { name type labelField relation } } } } } } }",
    "variables": { "where": { "name": { "_eq": $model } } }
  });
  $elements := $schemaResult.dataModel.edges[0].node.elements.edges.node;
  $count($elements) = 0 ? $error("Model not found: " & $model) : $merge([$, {
    "_model": $model,
    "_modelLower": $lowercase($substring($model, 0, 1)) & $substring($model, 1),
    "_schema": $elements,
    "_input": payload
  }])
)
```

**Transform 2 -- Build Fields (validates against schema):**
```jsonata
(
  $requestedFields := $._input.fields;
  $schemaFields := $._schema;
  $scalarTypes := ["ID", "ID!", "String", "String!", "Int", "Int!", "Float", "Float!", "Boolean", "Boolean!", "DateTime", "DateTime!", "Decimal", "Decimal!", "JSON", "JSON!"];
  $complexTypes := { "Measure": "{ value unit { id code } }", "Address": "{ line1 city state postalCode country }", "Money": "{ amount currency }" };

  $validFields := $requestedFields ? $filter($requestedFields, function($f) { $f in $schemaFields.name }) : $filter($schemaFields, function($f) { $f.type in $scalarTypes }).name;
  $warnings := $requestedFields ? $filter($requestedFields, function($f) { $not($f in $schemaFields.name) }).($ & " (not found)") : [];

  $merge([$, { "_fields": $validFields, "_warnings": $warnings }])
)
```

**Transform 3 -- Build Query (dynamic GraphQL string construction):**
```jsonata
(
  $fieldsStr := $join($._fields, " ");
  $merge([$, {
    "_query": "query Dynamic" & $._model & "Query($where: " & $._model & "WhereInput, $first: Int, $after: String, $orderBy: [" & $._model & "OrderByInput!]) { " & $._modelLower & "(where: $where, first: $first, after: $after, orderBy: $orderBy) { edges { node { " & $fieldsStr & " } } pageInfo { hasNextPage endCursor } total } }",
    "_variables": {
      "where": $._input.filter ? $._input.filter : {},
      "first": $._input.limit ? $._input.limit : 50,
      "after": $._input.after,
      "orderBy": $._input.orderBy
    }
  }])
)
```

**Transform 4 -- Execute (inline `$query()`):**
```jsonata
$merge([$, { "_result": $query({ "statement": $._query, "variables": $._variables }) }])
```

**Transform 5 -- Format Response:**
```jsonata
(
  $queryResult := $lookup($._result, $._modelLower);
  $records := $queryResult.edges.node;
  {
    "data": $records,
    "pagination": {
      "total": $queryResult.total,
      "returned": $count($records),
      "hasNextPage": $queryResult.pageInfo.hasNextPage,
      "endCursor": $queryResult.pageInfo.endCursor
    },
    "warnings": $count($._warnings) > 0 ? $._warnings : undefined
  }
)
```

**Enhanced error response with diagnostic "failedAt" tracing:**
```jsonata
{
  "error": true,
  "errorType": "EXECUTION_ERROR",
  "errorMessage": $state.lastError.message ? $state.lastError.message : "Dynamic query failed",
  "errorDetails": $state.lastError,
  "failedAt": $state.payload._model ? "Execute Query or Format Response"
    : $state.payload._schema ? "Build Fields or Build Query"
    : $state.payload._input ? "Fetch Schema"
    : "Validate or earlier"
}
```

**Key patterns:**
- Multi-step transform pipeline with progressive accumulation via `$merge([$, {...}])`
- `$query()` binding for inline GraphQL execution from transform nodes
- `$error()` for explicit error throwing (caught by tryCatch)
- `$lookup(object, key)` for dynamic property access by variable name
- Cursor-based pagination with `after`/`endCursor`/`hasNextPage`
- Schema-driven field validation warns about invalid fields instead of failing
- Complex type auto-expansion (Measure, Address, Money)
- `failedAt` diagnostic in error response traces which pipeline step failed

### Example G: Dynamic Mutate (Example) -- Schema-Driven Write Tool

**Purpose:** Execute create/update/delete mutations on ANY data model. Validates the operation against the schema, supports `dryRun` mode for safe validation without execution.

**mcpToolConfiguration:** `readOnly: false`, `destructive: true`, `idempotent: false`

**Flow (multi-step transform pipeline -- NO mutate node):**
```
debugSource-1 --> request-1 --> tryCatch-1
                    tryNextNodes: validate-1 --> transform-fetchSchema --> transform-validateOp
                      --> transform-buildMutation --> transform-execute --> transform-formatResponse --> response-1
                    catchNextNodes: error-response-1
```

**Input schema:**
```json
{
  "type": "object",
  "required": ["modelName", "operation"],
  "properties": {
    "modelName": { "type": "string", "description": "PascalCase name of the model" },
    "operation": { "type": "string", "enum": ["create", "update", "delete"], "description": "Mutation operation" },
    "data": { "type": "object", "description": "Fields to create or update. Required for create/update." },
    "id": { "type": "string", "description": "Record ID. Required for update/delete." },
    "returnFields": { "type": "array", "items": { "type": "string" }, "description": "Fields to return after mutation. Defaults to id + common fields." },
    "dryRun": { "type": "boolean", "default": false, "description": "Validate without executing. Uses GraphQL introspection." }
  }
}
```

**Transform -- Build Mutation (dynamic mutation construction):**
```jsonata
(
  $model := $._model;
  $op := $._input.operation;
  $returnFields := $._returnFields ? $join($._returnFields, " ") : "id";

  $mutation := $op = "create" ?
    "mutation Create" & $model & "($payload: [" & $model & "CreatePayloadInput!]!) { create" & $model & "(payload: $payload) { " & $returnFields & " } }" :
  $op = "update" ?
    "mutation Update" & $model & "($payload: [" & $model & "UpdatePayloadInput!]!) { update" & $model & "(payload: $payload) { " & $returnFields & " } }" :
    "mutation Delete" & $model & "($payload: [" & $model & "DeletePayloadInput!]!) { delete" & $model & "(payload: $payload) { id } }";

  $variables := $op = "create" ?
    { "payload": [{ "create": $._input.data }] } :
  $op = "update" ?
    { "payload": [{ "where": { "id": $._input.id }, "update": $._input.data }] } :
    { "payload": [{ "where": { "id": $._input.id } }] };

  $merge([$, { "_mutation": $mutation, "_variables": $variables }])
)
```

**Transform -- Execute (with dryRun support via GraphQL introspection):**
```jsonata
(
  $._input.dryRun ?
    (
      $inputTypeName := $._model & ($._input.operation = "create" ? "CreatePayloadInput" : "UpdatePayloadInput");
      $typeCheck := $query({
        "statement": "query ValidateType($name: String!) { __type(name: $name) { name inputFields { name type { name kind ofType { name kind } } } } }",
        "variables": { "name": $inputTypeName }
      });
      $merge([$, { "_result": { "dryRun": true, "valid": $typeCheck.__type != null, "inputType": $typeCheck.__type } }])
    )
  :
    $merge([$, { "_result": $mutate({ "statement": $._mutation, "variables": $._variables }) }])
)
```

**Key patterns:**
- **`$mutate()` binding** for write operations from transform nodes
- **Payload array wrapper** is MANDATORY: `[{ "create": ... }]`, `[{ "where": {...}, "update": {...} }]`, `[{ "where": {...} }]`
- **Mutation where uses direct id** (string), NOT `{ "_eq": ... }` like query filters
- **`dryRun` mode** uses GraphQL introspection (`__type`) to validate without executing
- **Smart return field defaults**: auto-detects common fields (id, code, name, active) from schema
- This is the ONLY flow with `readOnly: false`, `destructive: true`, `idempotent: false`

### Example H: Device Details (Example) -- Multi-Entity Single Query Tool

**Purpose:** Get details for any device-related entity (device, driver, driver type, or gateway) from a single tool.

**mcpToolConfiguration:** `readOnly: true`, `destructive: false`, `idempotent: true`

**Key pattern -- Multi-entity single query with cascading detection:**

The query fetches from FOUR root types simultaneously with the same ID:
```graphql
query($where: DeviceWhereInput, $driverWhere: DeviceDriverWhereInput, $typeWhere: DeviceDriverTypeWhereInput, $gwWhere: DeviceGatewayWhereInput) {
  device(where: $where, first: 1) { edges { node { id name ... } } }
  deviceDriver(where: $driverWhere, first: 1) { edges { node { id name ... } } }
  deviceDriverType(where: $typeWhere, first: 1) { edges { node { id name ... } } }
  deviceGateway(where: $gwWhere, first: 1) { edges { node { id name ... } } }
}
```

The transform uses cascading conditionals to detect which entity was found:
```jsonata
(
  $device := data.device.edges[0].node;
  $driver := data.deviceDriver.edges[0].node;
  $driverType := data.deviceDriverType.edges[0].node;
  $gateway := data.deviceGateway.edges[0].node;

  $device ? { "entityType": "device", ... } :
  $driver ? { "entityType": "deviceDriver", ... } :
  $driverType ? { "entityType": "deviceDriverType", ... } :
  $gateway ? { "entityType": "deviceGateway", ... } :
  { "error": true, "errorMessage": "No entity found with ID: " & payload.id }
)
```

**Key patterns:**
- Single query fetches from multiple root types simultaneously
- Cascading conditional determines which entity was found
- Reduces multiple tools to one by auto-detecting entity type

### Example I: Screen Details (Example) -- Graceful Error Recovery

**Purpose:** Get detailed info for a screen including elements, routes, versions, and deployments.

**Key pattern -- Graceful error recovery in catch block:**
```jsonata
(
  $errorInfo := $state.lastError;
  $screenData := $errorInfo.data.screen.edges[0].node;

  $screenData ? {
    "data": "## " & $screenData.name & "\n\n(Partial result -- some data may be incomplete)\n\n...",
    "_warning": "Query partially succeeded but threw error: " & $errorInfo.message
  } : {
    "error": true,
    "errorType": "EXECUTION_ERROR",
    "errorMessage": $errorInfo.message ? $errorInfo.message : "Failed to retrieve screen details",
    "errorDetails": $errorInfo
  }
)
```

**Key pattern:** When a query partially succeeds but throws (e.g., null reference on optional nested data), the catch block attempts to extract usable data from the error's partial response. Returns the data with a `_warning` field rather than a hard failure.
