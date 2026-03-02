# Fuuz Field Metadata Reference

Every field in a Fuuz data model includes a `metadata` object that controls how the field behaves in queries, mutations, and the platform UI. This reference documents every metadata property with examples from production models.

---

## Complete Field Metadata Structure

```json
{
  "name": "fieldName",
  "type": "String!",
  "description": "**`fieldName`** (`String!`)\nMarkdown description of the field's purpose.",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true },
      "dataChangeCapture": { "include": true },
      "sequence": { "id": "sequenceId" },
      "relation": {
        "deletionReferenceBehavior": "prevent",
        "fields": { "from": "foreignKeyField", "to": "id" }
      },
      "defaultValue": "someValue",
      "defaultExpression": "{jsonataExpression}",
      "expression": "(jsonataExpression)",
      "schema": {}
    },
    "unique": false
  }
}
```

---

## Required Metadata (Every Field)

### mutations

Controls whether the field is included in create and update mutations.

```json
"mutations": {
  "create": { "include": true },
  "update": { "include": true }
}
```

| Setting | Meaning |
|---------|---------|
| `create.include: true` | Field can be set when creating a record |
| `create.include: false` | Field cannot be set on create (system-managed or computed) |
| `update.include: true` | Field can be modified after creation |
| `update.include: false` | Field is immutable after creation |

**Common patterns:**
- `id` field: `create: true, update: false` — Set once, never changed
- Most data fields: `create: true, update: true` — Full read/write
- Computed fields: `create: false, update: false` — System-managed only

### queries

Controls whether the field appears in query results.

```json
"queries": { "include": true }
```

Set to `false` only for fields that should never be returned by the API (rare — most fields should be queryable).

### where

Controls whether the field can be used in GraphQL `where` filter clauses.

```json
"where": { "include": true }
```

Set to `true` for any field users might filter on. Set to `false` for large text fields, JSON blobs, or fields that would not benefit from filtering.

### unique

Top-level metadata flag (outside `mfgx`) indicating whether the field value must be unique across all records.

```json
"metadata": {
  "mfgx": { ... },
  "unique": true
}
```

**Common unique fields:**
- `id` — Always `unique: true`
- `code` — Usually `unique: true` for master/setup models (but `false` when using sequences since uniqueness is managed by the sequence)
- Most other fields — `unique: false`

---

## Optional Metadata

### dataChangeCapture

Controls whether changes to this field trigger data change events (used by `dataChanges` flow triggers).

```json
"dataChangeCapture": { "include": true }
```

Set to `true` for fields where changes should trigger reactive flows. Omit or set to `false` for fields that change frequently but don't need event tracking (e.g., raw telemetry values).

**Note:** The model itself must also have `dataChangeCapture.exposed: true` in its model-level metadata for this to take effect.

### sequence

Links the field to an auto-increment sequence. The sequence must be defined as a prerequisite in the package.

```json
"sequence": { "id": "alarmCode" }
```

When a record is created without providing a value for this field, the platform auto-generates the next value from the sequence (e.g., `ALM-001`, `ALM-002`).

**Rules:**
- The referenced sequence must exist before the model is deployed
- Typically used on `code` fields (String type) or counter fields (Int type)
- The sequence `id` must match a Sequence record in the package's `data` array
- See `references/prerequisites.md` for sequence definition format

### relation

Defines a relationship to another data model. Only used on relationship-type fields (where `type` is another model name).

```json
"relation": {
  "deletionReferenceBehavior": "prevent",
  "fields": {
    "from": "alarmStateId",
    "to": "id"
  }
}
```

| Property | Values | Description |
|----------|--------|-------------|
| `deletionReferenceBehavior` | `"prevent"` | Cannot delete the referenced record while this reference exists |
| | `"cascade"` | Deleting the referenced record also deletes this record |
| | `"setNull"` | Deleting the referenced record sets this field to null |
| `fields.from` | String | The foreign key field on THIS model |
| `fields.to` | String | The target field on the RELATED model (almost always `"id"`) |

See `references/relationship-patterns.md` for complete relationship examples.

### defaultValue

Static default value assigned when a record is created without providing this field.

```json
"defaultValue": false
```

```json
"defaultValue": 60000
```

```json
"defaultValue": "someStaticValueHere"
```

Supports any JSON-compatible value: strings, numbers, booleans, null.

### defaultExpression

JSONata expression evaluated at record creation time to compute a default value.

```json
"defaultExpression": "{jsonataExpression}"
```

Unlike `defaultValue` (static), `defaultExpression` runs server-side and can reference other fields, generate timestamps, or perform calculations.

### expression

JSONata expression evaluated at **query time** — the field's value is computed dynamically every time it is queried, rather than stored.

```json
"expression": "(\r\n  {\"$someJsonataHere that runs and returns values to the Query each time its called\"}\r\n)"
```

**Use cases:**
- Computed/derived values that should always reflect current state
- Aggregations or lookups that would be expensive to maintain as stored values
- Virtual fields that combine data from multiple sources

**IMPORTANT:** Expression fields add query-time computation cost. Use stored fields with triggers for frequently-queried computed values.

### schema

JSON Schema definition for `JSONObject` type fields. Validates the structure of JSON data on write.

```json
"schema": {
  "type": "object",
  "properties": {
    "threshold": { "type": "number" },
    "enabled": { "type": "boolean" }
  }
}
```

Use an empty object `{}` when the JSONObject field has no schema validation.

---

## Standard Field Templates

### Primary Key Field (REQUIRED on every model)

```json
{
  "name": "id",
  "type": "ID!",
  "description": "**`id`** (`ID!`)\nPrimary unique identifier for the record.",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": false }
      },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": true
  }
}
```

### Standard Data Field

```json
{
  "name": "name",
  "type": "String!",
  "description": "**`name`** (`String!`)\nDisplay name for the record.",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true },
      "dataChangeCapture": { "include": true }
    },
    "unique": false
  }
}
```

### Sequence-Backed Code Field

```json
{
  "name": "code",
  "type": "String!",
  "description": "**`code`** (`String!`)\nUnique, human-readable code. Auto-generated from sequence.",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true },
      "sequence": { "id": "mySequence" }
    },
    "unique": false
  }
}
```

### Boolean Flag with Default

```json
{
  "name": "active",
  "type": "Boolean!",
  "description": "**`active`** (`Boolean!`)\nWhether the record is active.",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true },
      "defaultValue": true
    },
    "unique": false
  }
}
```

---

## Field Description Format

Field descriptions support Markdown and should follow this pattern:

```
**`fieldName`** (`Type`)
Brief description of the field's purpose.

- Additional detail or constraints.
- Example: `value-example`.
```

This format renders well in the Fuuz data model editor and provides consistent documentation.
