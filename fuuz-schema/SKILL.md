---
name: fuuz-schema
description: Design data models and generate Fuuz package files for the Fuuz Industrial Operations platform (fuuz.com). Use when requests mention "fuuz data model", "fuuz schema", "data model", "schema design", "fuuz package", "package file", "field types", "model relationships", "master data", "setup data", "transactional data", "module", "moduleGroup", "sequence", or designing the data layer for Fuuz applications. Covers data model types (master, setup, transactional), field types (20+), relationships (1:1, 1:many, many:1, many:many), model metadata (indices, triggers, mutations), prerequisite artifacts (modules, sequences), and Fuuz package JSON output format.
---

# Fuuz Data Model Designer

**Version 2.0** | Last Updated: 2026-02-21

Design production-ready data models and generate importable Fuuz package files for the Fuuz Industrial Operations platform.

## System Schema vs Custom Schema

Fuuz has two distinct schema layers:

**System Schema** — Platform infrastructure present in every Fuuz environment. Includes users, roles, permissions, settings, screens, visualizations, connectors, device drivers. Each tenant has its own system schema instance (separate database) but they all share the same structure. System models are managed by the platform — **never modify system schema**.

**Custom Schema** — Developer-created models unique to each application. Each Fuuz app has its own database with a completely independent custom schema. This skill focuses on designing custom schema.

Both layers use the same JSON definition format and expose GraphQL APIs.

### Schema Design Standards

- Always start with a **standard MES schema** — add fields or tables as needed
- Search for existing schema before adding new ones
- Never delete standard MES schema — someone else may need it later
- Skip screen generations for anything not being used
- Each table must have a description (including triggers, unique indexing constraints, exemptions)
- Each column must have a description (including default values and exemptions)
- Every table must have a meaningful label field — in most cases, that will NOT be `id`

## Application Architecture

Every Fuuz application is organized as:

```
ModuleGroup (app-level grouping)
  └── Module (functional area)
        ├── DataModels (data layer — this skill)
        ├── DataFlows (logic layer — fuuz-flows skill)
        └── Screens (UI layer — fuuz-screens skill)
```

Every data model automatically gets a fully-featured GraphQL API with queries (with filtering, ordering, pagination, aggregation), mutations (create, update, upsert, delete), and subscriptions.

Fuuz provides **two GraphQL APIs**:
- **Application API** — Custom models built with this skill
- **System API** — Platform models (users, roles, settings, etc.)

## Prerequisites — MUST Define Before Models

**CRITICAL:** The following artifacts must exist before data models can be deployed. They are included in the package and ordered using `insertEarly: true` metadata.

| Order | Artifact | Purpose | Required? |
|-------|----------|---------|-----------|
| 1st | **ModuleGroup** | Top-level app hierarchy | Yes — at least one |
| 2nd | **Module** | Groups models/flows/screens within a ModuleGroup | Yes — at least one |
| 3rd | **Sequence** | Auto-increment generators for code fields | Only if models use auto-generated codes |

**The dependency chain is strict:** ModuleGroup → Module → Sequence → DataModel. See `references/prerequisites.md` for complete JSON examples and configuration options.

## Data Model Types

Classify every model into one of three types:

| Type | Purpose | Volume | Examples |
|------|---------|--------|----------|
| **Master** | Core reference entities that rarely change | Low | Asset, Product, Workcenter, DataPoint, Site, Area, Line, Cell |
| **Setup** | Configuration and lookup data that establishes app behavior | Low | Mode, State, EventCategory, AlarmState |
| **Transactional** | Event/activity data capturing operations | High | ProductionLog, Alarm, TelemetryRaw, OeeHourly, WorkOrder, WorkcenterHistory |

**Decision guide:**
- "Is this something the system is *configured* with?" → Setup
- "Is this a *thing* the business tracks long-term?" → Master
- "Is this an *event* or *record* of something that happened?" → Transactional

## Data Model Kind

**Reference** — The only supported kind. All Fuuz data models are Reference models.

**IMPORTANT:** Fuuz does NOT support embedded/nested models. Every relationship between models requires a separate, standalone model with a foreign key reference. There are no embedded documents or sub-objects stored within a parent model's record.

## Field Types

Fuuz supports 20+ field types. Append `!` to make any field required (non-nullable).

### Core Types

| Type | Description |
|------|-------------|
| `ID` / `ID!` | Unique identifier. Every model MUST have `id: ID!`. |
| `String` / `String!` | Text value. |
| `Int` / `Int!` | 32-bit integer. |
| `Float` / `Float!` | Double-precision floating point. |
| `Boolean` / `Boolean!` | True/false. |

### Date/Time Types

| Type | Description |
|------|-------------|
| `DateTime` | ISO 8601 timestamp with timezone (stored UTC). |
| `Date` | Calendar date only. |
| `Time` | Time of day only. |
| `Duration` | ISO 8601 duration (supports format args in GraphQL queries). |
| `TimeZone` | IANA timezone string. |
| `RRule` | iCalendar recurrence rule. |

### Structured & Specialized Types

| Type | Description |
|------|-------------|
| `JSON` | Arbitrary JSON value. |
| `JSONObject` | JSON object with optional `schema` validation. |
| `RichText` | Rich text (Draft.js format). |
| `Address` | Structured address object. |
| `Measure` | Numeric value with unit of measure. |
| `RatioMeasure` | Ratio of two measures (e.g., parts/hour). |
| `IPCidr` / `IPv4Cidr` / `IPv6Cidr` | IP address with CIDR notation. |

### Relationship Types

| Type | Description |
|------|-------------|
| `ModelName` / `ModelName!` | To-one reference to another model. |
| `[ModelName!]!` | One-to-many collection of related records. |

See `references/field-types.md` for complete type reference with decision tree and usage guidance.

## Field Metadata

Every field requires a `metadata` object controlling its API behavior:

```json
{
  "name": "fieldName",
  "type": "String!",
  "description": "Markdown description.",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
}
```

**Required on every field:** `mutations`, `queries`, `where`, `unique`.

**Optional:** `dataChangeCapture`, `sequence`, `relation`, `defaultValue`, `defaultExpression`, `expression`, `schema`.

See `references/field-metadata.md` for complete metadata reference with all optional properties.

### Field Definition — Three Metadata Shapes

Every field in Fuuz falls into one of three metadata shapes:

**Shape 1: The `id` Field** — `update.include` MUST be `false`:
```json
{
  "name": "id", "type": "ID!",
  "metadata": {
    "mfgx": {
      "mutations": { "create": { "include": true }, "update": { "include": false } },
      "queries": { "include": true }, "where": { "include": true }
    },
    "unique": true
  }
}
```

**Shape 2: Scalar Fields** — MUST have `mutations`, `queries`, `where`:
```json
{
  "name": "fieldName", "type": "String",
  "metadata": {
    "mfgx": {
      "mutations": { "create": { "include": true }, "update": { "include": true } },
      "queries": { "include": true }, "where": { "include": true }
    },
    "unique": false
  }
}
```

**Shape 3: Relationship Fields** — Uses `relation` instead of `mutations`:
```json
{
  "name": "parentModel", "type": "ParentModel",
  "metadata": {
    "mfgx": {
      "relation": { "fields": { "from": "parentModelId", "to": "id" } },
      "queries": { "include": true }
    },
    "unique": false
  }
}
```

**Critical:** Using the wrong shape causes import errors. Scalar fields missing `mutations`/`queries`/`where` produce: `Cannot read properties of undefined (reading 'relation')`.

## Relationship Patterns — CRITICAL

Fuuz supports: **1:1, 1:many, many:1, many:many** (via junction model).

**NO embedded models.** Every relationship = separate model + foreign key reference.

**Relationships are TRIPLETS — ALWAYS define ALL THREE fields:**

1. **FK scalar field** — `alarmStateId` (type `ID!`) on the child model
2. **Forward relation field** — `alarmState` (type `AlarmState!`) on the child model with `relation` metadata
3. **Inverse list relation field** — `alarms` (type `[Alarm!]!`) on the parent model pointing back

### Part 1 — FK scalar field (on child model):
```json
{
  "name": "alarmStateId",
  "type": "ID!",
  "metadata": { "mfgx": { "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
}
```

### Part 2 — Forward relation (on child model):
```json
{
  "name": "alarmState",
  "type": "AlarmState!",
  "metadata": { "mfgx": { "relation": { "deletionReferenceBehavior": "prevent", "fields": { "from": "alarmStateId", "to": "id" } }, "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
}
```

### Part 3 — Inverse list relation (on parent AlarmState model):
```json
{
  "name": "alarms",
  "type": "[Alarm!]!",
  "metadata": { "mfgx": { "relation": { "fields": { "from": "id", "to": "alarmStateId" } }, "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
}
```

**Inverse relation rules:**
- Type is ALWAYS `[ChildModel!]!` (required non-null list of non-null items)
- `relation.fields.from` is always `"id"`, `to` is the FK field name on the child model
- Field name is the plural camelCase of the child model: `BatchDeviation` → `batchDeviations`
- Add inverse relations for ALL FK references within the same package (including lookup/enum models)
- Do NOT add inverse relations for FK references to models in external packages (e.g., Product, Unit from MES)
- ALL inverse list relation fields include `mutations`, `queries`, `where` with full `{ "include": true }` shape

**deletionReferenceBehavior options:**
- `"prevent"` — Block deletion of referenced record if children exist (default for lookup/enum references)
- `"cascade"` — Auto-delete child records when parent is deleted (use for owned child detail records)
- `"setNull"` — Set reference to null when referenced record is deleted
- `"allow"` — Allow deletion without cascade or null (use for self-referential or optional references)

**General defaults (confirm with developer):**
- Lookup/enum model references → `"prevent"` (you shouldn't delete a RecipeType while recipes use it)
- Parent → owned child detail records → `"cascade"` (deleting a BatchRecipe should delete its BatchFormula records)
- Cross-references between peer models → `"prevent"`
- Self-referential/optional → `"allow"` (e.g., previousForecast on TelemetryForecast)

See `references/relationship-patterns.md` for all patterns including collections and system model references.

## Model-Level Metadata

Every model requires metadata controlling its behavior:

| Property | Required | Description |
|----------|----------|-------------|
| `module.id` + `group.id` | Yes | Module and ModuleGroup assignment |
| `exposed` | Yes | Whether GraphQL API is accessible (almost always `true`) |
| `mutable` | Yes | Whether records can be updated/deleted |
| `mutations` | Yes | Which mutation types are available (create, update, del) |
| `labelField` | Conditional | Field used as human-readable label in UI (see labelField Guidelines below) |
| `kind` | Yes | Always `"Reference"` |
| `indices` | No | Composite and TTL indexes |
| `triggers` | No | JSONata expressions on create/update/delete |
| `dataChangeCapture` | No | Enable change event tracking |
| `customFields` | No | Allow runtime custom field creation |

See `references/model-metadata.md` for complete reference with templates by model type.

### labelField Guidelines

| Model type | labelField | Action |
|---|---|---|
| Lookup/enum model (has `name` field) | `"name"` | Include the key |
| Transactional model with auto-number | The number field (e.g., `"batchNumber"`) | Include the key |
| Reference model with no single label | *(omit)* | Do NOT include the key |
| Child/detail model | *(omit)* | Do NOT include the key |
| Join/association table | *(omit)* | Do NOT include the key |

**Never set `labelField` to `null`** — either provide a valid field name or omit the key entirely.

## Indices

### Composite Index
Multi-field index for query performance:
```json
{ "name": "lookupIndex", "unique": false, "fields": ["dataPointId", "createdAt"] }
```

### TTL Index (Time-To-Live)
Auto-expires records after a specified duration. Essential for high-volume time-series data:
```json
{ "name": "TTLIndex", "unique": false, "ttl": 14400, "fields": ["createdAt"] }
```

`ttl` is in **seconds**. Common values: 14400 (4 hours) for raw telemetry, 86400 (1 day) for temp data.

## Triggers

Server-side JSONata expressions that execute on create, update, or delete:

```json
"triggers": {
  "create": "$ ~> | $ | { \"id\": code } |",
  "update": "$after ~> | $ | { \"oee\": $after.availability * $after.performance * $after.quality } |"
}
```

**Use cases:** Computed IDs from code fields, calculated fields (OEE), composite keys.

**Rule:** Keep triggers simple. Complex logic belongs in data flows, not triggers.

## Mutations

### GraphQL Mutation Standards

Four mutation types and when to use each:

1. **Create** — Only when confident data does not exist (historical records, time-sensitive data)
2. **Update** — Only when confident data does exist (event-driven updates, status changes)
3. **Upsert** — When unsure about existing data (integrations pulling from external systems)
4. **Delete** — Only when absolutely necessary; consider TTL indexing instead

All production mutations must:
- Start with the word `mutation`
- Be named the same as the mutation action itself
- Contain the parameter `$payload: [SomeObject!]!`
- Avoid `disable reference checks` without prior approval
- Disable mutation methods with caution — consider security policies first

## Naming Conventions

| Artifact | Convention | Example |
|----------|-----------|---------|
| Model name | PascalCase, singular | `Workcenter`, `ProductionLog`, `OeeHourly` |
| Model ID | camelCase | `workcenter`, `productionLog`, `oeeHourly` |
| Field name | camelCase | `quantityGood`, `idealCycleTime` |
| FK + Relation pair | `{entity}Id` + `{entity}` | `alarmStateId` + `alarmState` |
| Sequence ID | camelCase | `alarmCode`, `assetCode` |
| Module ID | camelCase | `machineDataApp` |
| ModuleGroup ID | camelCase | `machineTelemetrySiteApplication` |
| MongoDB collection | camelCase (matches model ID) | `alarm`, `productionLog` |

### Field Naming Standards

**Timestamp fields** — Use `At` suffix for all date/time fields:
```
occurAt, forecastAt, recordedAt, windowStartAt, windowEndAt,
processedAt, triggeredAt, acknowledgedAt, clearedAt
```
NOT: `forecastTime`, `windowStart`, `windowEnd`

**Paired relation fields** — When a model references two instances of the same type, prefix with `a`/`b`:
```
aDataPointId / bDataPointId / aDataPoint / bDataPoint
aAssetId / bAssetId / aAsset / bAsset
```
NOT: `dataPointAId` / `dataPointBId`

**Boolean fields** — Use `is` or `has` prefix:
```
isActive, isWarm, isCritical, isOnChange, hasChildren, hasAttachment
```

**Count/quantity fields** — Use descriptive names:
```
quantityGood, quantityScrap, sampleCount, batchSize
```

**Status/state fields** — Use descriptive naming, reference lookup tables:
```
alarmStateId, modeId, stateId (FK to lookup)
status (inline string for simple statuses)
```

### Model Naming Standards

**Aggregation models** — Suffix with the aggregation period:
```
TelemetryHourly, TelemetryDaily, OeeHourly, OeeDaily
```

**History models** — Suffix with `History`:
```
WorkcenterHistory, AssetHistory, ModeHistory
```

**Junction models** — Combine both model names:
```
ProductWorkcenter, AssetDataPoint, UserRole
```

### Column Ordering Standard

Sort fields in this order:
1. ID field
2. Required fields
3. Non-required fields
4. Pairs of required ID fields followed by the relation object
5. Pairs of non-required ID fields followed by the relation object
6. All back references

### Additional Naming Rules

| Convention | Rule | Example |
|---|---|---|
| DateTime fields | Must end with **At** | `createdAt`, `recordedAt`, `measuredAt` |
| ID reference fields | Reference complete object name + ID | `productStrategyId` (not `strategyId`) |
| Reference fields | Reference complete object name | `productStrategy` (not `strategy`) |
| Booleans | Must be **positively named** | `active = true` (not `inactive = false`) |
| Back references | Rename to remove duplicates | `salesOrderSalesOrderLines` → `salesOrderLines` |

### deletionReferenceBehavior Guidelines

| Scenario | Behavior | Example |
|---|---|---|
| Required parent (core entity) | `"cascade"` | Alarm → DataPoint (delete alarm when datapoint deleted) |
| Optional/self-referential | `"allow"` | TelemetryForecast → previousForecast (don't cascade chain deletions) |
| Prevent orphaned children | `"prevent"` | Product deletion blocked while ProductionLogs exist |
| Nullable reference cleanup | `"setNull"` | WorkOrder.assigneeId set to null when user deleted |

### High-Volume Model Metadata

For models that receive frequent writes (telemetry, forecasts, insights, logs), disable expensive features:

```json
{
  "dataChangeCapture": { "exposed": false, "retentionDays": 30 },
  "customFields": { "exposed": false }
}
```

TTL index values (seconds): 7 days = `604800`, 30 days = `2592000`, 90 days = `7776000`

### Data Retention Tiers

Always prompt the developer to confirm retention periods. Use these defaults:

| Model type | `retentionDays` | Duration | Examples |
|-----------|----------------|----------|----------|
| Setup/lookup models | `120` | ~4 months | AlarmState, Mode, EventCategory |
| Transactional models | `3650` | 10 years | ProductionLog, Alarm, WorkOrder |
| Master data models | `5475` | 15 years | Asset, Product, Workcenter, DataPoint |

### Data Change Capture Guidelines

- Default retention is 120 days — adjust per the tier table above
- Lower this for data changing regularly
- Tables with corresponding history tables must adjust accordingly
- If you prevent all updates to a model, turn off Data Change Capture

### `usable` vs `active` — Know the Difference

These two Boolean fields serve different purposes and should NEVER be confused:

| Field | Used on | Meaning | Controls |
|-------|---------|---------|----------|
| `usable` | **Lookup/enum models only** | Whether this enum value should appear in selection lists | Dropdown/select visibility — set `false` to hide a value without deleting it |
| `active` | **Master data and transactional models** | Whether the record itself is active/operational | Record lifecycle — `false` means deactivated (soft delete) |

**Rules:**
- Lookup/enum models → use `usable` (Boolean!, defaultValue: true). Do NOT add `active`.
- Master data models (Asset, Product, Workcenter) → use `active` (Boolean!, defaultValue: true). Do NOT add `usable`.
- Transactional models → use `active` (Boolean!, defaultValue: true) if lifecycle soft-delete is needed.
- NEVER put both `usable` and `active` on the same model.

### System Fields — NEVER Define

FUUZ automatically manages these fields on every model. **Never include them in your model definition:**
- `createdAt` — auto-set on record creation
- `updatedAt` — auto-set on every update
- `createdBy` / `createdByUserId` — auto-set to the creating user
- `updatedBy` / `updatedByUserId` — auto-set to the updating user
- `_trace`, `_metadata`, `_aggregate`, `_customFields` (system-managed portions)

**DO include domain-specific timestamp/user fields** that are part of the business transaction:
- `completedAt`, `completedBy` — when a batch/task was completed
- `acknowledgedAt`, `acknowledgedBy` — when an alarm/deviation was acknowledged
- `effectiveDate`, `expirationDate` — date range for validity

## Critical Rules

1. **NEVER** define embedded/embeddable models — use separate Reference models with foreign keys
2. **ALWAYS** define both the ID field AND the relation field for every relationship
3. **ALWAYS** define ModuleGroup → Module → Sequences BEFORE DataModels in the package
4. **NEVER** manually define system audit fields (`createdAt`, `updatedAt`, `createdByUserId`, `createdByUser`, `updatedByUserId`, `updatedByUser`, `_trace`, `_metadata`, `_aggregate`, `_customFields`) — the platform creates these automatically
5. **ALWAYS** include an `id` field as type `ID!` with `unique: true` and `update.include: false` as the first field on every model
6. **ALWAYS** assign every model to a module via `metadata.mfgx.module.id` and `group.id`
7. **ALWAYS** set `labelField` to the human-readable identifier field (usually `code` or `name`)
8. **ALWAYS** use TTL indices for high-volume time-series data to prevent unbounded storage growth
9. **ALWAYS** include `mongo.collection` (camelCase model ID) and `graphql.type: "Object"` in model metadata
10. Use triggers sparingly — only for computed fields, ID generation, and simple transformations

## Package Output Format

This skill outputs Fuuz package JSON — three files that are bundled as a `.fuuz` tar archive:

| File | Purpose |
|------|---------|
| `manifest.json` | Package identity: name, version, specVersion `"2.0.0"`, platformVersion |
| `definition.json` | Export selections with `insertBefore` ordering for prerequisites |
| `package-data.json` | Content: `dataModels[]` array + `data[]` array (seed records) |

**CRITICAL:** There is no standalone model import in Fuuz. All data model deployment goes through packages.

See `references/package-format.md` for complete annotated structure with examples.

## Package Validation and Import Standards

### Package Validation — Critical Rules

These rules prevent the most common import errors:

**Golden Rules:**
1. Every model is `"reference"` / `"Reference"` / `"Object"` — there are no other valid values
2. Every field needs complete metadata — the shape differs between scalar and relationship fields
3. The `id` field is never updatable — `update.include` must be `false`
4. Indices must only reference fields that exist — use `[]` if uncertain
5. Modules/groups must exist before models reference them
6. The `data` array is for seed records (Sequences, Modules, ModuleGroups) — not for enum value records
7. `package-data.json` has exactly 7 top-level keys: `dataModels`, `screens`, `dataFlows`, `dataMappings`, `documentDesigns`, `savedTransforms`, `data`
8. **FATAL ERROR keys to avoid in `package-data.json`:** `moduleGroups`, `modules`, `sequences`, `roles` — including these causes `Cannot read properties of undefined (reading 'filter')`
9. `labelField` must match a real field or be omitted — never set to `null`
10. Seed data payloads must only contain fields the model defines
11. `dataModelTypeId` must be `"setup"` for ALL lookup/enum models, `"transactional"` for high-volume operational models with auto-number sequences, `null` for master data and reference models
12. Every model must use `dataModelKindId: "reference"`, `kind: "Reference"`, `graphql.type: "Object"` — no exceptions
13. Every FK relationship must have an inverse list relation on the parent model — relationships are TRIPLETS (FK scalar + forward relation + inverse list)
14. `usable` is for enum models only, `active` is for master/transactional models only — never both on same model
15. NEVER include system audit fields (`createdAt`, `updatedAt`, `createdBy`, `updatedBy`) — FUUZ manages these automatically
16. FK field naming: always prefix, never suffix — `batchRecipeId` not `recipeIdBatch`
17. Naming conventions are the #1 source of package mistakes — PascalCase singular model names, camelCase field names, prefix-based FK naming
18. Do NOT pre-define custom fields in packages — `_customFields` are site-specific, created in FUUZ UI
19. `enterpriseId` and `environmentId` are tenant-specific — developer must provide correct values
20. Data retention tiers: setup=120d, transactional=3650d (10yr), master=5475d (15yr) — confirm with developer
21. Indices are mostly auto-created (id, FK, date, relation fields) — only define specialized composite indices
22. Enum model IDs must be abbreviated uppercase (e.g., `"MAINT"` not `"MAINTENANCE"`)
23. Enum models must have verbose descriptions — never empty or name-repeated
24. Lookup/enum models always use `dataModelTypeId: "setup"` — never `null`

### manifest.json Critical Values

| Field | Value | Error if wrong |
|---|---|---|
| `applicationPublisherId` | `"fuuz"` | `null` causes validation errors |
| `enterpriseId` | **Developer must provide** | Varies by tenant — ask for the correct value (e.g., `"mfgx"`) |
| `environmentId` | **Developer must provide** | Varies by tenant — ask for the correct value (e.g., `"build"`) |
| `dependencies` | `{}` | Listing dependencies triggers a tenantId filter bug |

### Common Import Errors

| Error Message | Root Cause | Fix |
|---|---|---|
| `Cannot read properties of undefined (reading 'filter')` | Wrong top-level keys in `package-data.json` | Use exactly the 7 required keys |
| `The update mutation of the 'id' field is marked true` | `id` field has `update.include: true` | Set to `false` on every `id` field |
| `Invalid Module on data model 'X'` | Module ID doesn't exist | Create via `data` array or use existing ID |
| `The field dataModelKindId contains an invalid reference id` | Used `"enumeration"` | Always use `"reference"` |
| `One or more fields contains an invalid type` | Relationship references non-installed model | Add target model or confirm it's installed |
| `Cannot read properties of undefined (reading 'relation')` | Missing `mutations`, `queries`, or `where` on scalar field | Use complete metadata |
| `tenantId filter not supported` | `dependencies` in `manifest.json` lists a package | Set `dependencies: {}` |
| `Field "X" is not defined by type "YCreateInput"` | Seed data payload has extra fields | Remove fields not in model definition |

## Data Model Design Workflow

### 1. Define the App Hierarchy
Create ModuleGroup(s) and Module(s) that organize your application's artifacts.

### 2. Identify All Entities
List every data entity needed. Classify each as master, setup, or transactional.

### 3. Plan Sequences
Identify which models need auto-generated codes. Define sequence configurations (prefix, padding, type).

### 4. Design Fields
For each model, define fields with types, required modifiers, and full metadata. Start with the `id` field.

### 5. Establish Relationships
Map relationships between models. Always create both FK field + relation field. Choose appropriate `deletionReferenceBehavior`.

### 6. Configure Model Metadata
Set indices (composite for performance, TTL for time-series), triggers for computed fields, dataChangeCapture for reactive flows.

### 7. Structure Seed Data
Define reference/lookup data that should ship with the package: modes, states, event categories, settings, units, schedules.

### 8. Generate Package JSON
Output the three package files in correct dependency order. Ensure `insertEarly` and `insertBefore` metadata is set on prerequisites.

### 9. Validate Against Output Checklist
Run through every item in the checklist below before delivering.

## Output Checklist

**Package Structure:**
- ✅ `manifest.json` has name, version, `specVersion: "2.0.0"`, platformVersion
- ✅ `definition.json` has `packageDefinitionSelections` for all included artifact types
- ✅ Module and Sequence selections have `insertBefore: [{ "modelName": "DataModel" }]`
- ✅ `package-data.json` has `dataModels[]` array and `data[]` array

**Prerequisite Ordering (data array):**
- ✅ ModuleGroup records first with `"metadata": { "insertEarly": true }`
- ✅ Module records second with `"metadata": { "insertEarly": true }`
- ✅ Sequence records third with `"metadata": { "insertEarly": true }`
- ✅ All other seed data follows in dependency order

**Every Data Model:**
- ✅ Has `id` field as type `ID!` with `unique: true`, `update.include: false`
- ✅ Has `module.id` and `group.id` in `metadata.mfgx`
- ✅ Has `labelField` set to a human-readable field
- ✅ Has `kind: "Reference"`
- ✅ Has `mongo.collection` (camelCase) and `graphql.type: "Object"`
- ✅ Has `exposed: true`, `mutable: true`, `mutations` config
- ✅ Model name is PascalCase, model ID is camelCase

**Every Relationship (TRIPLET):**
- ✅ Has the FK scalar field (`entityId`: `ID`/`ID!`) on the child model
- ✅ Has the forward relation field (`entity`: `ModelName`/`ModelName!`) on the child model
- ✅ Has the inverse list relation field (`entities`: `[ChildModel!]!`) on the parent model
- ✅ Forward relation field has `deletionReferenceBehavior` set
- ✅ Forward relation `fields.from` references the FK field, `fields.to` is `"id"`
- ✅ Inverse list relation `fields.from` is `"id"`, `fields.to` is the FK field name
- ✅ ALL parent models (including lookup/enum) have inverse list relations for every FK within the same package
- ✅ No inverse relations added for external package model references

**Every Field:**
- ✅ Has `mutations` (create + update include), `queries.include`, `where.include`
- ✅ Has `unique` set (true or false)
- ✅ Has a Markdown `description`
- ✅ Sequence fields reference an existing sequence ID
- ✅ No system audit fields manually defined

**Lookup/Enum Models:**
- ✅ `dataModelTypeId` is `"setup"` (never `null`)
- ✅ Has `usable` (Boolean!, defaultValue: true) — NOT `active`
- ✅ Has `labelField: "name"`
- ✅ Enum record `id` values are abbreviated uppercase (e.g., `"MAINT"`)
- ✅ Every enum value has a verbose, meaningful `description`
- ✅ `indices: []` (lookup tables are small)
- ✅ Has inverse list relations for every child FK pointing to it

**Master/Transactional Models:**
- ✅ Uses `active` (Boolean!, defaultValue: true) — NOT `usable`
- ✅ No system audit fields defined (`createdAt`, `updatedAt`, etc.)

**Time-Series Models:**
- ✅ TTL index configured on `createdAt` field
- ✅ `customFields.exposed: false` (high-volume models shouldn't allow runtime extension)
- ✅ `dataChangeCapture.exposed: false` (too expensive on high-volume data)

**Naming Conventions (cross-check — #1 source of mistakes):**
- ✅ ALL model names: PascalCase singular (`Workcenter`, not `workcenters`)
- ✅ ALL field names: camelCase (`quantityGood`, not `QuantityGood`)
- ✅ ALL FK fields: prefix-based (`batchRecipeId`, not `recipeIdBatch`)
- ✅ ALL module/group IDs: camelCase (`machineDataApp`)
- ✅ ALL mongo collection names: match `header.id` (camelCase)
- ✅ `enterpriseId` and `environmentId` are developer-provided (not assumed)

## How Models Connect to Flows and Screens

| This Skill Produces | Used By Flows (fuuz-flows) | Used By Screens (fuuz-screens) |
|---------------------|---------------------------|-------------------------------|
| Model name | `query`/`mutate` node `type` field | Table/Form `query.model` property |
| Field names | GraphQL query `fields`, mutation payloads | `query.fields` arrays, `dataPath` bindings |
| Relationships | `where` clause joins, nested field queries | Filter predicates, select input options |
| Model ID | `dataChanges` trigger `modelName` | Screen route paths |
| Sequences | Auto-populated on `create` mutations | Displayed in forms/tables as read-only |

## Resources

### references/
- `package-format.md` — Complete Fuuz package JSON structure with annotated examples
- `field-types.md` — All 20+ field types with descriptions, decision tree, and usage guidance
- `field-metadata.md` — Complete field metadata reference (mutations, queries, sequence, relation, defaults, expressions)
- `model-metadata.md` — Complete model-level metadata reference (module, indices, triggers, dataChangeCapture)
- `relationship-patterns.md` — All relationship types with complete JSON examples
- `prerequisites.md` — ModuleGroup, Module, Sequence dependency chain with JSON examples
- `model-templates.md` — Complete model definition templates for common industrial patterns
- `seed-data-patterns.md` — How to structure the data array: ordering, record formats, common patterns

### Application Schema (Customer-Specific)

**IMPORTANT:** Application API schemas are customer/application-specific and NOT included in this skill.

**When users need to extend or modify an existing schema:**
1. Ask: "Can you provide your current data model definitions or package export?"
2. User exports package from Fuuz (Configuration → Packages → Export)
3. Analyze the existing schema structure
4. Generate updated package JSON incorporating the changes

## Related Skills

### Fuuz Data Flow Builder (fuuz-flows skill)
**When to use:** After designing the data model, build data flows that query, transform, and mutate your models. Flows use the model names and field names defined here in their GraphQL query/mutate nodes.

### Fuuz Screen Designer (fuuz-screens skill)
**When to use:** After designing the data model, create screens that display and interact with your data. Screens bind to models via `query.model` and `query.fields` properties.

### Fuuz Platform Reference (fuuz-platform skill)
**When to use:** For platform-level reference material including system seeded values, visualization library, connectors, and cross-skill coordination.

---

## Version History

### Version 2.0 (February 2026)

**Major Sync with fuuz-packages v2.0.0** — Aligned all model design rules with the latest package creation skill (63 golden rules). Both skills now have the same correct information.

**Relationship TRIPLETS:**
- Relationships now documented as triplets: FK scalar + forward relation + inverse list relation on parent
- All parent models (including lookup/enum) must have inverse list relations for every FK pointing to them
- Complete inverse relation naming convention and scope rules

**New Critical Rules (13-24):**
- Inverse list relation requirement (Rule 13)
- `usable` vs `active` distinction (Rule 14)
- System audit fields prohibition (Rule 15)
- FK prefix naming convention (Rule 16)
- Naming conventions as #1 mistake source (Rule 17)
- Custom fields guidance (Rule 18)
- Tenant-specific enterprise/environment IDs (Rule 19)
- Data retention tiers with specific values (Rule 20)
- Auto-indexing guidance (Rule 21)
- Enum ID abbreviation convention (Rule 22)
- Enum verbose description requirement (Rule 23)
- Lookup `dataModelTypeId: "setup"` requirement (Rule 24)

**Fixes:**
- `dataModelTypeId` now correctly documented: `"setup"` for ALL lookup/enum models (was `null`)
- `enterpriseId` now documented as developer-provided (was hardcoded `"mfgx"`)
- Added `environmentId` to manifest critical values table
- `deletionReferenceBehavior` now includes `"allow"` option for self-referential references
- Added data retention tiers table with specific values
- Added `usable` vs `active` clear distinction section
- Added system fields "never define" list
- Expanded output checklist with lookup/enum, naming convention, and inverse relation checks

### Version 1.1 (February 2026)

**Training Data Update** — Integrated schema design standards from TeamChatTrainingDocs.md and package validation rules from fuuz-package-creator-skill.md

**Schema Design Standards Additions:**
- Added meaningful label field requirement to Schema Design Standards
- Added `dataModelTypeId` guidance: `"transactional"` for operational/auto-number models, `null` for everything else

**Package Validation Additions:**
- Added FATAL ERROR keys (`moduleGroups`, `modules`, `sequences`, `roles`) warning for `package-data.json`
- Added `tenantId filter not supported` to Common Import Errors table
- Added universal model kind rule: every model must use `dataModelKindId: "reference"`, `kind: "Reference"`, `graphql.type: "Object"`
- Expanded Golden Rules from 10 to 12 items

**Note:** Most content from TeamChatTrainingDocs.md and fuuz-package-creator-skill.md was already incorporated in v1.0, including: Schema Design Standards section, Mutation Standards section, Column Naming Conventions, Field Grouping & Ordering, Data Change Capture Guidelines, Field Definition Three Metadata Shapes, manifest.json Critical Values, and the Common Import Errors table.

---

### Version 1.0 (February 2026)

**Initial Release** — Comprehensive Fuuz data model design and package generation skill

**Core Features:**
- Complete Fuuz package format (manifest.json, definition.json, package-data.json)
- 20+ field types with full metadata reference
- All relationship patterns (1:1, 1:many, many:1, many:many via junction)
- Model-level metadata (indices, triggers, dataChangeCapture, customFields)
- Prerequisite dependency chain (ModuleGroup → Module → Sequence → DataModel)
- 6 production model templates (setup, master, transactional, time-series)
- Seed data patterns and ordering rules
- Output checklist for package validation

**Platform Knowledge:**
- System vs Custom schema distinction
- Package-based deployment (no standalone model import)
- GraphQL API auto-generation
- insertEarly / insertBefore ordering enforcement
- TTL indices for time-series data lifecycle
- Server-side JSONata triggers

**Maintainer:** Fuuz Development Team
**Distribution:** fuuz-schema-v2.0.skill
