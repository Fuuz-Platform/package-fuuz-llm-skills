# Fuuz Package Anatomy

High-level guide to Fuuz application packages — the distribution and deployment format for the Fuuz Industrial Operations Platform.

---

## What Is a Fuuz Package?

A `.fuuz` file is a **tar archive** containing three JSON files that fully describe an application or application component. Packages are the **only mechanism** for deploying data models, flows, screens, and seed data to Fuuz environments.

Key facts:
- File extension: `.fuuz`
- Archive format: tar (not gzip/compressed)
- Contains exactly 3 files: `manifest.json`, `definition.json`, `package-data.json`
- Naming convention: `AppName@version.fuuz` (e.g., `MachineTelemetryApp@0.0.4.fuuz`)
- Imported via **Configuration > Packages** in the Fuuz platform UI

---

## Package Files

### manifest.json — Package Identity

The manifest defines who the package is, what version it is, and what platform version it targets.

**Required fields:**

| Field | Type | Description |
|-------|------|-------------|
| `name` | String | Package name (PascalCase, no spaces) |
| `version` | String | Semantic version (e.g., "0.0.1") |
| `specVersion` | String | Always `"2.0.0"` |
| `label` | String | Human-readable display name |
| `description` | String | What this package contains |
| `platformVersion` | String | Minimum Fuuz platform version required |

**Optional fields:**

| Field | Type | Description |
|-------|------|-------------|
| `author` | String | Package author |
| `url` | String | Package homepage or documentation URL |
| `dependencies` | Object[] | Other packages this package depends on |

Example:
```json
{
  "name": "MachineTelemetryApp",
  "version": "0.0.4",
  "specVersion": "2.0.0",
  "label": "Machine Telemetry App",
  "description": "A machine telemetry and OEE monitoring application",
  "platformVersion": "4.41.0",
  "author": "Fuuz Development Team",
  "url": "https://fuuz.com"
}
```

---

### definition.json — Export Selections

The definition file describes what the package contains, with metadata about how each item should be installed. It consists of `packageDefinitionSelections` — an array of selection objects, one per artifact type.

**Structure:**
```json
{
  "packageDefinitionSelections": [
    {
      "modelName": "ArtifactType",
      "fields": [ ... ],
      "insertBefore": [ ... ]
    },
    ...
  ]
}
```

**Common modelName values:**

| modelName | Description |
|-----------|-------------|
| `DataModel` | Data model schema definitions |
| `DataFlow` | Data flow configurations |
| `Screen` | Screen component trees |
| `SavedTransform` | Reusable transform scripts |
| `DataMapping` | Data mapping configurations |
| `DocumentDesign` | Document templates |
| `Sequence` | Auto-increment sequences |
| `Module` | Application modules |
| `ModuleGroup` | Top-level module groups |

**fields**: Array of field selection objects — each specifies `{ "name": "fieldName", "children": [...] }` to control which fields are included in the export.

**insertBefore**: Defines dependency ordering. For example, Sequence and Module selections have:
```json
"insertBefore": [{ "modelName": "DataModel" }]
```
This ensures sequences and modules are installed before data models during import.

---

### package-data.json — Content

The package-data file contains all the actual artifact definitions and seed data. It has several top-level keys:

**Artifact Arrays:**

| Key | Content | Description |
|-----|---------|-------------|
| `dataModels` | Array of model definitions | Schema definitions with fields, metadata, relationships |
| `dataFlows` | Array of flow definitions | Flow JSON with node configurations and connections |
| `screens` | Array of screen definitions | Screen JSON with component trees, routes, properties |
| `savedTransforms` | Array of transforms | Reusable JSONata or JavaScript transforms |
| `dataMappings` | Array of data mappings | Data transformation mappings |
| `documentDesigns` | Array of doc designs | Document/report templates |

**Seed Data Array:**

| Key | Content | Description |
|-----|---------|-------------|
| `data` | Array of seed data groups | Pre-populated records for settings, reference data, roles, etc. |

**Notes:**
- Artifact arrays contain complete, self-contained definitions — not references or partial schemas.
- The `data` array ordering matters. Records are inserted in the order they appear, so dependencies must come first.

---

## Artifact Types in Detail

### Data Models (dataModels)

Each data model entry contains:
- **header**: id, name, version, description
- **version**: schema version info (major, minor)
- **modelDefinition**: The complete model schema
  - `name`, `id`, `type` (master/setup/transactional)
  - `kind`: Always `"Reference"`
  - `fields`: Array of field definitions with types and metadata
  - `metadata.mfgx`: Module assignment, indices, triggers, labelField, mutations config, etc.

**Model types:**
- **master**: Core business entities (e.g., Machine, Product, WorkOrder)
- **setup**: Configuration and reference data (e.g., MachineType, UnitType)
- **transactional**: Event and log records (e.g., TelemetryReading, ProductionLog)

### Data Flows (dataFlows)

Each flow entry contains:
- **header**: id, name, dataFlowTypeId (System/Screen/Edge/Integration/Document), moduleId
- **version**: flow version info
- **flow**: The complete flow definition with nodes, connections, and configurations

**Flow types by dataFlowTypeId:**
- **System**: Server-side background processing and scheduled tasks
- **Screen**: Client-side flows triggered by UI events
- **Edge**: Flows that run on edge gateway devices
- **Integration**: Flows that connect to external systems via connectors
- **Document**: Flows that generate documents and reports

### Screens (screens)

Each screen entry contains:
- **header**: id, name, moduleId
- **version**: screen version info
- **routes**: URL routing configuration
- **screen**: Component tree JSON with layout, data bindings, and event handlers

**Key screen concepts:**
- Screens are routed by URL path, configured in the `routes` property.
- The component tree is recursive — containers hold child components.
- Data bindings connect components to data model queries and flow outputs.
- Event handlers trigger screen flows or navigate between screens.

### Seed Data (data)

The data array contains groups of records to insert during import. Each group has:
```json
{
  "api": "application" | "system",
  "model": "ModelName",
  "records": [ { record fields... } ],
  "metadata": { "insertEarly": true }
}
```

**api values:**
- `"application"`: Records for application-defined models (custom models in the package)
- `"system"`: Records for platform system models (ModuleGroup, Module, Role, etc.)

**metadata.insertEarly:**
- When `true`, the record group is installed before other data during import.
- Used for prerequisite records that other artifacts depend on (ModuleGroup, Module, Sequence).

The platform processes data groups in array order. Groups that other records depend on must appear earlier in the array.

---

## Dependency Ordering

Fuuz enforces strict dependency ordering during package import. The platform uses two mechanisms:

### 1. insertBefore (in definition.json)
Artifact type selections can declare that they must be installed before other types:
```json
{
  "modelName": "Sequence",
  "insertBefore": [{ "modelName": "DataModel" }]
}
```

### 2. insertEarly (in package-data.json seed records)
Individual seed data groups can be flagged to install before other data:
```json
{
  "api": "system",
  "model": "ModuleGroup",
  "records": [{ ... }],
  "metadata": { "insertEarly": true }
}
```

### Required Ordering

The correct dependency order for a complete application package:

| Order | Artifact | Mechanism | Why |
|-------|----------|-----------|-----|
| 1 | ModuleGroup records | `insertEarly: true` | Everything belongs to a module group |
| 2 | Module records | `insertEarly: true` | Models, flows, screens belong to modules |
| 3 | Sequence records | `insertEarly: true` + `insertBefore: DataModel` | Fields reference sequences |
| 4 | Data Models | definition.json selection | Schema must exist before data |
| 5 | Setup seed data | data[] ordering | Reference data for other records |
| 6 | Master seed data | data[] ordering | Core records that transactions reference |
| 7 | Screens & Flows | definition.json selections | Depend on models existing |
| 8 | Settings, Roles, Access Control | data[] ordering | Configuration records |
| 9 | Schedule configurations | data[] ordering | Depend on flows existing |

---

## Package Types

Packages vary in complexity:

### Schema-Only Package
Contains only data models and their prerequisites:
- `dataModels`: Model definitions
- `data`: ModuleGroup + Module + Sequence records
- No flows, no screens

Example: `ExampleSchemaPackage-CustomModels@0.0.1`

### Full Application Package
Contains the complete application:
- `dataModels`: All model definitions
- `dataFlows`: All flow definitions
- `screens`: All screen definitions
- `savedTransforms`: Reusable transforms
- `data`: Full seed data (prerequisites + reference data + settings + roles + schedules)

Example: `MachineTelemetryApp@0.0.4` (24 models, 18 flows, 13 screens, 35 data groups)

### System Schema Package
Contains platform infrastructure models:
- `dataModels`: System model definitions (User, Role, Screen, etc.)
- System packages are managed by the platform — developers rarely create these

Example: `SystemSchema@0.0.2`

---

## Common data[] Model Types

These system models commonly appear in the data[] seed array:

| System Model | Purpose | Typical Records |
|-------------|---------|-----------------|
| `ModuleGroup` | App hierarchy top level | 1 per application |
| `Module` | Groups artifacts within an app | 1+ per module group |
| `Sequence` | Auto-increment generators | 1 per auto-code field |
| `Setting` | Platform settings overrides | Locale, theme, formats |
| `Role` | User roles | Admin, Operator, Viewer |
| `AccessControlPolicyGroup` | Permission sets | Per-role access policies |
| `AccessControlPolicy` | Individual permissions | CRUD per model per role |
| `UnitType` | Measurement categories | Length, Weight, Volume |
| `Unit` | Specific units | meters, kg, liters |
| `UnitConversion` | Conversion factors | Between units |
| `ScheduleGroup` | Schedule categories | Shift schedules |
| `Schedule` | Named schedules | 1st Shift, 2nd Shift |
| `ScheduleEvent` | Schedule occurrences | RRule + Duration |
| `DataFlowSchedule` | Flow-schedule links | Which flow runs when |
| `DataFlowScheduleFrequency` | Cron schedules | "*/5 * * * *" |

---

## Creating a Package

### For Data Models (use fuuz-schema skill)
The fuuz-schema skill generates complete package JSON (all 3 files) for data model packages. It handles:
- Manifest generation with proper versioning
- Definition selections with insertBefore ordering
- Package-data with model definitions and seed data in correct dependency order

### For Flows (use fuuz-flows skill)
Data flows are defined using the fuuz-flows skill. Flow JSON is placed in the `dataFlows` array of package-data.json.

### For Screens (use fuuz-screens skill)
Screens are defined using the fuuz-screens skill. Screen JSON is placed in the `screens` array of package-data.json.

### For Complete Applications
Use all four skills together:
1. **fuuz-schema** — Design data models and generate the base package structure
2. **fuuz-flows** — Build flow definitions and add them to the package
3. **fuuz-screens** — Create screen definitions and add them to the package
4. **fuuz-platform** — Reference for connectors, seeded values, and platform architecture

---

## Importing Packages

1. Navigate to **Configuration > Packages** in the Fuuz platform
2. Upload the `.fuuz` file
3. The platform validates the manifest and checks dependencies
4. Artifacts are installed in dependency order (respecting `insertBefore` declarations)
5. Early seed data groups (with `insertEarly: true`) are inserted first
6. Data models are deployed (creating GraphQL API endpoints and database collections)
7. Remaining seed data is inserted, then flows and screens are registered

**Notes:**
- If a package with the same name already exists, the platform performs an upgrade (not a duplicate install).
- Model schema changes are applied as migrations — existing data is preserved where possible.
- Failed imports are rolled back to maintain environment consistency.

---

## Versioning

Package versions follow semantic versioning conventions:

- **Major version** (X.0.0): Breaking changes to the data model schema or flow interfaces
- **Minor version** (0.X.0): New models, flows, screens, or seed data additions
- **Patch version** (0.0.X): Bug fixes, label changes, minor configuration updates

When upgrading (importing a newer version over an existing one), new models are created, existing models gain new fields (preserving existing data), seed data records are upserted by ID, and flows/screens are replaced with the new versions.

---

## Related References

| Reference | Skill | Content |
|-----------|-------|---------|
| `fuuz schema/references/package-format.md` | fuuz-schema | Detailed package JSON structure with annotated examples |
| `fuuz schema/references/prerequisites.md` | fuuz-schema | ModuleGroup to Module to Sequence dependency chain |
| `fuuz schema/references/seed-data-patterns.md` | fuuz-schema | Data array structure and ordering rules |
| `fuuz-platform/references/platform-architecture.md` | fuuz-platform | Deployment pipeline and package content types |
