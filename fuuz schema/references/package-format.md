# Fuuz Package Format Reference

Every Fuuz package is a `.fuuz` file (a `.tar` archive) containing exactly three JSON files. This is the only mechanism for importing data models into Fuuz — there is no standalone model import feature.

---

## Package File Structure

```
MyPackage@1.0.0.fuuz (tar archive)
├── manifest.json         # Package identity and version
├── definition.json       # Export selections and ordering metadata
└── package-data.json     # Actual content: models, flows, screens, seed data
```

---

## 1. manifest.json

Identifies the package and its compatibility requirements.

```json
{
  "name": "MachineTelemetryApp",
  "version": "0.0.4",
  "applicationPublisherId": "fuuz",
  "enterpriseId": "mfgx",
  "environmentId": "build",
  "dependencies": {},
  "specVersion": "2.0.0",
  "platformVersion": "2026.2.0"
}
```

### Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | String | Yes | Package name. PascalCase or hyphenated. Must be unique within the enterprise. |
| `version` | String | Yes | Semantic version (`major.minor.patch`). |
| `applicationPublisherId` | String | Yes | Publisher identifier (e.g., `"fuuz"`). |
| `enterpriseId` | String | Yes | Enterprise identifier (e.g., `"mfgx"`). |
| `environmentId` | String | Yes | Source environment (e.g., `"build"`, `"staging"`, `"production"`). |
| `dependencies` | Object | Yes | Map of dependent package names to version ranges. Empty `{}` if none. |
| `specVersion` | String | Yes | Package specification version. Use `"2.0.0"`. |
| `platformVersion` | String | Yes | Minimum Fuuz platform version required (e.g., `"2026.2.0"`). |

---

## 2. definition.json

Declares what artifact types the package contains and controls import ordering.

```json
{
  "packageDefinition": {
    "name": "ExampleSchemaPackage-CustomModels"
  },
  "packageDefinitionVersion": {
    "id": "cmldswa0z002v356rmaja3s5n",
    "version": "0.0.1",
    "packageDefinitionId": "exampleSchemaPackageCustomModels",
    "exportedFileId": null
  },
  "packageDefinitionSelections": [
    {
      "id": "unique-selection-id",
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
      "packageDefinitionVersionId": "cmldswa0z002v356rmaja3s5n"
    }
  ]
}
```

### packageDefinitionSelections Array

Each entry declares one artifact type included in the package:

| Field | Description |
|-------|-------------|
| `id` | Unique ID for this selection entry (CUID format). |
| `api` | Always `"application"` for custom schema packages. |
| `modelName` | The system model name: `"ModuleGroup"`, `"Module"`, `"Sequence"`, `"DataModel"`, `"Screen"`, `"DataFlow"`, etc. |
| `selected` | Array of `{ id, label }` for each record being exported. |
| `fields` | Array of `{ name }` for each field to include in the export. |
| `metadata.labelField` | Which field to use as the display label. |
| `metadata.insertBefore` | **CRITICAL for ordering.** Array of `{ modelName }` entries. |
| `packageDefinitionVersionId` | Must match `packageDefinitionVersion.id`. |

### Ordering with insertBefore

**CRITICAL:** Prerequisites must declare `insertBefore` to ensure they are imported before dependent artifacts:

```json
{
  "modelName": "Module",
  "metadata": {
    "labelField": "name",
    "insertBefore": [
      { "modelName": "DataModel" }
    ]
  }
}
```

```json
{
  "modelName": "Sequence",
  "metadata": {
    "labelField": "name",
    "insertBefore": [
      { "modelName": "DataModel" }
    ]
  }
}
```

### Standard Selection Order for Schema Packages

1. `ModuleGroup` — no insertBefore needed (always first)
2. `Module` — `insertBefore: [{ modelName: "DataModel" }]`
3. `Sequence` — `insertBefore: [{ modelName: "DataModel" }]`
4. `DataModel` — the models themselves

For full application packages, additional selections follow:
5. `Screen`, `DataFlow`, `DataFlowSchedule`, `DataFlowScheduleFrequency`
6. Any seed data model selections (e.g., `Setting`, `Role`, `UnitType`, `Unit`, etc.)

---

## 3. package-data.json

Contains the actual content. Structure varies by package type.

### Schema-Only Package

```json
{
  "dataModels": [
    {
      "header": { ... },
      "version": { ... },
      "migration": null,
      "migrations": []
    }
  ],
  "data": [
    { "api": "application", "modelName": "ModuleGroup", "payload": [...], "metadata": { "insertEarly": true } },
    { "api": "application", "modelName": "Module", "payload": [...], "metadata": { "insertEarly": true } },
    { "api": "application", "modelName": "Sequence", "payload": [...], "metadata": { "insertEarly": true } }
  ]
}
```

### Full Application Package

```json
{
  "dataModels": [ ... ],
  "dataFlows": [ ... ],
  "screens": [ ... ],
  "savedTransforms": [ ... ],
  "dataMappings": [ ... ],
  "documentDesigns": [ ... ],
  "data": [ ... ]
}
```

### dataModels Array

Each entry contains the complete model definition:

```json
{
  "header": {
    "id": "alarm",
    "name": "Alarm",
    "description": "Represents an alarm event...",
    "dataModelKindId": "reference",
    "_customFields": { "_externalId": null }
  },
  "version": {
    "id": "cml5f1alfmi8e0170awrfab06",
    "number": "4",
    "dataModelId": "alarm",
    "modelDefinition": {
      "name": "Alarm",
      "metadata": { ... },
      "kind": "Reference",
      "fields": [ ... ],
      "description": "Represents an alarm event..."
    }
  },
  "migration": null,
  "migrations": []
}
```

**Header fields:**

| Field | Description |
|-------|-------------|
| `id` | camelCase model identifier. |
| `name` | PascalCase display name. |
| `description` | Markdown description of the model's purpose. |
| `dataModelKindId` | Always `"reference"`. |
| `_customFields._externalId` | External system mapping. Set to `null` if not used. |

**Version fields:**

| Field | Description |
|-------|-------------|
| `id` | Unique version identifier (CUID format). |
| `number` | Version number as string (e.g., `"1"`, `"4"`). |
| `dataModelId` | Must match the header `id`. |
| `modelDefinition` | The complete model definition (see Model Metadata and Field references). |

### data Array

Seed data records, ordered by dependency. Each entry:

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

**CRITICAL ordering rules for the data array:**
1. `ModuleGroup` — `insertEarly: true`
2. `Module` — `insertEarly: true`
3. `Sequence` — `insertEarly: true`
4. Setup/reference data (AlarmState, Mode, State, EventCategory, etc.)
5. Master data (Site, Area, Line, Cell, Workcenter, Asset, DataPoint, Product)
6. Transactional data (WorkOrder, ProductionLog, OeeHourly, etc.)
7. Schedule data (DataFlowSchedule, DataFlowScheduleFrequency) — last

See `references/seed-data-patterns.md` for complete examples of each data type.

---

## ID Generation

Package IDs use CUID format (e.g., `cmldswa0z002v356rmaja3s5n`). When generating packages:

- **Model IDs** (`header.id`): Use meaningful camelCase identifiers (e.g., `alarm`, `productionLog`)
- **Version IDs** (`version.id`): Generate CUIDs or use descriptive IDs
- **Selection IDs** (`packageDefinitionSelections[].id`): Generate CUIDs
- **Package definition version ID**: Generate a CUID, ensure all selections reference it via `packageDefinitionVersionId`
- **Seed data record IDs**: Use meaningful identifiers where possible (e.g., `"first"` for a shift, `"alarmCode"` for a sequence)

---

## Package Naming Convention

```
{PackageName}@{version}.fuuz
```

Examples:
- `MachineTelemetryApp@0.0.4.fuuz`
- `SystemSchema@0.0.2.fuuz`
- `ExampleSchemaPackage-CustomModels@0.0.1.fuuz`
