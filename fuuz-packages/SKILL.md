---
name: fuuz-packages
description: Generate valid Fuuz platform data model packages (.fuuz files) that import without errors. Use when requests mention "fuuz package", "package file", ".fuuz file", "package generation", "package creator", "import package", "export package", "manifest.json", "definition.json", "package-data.json", "data model package", or when generating importable packages for the Fuuz Industrial Operations platform. Covers package file structure (manifest.json, definition.json, package-data.json), validation rules, field metadata shapes, module/sequence prerequisites, seed data patterns, definition selections, relationship triplets, lookup/enum patterns, triggers, import file generation, and pre-import validation. This skill is the authoritative reference for Fuuz package format — use it alongside fuuz-schema for data model design. Version 2.0.0 with 63 golden rules.
---

# FUUZ Package Creator Skill

**Version 2.1.0** | Last Updated: 2026-02-21

## Purpose

Generate valid FUUZ platform data model packages (`.fuuz` files) that import without errors on the first attempt. This document is the single source of truth for FUUZ package structure, field metadata shapes, and validated patterns. It is subject-matter agnostic — the rules apply to any domain (batch processing, quality management, warehouse management, etc.).

---

## Golden Rules

1. **Every model is `"reference"` / `"Reference"` / `"Object"`** — there are no other valid values.
2. **Every field needs complete metadata** — the shape differs between scalar fields and relationship fields.
3. **The `id` field is never updatable** — `update.include` must be `false`.
4. **Indices must only reference fields that exist** — use `[]` if uncertain.
5. **Modules/groups must exist before models reference them** — create them in the `data` array.
6. **The `data` array is for seed records (Sequences, Modules, ModuleGroups)** — not for enum value records.
7. **package-data.json has exactly 7 top-level keys** — no more, no fewer.
8. **`labelField` must match a real field or be omitted** — set it to the model's display field name (e.g., `"name"` for lookups, `"batchNumber"` for transactional), or omit the key entirely. Never set it to `null`.
9. **Seed data payloads must only contain fields the model defines** — extra fields cause `"Field X is not defined by type YCreateInput"`.
10. **`dataModelTypeId` must be `"transactional"` for high-volume operational models** — models with auto-number sequences (Batch, Campaign, etc.) need this; all others use `null`.
11. **Do NOT include `externalId`, `integrationData`, or `customData` fields unless explicitly requested** — these are optional fields. Only add them when the user specifically asks for them.
12. **Boolean values must always be lowercase** — use `true` / `false`, never `True` / `False` or `TRUE` / `FALSE`. This applies everywhere: JSON field defaults, seed data payloads, and import files.
13. **UoM fields must reference the `Unit` model** — never use a plain `String` for units of measure. Instead, create an FK + relation pair (e.g., `yieldUnitId: ID!` + `yieldUnit: Unit`). Name the FK with a descriptive prefix (e.g., `capacityUnitId`, `quantityUnitId`, `batchSizeUnitId`). Mark as required (`ID!`) when the parent quantity field is required.
14. **Every FK relationship must have an inverse list relation on the parent model** — when ModelA has `parentModelId` (FK) + `parentModel` (relation), the ParentModel MUST also have a `modelAs` field of type `[ModelA!]!` with relation `{ from: "id", to: "parentModelId" }`. This applies to ALL parent models: lookup/enum models, reference models, and cross-references within the same package. Inverse relations for models in external packages (e.g., Product, Unit from MES) are NOT added — those would need to be added to the external package.
15. **NEVER hallucinate screens, dataFlows, dataMappings, documentDesigns, or savedTransforms** — always leave these as empty arrays `[]`. These are complex platform objects built through the FUUZ UI. Never generate fake screen definitions, flow node configurations, or data mappings. Only `dataModels` and `data` should contain content.
16. **Modules and module groups must come from the developer** — NEVER invent module or module group IDs. Either (a) ask the developer for the exact IDs of existing modules/groups they want to use, or (b) ask the developer to define the new module groups and modules that the package should create. Then include the corresponding records in the `data` array and selections in `definition.json`.
17. **Sequence-backed fields must be `String` or `Int` type** — when a field uses a sequence for auto-generated values, the field type MUST be `String` or `Int` (matching the sequence's `type`). Most sequences use `type: "String"`. The field metadata includes a `"sequence": { "id": "sequenceId" }` property that links it to the sequence definition.
18. **Ask the developer about lookup/enum seed values** — when creating models for enumerated values (states, types, categories, severities), ask the developer if they want those values pre-populated via `definition.json` `packageDefinitionSelections`. Offer to list proposed values for their review. Never silently assume a set of enum values.
19. **Enum model IDs must be abbreviated** — the `id` field value for enum/lookup seed records should be an abbreviated, uppercase version of the code or name for easy reference (e.g., `"MAINT"` not `"MAINTENANCE"`, `"SEMI_AUTO"` not `"SEMI_AUTOMATIC"`). Keep them short but recognizable.
20. **Enum models must have verbose descriptions** — every enum/lookup seed record MUST include a meaningful, human-readable `description` field. Never leave descriptions empty or use just the name repeated. Descriptions should explain what the value means in context.
21. **Enum models always use `dataModelTypeId: "setup"`** — all lookup/enum models (status, type, category, severity, mode, etc.) use `"setup"` for `dataModelTypeId`, not `null`. Only high-volume operational models with auto-number sequences use `"transactional"`.
22. **Enum models should include `color` and `default` fields when appropriate** — `color` (String) for values that appear in status badges, chips, or visual indicators. `default` (Boolean, defaultValue: false) for setting the pre-selected value in form field select inputs via predicate filters. Ask the developer which enum models need these fields.
23. **Ask the developer about `deletionReferenceBehavior` preferences** — the default is `"prevent"` (block parent deletion when children exist), but `"cascade"` (auto-delete children when parent is deleted) is preferred for master-data/transactional child references. For example: deleting a WorkOrder cascades to its process, BOM, and schedule records — no extra flow steps or screen development needed. Always ask the developer: "For [parent] → [child] relationships, should deleting the parent cascade-delete the children, or prevent deletion?"
24. **`usable` is for enum models, `active` is for master/transactional models** — never mix them. `usable` controls whether an enum value appears in selection lists. `active` indicates whether a record (equipment, recipe, batch) is operationally active. Never put both on the same model.
25. **Module group icons must be FontAwesome names** — FUUZ uses FontAwesome Pro. Use the icon name without the `fa-` prefix (e.g., `"boxes"`, `"flask"`, `"industry"`). Ask the developer which icons they prefer.
26. **Do NOT pre-define custom fields in packages** — `_customFields` are site/location-specific and should be created by developers through the FUUZ UI after import. Only include `_customFields._externalId` (String) as the standard external integration hook, and `"customFields": { "exposed": true }` in model metadata to enable the feature.
27. **Model names are always PascalCase singular** — e.g., `BatchRecipe`, `EquipmentUnit`, `WorkOrder`. Never plural. Domain prefixes are OK but often unnecessary since the module association provides context.
28. **NEVER include system audit fields** — `createdAt`, `updatedAt`, `createdBy`, `updatedBy` are auto-managed by FUUZ. Never define them in your model. DO include domain-specific timestamp/user fields that are part of the business transaction (e.g., `completedAt`, `acknowledgedAt`, `approvedBy`, `approvedDate`).
29. **FK field naming: always prefix, never suffix** — FK fields follow `{camelCaseModelName}Id` (e.g., `batchRecipeId`). When two FKs reference the same model, differentiate with a prefix: `sourceBatchId` / `targetBatchId` — never `batchIdSource` / `batchIdTarget`.
30. **FK requiredness depends on model type** — setup/enum references (statusId, typeId, stateId) are typically required (`ID!`). Other references (groupId, categoryId, cross-package FKs) may be optional (`ID`). Always verify with the developer during package creation.
31. **Package versioning starts at `1.0.0`** — all development happens in the FUUZ build environment (pre-production). Use `1.0.0` for the initial package and increment as needed. When updating a package, the new version MUST be greater than what is already installed. If the developer has made changes in the app since the last import, the new package version must also be greater than the current app version.
32. **Data retention tiers by model type** — always prompt the developer to confirm, but use these defaults: setup/lookup models = `120` days, transactional models = `3650` days (10 years), master data models = `5475` days (15 years). These values go in `dataChangeCapture.retentionDays`.
33. **Indices are mostly automatic — only define specialized ones** — FUUZ auto-indexes: all `id` fields, all FK fields, all date fields, and all relation fields. Do NOT manually define indices for these. Only define custom composite indices or specialized indices the developer specifically requests. Default to `indices: []` for most models.
34. **State machines are implemented in data flows, not model definitions** — FUUZ has no built-in state machine. State transitions, validation rules, and workflow logic are all built through the FUUZ data flow designer. The package only defines the state/status field and its lookup model.
35. **Document all package dependencies** — when a package references models from other packages, the developer MUST provide the list of dependencies. Document these in the manifest (even though `dependencies: {}` is used for import), and note them clearly so the developer knows what must be installed first.
36. **Prefer smaller, focused packages when iterating** — while everything CAN go in one package, it's better to create smaller packages organized by concern (data models, screens, flows, documents, scripts). This way, updates only affect the specific package that changed. This is especially important when iterating with Claude.
37. **Document screen candidates for the developer** — even though we never generate screens, note which models are "list + detail screen" candidates (master data, transactional) vs "embedded table" candidates (child detail records shown inline on parent screens) vs "setup screen" candidates (lookup/enum models). This helps the developer plan their screen development.
38. **ID generation depends on model type** — setup/enum models: use camelCase of the `code` value as the `id`. Master data: FUUZ assigns CUIDs automatically. Transactional data: FUUZ assigns CUIDs, but composite IDs can be computed via create triggers (e.g., sequence + product + datetime). Import files may use human-readable IDs for setup data and CUIDs or meaningful codes for master/transactional data.
39. **All import file IDs must match exactly** — every FK value in an import CSV MUST match an existing record's `id` field 100% exactly (case-sensitive). Mismatches cause import errors.
40. **Delete triggers are rarely used — prefer data flows** — while `"delete"` triggers are technically supported, they are uncommon. If you need to react to a record deletion (e.g., cascading side effects, audit logging), use a data flow with a **data change node** instead. Data flows offer more control, visibility, and debuggability.
41. **Consider flows with request-response nodes as APIs** — instead of relying solely on direct API CRUD calls, consider building data flows with request-response nodes. These flows can be called from anywhere as an API, allow standardized transforms, and are easier to test and iterate on than raw triggers.
42. **The model package is ALWAYS the foundation — install it first** — in all situations, regardless of how many packages exist, the data model package must be installed first. It is the fundamental building block for all FUUZ applications. Screens, flows, documents, and scripts all depend on the models existing first.
43. **`dataTransform: "$"` transforms ALL existing data** — in `definition.json` selection metadata, `"dataTransform": "$"` means the transform applies to every record already in the table. This is critical when adding new required fields to an existing model — you need a migration for existing data. In the build environment, you can simply delete the package and start over (then re-import data). In production, deleting data is not an option — plan migrations carefully.
44. **External model selections require the same metadata as new models** — when referencing models from other installed packages (e.g., Product, Unit, Area from MES) in your `definition.json` DataModel selection, include ALL the same selection metadata fields (`id`, `label`, `fields`, `metadata`, `viewFields`, `labelField`, `dataTransform`) as you would for models you're creating. External models are not exempt from selection metadata.
45. **Naming conventions are the #1 source of mistakes** — improper field naming, model naming, and screen/flow naming are the most common errors developers make with FUUZ packages. Always follow: PascalCase singular for model names, camelCase for field names and FKs, prefix-based FK naming (`batchRecipeId` not `recipeIdBatch`). Consistent naming prevents import failures and confusion.
46. **Roles and policies CAN be in packages, but implement natively first** — while `roles`, `policyGroups`, and `policies` can be included in the package definition, it's simpler to: (1) install the model package from Claude first, (2) implement roles and policies natively within FUUZ using the UI, (3) then export the updated FUUZ package including the roles and policies, and (4) provide the exported package back to Claude for further training. This approach lets the developer use FUUZ's native role/policy editor, which is more intuitive than hand-coding JSON.
47. **50+ data flow node types are available** — FUUZ has comprehensive node types for flow design including: trigger nodes (schedule, dataChanges, request, webhook), data nodes (query, mutate), transform nodes (JSONata, JavaScript, savedTransform), control flow (when, unless, ifElse, switch, broadcast, loop), UI interaction (formDialog, snackbar, confirmDialog, navigateScreen), integration (integrateV2, awsLambda, openaiChat), notifications (sendNotification, emailSend), and gateway/device nodes. For full node configurations with JSON examples, refer to the **fuuz-flows** skill.
48. **Screen field grouping is a screen concern, not a model concern** — nothing in the data model definition impacts screen field grouping or sections. Field grouping, ordering, and layout sections are all configured during screen development in the FUUZ screen designer.
49. **Exported packages have the same 3-file structure** — when a developer exports a package from FUUZ (e.g., after adding roles/policies natively), the exported `.fuuz` file follows the exact same structure: `manifest.json`, `definition.json`, `package-data.json`. This means exported packages can be provided directly to Claude for learning.
50. **Field order in the `fields` array does not matter** — the order of fields in the model definition's `fields` array has no impact on import or runtime behavior. Convention is to put `id` first, but it's not required.
51. **Use the GraphQL type name for queries** — when building screens or flows, use the PascalCase model name as the GraphQL type (e.g., `BatchRecipe`, `EquipmentUnit`). Queries use the camelCase plural for list queries (e.g., `batchRecipes { ... }`) and camelCase singular for single-record queries (e.g., `batchRecipe(id: "...") { ... }`).
52. **`enterpriseId` and `environmentId` are tenant-specific** — packages will have different `enterpriseId` and `environmentId` depending on the target tenant. The developer MUST provide the correct values for their environment. Do not assume `"mfgx"` / `"build"` — always ask.
53. **Packages can be rolled back** — developers can uninstall/delete packages that were imported. This is particularly useful in the build environment when iterating. If a package import causes issues, the developer can roll it back and re-import a corrected version.
54. **Flow schedules are configured natively in FUUZ** — do NOT attempt to configure data flow schedules in packages. Suggest to the developer: "Configure flow schedules using the Data Flow Schedules screen in FUUZ." Schedule configuration is a runtime concern, not a package concern.
55. **Keep skills split by concern** — `fuuz-schema` handles data model design (what fields, relationships, constraints). `fuuz-packages` handles package generation (producing valid JSON that imports). Not every interaction requires package generation — sometimes the developer only needs model design guidance.
56. **Exported FUUZ flow JSON files are training examples** — flow JSON files exported from FUUZ (e.g., `Production Functions 0.0.66.json`, `Dispatch Functions 0.0.54.json`) MUST be used as training data for the flow builder skill. These are real, production-tested flow definitions.
57. **FUUZ has a flow-based screen generator** — the platform includes a flow-based screen generator that users can run from within the platform to generate screens. This is a capability developers should be aware of. Screen generation flows may need updates to output screens formatted to current standards.
58. **Skills are deployed via the Claude admin console** — to deploy a skill, go to the Claude admin console, select the `.skill` file, and import it. Each skill is loaded individually (one at a time), not all at once.
59. **Each skill must be fully self-contained** — skills are shared between employees, partners, and customers. They must be comprehensive and CANNOT rely on references to files outside the skills directory. All content from external documentation must be baked directly into the skill files. Never reference `Documentation/` folder paths or other external file locations in skill content.
60. **No known skill file size limits** — there are no known practical limits on skill file size for FUUZ deployment targets. Claude's context window is the practical constraint (~200K tokens). Keep skills comprehensive but avoid unnecessary repetition.
61. **Documentation folder is reference only — not distributed** — the `Documentation/` folder contains reference material but is NOT distributed to users. All useful content from documentation MUST be incorporated directly into the appropriate skill files. If information exists only in documentation, it effectively doesn't exist for skill users.
62. **Skill files are the single distribution artifact** — the `.skill` file (binary archive containing `SKILL.md` and `references/` directory) is the only thing distributed. Everything a user needs must be inside the skill package.
63. **Always update deployed skills when source skills change** — when a skill's source content (like this file) is updated with new rules or patterns, the corresponding deployed skill in `fuuz skills/` must be updated to match. Keep the deployed version in sync.
64. **Documentation bake-in priority is screens, data models, flows, documents** — when baking documentation content into skills, prioritize in this order: screen-related content first, then data model content, then flow content, then document/report content.
65. **Screen generator flow updates are a separate task** — while FUUZ has a flow-based screen generator, updating its output format to match current standards is a separate task from skill maintenance. Do not conflate the two.
66. **Both fuuz-schema and fuuz-packages must have the same correct information** — when model design rules are updated in one skill, the other must be updated to match. The schema skill focuses on design guidance; the packages skill focuses on valid JSON generation. Both need identical rules for naming, relationships, metadata, and validation.
67. **Extract key flow patterns with examples from exported flow JSON files** — when incorporating flow training data, extract the key patterns and include complete examples. Also include the raw exported JSON files in the `references/` directory so additional patterns can be extracted later.
68. **Claude must repackage all .skill files** — `.skill` files are ZIP archives. When skill content is updated, Claude must create new ZIP archives containing the updated `SKILL.md` and all `references/` files. Use Python's `zipfile` module to create the archives.
69. **Customers and partners have their own Claude instances** — skills are used by employees, partners, and customers who have their own Claude instances to build FUUZ apps. The skills save them time by providing validated patterns and examples. Skills must be comprehensive enough to work standalone.
70. **Skill testing is prompts and testing, not automated validation** — there is no automated validation pipeline for skills. Testing consists of loading the skill into Claude and testing it with prompts to verify it produces correct output.
71. **Both schema and packages skills must use the latest updates across both** — when syncing the two skills, always use the most recent version of any shared rule or pattern. If the schema skill has a more detailed version of a rule, use that version in both skills. If the packages skill has a newer correction, apply it to both.

---

## 1. Package File Structure

A `.fuuz` file is a gzipped tarball containing exactly 3 JSON files:

```bash
tar -czf PackageName@version.fuuz manifest.json definition.json package-data.json
```

Only include the 3 JSON files. No subdirectories, no extra files.

---

## 2. manifest.json

```json
{
  "name": "PackageName",
  "version": "1.0.0",
  "applicationPublisherId": "fuuz",
  "enterpriseId": "<ask developer>",
  "environmentId": "<ask developer>",
  "dependencies": {},
  "specVersion": "2.0.0",
  "platformVersion": "<ask developer for current platform version>"
}
```

| Field | Value | Why |
|-------|-------|-----|
| `applicationPublisherId` | `"fuuz"` | `null` causes validation errors |
| `enterpriseId` | **Developer must provide** | Varies by tenant — ask the developer for the correct value (e.g., `"mfgx"`) |
| `environmentId` | **Developer must provide** | Varies by tenant — ask the developer for the correct value (e.g., `"build"`) |
| `dependencies` | `{}` | Listing dependencies triggers a tenantId filter bug in the installer |
| `platformVersion` | Current platform version | Must match target environment |
| `version` | `"1.0.0"` initial, increment for updates | New version MUST be greater than what's installed |
| `name` | Directory name without `@version` | Used for package identity |

### Versioning rules:
- Start at `1.0.0` — all development is in the FUUZ build environment (pre-production)
- Increment version when updating: `1.0.0` → `1.1.0` → `1.2.0` etc.
- **CRITICAL:** The package version MUST be greater than what is currently installed in the tenant. If the developer has made any changes in the app since the last import, the new package version must exceed the current app version.
- The same version applies in `manifest.json` and `definition.json` (`packageDefinitionVersion.version`)

### Dependencies:
- Even though `dependencies: {}` is used in manifest (due to the installer bug), **document all package dependencies clearly** for the developer
- Ask the developer: "What other packages must be installed before this one?" (e.g., MES@1.0.0 for Product, Unit, Area models)
- Note dependencies in comments or documentation so the developer knows the install order

### Package scope strategy:

When iterating on application development (typical when using Claude), prefer **smaller, focused packages** organized by concern:

| Package type | Contains | When to use |
|-------------|----------|-------------|
| Data model package | `dataModels` + `data` (modules, sequences) + lookup seed values | Core schema — changes infrequently |
| Screen package | `screens` only | UI development — changes frequently |
| Flow package | `dataFlows` only | Automation — changes independently |
| Document package | `documentDesigns` only | Report templates — changes independently |

**For a new application:** Start with a single package containing all data models. Split into focused packages as the application matures and the developer needs to iterate on specific areas.

---

## 3. package-data.json — Top-Level Structure

MUST contain exactly these 7 keys:

```json
{
  "dataModels": [],
  "screens": [],
  "dataFlows": [],
  "dataMappings": [],
  "documentDesigns": [],
  "savedTransforms": [],
  "data": []
}
```

**FATAL ERROR if you use any of these keys instead:** `moduleGroups`, `modules`, `sequences`, `roles`. These cause `Cannot read properties of undefined (reading 'filter')`.

**CRITICAL — do NOT populate these arrays:**
- `screens` — always `[]`. Screen definitions are complex UI objects built in the FUUZ screen designer. Never generate fake screen JSON.
- `dataFlows` — always `[]`. Data flows are visual automation pipelines built in the FUUZ flow designer. Never generate fake flow node configurations.
- `dataMappings` — always `[]`. Built through the UI.
- `documentDesigns` — always `[]`. Built through the UI.
- `savedTransforms` — always `[]`. Built through the UI.

Only `dataModels` and `data` should contain content in a package.

---

## 4. Data Model — Complete Template

Every model — whether it holds lookup values, master data, or transactional records — uses the same structure:

```json
{
  "header": {
    "id": "myModel",
    "name": "MyModel",
    "description": "What this model represents",
    "dataModelKindId": "reference",
    "_customFields": { "_externalId": null }
  },
  "version": {
    "id": "myModel_v1",
    "number": "1",
    "dataModelId": "myModel",
    "modelDefinition": {
      "name": "MyModel",
      "metadata": {
        "mfgx": {
          "module": {
            "id": "targetModuleId",
            "group": {
              "id": "targetModuleGroupId"
            }
          },
          "exposed": true,
          "mutable": true,
          "mutations": {
            "create": true,
            "update": true,
            "del": true
          },
          "dataChangeCapture": {
            "exposed": true,
            "retentionDays": 120
          },
          "customFields": {
            "exposed": true
          },
          "labelField": "name",       // ONLY for lookup models — omit for other models. See labelField Guidelines.
          "editor": {
            "autosort": true
          },
          "indices": []
        },
        "mongo": {
          "collection": "myModel"
        },
        "graphql": {
          "type": "Object"
        }
      },
      "kind": "Reference",
      "description": "What this model represents"
    },
    "dataModelTypeId": null           // Set to "transactional" for auto-number operational models
  }
}
```

### Immutable Values

These values are always the same — no exceptions:

| Path | Value | Error if wrong |
|------|-------|----------------|
| `header.dataModelKindId` | `"reference"` | `"The field dataModelKindId contains an invalid reference id"` |
| `modelDefinition.kind` | `"Reference"` | Import failure |
| `metadata.graphql.type` | `"Object"` | `"One or more fields contains an invalid type"` |
| `metadata.mfgx.exposed` | `true` | — |
| `metadata.mfgx.mutable` | `true` | — |

### Required mfgx Metadata

Every model MUST have ALL of these properties in `metadata.mfgx`. Missing any causes import errors:

| Property | Value | Notes |
|----------|-------|-------|
| `module` | `{ id, group: { id } }` | Both IDs must resolve to existing module/group in the tenant |
| `exposed` | `true` | — |
| `mutable` | `true` | — |
| `mutations` | `{ create: true, update: true, del: true }` | Model-level mutation config |
| `dataChangeCapture` | `{ exposed: true, retentionDays: N }` | See retention tiers below — always confirm with developer |
| `customFields` | `{ exposed: true }` | — |
| `labelField` | `string` or **omit entirely** | See guidelines below — do NOT set to `null`; either provide a value or omit the key |
| `editor` | `{ autosort: true }` | — |
| `indices` | `[]` or array of index objects | See Index Rules section — most indices are auto-created |

### Data Retention Tiers

Always prompt the developer to confirm retention periods. Use these defaults:

| Model type | `retentionDays` | Duration | Examples |
|-----------|----------------|----------|----------|
| Setup/lookup models | `120` | ~4 months | RecipeType, BatchState, EquipmentStatus |
| Transactional models | `3650` | 10 years | Batch, Campaign, WorkOrder |
| Master data models | `5475` | 15 years | EquipmentUnit, BatchRecipe, ProcessCell |

**Ask the developer:** "Are these retention periods appropriate for your compliance requirements, or do any models need longer/shorter retention?"

### System Fields — NEVER Define

FUUZ automatically manages these fields on every model. **Never include them in your model definition:**
- `createdAt` — auto-set on record creation
- `updatedAt` — auto-set on every update
- `createdBy` — auto-set to the creating user
- `updatedBy` — auto-set to the updating user

**DO include domain-specific timestamp/user fields** that are part of the business transaction itself:
- `completedAt`, `completedBy` — when a batch or task was completed
- `approvedDate`, `approvedBy` — when a recipe was approved
- `acknowledgedAt`, `acknowledgedBy` — when a deviation was acknowledged
- `effectiveDate`, `expirationDate` — date range for recipe validity

These business fields use standard types (`DateTime`, `String`, `ID`) and follow Shape 2 metadata.

### Naming Conventions ⚠️ (#1 Source of Package Mistakes)

| Element | Convention | Example |
|---------|-----------|---------|
| `header.id` | camelCase, singular | `"workOrder"`, `"equipmentStatus"` |
| `header.name` | PascalCase, singular | `"WorkOrder"`, `"EquipmentStatus"` |
| `mongo.collection` | camelCase (matches `header.id`) | `"workOrder"` |
| `version.id` | `{header.id}_v1` | `"workOrder_v1"` |
| `version.dataModelId` | Same as `header.id` | `"workOrder"` |
| Field names | camelCase | `"batchRecipeId"`, `"estimatedDuration"` |
| FK fields | `{camelCaseModelName}Id` — prefix, never suffix | `"batchRecipeId"`, `"sourceBatchId"` |

**Model naming rules:**
- Always PascalCase singular: `BatchRecipe`, `EquipmentUnit` — never `BatchRecipes` or `batch_recipe`
- Domain prefixes are acceptable but often unnecessary (the module association provides context)
- When two FKs reference the same target model, differentiate with a **prefix**: `sourceBatchId` / `targetBatchId` — never a suffix like `batchIdSource`

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

Sort fields in this order within the `fields` array:
1. ID field
2. Required scalar fields
3. Non-required scalar fields
4. Pairs of required FK fields followed by the relation object
5. Pairs of non-required FK fields followed by the relation object
6. All back references (inverse list relations)

### Additional Naming Rules

| Convention | Rule | Example |
|---|---|---|
| DateTime fields | Must end with **At** | `recordedAt`, `measuredAt` |
| ID reference fields | Reference complete object name + ID | `productStrategyId` (not `strategyId`) |
| Reference fields | Reference complete object name | `productStrategy` (not `strategy`) |
| Booleans | Must be **positively named** | `active = true` (not `inactive = false`) |
| Back references | Rename to remove duplicates | `salesOrderSalesOrderLines` → `salesOrderLines` |

### Mutation Standards

Four mutation types and when to use each:
1. **Create** — Only when confident data does not exist (historical records, time-sensitive data)
2. **Update** — Only when confident data does exist (event-driven updates, status changes)
3. **Upsert** — When unsure about existing data (integrations pulling from external systems)
4. **Delete** — Only when absolutely necessary; consider TTL indexing instead

### labelField Guidelines

The `labelField` tells the platform which field to display as the record's label in dropdowns, lists, and relationship selectors.

**Rules:**
- If a model has a meaningful label field → set `labelField` to that field name
- If a model has NO meaningful single label → **omit the `labelField` key entirely** (do NOT set it to `null`)

| Model type | labelField | Action |
|-----------|-----------|--------|
| Lookup/enum model (has `name` field) | `"name"` | Include the key |
| Transactional model with auto-number field | The number field name (e.g., `"batchNumber"`, `"campaignNumber"`) | Include the key |
| Reference model with no single meaningful label | *(omit)* | Do NOT include the key |
| Child/detail model (e.g., formula, material line) | *(omit)* | Do NOT include the key |
| Join/association table | *(omit)* | Do NOT include the key |

### version.dataModelTypeId

Controls UI behavior and how the platform categorizes the model. Set at `version.dataModelTypeId` (sibling to `version.id`, `version.number`, etc.).

**Rules:**
- Lookup/enum models (status, type, category, severity, mode) → set to `"setup"`
- High-volume operational record models with auto-number sequences → set to `"transactional"`
- Master data and reference models (equipment, recipes, processes) → set to `null` (or omit)

| Value | Use for | Examples |
|-------|---------|----------|
| `"setup"` | All lookup/enum models — statuses, types, categories, severities, modes | RecipeType, BatchState, EquipmentStatus, DeviationSeverity |
| `"transactional"` | High-volume operational records with auto-number sequences | Batch, Campaign, WorkOrder |
| `null` | Master data, reference models, equipment, recipes, child detail records | EquipmentUnit, BatchRecipe, BatchFormula, ProcessCell |
| `"master"` | Slowly changing master data (rare) | — |

**Key indicators:**
- `"setup"` → the model holds a fixed/semi-fixed set of selectable values (has `code`, `name`, `usable` fields). Typically has `labelField: "name"`.
- `"transactional"` → the model has a sequence-generated number field (e.g., `batchNumber`) and represents individual business events.
- `null` → everything else (master data, child detail records, equipment, recipes).

---

## 5. Field Definitions — Three Shapes

Every field in FUUZ falls into one of three metadata shapes. Using the wrong shape causes import errors.

### Shape 1: The `id` Field

Every model must have this as its first field. The `update.include` MUST be `false`.

```json
{
  "name": "id",
  "type": "ID!",
  "description": "Unique identifier",
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

**If `update.include` is `true`, import fails with:** `"The update mutation of the 'id' field is marked true for data model 'X'. Please set it to false to continue."`

### Shape 2: Scalar Fields

All non-relationship, non-id fields. MUST have `mutations`, `queries`, `where` in metadata. Missing any causes: `"Cannot read properties of undefined (reading 'relation')"`.

```json
{
  "name": "fieldName",
  "type": "String",
  "description": "Field description",
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

**Supported scalar types:** `String`, `String!`, `Int`, `Int!`, `Float`, `Float!`, `Boolean`, `Boolean!`, `DateTime`, `DateTime!`, `Duration`, `JSON`, `ID`, `ID!`

**Optional properties:**
- `"defaultValue": <value>` — add inside `mfgx` alongside mutations/queries/where

### Common Optional Fields for Master Data Models

Consider offering these to the developer when designing master data or transactional models:

| Field | Type | When to include |
|-------|------|----------------|
| `notes` | `String` | General-purpose text field for comments, observations, or context |
| `code` | `String!` | When the model needs a human-readable unique identifier (often unique: true) |
| `active` | `Boolean!` | Master data / transactional models — soft-delete lifecycle indicator (defaultValue: true) |

**Computed `code` fields:** When a model needs a composite unique identifier (e.g., product code + revision), the `code` field can be auto-populated via a create-trigger JSONata transform in the FUUZ flow designer. This keeps the UI simple — users don't manually type the code. Discuss with the developer if any models need computed codes.

### Shape 3: Relationship Fields

Relationships come in **triplets** — a scalar FK field (Shape 2) + a forward relation object field + an inverse list relation field on the parent model.

**The FK scalar field** follows Shape 2 exactly:
```json
{
  "name": "parentModelId",
  "type": "ID",
  "description": "FK to ParentModel",
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

**The forward relation object field** has `relation` plus full `mutations`, `queries`, and `where`:
```json
{
  "name": "parentModel",
  "type": "ParentModel!",
  "description": "",
  "metadata": {
    "mfgx": {
      "relation": {
        "deletionReferenceBehavior": "prevent",
        "fields": {
          "from": "parentModelId",
          "to": "id"
        }
      },
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

**Relationship field rules:**
- MUST have `relation.fields.from` (local FK field name) and `relation.fields.to` (remote field, usually `"id"`)
- MUST have `mutations`, `queries`, and `where` — all set to `{ "include": true }`
- Forward relations MUST include `"deletionReferenceBehavior"` — either `"prevent"` or `"cascade"` (see guidelines below)
- The relation type should match the FK nullability: required FK (`ID!`) → required relation (`ParentModel!`), optional FK (`ID`) → optional relation (`ParentModel`)

**FK requiredness guidelines (confirm with developer):**
- Setup/enum references (statusId, typeId, stateId) → typically **required** (`ID!`) — every record should have a status/type
- Other references (groupId, categoryId) → **may be optional** (`ID`) — depends on developer preference
- Cross-package FKs (productId, workunitId, areaId) → often **optional** (`ID`) — may not be mapped yet
- Parent-child FKs (batchRecipeId on BatchFormula) → typically **required** (`ID!`) — child must belong to a parent

**deletionReferenceBehavior guidelines:**

Always ask the developer: _"For [parent] → [child] relationships, should deleting the parent cascade-delete the children, or prevent deletion?"_

| Value | Behavior | Use when |
|-------|----------|----------|
| `"prevent"` | Blocks parent deletion if child records exist | **Lookup/enum → any child**: deleting a status/type value while records reference it should be blocked |
| `"cascade"` | Auto-deletes child records when parent is deleted | **Master/transactional parent → owned children**: e.g., deleting a WorkOrder cascades to its process, BOM, schedule records — simplifies UI (no extra flow steps or screen development needed) |

**General defaults (confirm with developer):**
- Lookup/enum model references → `"prevent"` (you shouldn't delete a RecipeType while recipes use it)
- Parent → owned child detail records → `"cascade"` (deleting a BatchRecipe should delete its BatchFormula, BatchProcedure records)
- Cross-references between peer models → `"prevent"` (deleting an EquipmentClass shouldn't cascade to unrelated models)

**Inverse/list relationships** (parent → child lookups). **Every FK relationship MUST have a corresponding inverse list relation on the parent model:**
```json
{
  "name": "childItems",
  "type": "[ChildModel!]!",
  "description": "",
  "metadata": {
    "mfgx": {
      "relation": {
        "fields": {
          "from": "id",
          "to": "parentModelId"
        }
      },
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

**Inverse relation naming convention:**
- Use the **plural camelCase** of the child model name: `BatchDeviation` → `batchDeviations`, `EquipmentUnit` → `equipmentUnits`
- If a parent has multiple inverse relations to the same child type via different FKs, differentiate by context

**Inverse relation scope:**
- Add inverse relations for ALL FK references within the same package (lookup models, reference models, etc.)
- Do NOT add inverse relations for FK references to models in external packages (e.g., Product, Unit from MES) — those belong in the external package

### Complete Worked Example: DeviationType ↔ BatchDeviation

This example shows all three parts of the relationship triplet between a lookup model (`DeviationType`) and a child model (`BatchDeviation`).

**Part 1 — FK scalar field** (on `BatchDeviation`):
```json
{
  "name": "deviationTypeId",
  "type": "ID!",
  "description": "",
  "metadata": {
    "unique": false,
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true }
    }
  }
}
```

**Part 2 — Forward relation** (on `BatchDeviation`):
```json
{
  "name": "deviationType",
  "description": "",
  "type": "DeviationType!",
  "metadata": {
    "mfgx": {
      "relation": {
        "deletionReferenceBehavior": "prevent",
        "fields": {
          "from": "deviationTypeId",
          "to": "id"
        }
      },
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

**Part 3 — Inverse list relation** (on `DeviationType`):
```json
{
  "name": "batchDeviations",
  "description": "",
  "type": "[BatchDeviation!]!",
  "metadata": {
    "mfgx": {
      "relation": {
        "fields": {
          "from": "id",
          "to": "deviationTypeId"
        }
      },
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

**Key observations from this example:**
- The FK is `ID!` (required) → the forward relation type is `DeviationType!` (also required, with `!`)
- The forward relation has `deletionReferenceBehavior: "prevent"` — you cannot delete a DeviationType while BatchDeviation records reference it
- The inverse list relation type is always `[ChildModel!]!` (required non-null list of non-null items)
- The inverse `from` is always `"id"` and `to` is the FK field name on the child model
- The field name `batchDeviations` is the plural camelCase of `BatchDeviation`
- All three fields (FK, forward, inverse) include `mutations`, `queries`, and `where`

### Multiple Inverse Relations Example: EquipmentClass

When a parent model is referenced by multiple child models, it gets one inverse list relation for each:

```json
// On EquipmentClass model — two different child models point to it
{
  "name": "equipmentUnits",
  "type": "[EquipmentUnit!]!",
  "description": "",
  "metadata": {
    "mfgx": {
      "relation": { "fields": { "from": "id", "to": "equipmentClassId" } },
      "mutations": { "create": { "include": true }, "update": { "include": true } },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
},
{
  "name": "batchProcedures",
  "type": "[BatchProcedure!]!",
  "description": "",
  "metadata": {
    "mfgx": {
      "relation": { "fields": { "from": "id", "to": "equipmentClassId" } },
      "mutations": { "create": { "include": true }, "update": { "include": true } },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
}
```

### Cross-Package References

When a field's type references a model from another installed package (e.g., `Product`, `WorkOrder`):
- The referenced type MUST already exist in the tenant as an installed data model
- If it doesn't exist, import fails with: `"One or more fields contains an invalid type on data model 'X'"`
- Include those external model IDs in your `definition.json` DataModel selection

---

## 6. Lookup/Enum Models

Models that hold a fixed set of selectable values (status codes, type categories, etc.) are structurally identical to all other models. They use the same `"reference"` / `"Reference"` / `"Object"` values.

### Standard lookup field set:

| Field | Type | Unique | Notes |
|-------|------|--------|-------|
| `id` | `ID!` | yes | `update.include: false` — use abbreviated uppercase IDs (e.g., `"MAINT"` not `"MAINTENANCE"`) |
| `code` | `String!` | yes | Short identifier — uppercase with underscores (e.g., `"IN_PROGRESS"`, `"SEMI_AUTO"`) |
| `name` | `String!` | no | Human-readable display label (e.g., `"In Progress"`, `"Semi-Automatic"`) |
| `description` | `String` | no | **Always verbose** — explain what the value means in context, never leave empty or repeat the name |
| `usable` | `Boolean!` | no | `defaultValue: true` — controls whether the value appears in selection lists |
| `color` | `String` | no | **Include when appropriate** — MUST be hex color codes only (e.g., `"#4CAF50"` for success, `"#F44336"` for error). Never use named colors. Ask the developer which models need colors. |
| `default` | `Boolean` | no | **Include when appropriate** — `defaultValue: false`. Set ONE value to `true` to pre-select it in form field select inputs via predicate filter. Ask the developer which models need a default. |

### Lookup model metadata:
- `labelField`: `"name"`
- `editor`: `{ autosort: true }`
- `indices`: `[]` (lookup tables are small; no performance benefit from indices)
- `dataModelTypeId`: `"setup"` — ALL lookup/enum models use `"setup"`, never `null`

### Enum ID Convention:
- Seed record `id` values should be **abbreviated uppercase** for easy reference
- Keep them short but recognizable: `"MAINT"` not `"MAINTENANCE"`, `"QC_PASS"` not `"QUALITY_CONTROL_PASSED"`
- Use underscores for multi-word: `"IN_PROGRESS"`, `"SEMI_AUTO"`
- The `code` field can be more verbose than `id` but typically matches the `id` value

### Enum Description Convention:
- **Every enum value MUST have a meaningful description** — never leave blank or just repeat the name
- Descriptions should explain what the value means in context and when to use it
- Example: For `BatchState` value `RUNNING`: description = `"Batch is actively executing procedure phases on the equipment"` (not just `"Running"` or `""`)

### When to include `color` field:
Ask the developer: "Which enum models need color values for UI display (status badges, chips, indicators)?"
- **Typically yes**: Status models (BatchStatus, EquipmentStatus, QualityStatus, CampaignStatus, RecipeStatus)
- **Typically yes**: Severity models (DeviationSeverity)
- **Typically no**: Type/category models (RecipeType, MaterialType, PhaseType) — unless the developer wants color-coded categories

### When to include `default` field:
Ask the developer: "Which enum models should have a default value pre-selected in form inputs?"
- **Typically yes**: Status models (e.g., `DRAFT` is default for new recipes, `AVAILABLE` is default for equipment)
- **Typically yes**: Type models when there's a clear default (e.g., `STANDARD` phase type)
- **Typically no**: Category models where no single value is a natural default

### `usable` vs `active` — Know the Difference

These two Boolean fields serve different purposes and should NEVER be confused:

| Field | Used on | Meaning | Controls |
|-------|---------|---------|----------|
| `usable` | **Lookup/enum models only** | Whether this enum value should appear in selection lists | Dropdown/select visibility — set `false` to hide a value from users without deleting it |
| `active` | **Master data and transactional models** | Whether the record itself is active/operational | Record lifecycle — `false` means the record is deactivated (soft delete) |

**Rules:**
- Lookup/enum models → use `usable` (Boolean!, defaultValue: true). Do NOT add `active` to enum models.
- Master data models (Equipment, Recipes, Process Cells) → use `active` (Boolean!, defaultValue: true). Do NOT add `usable` to master data.
- Transactional models (Batches, Campaigns) → use `active` (Boolean!, defaultValue: true) if lifecycle soft-delete is needed.
- NEVER put both `usable` and `active` on the same model.

### Lookup models MUST have inverse list relations:
When another model references a lookup via FK (e.g., `BatchDeviation.deviationTypeId` → `DeviationType`), the lookup model MUST have an inverse list relation field pointing back (e.g., `DeviationType.batchDeviations` of type `[BatchDeviation!]!`). This is required for every FK reference within the same package. See Shape 3 for the full field template.

### Lookup/Enum Seed Values — Ask the Developer

When the package includes lookup/enum models (e.g., BatchState, RecipeType, DeviationSeverity), the developer may want to pre-populate them with values. **Always ask the developer:**

1. "Do you want these lookup values included in the package, or will you add them manually after import?"
2. If yes: "Here are the values I'd propose for [ModelName] — please review: `DRAFT`, `ACTIVE`, `RETIRED`..."
3. NEVER silently assume what lookup values should be

**If the developer wants values included**, add them as `packageDefinitionSelections` in `definition.json`:
```json
{
  "id": "sel_recipeType",
  "api": "application",
  "modelName": "RecipeType",
  "selected": [
    { "id": "GENERAL", "label": "General" },
    { "id": "SITE", "label": "Site" },
    { "id": "MASTER", "label": "Master" },
    { "id": "CONTROL", "label": "Control" }
  ],
  "fields": [
    { "name": "id" },
    { "name": "name" },
    { "name": "description" }
  ],
  "metadata": {
    "dataTransform": "$",
    "viewFields": [
      { "id": "id", "label": "id" },
      { "id": "name", "label": "name" }
    ],
    "labelField": "name"
  },
  "packageDefinitionVersionId": "matches-parent-pdv-id"
}
```

**Important:** Lookup value records go in `definition.json` `packageDefinitionSelections` — NOT in the `package-data.json` `data` array. The `data` array is only for Sequences, ModuleGroups, and Modules.

### Where lookup models go:
- **YES** — in `package-data.json` `dataModels` array (they ARE data models)
- **YES** — in `definition.json` `DataModel` selection
- **YES** — their value records go in `definition.json` `packageDefinitionSelections` (if developer approves)
- **NO** — their value records do NOT go in the `package-data.json` `data` array

---

## 7. Index Rules

### FUUZ Auto-Indexes These — Do NOT Manually Define:

FUUZ automatically creates indices for:
- All `id` fields
- All FK fields (any field ending in `Id` that is a relation)
- All `DateTime` fields
- All relation fields

**Because of auto-indexing, most models should use `indices: []`.** Only define custom indices for specialized scenarios the developer explicitly requests.

### Default: Use empty indices `[]`

Incorrect indices **block import entirely**. When in doubt, omit them. Indices can always be added later through the platform UI.

### When to add custom indices:

Only for specialized scenarios NOT covered by auto-indexing:

| Scenario | Unique? | Example |
|----------|---------|---------|
| Composite index across multiple fields | `false` | `{ "name": "idx_recipe_seq", "unique": false, "fields": ["batchRecipeId", "sequenceNumber"] }` |
| Unique constraint on a non-id field | `true` | `{ "name": "idx_unique_code", "unique": true, "fields": ["code"] }` |
| TTL index (time-to-live) | `false` | `{ "name": "TTLIndex", "unique": false, "ttl": 14400, "fields": ["createdAt"] }` |

### TTL Index (Time-To-Live)

Auto-expires records after a specified duration (in seconds). Essential for high-volume time-series data:
```json
{ "name": "TTLIndex", "unique": false, "ttl": 14400, "fields": ["createdAt"] }
```
Common TTL values: `14400` (4 hours) for raw telemetry, `86400` (1 day) for temp data, `604800` (7 days), `2592000` (30 days), `7776000` (90 days).

### High-Volume Model Metadata

For models that receive frequent writes (telemetry, forecasts, insights, logs), disable expensive features:
```json
{
  "dataChangeCapture": { "exposed": false, "retentionDays": 30 },
  "customFields": { "exposed": false }
}
```

### When NOT to add indices:

- **FK fields** — auto-indexed
- **Date fields** — auto-indexed
- **Relation fields** — auto-indexed
- **`id` fields** — auto-indexed
- **Lookup/enum models** — too small to benefit
- When you are **not 100% certain** the field name exists in the model's field list

**Inform the developer:** "FUUZ auto-indexes all id, FK, date, and relation fields. You only need custom indices for composite lookups or unique constraints not already covered."

### Index validation rule:

Every field name listed in an index's `fields` array MUST exist as a `name` in the model's `fields` array. If it doesn't, import fails silently or with an error.

---

## 8. The `data` Array

The `data` array in `package-data.json` holds seed records that get created during package import.

### What goes in `data`:

| modelName | When | Record shape |
|-----------|------|-------------|
| `Sequence` | Package defines auto-number sequences | `{ id, name, type, increment, prefix, suffix, zeroPadding, _customFields }` |
| `ModuleGroup` | Package creates new module groups | `{ id, name, icon, description, _customFields }` |
| `Module` | Package creates new modules | `{ id, name, description, moduleGroupId, _customFields }` |

### What does NOT go in `data`:
- Lookup/enum value records
- Any model's transactional data

### Seed data payload validation rule:

Every key in a seed data payload record MUST exist as a `name` in the target model's field definition. If the payload contains a field the model doesn't define, import fails with: `Field "X" is not defined by type "YCreateInput"`. This commonly happens when lookup models don't have an `active` field but seed data includes `"active": true`.

### Data entry format:
```json
{
  "api": "application",
  "modelName": "Sequence",
  "payload": [ /* array of records */ ],
  "metadata": {}
}
```

### Sequences — Complete Guide

Sequences generate auto-incrementing numbers for transactional models (e.g., batch numbers, work order numbers). They are ONLY needed when a model has an auto-number field. Standard FUUZ apps starting from scratch will NOT include sequences — they are only present when building on an application accelerator or when the developer explicitly requests auto-numbering.

**When to create sequences:**
- The developer explicitly requests auto-generated number fields
- The package extends an existing application that already uses sequences
- NEVER assume a model needs a sequence — ask the developer

**Sequence record structure:**
```json
{
  "id": "batchNumber",
  "name": "Batch Number",
  "type": "String",
  "increment": 1,
  "prefix": "B",
  "suffix": "",
  "zeroPadding": 8,
  "_customFields": { "_externalId": null }
}
```

**Sequence fields explained:**

| Field | Type | Description | Example |
|-------|------|-------------|---------|
| `id` | String | Unique sequence identifier (camelCase) | `"batchNumber"` |
| `name` | String | Display name | `"Batch Number"` |
| `type` | String | Output type — MUST be `"String"` or `"Int"` | `"String"` |
| `increment` | Int | Step value per generated number | `1` |
| `prefix` | String | Text prepended to the number | `"B"`, `"WO"`, `"RCP"` |
| `suffix` | String | Text appended to the number (usually empty) | `""` |
| `zeroPadding` | Int | Pad number with leading zeros to this width (0 = no padding) | `8` → `B00000001` |
| `_customFields` | Object | Always `{ "_externalId": null }` | — |

**Example sequences from a batch processing application:**

```json
// Batch number: generates B00000001, B00000002, ...
{ "id": "batchNumber", "name": "Batch Number", "type": "String", "increment": 1, "prefix": "B", "suffix": "", "zeroPadding": 8, "_customFields": { "_externalId": null } }

// Recipe code: generates RCP000001, RCP000002, ...
{ "id": "batchRecipeCode", "name": "Batch Recipe Code", "type": "String", "increment": 1, "prefix": "RCP", "suffix": "", "zeroPadding": 6, "_customFields": { "_externalId": null } }

// Campaign number: generates CAMP000001, CAMP000002, ...
{ "id": "campaignNumber", "name": "Campaign Number", "type": "String", "increment": 1, "prefix": "CAMP", "suffix": "", "zeroPadding": 6, "_customFields": { "_externalId": null } }

// Deviation code with no zero padding: generates BD1, BD2, ...
{ "id": "batchDeviationCode", "name": "Batch Deviation Code", "type": "String", "increment": 1, "prefix": "BD", "suffix": "", "zeroPadding": 0, "_customFields": { "_externalId": null } }
```

### Linking a Sequence to a Model Field

When a model field is backed by a sequence, add a `"sequence"` property to the field's `mfgx` metadata. The field's `type` MUST match the sequence's `type` (usually `String!`).

**Example: `code` field on BatchDeviation linked to `batchDeviationCode` sequence:**
```json
{
  "name": "code",
  "type": "String!",
  "description": "",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true },
      "sequence": {
        "id": "batchDeviationCode"
      }
    },
    "unique": true
  }
}
```

**Key rules for sequence-backed fields:**
- The `sequence.id` MUST match a Sequence record `id` in the `data` array
- The field `type` MUST be `String!` or `Int!` (matching the sequence's `type`)
- The field is typically `unique: true` (auto-numbers should be unique)
- The sequence MUST also be listed in the `definition.json` Sequence selection
- The model using the sequence typically has `dataModelTypeId: "transactional"` and `labelField` set to the sequence-backed field name

### ModuleGroup record:
```json
{
  "id": "myModuleGroup",
  "name": "My Module Group",
  "icon": "boxes",
  "description": "Description of this functional area",
  "_customFields": { "_externalId": null }
}
// icon MUST be a FontAwesome icon name (Pro license available)
// Examples: "boxes", "flask", "industry", "cogs", "clipboard-list", "chart-bar"
// Reference: https://fontawesome.com/icons — use the icon name without the "fa-" prefix
```

### Module record:
```json
{
  "id": "myModule",
  "name": "My Module",
  "description": "Description of this module",
  "moduleGroupId": "myModuleGroup",
  "_customFields": { "_externalId": null }
}
```

---

## 8a. Model Triggers (JSONata Transforms)

Triggers are JSONata expressions that execute automatically on create, update, or delete operations. They are defined in the model's `metadata.mfgx.triggers` property.

### Trigger placement in model metadata:

```json
"metadata": {
  "mfgx": {
    "module": { ... },
    "exposed": true,
    "triggers": {
      "create": "JSONata expression for create",
      "update": "JSONata expression for update"
    },
    "mutations": { ... }
  }
}
```

### Trigger event types:

| Event | Fires when | Access to |
|-------|-----------|-----------|
| `"create"` | New record is created | `$` (incoming payload), `$after` (new values) |
| `"update"` | Existing record is updated | `$` (incoming payload), `$before` (old values), `$after` (new values) |

### Common trigger patterns (from MES package):

**Pattern A: Auto-set ID from code (uppercase)**
```jsonata
"create": "$isNilOrEmpty($.id)? $~>|$|{\"id\":$uppercase($.code)}| : $"
```
Only sets `id` if it's empty. Uses `$uppercase()` to normalize the code.

**Pattern B: Auto-set ID from code (camelCase)**
```jsonata
"create": "$merge([$, {\"id\": $camelCase($after.code)}])"
```
Uses `$merge()` to combine the payload with a computed `id` from the camelCase of the code. This is the standard pattern for setup/enum models.

**Pattern C: Calculated field (create + update)**
```jsonata
"create": "$~>|$|{\"balanceQuantity\": $after.quantity - $after.producedQuantity}|",
"update": "$~>|$|{\"balanceQuantity\": $after.quantity - $after.producedQuantity}|"
```
Keeps a derived field in sync on both create and update. Uses the pipe operator `~>|$|{}|` syntax.

**Pattern D: Conditional logic**
```jsonata
"create": "$isNilOrEmpty($.id)? $~>|$|{\"id\": value}| : $"
```
Only applies the transform if a condition is true, otherwise returns the payload unchanged.

### JSONata functions available in triggers:

| Function | Purpose | Example |
|----------|---------|---------|
| `$merge([...])` | Combine objects | `$merge([$, {"id": "value"}])` |
| `$uppercase(str)` | Convert to uppercase | `$uppercase($.code)` |
| `$camelCase(str)` | Convert to camelCase | `$camelCase($after.code)` |
| `$isNilOrEmpty(val)` | Check null/empty | `$isNilOrEmpty($.id)` |
| `$isNil(val)` | Check null/undefined | `$isNil($after.endDate)` |
| `$moment(date)` | Date manipulation | `$moment($after.startDate)` |
| `$after.field` | Access new/updated value | `$after.quantity` |
| `$before.field` | Access previous value (update only) | `$before.status` |

### When to use triggers:

- **Auto-generating IDs** from code/name fields (setup models)
- **Computed/derived fields** that must stay in sync (balances, totals, composite codes)
- **Formatting values** (uppercase, camelCase, concatenation)
- **Composite unique identifiers** (e.g., product code + revision + date)

### When NOT to use triggers:

- **State machine logic** — use data flows instead
- **Complex business rules** — use data flows instead
- **Cross-model operations** — triggers only affect the current record; use data flows for multi-model operations
- **Delete-event reactions** — while `"delete"` triggers are technically supported, they are rarely used. Use a data flow with a **data change node** instead for cascading side effects, audit logging, or cleanup operations after deletion.

### Alternative to triggers: Flows as APIs

Instead of relying on triggers for complex transformations, consider building **data flows with request-response nodes**. These flows:
- Can be called from anywhere as an API (from screens, other flows, external systems)
- Allow more standardized, visual transform pipelines (query → transform → mutate)
- Are easier to test and debug than inline JSONata trigger expressions
- Support multi-model operations that triggers cannot handle

**When to use flows as APIs instead of triggers:**
- The transform needs to read/write multiple models
- The logic needs to be testable and debuggable step-by-step
- The operation needs to be callable from multiple places (not just on create/update)
- The transform is complex enough that trial-and-error in a trigger would be painful

### Important notes:

- Triggers are an **advanced use case** — discuss with the developer before adding them
- Trigger testing is trial and error in the build environment — this works fine with simple CRUD operations, but consider flows for anything more complex
- Always test trigger expressions carefully — a broken JSONata expression can block record creation
- Setup/enum models commonly use Pattern B (camelCase ID from code) — this is the standard convention

---

## 8b. Screen Development Guidance

Even though packages NEVER include screen definitions (screens are built in the FUUZ screen designer), documenting screen candidates helps the developer plan their UI development.

### Screen type recommendations by model type:

| Model type | Recommended screen pattern | Notes |
|-----------|--------------------------|-------|
| Setup/enum models | **Setup screen** — simple list with inline edit | Standard grid view with add/edit/delete. Group similar lookups together. |
| Master data models | **List + Detail screen** — grid list view with click-to-open detail form | Search/filter on the list, full form on detail. Include related child records as embedded tables. |
| Transactional models | **List + Detail screen** — grid list with detail form, possibly with workflow actions | Status badges, action buttons for state transitions. Show child records as tabs or embedded tables. |
| Child detail models | **Embedded table** — shown inline on the parent's detail screen | NOT standalone screens. Shown as a grid/table within the parent record's detail view. |

### When documenting models for the developer, note:

- Which models need standalone list screens (master data, transactional)
- Which models should be embedded tables on parent screens (child detail records)
- Which lookup models can be grouped on a single setup screen
- Which transactional models need workflow/action buttons for state transitions

---

## 9. definition.json

### Structure:
```json
{
  "packageDefinition": {
    "name": "PackageName"
  },
  "packageDefinitionVersion": {
    "id": "unique-pdv-id",
    "version": "1.0.0",
    "packageDefinitionId": "packageId",
    "exportedFileId": null
  },
  "packageDefinitionSelections": []
}
```

### Required selections:

**1. DataModel** — MUST list every model in the package (and any external models referenced by relationship fields):
```json
{
  "id": "sel_datamodel",
  "api": "application",
  "modelName": "DataModel",
  "selected": [
    { "id": "myModel", "label": "MyModel" },
    { "id": "myLookup", "label": "MyLookup" }
  ],
  "fields": [
    { "name": "id" },
    { "name": "name" },
    { "name": "description" },
    { "name": "dataModelKindId" },
    { "name": "_customFields._externalId" }
  ],
  "metadata": {
    "dataTransform": "$",
    "viewFields": [
      { "id": "id", "label": "id" },
      { "id": "name", "label": "name" },
      { "id": "description", "label": "description" }
    ],
    "labelField": "name"
  },
  "packageDefinitionVersionId": "pdv-id"
}
```

**2. Sequence** — if the package defines any sequences:
```json
{
  "id": "sel_sequence",
  "api": "application",
  "modelName": "Sequence",
  "selected": [
    { "id": "orderNumber", "label": "Order Number" }
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
    "dataTransform": "$",
    "viewFields": [
      { "id": "id", "label": "id" },
      { "id": "name", "label": "name" }
    ],
    "labelField": "name",
    "insertBefore": [
      { "modelName": "DataModel" }
    ]
  },
  "packageDefinitionVersionId": "pdv-id"
}
```

**3. ModuleGroup** — if creating new module groups
**4. Module** — if creating new modules

Both follow the same selection pattern with their respective fields.

### External model selections:

When your package references models from other installed packages (e.g., `Product`, `Unit`, `Area` from MES), those external models MUST be included in the `DataModel` selection with **all the same metadata** as new models. External models are not exempt from selection metadata — they need the same `fields`, `metadata`, `viewFields`, `labelField`, and `dataTransform` as any model you create.

### What does NOT need a selection:
- Individual lookup/enum model value lists — not required

### Understanding `dataTransform: "$"`:

The `"dataTransform": "$"` value in selection metadata means the transform applies to **ALL existing data** in the table during import. This is particularly important for:

- **Adding new required fields to an existing model** — existing records will need a migration to populate the new field
- **In the build environment:** You can delete the package and start over, then re-import data. This is the simplest approach.
- **In production:** Deleting data is NOT an option. Plan data migrations carefully before upgrading packages with new required fields.

**Best practice:** When adding required fields to a model that already has data in production, include a `dataTransform` that provides default values for existing records, or make the new field optional initially and populate it through a data flow.

### Key rule:
All `packageDefinitionVersionId` values MUST match `packageDefinitionVersion.id`.

---

## 10. Module Assignment

Every model's `metadata.mfgx.module` references a module and module group. Both MUST exist in the tenant at import time.

**CRITICAL: NEVER invent module or module group IDs.** Always get them from the developer. Module groups and modules organize the application's navigation and data model ownership — inventing IDs that don't match the developer's intent causes confusion and broken navigation.

### Before generating any package, ask the developer:

1. "What module groups and modules should these models belong to?"
2. "Do these modules already exist in your tenant, or should the package create them?"
3. "What are the exact camelCase IDs you want to use?" (e.g., `recipeManagement`, `batchExecution`)

### How to ensure they exist:

**Option A (recommended for new packages):** Include ModuleGroup and Module records in the `data` array AND their corresponding selections in `definition.json`. Use the IDs the developer provided.

**Option B:** Reference modules that already exist in the tenant. Confirm the exact IDs with the developer before generating the package.

### ModuleGroup and Module records in the `data` array:

```json
// ModuleGroup
{
  "id": "batchProcessing",
  "name": "Batch Processing",
  "icon": "boxes",
  "description": "ISA-88 batch control models",
  "_customFields": { "_externalId": null }
}

// Module (must reference its parent group)
{
  "id": "recipeManagement",
  "name": "Recipe Management",
  "description": "Batch recipe definitions and procedures",
  "moduleGroupId": "batchProcessing",
  "_customFields": { "_externalId": null }
}
```

### Common error messages:
- `"Invalid Module on data model 'X'. The provided module 'Y' is invalid or cannot be found."` — module ID doesn't exist
- `"Invalid Module Group on data model 'X'. The provided module group 'Y' is invalid or is not associated with the module 'Z'"` — group doesn't exist or module isn't a member

### ID format:
- Both module and module group IDs use camelCase: `"qualityManagement"`, `"productionManagement"`

### Icon convention:
- Module group icons MUST use **FontAwesome** icon names (FUUZ has the Pro license)
- Use the icon name without the `fa-` prefix: `"boxes"`, `"flask"`, `"industry"`, `"cogs"`, `"clipboard-list"`
- Reference: https://fontawesome.com/icons
- Ask the developer which icons they prefer for each module group

---

## 10a. Custom Fields (`_customFields`)

Every model automatically supports `_customFields` — these are developer-defined fields added through the FUUZ UI after package import. They are typically site-specific or location-specific.

**Rules:**
- Every model definition MUST include `"customFields": { "exposed": true }` in its `mfgx` metadata — this enables the feature
- Do NOT pre-define custom field schemas in the package — let developers create them in FUUZ directly
- The only custom field to include in the model's field list is `_customFields._externalId` (type `String`) — this is the standard external system integration hook
- Seed data records should include `"_customFields": { "_externalId": null }` as a placeholder
- Import CSV files should include a `_customFields` column (leave values blank)

---

## 11. Import Error Reference

| Error Message | Root Cause | Fix |
|---------------|-----------|-----|
| `Cannot read properties of undefined (reading 'filter')` | Wrong top-level keys in `package-data.json` | Use exactly: `dataModels, screens, dataFlows, dataMappings, documentDesigns, savedTransforms, data` |
| `The update mutation of the 'id' field is marked true` | `id` field has `update.include: true` | Set `update.include: false` on every `id` field |
| `Invalid Module on data model 'X'` | Module ID doesn't exist in tenant | Create via `data` array or use existing ID |
| `Invalid Module Group on data model 'X'` | Module group ID doesn't exist or doesn't own the module | Create via `data` array or use existing ID |
| `The field dataModelKindId contains an invalid reference id (enumeration)` | Used `"enumeration"` | Always use `"reference"` |
| `One or more fields contains an invalid type` | Relationship type references a model not installed in tenant | Add target model to `dataModels` or confirm it's already installed |
| `Cannot read properties of undefined (reading 'relation')` | Scalar field missing `mutations`, `queries`, or `where` | Use complete Shape 2 metadata on all scalar fields |
| Index-related errors | Index references a field name not in the model's field list | Remove the index or fix the field name; default to `[]` |
| `tenantId filter not supported` | `dependencies` lists a package name | Set `dependencies: {}` |
| `Field "X" is not defined by type "YCreateInput"` | Seed data payload contains a field not in the model's field definition | Remove the field from the seed data payload; every key in a payload record must exist as a field `name` in the model |

---

## 12. Pre-Package Validation Checklist

Run through this before creating the `.fuuz` file:

### manifest.json
- [ ] `applicationPublisherId` is `"fuuz"`
- [ ] `enterpriseId` is correct for the target tenant (developer-provided, NOT assumed)
- [ ] `environmentId` is correct for the target tenant (developer-provided, NOT assumed)
- [ ] `dependencies` is `{}`
- [ ] `platformVersion` matches target (developer-provided)

### package-data.json
- [ ] Top-level keys are exactly the 7 required keys
- [ ] `screens`, `dataFlows`, `dataMappings`, `documentDesigns`, `savedTransforms` are ALL empty `[]` — NEVER populated
- [ ] ALL models are in `dataModels` (including lookup/enum models)
- [ ] ALL models: `dataModelKindId: "reference"`, `kind: "Reference"`, `graphql.type: "Object"`
- [ ] ALL models have complete `mfgx` metadata: `module`, `exposed`, `mutable`, `mutations`, `dataChangeCapture`, `customFields`, `editor`, `indices`
- [ ] `labelField` is set to a valid field name for lookup models (`"name"`) and transactional models (their number field), or **omitted entirely** for other models
- [ ] `version.dataModelTypeId` is `"setup"` for ALL lookup/enum models, `"transactional"` for operational/auto-number models, `null` for master data and reference models
- [ ] ALL `id` fields: `update.include` is `false`
- [ ] ALL scalar fields: metadata has `mutations.create.include`, `mutations.update.include`, `queries.include`, `where.include`
- [ ] ALL relationship fields: metadata has `relation.fields.from`, `relation.fields.to`, `queries.include`
- [ ] ALL relationships come in **triplets**: FK scalar field + forward relation object field + inverse list relation on the parent model
- [ ] ALL forward relation fields include `mutations`, `queries`, `where`, and `deletionReferenceBehavior: "prevent"`
- [ ] ALL inverse list relation fields include `mutations`, `queries`, `where` with full `{ "include": true }` shape
- [ ] ALL parent models (including lookup/enum models) have inverse list relations for every FK that points to them from within the same package
- [ ] No inverse list relations added for FKs pointing to external package models (e.g., Product, Unit)
- [ ] ALL indices reference only fields that exist in the model
- [ ] Lookup/enum models have `indices: []`
- [ ] `data` array has Sequence records if needed — sequence `type` matches the linked field's type (`String` or `Int`)
- [ ] ALL sequence-backed fields have `"sequence": { "id": "sequenceId" }` in their `mfgx` metadata
- [ ] ALL sequence-backed fields are `String!` or `Int!` type with `unique: true`
- [ ] `data` array has ModuleGroup and Module records if creating new ones — IDs confirmed with developer
- [ ] No module/group IDs were invented — all came from developer input
- [ ] No lookup/enum value records in `data` array (they go in `definition.json` `packageDefinitionSelections`)
- [ ] ALL seed data payload records contain ONLY fields that exist in the target model's field definition (no extra fields like `active` unless the model defines it)
- [ ] ALL lookup/enum models have `dataModelTypeId: "setup"` (not `null`)
- [ ] ALL lookup/enum models have `usable` (Boolean!, defaultValue: true) field
- [ ] ALL lookup/enum seed values have verbose, meaningful `description` fields (never empty or name-repeated)
- [ ] Lookup/enum models that need visual indicators have `color` (String) field — confirmed with developer
- [ ] Lookup/enum models that need form defaults have `default` (Boolean, defaultValue: false) field — confirmed with developer
- [ ] Enum record `id` values are abbreviated uppercase (e.g., `"MAINT"` not `"MAINTENANCE"`)
- [ ] `deletionReferenceBehavior` is set appropriately: `"prevent"` for lookup references, `"cascade"` for owned child records — confirmed with developer
- [ ] Lookup/enum models use `usable` (Boolean!) — NOT `active`
- [ ] Master data and transactional models use `active` (Boolean!) — NOT `usable`
- [ ] No model has BOTH `usable` and `active`
- [ ] Module group icons are valid FontAwesome icon names (no `fa-` prefix)
- [ ] Color fields on enum models contain hex values only (e.g., `"#4CAF50"`) — no named colors
- [ ] `_customFields._externalId` included in field list; no other custom fields pre-defined in the package
- [ ] Model names are PascalCase singular (never plural)
- [ ] NO system audit fields defined (`createdAt`, `updatedAt`, `createdBy`, `updatedBy`) — FUUZ manages these
- [ ] Domain-specific timestamps/users ARE included where needed (`completedAt`, `approvedBy`, etc.)
- [ ] FK fields use prefix naming convention: `{modelName}Id` — never suffix (`sourceBatchId` not `batchIdSource`)
- [ ] `dataChangeCapture.retentionDays` set per tier: setup=120, transactional=3650, master=5475 — confirmed with developer
- [ ] Package version is `1.0.0` or greater, and greater than any currently installed version
- [ ] No manually defined indices for fields that are auto-indexed (id, FK, date, relation fields)
- [ ] No `"delete"` triggers used — prefer data flows with data change nodes for deletion reactions
- [ ] Triggers are only used for simple cases (auto-ID, computed fields, formatting) — complex logic uses flows

### definition.json
- [ ] `DataModel` selection lists ALL models (plus any external referenced types)
- [ ] External model selections (from other packages) include the SAME metadata as new models (fields, viewFields, labelField, dataTransform)
- [ ] `Sequence` selection matches sequences in `data` array (every sequence in `data` has a matching selection entry)
- [ ] `ModuleGroup` / `Module` selections if creating new ones
- [ ] Lookup/enum value selections included ONLY if developer approved them
- [ ] All `packageDefinitionVersionId` values match `packageDefinitionVersion.id`

### Naming conventions (cross-check — #1 source of mistakes)
- [ ] ALL model names: PascalCase singular (`BatchRecipe`, not `batchRecipes` or `batch_recipe`)
- [ ] ALL field names: camelCase (`batchRecipeId`, not `BatchRecipeId` or `batch_recipe_id`)
- [ ] ALL FK fields: prefix-based (`batchRecipeId`, not `recipeIdBatch`)
- [ ] ALL module/group IDs: camelCase (`recipeManagement`, not `Recipe-Management`)
- [ ] ALL mongo collection names: match `header.id` (camelCase)

---

## 13. Validation Script

Run this after generating the package files to catch errors before import:

```bash
# 1. Validate JSON syntax
cat manifest.json | python -m json.tool > /dev/null && echo "manifest OK"
cat definition.json | python -m json.tool > /dev/null && echo "definition OK"
cat package-data.json | python -m json.tool > /dev/null && echo "package-data OK"

# 2. Full structural validation
python -c "
import json, sys
errors = []
d = json.load(open('package-data.json'))

# Check top-level keys
expected = ['data','dataFlows','dataMappings','dataModels','documentDesigns','savedTransforms','screens']
actual = sorted(d.keys())
if actual != expected:
    errors.append(f'Top-level keys wrong: {actual} (expected {expected})')

for m in d.get('dataModels', []):
    name = m['header']['name']
    mid = m['header']['id']

    # dataModelKindId
    if m['header'].get('dataModelKindId') != 'reference':
        errors.append(f'{name}: dataModelKindId is not reference')

    md = m['version']['modelDefinition']

    # kind
    if md.get('kind') != 'Reference':
        errors.append(f'{name}: kind is not Reference')

    # graphql type
    if md.get('metadata',{}).get('graphql',{}).get('type') != 'Object':
        errors.append(f'{name}: graphql.type is not Object')

    # mfgx completeness
    mfgx = md.get('metadata',{}).get('mfgx',{})
    for prop in ['module','exposed','mutable','mutations','dataChangeCapture','customFields','editor','indices']:
        if prop not in mfgx:
            errors.append(f'{name}: missing mfgx.{prop}')
    # labelField validation: if present, must reference an actual field
    if 'labelField' in mfgx:
        lf = mfgx['labelField']
        if lf is None:
            errors.append(f'{name}: labelField is null (omit the key entirely instead)')

    # dataModelTypeId validation
    dt = m['version'].get('dataModelTypeId')
    if dt is not None and dt not in ('transactional', 'master', 'setup'):
        errors.append(f'{name}: invalid dataModelTypeId \"{dt}\"')

    # Check if model looks like a lookup/enum (has code, name, usable fields, few total fields)
    is_enum_like = 'code' in field_names and 'usable' in field_names and 'name' in field_names and len(fields) < 15
    if is_enum_like and dt != 'setup':
        errors.append(f'{name}: looks like lookup/enum model but dataModelTypeId is \"{dt}\" (should be \"setup\")')

    # Check enum models have description field
    if is_enum_like and 'description' not in field_names:
        errors.append(f'{name}: lookup/enum model missing description field')

    # Field checks
    fields = md.get('fields', [])
    field_names = {f['name'] for f in fields}

    # Validate labelField references a real field
    if 'labelField' in mfgx and mfgx['labelField'] is not None:
        if mfgx['labelField'] not in field_names:
            errors.append(f'{name}: labelField \"{mfgx[\"labelField\"]}\" not in field list')

    for f in fields:
        fname = f['name']
        meta = f.get('metadata',{}).get('mfgx',{})
        has_relation = 'relation' in meta

        # id field update check
        if fname == 'id':
            upd = meta.get('mutations',{}).get('update',{}).get('include')
            if upd != False:
                errors.append(f'{name}.id: update.include is not False')

        # Scalar field completeness
        if not has_relation and fname != 'id':
            for prop in ['mutations','queries','where']:
                if prop not in meta:
                    errors.append(f'{name}.{fname}: missing mfgx.{prop}')

        # Relationship field completeness
        if has_relation:
            rel = meta.get('relation',{})
            if 'fields' not in rel or 'from' not in rel.get('fields',{}) or 'to' not in rel.get('fields',{}):
                errors.append(f'{name}.{fname}: incomplete relation.fields')
            if 'queries' not in meta:
                errors.append(f'{name}.{fname}: missing queries on relation field')

    # Index field validation
    for idx in mfgx.get('indices', []):
        for ifield in idx.get('fields', []):
            if ifield not in field_names:
                errors.append(f'{name}: index \"{idx[\"name\"]}\" references missing field \"{ifield}\"')

# Inverse list relation validation
# Build FK map: for each model, find FK fields pointing to other models in this package
model_names_set = {m['header']['name'] for m in d.get('dataModels', [])}
# Map model name -> set of inverse relation 'to' fields already defined
model_inverses = {}
for m in d.get('dataModels', []):
    mname = m['header']['name']
    model_inverses[mname] = set()
    for f in m['version']['modelDefinition'].get('fields', []):
        meta = f.get('metadata', {}).get('mfgx', {})
        rel = meta.get('relation', {}).get('fields', {})
        if f['type'].startswith('[') and rel.get('from') == 'id':
            model_inverses[mname].add(rel.get('to', ''))

# Now check every forward relation has a matching inverse
for m in d.get('dataModels', []):
    mname = m['header']['name']
    for f in m['version']['modelDefinition'].get('fields', []):
        meta = f.get('metadata', {}).get('mfgx', {})
        rel = meta.get('relation', {}).get('fields', {})
        ftype = f['type'].replace('!', '')
        # Forward relation: from is a local FK field, to is 'id', type is a single model name
        if rel.get('to') == 'id' and not f['type'].startswith('[') and ftype in model_names_set:
            fk_field = rel.get('from', '')
            # Check if target model has inverse pointing back via this FK
            if fk_field not in model_inverses.get(ftype, set()):
                errors.append(f'{ftype}: missing inverse list relation for {mname}.{fk_field}')

# Seed data payload validation
models_by_name = {}
for m in d.get('dataModels', []):
    flds = m['version']['modelDefinition'].get('fields', [])
    models_by_name[m['header']['name']] = {f['name'] for f in flds}

for entry in d.get('data', []):
    mn = entry.get('modelName', '')
    if mn in models_by_name:
        model_fields = models_by_name[mn]
        for i, rec in enumerate(entry.get('payload', [])):
            for key in rec:
                if key != '_customFields' and key not in model_fields:
                    errors.append(f'Seed data {mn}[{i}]: field \"{key}\" not in model definition')

if errors:
    print(f'FOUND {len(errors)} ERROR(S):')
    for e in errors:
        print(f'  - {e}')
    sys.exit(1)
else:
    print(f'ALL CHECKS PASSED ({len(d.get(\"dataModels\",[]))} models, {len(d.get(\"data\",[]))} data entries)')
"

# 3. Create package
tar -czf PackageName@version.fuuz manifest.json definition.json package-data.json
```

---

## 14. Import File Generation Rules

When generating CSV import files for loading data into FUUZ models, follow these rules:

### Column inclusion rules:

**MUST include:**
- Every scalar field defined in the model (id, code, name, description, etc.)
- Every FK field (e.g., `processCellId`, `batchRecipeId`)
- `_customFields` column (leave values empty/null unless user provides specific custom field data)

**MUST NOT include:**
- Relationship object fields (e.g., `processCell`, `batchRecipe` — the resolved objects). Only include the FK scalar field (e.g., `processCellId`).
- CRUD/system fields: `createdAt`, `updatedAt`, `createdBy`, `updatedBy`, `_trace`, `_metadata`
- `externalId` or `integrationData` — unless the user explicitly requests them

### Boolean formatting:

All boolean values MUST be lowercase: `true` or `false`. Never `True`, `False`, `TRUE`, or `FALSE`.

### Import order:

Files must be numbered and imported in dependency order (parents before children). A child record's FK must reference an already-imported parent ID.

### FK values:

- Required FK fields (`ID!`) must always have a value — never leave blank
- Optional FK fields (`ID`) may be left blank
- **ALL FK values must match the target record's `id` field 100% exactly (case-sensitive)** — any mismatch causes import errors
- For setup/enum FKs, the value matches the enum record's `id` (e.g., `MASTER`, `DRAFT`, `AVAILABLE`)
- For master data FKs, the value matches the parent record's `id` from a previously imported file

### ID generation in import files:

| Model type | ID convention | Example |
|-----------|--------------|---------|
| Setup/enum records | camelCase of the code value (or the code itself if already short) | `"master"`, `"draft"`, `"inProgress"` |
| Master data records | Human-readable prefixed codes or CUIDs | `"PC-REACTOR-01"`, `"EQ-MIXER-A"` |
| Transactional records | CUIDs (auto-assigned by FUUZ if left blank) or meaningful composites | Leave blank for FUUZ to assign, or use structured codes |

**Note:** If the `id` column is left blank in an import file, FUUZ will auto-assign a CUID. For setup/enum records, always provide an explicit `id` matching the seed data. For master data, provide meaningful IDs that make FK references readable in subsequent import files.

### File naming convention:

`NN_ModelName.csv` where `NN` is the import sequence number (e.g., `01_ProcessCell.csv`, `02_EquipmentClass.csv`)

---

## 15. Package Promotion: Build → QA → Production

FUUZ has an internal environment promotion process. Claude does NOT manage this — developers handle it through FUUZ's package management features. However, developers should understand the process:

### Environment flow:

```
Build → QA → Production
```

### Key points for developers:

1. **Build environment** — where Claude-generated packages are imported, tested, and iterated on. Mistakes are cheap here — you can delete the package and start over.
2. **QA environment** — use FUUZ package management to promote the package from build to QA for formal testing.
3. **Production environment** — use FUUZ package management to promote from QA to production. This is where data migrations matter (see `dataTransform: "$"` in Section 9).
4. **Version management** — the package version must be greater than what's installed in each target environment. Plan version numbers accordingly across environments.
5. **Data considerations** — in build, you can freely delete and re-import. In production, plan data migrations before upgrading packages with new required fields.

### Package rollback:

- Developers CAN uninstall/delete packages that were imported
- This is especially useful in the build environment when iterating — if a package import causes issues, roll it back and re-import a corrected version
- In production, rollback is more sensitive — data created under the package may be affected

### Claude's role:

- Claude generates and iterates on packages in the **build environment only**
- Claude does NOT manage promotion between environments
- When a developer says "promote to production," direct them to use FUUZ's package management UI

---

## 16. Roles, Policy Groups, and Policies

FUUZ packages CAN include `roles`, `policyGroups`, and `policies` in the package definition. However, the recommended workflow is:

### Recommended approach:

1. **Install the model package** generated by Claude (data models, modules, sequences)
2. **Implement roles and policies natively** within FUUZ using the built-in role/policy editor — this is more intuitive than hand-coding JSON
3. **Export the updated FUUZ package** (which now includes the roles and policies)
4. **Provide the exported package to Claude** for further training — Claude can learn the role/policy structure from real examples

### Why native-first:

- FUUZ's role/policy editor provides a visual interface for defining permissions
- Role/policy structures are complex and deeply tied to the specific models and screens in the application
- Getting the JSON structure right on the first attempt is difficult without examples
- Exporting from FUUZ guarantees the correct format

### When Claude CAN include roles/policies:

- When the developer provides a previously exported package that includes roles/policies as a reference
- When iterating on an existing package that already has role/policy definitions
- When the developer provides explicit, detailed role/policy specifications

---

## 17. Common Mistakes Reference

The most common mistakes when creating FUUZ packages, in order of frequency:

### #1: Naming Conventions (most frequent)

| Element | ❌ Wrong | ✅ Correct |
|---------|---------|-----------|
| Model name | `batchRecipes`, `batch_recipe` | `BatchRecipe` (PascalCase singular) |
| Field name | `BatchRecipeId`, `batch_recipe_id` | `batchRecipeId` (camelCase) |
| FK field | `recipeIdBatch`, `batch_id` | `batchRecipeId` (prefix, camelCase) |
| Module ID | `Recipe-Management` | `recipeManagement` (camelCase) |
| Screen name | varies | Follow developer conventions |
| Flow name | varies | Follow developer conventions |

### #2: Missing Relationship Triplets

Forgetting the inverse list relation on the parent model. Every FK needs all three parts:
1. FK scalar field on the child model
2. Forward relation on the child model
3. Inverse list relation on the parent model

### #3: Wrong Metadata Shape

Using incomplete metadata on scalar or relation fields. Every field type has a specific required metadata shape (see Section 5).

### #4: Wrong `dataModelTypeId`

Using `null` for lookup models (should be `"setup"`) or forgetting `"transactional"` for auto-number operational models.

### #5: System Fields in Model Definition

Including `createdAt`, `updatedAt`, `createdBy`, `updatedBy` — these are auto-managed by FUUZ and must never be defined.

### #6: Inventing Module/Group IDs

Creating module or module group IDs without developer input. Always get the exact IDs from the developer.

---

## 18. Data Flow Node Reference

FUUZ provides **50+ node types** for building data flows. Packages NEVER include flow definitions (always `dataFlows: []`), but understanding the available nodes helps when documenting flow candidates for the developer.

### Node categories (quick reference):

| Category | Node Types | Purpose |
|----------|-----------|---------|
| **Triggers** | `schedule`, `dataChanges`, `request`, `webhook`, `response` | Flow entry/exit points |
| **Data** | `query`, `mutate`, `queryFile` | Read/write data via GraphQL |
| **Transform** | `transform` (JSONata), `javascriptTransform` (ES6), `savedTransformV2` | Data transformation |
| **State** | `setContext`, `mergeContext`, `removeFromContext` | Flow state management |
| **Control Flow** | `when`, `unless`, `ifElse`, `switch`, `filterArray`, `broadcast`, `loop` | Branching and iteration |
| **Concurrency** | `mutexLock`, `mutexUnlock` | Critical sections |
| **UI (Web Flows)** | `formDialog`, `snackbar`, `searchTable`, `alertDialog`, `confirmDialog`, `navigateScreen` | User interaction |
| **Integration** | `integrateV2`, `awsLambda`, `openaiChat` | External systems |
| **Notification** | `sendNotification`, `receiveNotification`, `emailSend`, `emailRead` | Messaging |
| **Gateway/IoT** | `devicePrintFile`, `deviceSubscription`, `executeDeviceFunctionV2` | Edge devices |
| **Debug** | `debugSource`, `debugFile`, `debugDataChange`, `echo`, `log` | Testing |
| **Utility** | `validate`, `executeFlow`, `previewFile` | Miscellaneous |

### Flow scheduling:
- Flow schedules are configured **natively in FUUZ** using the **Data Flow Schedules** screen
- Do NOT attempt to configure schedules in packages
- Suggest to the developer: "Set up your flow schedules in the FUUZ Data Flow Schedules screen after import"

### Detailed node configurations:
For full JSON configuration examples for each node type, refer to the **fuuz-flows** skill. The node catalog includes complete configuration schemas, example payloads, timeout specifications, and debugging guidance for all 50+ node types.

### GraphQL in flows:
- Queries use camelCase plural for list queries: `batchRecipes { edges { node { id name } } }`
- Queries use camelCase singular for single records: `batchRecipe(id: "...") { id name }`
- Mutations use camelCase: `createBatchRecipe`, `updateBatchRecipe`, `deleteBatchRecipe`
- The GraphQL type name is the PascalCase model name: `BatchRecipe`, `EquipmentUnit`

---

## 19. Reference Files

This skill includes detailed reference files in the `references/` directory.

| File | Content |
|------|---------|
| `references/package-deployment-lifecycle.md` | Package deployment lifecycle: Green/Blue deployment strategy, enterprise tenant architecture (Build/QA/Production/Staging/Integration), package validation and linting (green/yellow/red), full vs partial package creation, industry accelerators (MES, WMS, CMMS, Quality, Integration Broker, Track & Trace), data migration between environments, deployment checklist (10-step), rollback procedures, package JSON directory structure, anti-patterns |
