# Fuuz Prerequisites Reference

Before defining data models, certain artifacts must exist in the Fuuz environment. These prerequisites are included in the package and deployed in dependency order using `insertEarly: true` metadata and `insertBefore` declarations.

---

## Dependency Chain

```
ModuleGroup  →  Module  →  Sequence  →  DataModel
   (1st)          (2nd)      (3rd)        (last)
```

**CRITICAL:** This ordering is enforced by the platform. A data model cannot be deployed without its module, and a module cannot be deployed without its module group. Sequences must exist before any model fields reference them.

---

## 1. ModuleGroup

The top-level organizational unit for a Fuuz application. Every app needs at least one ModuleGroup.

### Seed Data Record

```json
{
  "api": "application",
  "modelName": "ModuleGroup",
  "payload": [
    {
      "id": "machineTelemetrySiteApplication",
      "name": "Machine Telemetry Site Application",
      "icon": "g",
      "description": "Manufacturing telemetry data collection and analysis.",
      "_customFields": { "_externalId": null }
    }
  ],
  "metadata": { "insertEarly": true }
}
```

### Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | String | Yes | camelCase unique identifier. |
| `name` | String | Yes | Human-readable display name. |
| `icon` | String | No | Single character or icon identifier for the UI. |
| `description` | String | No | Description of the module group's purpose. |
| `_customFields._externalId` | String | No | External system mapping. Set to `null` if not used. |

### definition.json Selection

```json
{
  "id": "generated-cuid",
  "api": "application",
  "modelName": "ModuleGroup",
  "selected": [
    { "id": "machineTelemetrySiteApplication", "label": "Machine Telemetry Site Application" }
  ],
  "fields": [
    { "name": "id" },
    { "name": "name" },
    { "name": "icon" },
    { "name": "description" },
    { "name": "_customFields._externalId" }
  ],
  "metadata": {
    "dataTransform": "$",
    "viewFields": [],
    "labelField": "name"
  },
  "packageDefinitionVersionId": "version-cuid"
}
```

### Naming Convention

- `id`: camelCase, descriptive of the application domain (e.g., `machineTelemetrySiteApplication`, `qualityManagementSystem`, `warehouseOperations`)
- `name`: Human-readable, title case

---

## 2. Module

Groups related artifacts (data models, data flows, screens) within a ModuleGroup. Models, flows, and screens reference their module by `moduleId`.

### Seed Data Record

```json
{
  "api": "application",
  "modelName": "Module",
  "payload": [
    {
      "id": "machineDataApp",
      "name": "Machine Data App",
      "description": "Core data models for machine telemetry and OEE tracking.",
      "moduleGroupId": "machineTelemetrySiteApplication",
      "_customFields": { "_externalId": null }
    }
  ],
  "metadata": { "insertEarly": true }
}
```

### Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | String | Yes | camelCase unique identifier. |
| `name` | String | Yes | Human-readable display name. |
| `description` | String | No | Description of the module's purpose. |
| `moduleGroupId` | String | Yes | Foreign key to the parent ModuleGroup. |
| `_customFields._externalId` | String | No | External system mapping. |

### definition.json Selection

```json
{
  "id": "generated-cuid",
  "api": "application",
  "modelName": "Module",
  "selected": [
    { "id": "machineDataApp", "label": "Machine Data App" }
  ],
  "fields": [
    { "name": "id" },
    { "name": "name" },
    { "name": "description" },
    { "name": "moduleGroupId" },
    { "name": "_customFields._externalId" }
  ],
  "metadata": {
    "labelField": "name",
    "insertBefore": [
      { "modelName": "DataModel" }
    ]
  },
  "packageDefinitionVersionId": "version-cuid"
}
```

**Note the `insertBefore`** — this ensures Modules are imported before DataModels.

### Naming Convention

- `id`: camelCase, functional name (e.g., `machineDataApp`, `qualityModule`, `inventoryModule`)
- `name`: Human-readable, title case
- An app can have multiple modules to organize different functional areas

### How Models Reference Their Module

Every model's `metadata.mfgx` must include:

```json
"module": {
  "id": "machineDataApp",
  "group": {
    "id": "machineTelemetrySiteApplication"
  }
}
```

---

## 3. Sequence

Auto-increment generators for human-readable codes. Must be defined before any model field that references them.

### Seed Data Record

```json
{
  "api": "application",
  "modelName": "Sequence",
  "payload": [
    {
      "id": "alarmCode",
      "name": "AlarmCode",
      "type": "String",
      "increment": 1,
      "prefix": "ALM-",
      "suffix": "",
      "zeroPadding": 3,
      "_customFields": { "_externalId": null }
    },
    {
      "id": "assetCode",
      "name": "AssetCode",
      "type": "String",
      "increment": 1,
      "prefix": "ASSET-",
      "suffix": "",
      "zeroPadding": 1,
      "_customFields": { "_externalId": null }
    },
    {
      "id": "dataPointCode",
      "name": "DataPointCode",
      "type": "String",
      "increment": 1,
      "prefix": "DP-0",
      "suffix": "",
      "zeroPadding": 3,
      "_customFields": { "_externalId": null }
    }
  ],
  "metadata": { "insertEarly": true }
}
```

### Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | String | Yes | camelCase unique identifier. |
| `name` | String | Yes | PascalCase display name. |
| `type` | String | Yes | `"String"` or `"Int"`. Determines the generated value type. |
| `increment` | Int | Yes | How much to increment per generated value (usually `1`). |
| `prefix` | String | No | Text prepended to the generated number (e.g., `"ALM-"`, `"ASSET-"`). Only for String type. |
| `suffix` | String | No | Text appended to the generated number. Only for String type. |
| `zeroPadding` | Int | No | Minimum digits (left-padded with zeros). `3` means `001, 002, 003`. |
| `_customFields._externalId` | String | No | External system mapping. |

### definition.json Selection

```json
{
  "id": "generated-cuid",
  "api": "application",
  "modelName": "Sequence",
  "selected": [
    { "id": "alarmCode", "label": "AlarmCode" },
    { "id": "assetCode", "label": "AssetCode" },
    { "id": "dataPointCode", "label": "DataPointCode" }
  ],
  "fields": [
    { "name": "id" },
    { "name": "name" },
    { "name": "type" },
    { "name": "increment" },
    { "name": "prefix" },
    { "name": "suffix" },
    { "name": "zeroPadding" },
    { "name": "_customFields._externalId" }
  ],
  "metadata": {
    "labelField": "name",
    "insertBefore": [
      { "modelName": "DataModel" }
    ]
  },
  "packageDefinitionVersionId": "version-cuid"
}
```

### How Fields Reference Sequences

A model field's metadata includes:

```json
"sequence": { "id": "alarmCode" }
```

This tells the platform to auto-generate the next value from the `alarmCode` sequence when a record is created without a value for this field.

### Generated Value Examples

| Sequence Config | Generated Values |
|----------------|-----------------|
| `prefix: "ALM-", zeroPadding: 3` | ALM-001, ALM-002, ALM-003, ... |
| `prefix: "ASSET-", zeroPadding: 1` | ASSET-1, ASSET-2, ..., ASSET-10, ... |
| `prefix: "DP-0", zeroPadding: 3` | DP-0001, DP-0002, DP-0003, ... |
| `type: "Int", increment: 1` | 1, 2, 3, 4, ... |

### Planning Sequences

**When designing a schema, identify sequence needs early:**

1. Which models need auto-generated human-readable codes? (Most master models do)
2. What prefix pattern makes codes easily identifiable? (e.g., `WO-` for work orders, `PROD-` for production logs)
3. How much zero padding? (`3` = up to 999 before expanding, `5` = up to 99,999)
4. String or Int? (String for prefixed codes, Int for simple counters)

**Common sequence patterns for industrial apps:**

| Model | Sequence ID | Prefix | Padding | Example Output |
|-------|-------------|--------|---------|----------------|
| Alarm | `alarmCode` | `ALM-` | 3 | ALM-001 |
| Asset | `assetCode` | `ASSET-` | 4 | ASSET-0001 |
| DataPoint | `dataPointCode` | `DP-` | 5 | DP-00001 |
| WorkOrder | `workOrderCode` | `WO-` | 4 | WO-0001 |
| ProductionLog | `productionLogCode` | `PROD-` | 6 | PROD-000001 |
| InspectionLog | `inspectionCode` | `INS-` | 4 | INS-0001 |

---

## Complete Prerequisites Section in package-data.json

The `data` array must start with prerequisites in this exact order:

```json
{
  "data": [
    {
      "api": "application",
      "modelName": "ModuleGroup",
      "payload": [ ... ],
      "metadata": { "insertEarly": true }
    },
    {
      "api": "application",
      "modelName": "Module",
      "payload": [ ... ],
      "metadata": { "insertEarly": true }
    },
    {
      "api": "application",
      "modelName": "Sequence",
      "payload": [ ... ],
      "metadata": { "insertEarly": true }
    }
  ]
}
```

All three use `"metadata": { "insertEarly": true }` to ensure they are processed before any other data in the package.
