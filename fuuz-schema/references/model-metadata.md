# Fuuz Model-Level Metadata Reference

Every data model in Fuuz has a `metadata` object within its `modelDefinition` that controls the model's behavior, API exposure, indexing, triggers, and organizational placement. This reference documents every property with examples from production models.

---

## Complete Model Metadata Structure

```json
{
  "name": "Alarm",
  "metadata": {
    "mfgx": {
      "module": {
        "id": "machineDataApp",
        "group": {
          "id": "machineTelemetrySiteApplication"
        }
      },
      "indices": [],
      "exposed": true,
      "mutable": true,
      "mutations": {
        "create": true,
        "update": true,
        "del": true
      },
      "dataChangeCapture": {
        "exposed": false,
        "retentionDays": 120
      },
      "customFields": {
        "exposed": true
      },
      "labelField": "code",
      "editor": {
        "autosort": true
      },
      "triggers": {
        "create": "(jsonataExpression)",
        "update": "(jsonataExpression)",
        "delete": "(jsonataExpression)"
      },
      "queries": {
        "defaultReadPreference": "primaryPreferred"
      }
    },
    "mongo": {
      "collection": "alarm"
    },
    "graphql": {
      "type": "Object"
    }
  },
  "kind": "Reference",
  "fields": [],
  "description": "Model description in Markdown."
}
```

---

## Required Metadata

### module (REQUIRED)

Assigns the model to a Module within a ModuleGroup. Both must exist before the model is deployed.

```json
"module": {
  "id": "machineDataApp",
  "group": {
    "id": "machineTelemetrySiteApplication"
  }
}
```

**CRITICAL:** Every model MUST be assigned to a module. The `module.id` and `group.id` must reference existing Module and ModuleGroup records in the package.

### exposed (REQUIRED)

Controls whether the model's GraphQL API is accessible.

```json
"exposed": true
```

| Value | Meaning |
|-------|---------|
| `true` | Model has a public GraphQL API (query, mutate, subscribe) |
| `false` | Model exists in the database but has no API exposure |

Almost always `true` for custom models.

### mutable (REQUIRED)

Controls whether records can be modified after creation.

```json
"mutable": true
```

| Value | Meaning |
|-------|---------|
| `true` | Records can be updated and deleted |
| `false` | Records are append-only (create only, no updates or deletes) |

Use `false` for audit logs or immutable event streams.

### mutations (REQUIRED)

Fine-grained control over which mutation types are available.

```json
"mutations": {
  "create": true,
  "update": true,
  "del": true
}
```

| Property | When `false` |
|----------|-------------|
| `create` | No `createModelName` mutation available |
| `update` | No `updateModelName` mutation available |
| `del` | No `deleteModelName` mutation available |

**Note:** The property is `del`, not `delete` (reserved word).

### labelField (REQUIRED)

Specifies which field is used as the human-readable label in the platform UI (dropdowns, references, search results).

```json
"labelField": "code"
```

**Common patterns:**
- Master models: `"code"` or `"name"`
- Setup models: `"code"` or `"name"`
- Transactional models: `"code"` (sequence-generated)

### kind (REQUIRED — top-level, not inside metadata)

```json
"kind": "Reference"
```

Always `"Reference"`. Fuuz does not support embedded models. Every model is a standalone Reference model.

---

## Optional Metadata

### indices

Array of database indices for query performance and data lifecycle management.

#### Composite Index

Multi-field index for queries that filter on multiple fields together.

```json
"indices": [
  {
    "name": "dataType",
    "unique": false,
    "fields": ["dataType", "historize", "enabled"]
  }
]
```

| Property | Description |
|----------|-------------|
| `name` | Descriptive name for the index |
| `unique` | Whether the combination of fields must be unique |
| `fields` | Array of field names to index together |

#### TTL Index (Time-To-Live)

Automatically expires and deletes records after a specified duration. Essential for high-volume time-series data.

```json
"indices": [
  {
    "name": "TTLIndex",
    "unique": false,
    "ttl": 14400,
    "partialFilterPredicate": {},
    "fields": ["createdAt"]
  }
]
```

| Property | Description |
|----------|-------------|
| `ttl` | Time-to-live in **seconds**. Records older than this are automatically deleted. |
| `fields` | Must reference a DateTime field (typically `"createdAt"`). |
| `partialFilterPredicate` | Optional MongoDB filter to apply TTL only to matching records. Empty `{}` applies to all. |

**Common TTL values:**
| Duration | Seconds | Use Case |
|----------|---------|----------|
| 4 hours | `14400` | Raw telemetry data (TelemetryRaw, TelemetryRawBool, TelemetryRawString) |
| 1 day | `86400` | Temporary processing data |
| 30 days | `2592000` | Short-term logs |
| 120 days | `10368000` | Medium-term event data |

#### Combined Indices

A model can have multiple indices of different types:

```json
"indices": [
  {
    "name": "TTLIndex",
    "unique": false,
    "ttl": 14400,
    "fields": ["createdAt"]
  },
  {
    "name": "lookupIndex",
    "unique": false,
    "fields": ["dataPointId", "createdAt"]
  }
]
```

### dataChangeCapture

Controls whether the platform records data change events for this model (used by `dataChanges` flow triggers).

```json
"dataChangeCapture": {
  "exposed": true,
  "retentionDays": 120
}
```

| Property | Description |
|----------|-------------|
| `exposed` | `true` to enable data change capture, `false` to disable |
| `retentionDays` | How long to keep change records (in days) |

**When to enable:**
- Models that trigger reactive flows via `dataChanges` nodes
- Models requiring audit trails of what changed

**When to disable:**
- High-volume transactional models where change tracking would be expensive (e.g., TelemetryRaw)
- Models that don't need reactive flow triggers

### customFields

Controls whether end users can add custom fields to this model at runtime (without schema redeployment).

```json
"customFields": {
  "exposed": true
}
```

Enable for models where users may need to extend the schema with additional fields specific to their environment.

### triggers

Server-side JSONata expressions that execute automatically on create, update, or delete operations.

```json
"triggers": {
  "create": "$ ~> | $ | { \"id\": code } |",
  "update": "$after ~> | $ | {\n  \"oee\": $after.availability * $after.performance * $after.quality\n} |",
  "delete": "(jsonataExpression)"
}
```

**Trigger contexts:**
| Trigger | Variable | Description |
|---------|----------|-------------|
| `create` | `$` | The document being created |
| `update` | `$after` | The document after the update is applied |
| `delete` | `$` | The document being deleted |

**Common use cases:**

1. **ID Generation from Code** (AlarmState):
```json
"create": "$ ~> | $ | { \"id\": code } |"
```
Sets the record `id` to match the `code` field value on creation.

2. **Computed Field on Update** (OeeHourly):
```json
"update": "$after~>| $ | {\n  \"oee\": $after.availability * $after.performance * $after.quality\n}|"
```
Recalculates OEE whenever availability, performance, or quality values change.

3. **Composite Key Generation**:
```json
"create": "(\n  $doc := doc? doc : $;\n  $doc~>|$|{\"id\": exerciseId & \"::\" & userId}|\n)"
```
Creates a composite ID from two field values.

**Rules:**
- Triggers use JSONata syntax with the `~>` transform operator
- Use `| $ | { "field": value } |` pattern to merge computed fields into the document
- Keep triggers simple — complex business logic belongs in data flows
- Triggers run synchronously and add latency to mutations

### editor

Controls behavior in the Fuuz data model editor UI.

```json
"editor": {
  "autosort": true
}
```

| Property | Description |
|----------|-------------|
| `autosort` | `true` to auto-sort fields alphabetically in the editor |

### queries

Controls query behavior at the database level.

```json
"queries": {
  "defaultReadPreference": "primaryPreferred"
}
```

| Value | Description |
|-------|-------------|
| `"primaryPreferred"` | Read from primary replica when available, fall back to secondary |
| `"secondaryPreferred"` | Read from secondary replica when available |

Only set this for models with specific read consistency requirements.

---

## Standard Metadata by Model Type

### Master Model (e.g., Asset, Product, Workcenter)

```json
"metadata": {
  "mfgx": {
    "module": { "id": "myModule", "group": { "id": "myModuleGroup" } },
    "exposed": true,
    "mutable": true,
    "mutations": { "create": true, "update": true, "del": true },
    "dataChangeCapture": { "exposed": true, "retentionDays": 120 },
    "customFields": { "exposed": true },
    "labelField": "code",
    "editor": { "autosort": true }
  },
  "mongo": { "collection": "modelNameCamelCase" },
  "graphql": { "type": "Object" }
}
```

### Setup Model (e.g., Mode, State, EventCategory)

```json
"metadata": {
  "mfgx": {
    "module": { "id": "myModule", "group": { "id": "myModuleGroup" } },
    "exposed": true,
    "mutable": true,
    "mutations": { "create": true, "update": true, "del": true },
    "dataChangeCapture": { "exposed": false, "retentionDays": 120 },
    "customFields": { "exposed": true },
    "labelField": "code",
    "editor": { "autosort": true },
    "triggers": {
      "create": "$ ~> | $ | { \"id\": code } |"
    }
  },
  "mongo": { "collection": "modelNameCamelCase" },
  "graphql": { "type": "Object" }
}
```

### Transactional Model (e.g., Alarm, ProductionLog)

```json
"metadata": {
  "mfgx": {
    "module": { "id": "myModule", "group": { "id": "myModuleGroup" } },
    "exposed": true,
    "mutable": true,
    "mutations": { "create": true, "update": true, "del": true },
    "dataChangeCapture": { "exposed": false, "retentionDays": 120 },
    "customFields": { "exposed": true },
    "labelField": "code",
    "editor": { "autosort": true }
  },
  "mongo": { "collection": "modelNameCamelCase" },
  "graphql": { "type": "Object" }
}
```

### High-Volume Time-Series Model (e.g., TelemetryRaw)

```json
"metadata": {
  "mfgx": {
    "module": { "id": "myModule", "group": { "id": "myModuleGroup" } },
    "indices": [
      {
        "name": "TTLIndex",
        "unique": false,
        "ttl": 14400,
        "fields": ["createdAt"]
      }
    ],
    "exposed": true,
    "mutable": true,
    "mutations": { "create": true, "update": true, "del": true },
    "dataChangeCapture": { "exposed": false, "retentionDays": 120 },
    "customFields": { "exposed": false },
    "labelField": "id",
    "editor": { "autosort": true }
  },
  "mongo": { "collection": "modelNameCamelCase" },
  "graphql": { "type": "Object" }
}
```

---

## mongo and graphql Metadata

These are standard platform metadata objects included on every model:

```json
"mongo": {
  "collection": "alarm"
}
```

The `collection` value is the MongoDB collection name — use the camelCase model ID.

```json
"graphql": {
  "type": "Object"
}
```

Always `"Object"` for standard data models.
