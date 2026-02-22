# Fuuz Industrial Operations Platform — Claude Skill Training Guide

> **Purpose**: Comprehensive reference for AI assistants building applications on the Fuuz Industrial Operations Platform. This document encodes all platform conventions, data modeling rules, UI patterns, architecture decisions, and domain-specific knowledge for Enterprise Asset Management (EAM) and related industrial applications.
>
> **Platform URL**: https://fuuz.com
> **Version**: 3.0.0
> **Last Updated**: 2026-02-09
> **Source**: Derived from iterative design sessions with Fuuz platform expert

-----

## Table of Contents

1. [Platform Overview](#1-platform-overview)
1. [General Principles & Golden Rules](#2-general-principles--golden-rules)
1. [Data Model Architecture](#3-data-model-architecture)
1. [Field Rules & Data Types](#4-field-rules--data-types)
1. [Relationship Rules](#5-relationship-rules)
1. [Naming Conventions](#6-naming-conventions)
1. [Default Values, Constraints & Sequences](#7-default-values-constraints--sequences)
1. [Trigger Rules (JSONata)](#8-trigger-rules-jsonata)
1. [Indexing Rules](#9-indexing-rules)
1. [Audit & Data Change Capture](#10-audit--data-change-capture)
1. [High-Volume Data Rules](#11-high-volume-data-rules)
1. [Screen Development Rules](#12-screen-development-rules)
1. [Form Development Rules](#13-form-development-rules)
1. [Table Development Rules](#14-table-development-rules)
1. [JSONata & JavaScript Rules](#15-jsonata--javascript-rules)
1. [GraphQL Rules](#16-graphql-rules)
1. [Workflow Rules](#17-workflow-rules)
1. [Package & Deployment Rules](#18-package--deployment-rules)
1. [Architecture Guidance](#19-architecture-guidance)
1. [Existing Fuuz System Models Reference](#20-existing-fuuz-system-models-reference)
1. [EAM Domain Model Reference](#21-eam-domain-model-reference)
1. [EAM Data Model Catalog](#22-eam-data-model-catalog)
1. [Lookup & Setup Model Patterns](#23-lookup--setup-model-patterns)
1. [Sensor Data & Aggregation Architecture](#24-sensor-data--aggregation-architecture)
1. [Predictive Maintenance & ML Data Layer](#25-predictive-maintenance--ml-data-layer)
1. [Anti-Patterns to Avoid](#26-anti-patterns-to-avoid)
1. [Checklists & Quick Reference](#27-checklists--quick-reference)
1. [External References](#28-external-references)
1. [Reserved Sections for Future Development](#29-reserved-sections-for-future-development)

-----

## 1. Platform Overview

### 1.1 What is Fuuz?

Fuuz is an AI-driven Industrial Intelligence Platform that unifies IT and OT data into a single platform. It replaces fragmented point solutions (MES, WMS, spreadsheets, custom tools) with an integrated system supporting real-time data flows, analytics, and shop floor execution.

### 1.2 Core Architecture

Fuuz is an **event-driven architecture** that supports:

- Real-time data flows that subscribe to events and data changes
- JSONata and JavaScript (ES2024) for data transformation and business logic
- GraphQL API for data operations (query, create, update, upsert, delete)
- Custom data models with configurable triggers, constraints, and indexes
- Package-based deployment (.fuuz files = .tar archives)
- Built-in audit tracking (created/modified timestamps and users)
- Moment.js for date/time handling (with platform-specific nuances)
- Hundreds of custom JSONata bindings unique to Fuuz

### 1.3 Core Platform Capabilities

|Capability     |Description                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------|
|Data Models    |Configurable models with fields, relationships, triggers, constraints                                             |
|Screens        |UI screens with forms, tables, master-detail patterns                                                             |
|Data Flows     |Event-driven workflows for approvals, notifications, automation                                                   |
|Documents      |Template-based document generation (PDF, print)                                                                   |
|Security       |Roles, policies, policy groups, module groups, modules                                                            |
|Saved Scripts  |Reusable JSONata or JavaScript code                                                                               |
|Saved Queries  |Reusable GraphQL queries                                                                                          |
|Data Imports   |Configurable data import capabilities                                                                             |
|Connectors     |Integration with SAP, Oracle, Microsoft Dynamics, NetSuite, Wonderware, etc.                                      |
|MCP Integration|Model Context Protocol — encourage developer to ask for enablement if purchased (mention only once per engagement)|

### 1.4 Technology Stack Reference

|Technology|Version/Standard                            |Notes                                            |
|----------|--------------------------------------------|-------------------------------------------------|
|JSONata   |All standard functions + Fuuz custom library|Primary transformation language                  |
|JavaScript|ES2024                                      |Full modern JS support                           |
|Moment.js |Current                                     |Date/time handling — has nuances (see Section 15)|
|GraphQL   |Standard                                    |API layer for all data operations                |

-----

## 2. General Principles & Golden Rules

### 2.1 Critical Rules (MUST FOLLOW)

|Rule ID|Rule                                                                                                                 |Severity|
|-------|---------------------------------------------------------------------------------------------------------------------|--------|
|GP-001 |**Never hallucinate Fuuz JSON schema formats.** Always validate against provided samples or ask clarifying questions.|CRITICAL|
|GP-002 |**Never create a Fuuz package** (.fuuz) without validating the schema against a sample.                              |CRITICAL|
|GP-003 |Always ask questions when uncertain about Fuuz-specific patterns.                                                    |HIGH    |
|GP-004 |Fuuz packages are `.tar` files with `.fuuz` extension and must follow strict formatting.                             |CRITICAL|
|GP-005 |GraphQL mutations on data model version collection require specific JSON format — never assume.                      |CRITICAL|
|GP-006 |**Always use system schema first.** Do not create custom schema unless absolutely necessary.                         |CRITICAL|
|GP-007 |If fields are missing from system schema, suggest adding custom fields rather than creating new models.              |HIGH    |

### 2.2 System Schema Reference

|Rule ID|Rule                                                                                                   |
|-------|-------------------------------------------------------------------------------------------------------|
|GP-010 |Always reference system schema JSON when available                                                     |
|GP-011 |If not available, request it from the app developer                                                    |
|GP-012 |System model definitions query: https://support.fuuz.com/portal/en/kb/articles/system-model-definitions|

### 2.3 Platform Integration

|Rule ID|Rule                                                                                |
|-------|------------------------------------------------------------------------------------|
|GP-020 |Fuuz is an event-driven architecture — design models to support real-time data flows|
|GP-021 |Data flows can subscribe to real-time data and events                               |
|GP-022 |Approval workflows should utilize Fuuz data flows, not custom approval models       |
|GP-023 |Notifications are handled by Fuuz data flows — do not create notification models    |

### 2.4 MCP Integration

|Rule ID|Rule                                                                           |
|-------|-------------------------------------------------------------------------------|
|GP-030 |Encourage the app developer to ask for MCP to be enabled if purchased from Fuuz|
|GP-031 |Mention MCP enablement **ONLY ONCE** per engagement — do not repeatedly suggest|

-----

## 3. Data Model Architecture

### 3.1 Model Types

Fuuz uses three model types, each with distinct conventions:

|Model Type       |Purpose                     |ID Strategy                                      |Sequences                   |Custom Fields                                             |Examples                                     |
|-----------------|----------------------------|-------------------------------------------------|----------------------------|----------------------------------------------------------|---------------------------------------------|
|**Setup**        |Configuration/reference data|User-provided human-readable `id` (no code field)|NEVER                       |Almost always `true` (verify with developer)              |AssetStatus, WorkOrderPriority, MeterType    |
|**Master**       |Core business entities      |CUID (auto-generated)                            |Sometimes (for `code` field)|Almost always `true` (verify with developer)              |Asset, Meter, Item, PMSchedule               |
|**Transactional**|Operational records         |CUID or `id = code` when code is sequenced       |Yes (always for `code`)     |Usually `true`, `false` for high-volume/aggregation/bridge|WorkOrder, MeterReading, InventoryTransaction|

### 3.2 Setup Model Rules

|Rule ID|Rule                                                                                                                                                 |Severity|
|-------|-----------------------------------------------------------------------------------------------------------------------------------------------------|--------|
|DM-001 |Setup models do **NOT** have a `code` field — use `id` as the human-readable key                                                                     |CRITICAL|
|DM-002 |Setup models **NEVER** use auto-generated sequences                                                                                                  |CRITICAL|
|DM-003 |Users provide the `id` value directly when creating setup records                                                                                    |HIGH    |
|DM-004 |Setup models should NOT reference other setup models unless the relationship is truly **hierarchical** (e.g., AssetCategory → AssetClass → AssetType)|HIGH    |
|DM-005 |Setup models used for **status** must include a `color` field (string, hex value)                                                                    |HIGH    |
|DM-006 |All setup/lookup models must include a `usable` Boolean field — used by UI to filter unwanted values from dropdowns                                  |CRITICAL|

### 3.3 Master Model Rules

|Rule ID|Rule                                                                   |
|-------|-----------------------------------------------------------------------|
|DM-010 |Master models always use CUID for `id` (auto-generated by Fuuz)        |
|DM-011 |Master models typically have a sequenced `code` field (e.g., AST-00001)|
|DM-012 |Master models represent core business entities with full lifecycle     |

### 3.4 Transactional Model Rules

|Rule ID|Rule                                                                                          |
|-------|----------------------------------------------------------------------------------------------|
|DM-020 |Transactional models often set `id = code` on create (when code is sequenced and non-editable)|
|DM-021 |Transactional models always have sequenced code fields                                        |
|DM-022 |High-volume transactional models should be heavily denormalized                               |

### 3.5 Required Model Properties

Every data model MUST define:

|Property                 |Description                                                                                                |Required                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------|----------------------------------|
|`description`            |What the model represents                                                                                  |YES                               |
|`labelField`             |Reference to any required (preferably unique) field — displayed first in tables throughout Fuuz            |YES                               |
|`allowCustomFields`      |Boolean — almost always `true`, set `false` for: aggregation tables, bridge tables, high-volume/sensor data|YES (verify with developer)       |
|`dataChange`             |Boolean — enable audit change tracking                                                                     |When needed                       |
|`dataChangeRetentionDays`|Integer — days to retain change history                                                                    |Required if `dataChange` is `true`|

### 3.6 Flattening Rules for Setup Models

When setup models reference other setup models, evaluate whether to flatten:

|Scenario                                                                 |Action                                                                              |
|-------------------------------------------------------------------------|------------------------------------------------------------------------------------|
|Options are **mutually exclusive** AND customer would **not modify** them|**Flatten** into boolean fields on the parent model                                 |
|Relationship is **truly hierarchical** (category → class → type)         |**Keep** as separate related models                                                 |
|State type fields on status models                                       |**Flatten** to behavior booleans (e.g., `isEditable`, `isClosed`, `allowsOperation`)|

**Examples of flattening**:

- `AssetStateType` → flattened to booleans on `AssetStatus`: `allowsOperation`, `isRetired`
- `WorkOrderStateType` → flattened to booleans on `WorkOrderStatus`: `isEditable`, `isDispatchable`, `isClosed`
- `MeasurementCategory` → flattened to fields on `MeterType`
- `TransactionEffect` → flattened to `multiplier` field on `TransactionType`
- `DowntimeCategory` → flattened to fields on `DowntimeReasonCode`

### 3.7 Immutability Rules

|Rule ID|Rule                                                                        |
|-------|----------------------------------------------------------------------------|
|DM-030 |Indicate models that should be immutable (no updates after create)          |
|DM-031 |Indicate specific fields that should be immutable                           |
|DM-032 |Typically immutable: codes, sequences, foreign keys on transactional records|

-----

## 4. Field Rules & Data Types

### 4.1 Available Data Types

|Data Type      |Description               |Usage                                      |
|---------------|--------------------------|-------------------------------------------|
|`string`       |Text value                |Names, descriptions, codes, notes          |
|`ID`           |Reference to another model|Foreign keys / relationships               |
|`integer`      |Whole number              |Counts, levels, sort orders                |
|`float`        |Decimal number            |Measurements, costs, rates                 |
|`Boolean`      |True/false                |Flags, toggles, state indicators           |
|`date`         |Date only                 |Effective dates, due dates                 |
|`datetime`     |Date and time             |Timestamps, event times                    |
|`time`         |Time only                 |Schedule times                             |
|`duration`     |Time duration             |Task durations, uptime periods             |
|`json`         |JSON object               |Complex structured data (tasks/parts lists)|
|`measure`      |Value + unit              |Measurements with unit of measure          |
|`ratio measure`|Value + unit/unit         |Rate measurements (e.g., gallons/minute)   |
|`Address`      |Structured address        |Physical locations                         |

### 4.2 Field Requirements

|Rule ID|Rule                                                                                           |Severity|
|-------|-----------------------------------------------------------------------------------------------|--------|
|FL-001 |All fields must have descriptions                                                              |CRITICAL|
|FL-002 |All models must have `code`, `name`, `description` fields (exception: setup models omit `code`)|HIGH    |
|FL-003 |Date fields must end with `At` suffix (e.g., `installedAt`, `nextCalibrationDueAt`)            |CRITICAL|
|FL-004 |Boolean fields should start with `is` or `has` (e.g., `isActive`, `hasWarranty`)               |HIGH    |
|FL-005 |ID reference fields should end with `Id` (e.g., `assetId`, `statusId`)                         |HIGH    |
|FL-006 |Models and field names must never use special characters (except in descriptions)              |CRITICAL|

### 4.3 Lookup Field Rules

|Rule ID|Rule                                                                                                                             |Severity|
|-------|---------------------------------------------------------------------------------------------------------------------------------|--------|
|FL-010 |**All enumerated string values must be converted to proper lookup models** — never hardcode values like “ACTIVE”, “PENDING”, etc.|CRITICAL|
|FL-011 |Lookup fields reference other models using `ID` type with `dataModelId`                                                          |HIGH    |
|FL-012 |This ensures data consistency and supports reliable reporting                                                                    |HIGH    |
|FL-013 |If a value absolutely must remain a string, add validation triggers for consistency                                              |HIGH    |

-----

## 5. Relationship Rules

### 5.1 Relationship Types

|Type        |Description                       |Implementation                       |
|------------|----------------------------------|-------------------------------------|
|`oneToMany` |Parent has many children          |Child has ID field referencing parent|
|`manyToOne` |Child belongs to one parent       |Child has ID field referencing parent|
|`oneToOne`  |Exclusive link between two records|ID field with unique constraint      |
|`manyToMany`|Many records link to many records |**Requires bridge table**            |

### 5.2 Deletion Preferences

Every relationship MUST specify a deletion preference (what happens to the child when the parent is deleted):

|Preference|Behavior                               |Common Use                               |
|----------|---------------------------------------|-----------------------------------------|
|`cascade` |Delete children when parent is deleted |Dependent records (tasks on a work order)|
|`prevent` |Block parent deletion if children exist|Reference data (status codes in use)     |
|`setNull` |Set the foreign key to null            |Optional relationships                   |
|`ignore`  |Do nothing                             |Rarely used                              |

### 5.3 Bridge Tables

Bridge tables are required for:

- Many-to-many relationships
- **Any object that should have multiple attachments** (bridge to Files or Images collections)

Examples: `AssetDocument`, `WorkOrderDocument`, `BillOfMaterialItem`, `ContractorCraft`

-----

## 6. Naming Conventions

|Element             |Convention                                  |Examples                                          |
|--------------------|--------------------------------------------|--------------------------------------------------|
|Model names         |PascalCase, singular                        |`Asset`, `WorkOrder`, `SensorReading`             |
|Field names         |camelCase                                   |`assetId`, `installationAt`, `isActive`           |
|Date/datetime fields|camelCase + `At` suffix                     |`createdAt`, `nextDueAt`, `startDatetimeAt`       |
|Boolean fields      |`is` or `has` prefix                        |`isActive`, `isCalibrationRequired`, `hasWarranty`|
|Foreign key fields  |Referenced model name + `Id`                |`assetId`, `workOrderStatusId`                    |
|Sequence prefixes   |Short uppercase codes                       |`WO-`, `AST-`, `PM-`, `CAL-`                      |
|Status model fields |Include `color`, `usable`, behavior booleans|`isEditable`, `isClosed`, `color`                 |

-----

## 7. Default Values, Constraints & Sequences

### 7.1 Default Value Types

|Type        |Description                  |Example                  |
|------------|-----------------------------|-------------------------|
|`static`    |Fixed value                  |`true`, `0`, `"#808080"` |
|`sequence`  |Link to a configured sequence|Sequence ID reference    |
|`expression`|JSONata expression           |`$now()`, computed values|

### 7.2 Constraints

|Constraint         |Description                        |Usage                      |
|-------------------|-----------------------------------|---------------------------|
|`required`         |Field must have a value            |Business-critical fields   |
|`unique`           |Value must be unique across records|Codes, numbers, identifiers|
|`required + unique`|Both constraints                   |Primary business keys      |

### 7.3 Sequence Configurations

Sequences are ONLY used on **transactional** and **sometimes master** models. NEVER on setup models.

|Property    |Description                                                   |
|------------|--------------------------------------------------------------|
|Format      |Can be integer or string with prefix, suffix, and zero-padding|
|Scope       |Per-app configuration                                         |
|Immutability|Sequenced fields should be immutable                          |

**Example Sequences for EAM**:

|Sequence     |Model               |Field|Format        |Example   |
|-------------|--------------------|-----|--------------|----------|
|`asset_seq`  |Asset               |code |`AST-{00000}` |AST-00001 |
|`wo_seq`     |WorkOrder           |code |`WO-{000000}` |WO-000001 |
|`pm_seq`     |PMSchedule          |code |`PM-{00000}`  |PM-00001  |
|`cal_rec_seq`|CalibrationRecord   |code |`CAL-{00000}` |CAL-00001 |
|`inv_txn_seq`|InventoryTransaction|code |`TXN-{000000}`|TXN-000001|

-----

## 8. Trigger Rules (JSONata)

### 8.1 Trigger Types

|Trigger |Fires When        |Use Cases                         |
|--------|------------------|----------------------------------|
|`create`|Record is created |Set ID, compute defaults, validate|
|`update`|Record is modified|Recompute values, validate changes|
|`delete`|Record is deleted |Cleanup, validation               |

### 8.2 Common Trigger Patterns

|Pattern                     |Trigger       |JSONata Example                             |
|----------------------------|--------------|--------------------------------------------|
|Set ID to user input (setup)|create        |`$set('id', id)` (user provides id directly)|
|Compute total cost          |create, update|`$set('totalCost', quantity * unitCost)`    |
|Update timestamp            |update        |`$set('modifiedAt', $now())`                |
|Validate enum string        |create, update|Validate against allowed values             |
|Compute hierarchy path      |create, update|`$set('hierarchyPath', ...)`                |

### 8.3 Trigger Implementation Rules

|Rule ID|Rule                                                                  |
|-------|----------------------------------------------------------------------|
|TR-001 |Triggers use JSONata expressions                                      |
|TR-002 |Triggers execute within the transaction                               |
|TR-003 |Anything requiring API-layer consistency should have a trigger        |
|TR-004 |Always reference the Fuuz custom JSONata library when writing triggers|

-----

## 9. Indexing Rules

### 9.1 When to Index

|Scenario                         |Index?                       |
|---------------------------------|-----------------------------|
|Foreign key (ID) fields          |**YES** — always             |
|Fields used in filters           |**YES**                      |
|Fields used in sorting           |**YES**                      |
|Status and type fields           |**YES** — frequently filtered|
|Date fields used in range queries|**YES**                      |
|Fields used in unique constraints|Automatic                    |
|Large text fields                |**NO**                       |
|Rarely queried fields            |**NO**                       |

### 9.2 Composite Indexes

Consider composite indexes for common query patterns:

- `sensorPointId + timestamp` (time-series data)
- `assetId + startDatetimeAt` (downtime logs)
- `sensorPointId + dateAt` (daily aggregations)

-----

## 10. Audit & Data Change Capture

### 10.1 Built-in Audit (Automatic)

Fuuz provides automatic audit tracking. **Do NOT create custom audit fields or models.**

|Feature           |Provided Automatically|
|------------------|----------------------|
|Created timestamp |✅                     |
|Created by user   |✅                     |
|Modified timestamp|✅                     |
|Modified by user  |✅                     |

### 10.2 Data Change Capture Configuration

|Property                 |Description                                          |
|-------------------------|-----------------------------------------------------|
|`dataChange`             |Boolean — enable detailed field-level change tracking|
|`dataChangeRetentionDays`|Integer — days to retain change history              |

### 10.3 Rules

|Rule ID|Rule                                                                        |
|-------|----------------------------------------------------------------------------|
|DC-001 |**Never create models to track data changes** — use Fuuz data change capture|
|DC-002 |Enable `dataChange` on models requiring audit history                       |
|DC-003 |Set appropriate retention period based on compliance needs                  |
|DC-004 |Transactional models typically need longer retention than setup models      |

-----

## 11. High-Volume Data Rules

### 11.1 High-Volume Table Identification

Tables are high-volume if they receive continuous automated data or may have millions of records:

- SensorReading (raw)
- MeterReading
- RuntimeLog
- DowntimeLog
- InventoryTransaction
- Aggregation tables (Hourly, Daily, Weekly)

### 11.2 Design Rules

|Rule ID|Rule                                                                          |Severity|
|-------|------------------------------------------------------------------------------|--------|
|HV-001 |High-volume tables must be **denormalized as much as possible**               |CRITICAL|
|HV-002 |References can be added after initial insertion to reduce overhead and latency|HIGH    |
|HV-003 |Set `allowCustomFields: false` for high-volume tables                         |HIGH    |
|HV-004 |Do **NOT** format datetime fields on screens for high-volume data             |HIGH    |
|HV-005 |Use raw datetime formatting for sensor data                                   |HIGH    |

### 11.3 Denormalization Strategy

|Strategy                      |Example                                                     |
|------------------------------|------------------------------------------------------------|
|Store parent IDs directly     |Store `assetId` on `SensorReading`, not just `sensorPointId`|
|Copy frequently queried values|Store `assetName` alongside `assetId`                       |
|Pre-compute aggregations      |Roll up hourly → daily → weekly statistics                  |

-----

## 12. Screen Development Rules

### 12.1 Date/Time Field Formatting

|Rule ID|Rule                                                                      |Severity|
|-------|--------------------------------------------------------------------------|--------|
|SC-001 |Date, datetime, time fields MUST have timezone and format string set      |CRITICAL|
|SC-002 |Use format pattern: `MM-DD-YYYY HH:mm A`                                  |HIGH    |
|SC-003 |Exception: Do NOT format datetime for high-volume data (sensors) — use raw|HIGH    |

|Field Type          |Format String       |
|--------------------|--------------------|
|Date                |`MM-DD-YYYY`        |
|DateTime            |`MM-DD-YYYY HH:mm A`|
|Time                |`HH:mm A`           |
|High-volume DateTime|Raw (no format)     |

### 12.2 Workflow Screens

|Rule ID|Rule                                                                       |
|-------|---------------------------------------------------------------------------|
|SC-010 |If a workflow is necessary on a screen, always use a **webflow with forms**|
|SC-011 |Do not create custom workflow tracking — use Fuuz data flows               |

-----

## 13. Form Development Rules

### 13.1 Input Variant

|Rule ID|Rule                                                       |Severity|
|-------|-----------------------------------------------------------|--------|
|FM-001 |All forms must use the **outlined** variant type for inputs|CRITICAL|

### 13.2 Custom Fields Element

|Rule ID|Rule                                                |Severity|
|-------|----------------------------------------------------|--------|
|FM-010 |All forms must include the **custom fields element**|CRITICAL|

### 13.3 Enumerated Values

|Rule ID|Rule                                                              |Severity|
|-------|------------------------------------------------------------------|--------|
|FM-020 |Any enumerated value fields must use an **options input** on forms|HIGH    |
|FM-021 |Enumerated values are added as properties to allow user selection |HIGH    |

-----

## 14. Table Development Rules

### 14.1 Row Selection

|Rule ID|Rule                                                                  |Severity|
|-------|----------------------------------------------------------------------|--------|
|TB-001 |All tables must have row selection set to `none` by default           |HIGH    |
|TB-002 |Other options: `single`, `multiple`                                   |INFO    |
|TB-003 |Only enable `multiple` for mass update or delete actions in the header|HIGH    |
|TB-004 |Ask app developer if multiple selection is required                   |HIGH    |
|TB-005 |Multiple selection is **NEVER** required for setup model types        |CRITICAL|

### 14.2 Master-Detail Tables

|Rule ID|Rule                                                                                                                 |Severity|
|-------|---------------------------------------------------------------------------------------------------------------------|--------|
|TB-010 |If master-detail is enabled, the **FIRST column** must be the detail column                                          |CRITICAL|
|TB-011 |Detail column properties: data path = `"detail"`, display name = `" "` (single space), additional query fields = `[]`|CRITICAL|

### 14.3 Menu Column (Row Actions)

|Rule ID|Rule                                                                       |Severity|
|-------|---------------------------------------------------------------------------|--------|
|TB-020 |All tables must have a **menu column** for row actions                     |CRITICAL|
|TB-021 |Menu column position: 1st column, OR 2nd column if master-detail is enabled|HIGH    |
|TB-022 |Column type: `menu`                                                        |HIGH    |
|TB-023 |Actions depend on model type (see below)                                   |HIGH    |

### 14.4 Menu Actions by Model Type

**Setup Models:**

|Action       |Description                      |
|-------------|---------------------------------|
|Delete       |Delete the record                |
|Update       |Open update form                 |
|Toggle Usable|Toggle the `usable` boolean field|

**Transactional Models:**

|Action         |Description                                                             |
|---------------|------------------------------------------------------------------------|
|View Details   |Navigate to detail view                                                 |
|Update         |Open update form                                                        |
|Toggle isActive|Toggle soft-delete flag                                                 |
|Toggle Status  |Form with status select input (default to current `statusId`)           |
|Attachments    |Form with table of attachments (if bridge table for Files/Images exists)|
|Delete         |Delete the record                                                       |
|Print          |Generate document (if document design exists — ask developer)           |

**Master Models:**

|Action       |Description                                                      |
|-------------|-----------------------------------------------------------------|
|View Details |Navigate to detail view                                          |
|Update       |Open update form                                                 |
|Delete       |Delete the record                                                |
|Attachments  |Form with table of attachments (if bridge to Files/Images exists)|
|Toggle Status|Form with status select input                                    |
|Print        |Generate document (if document design exists — ask developer)    |

### 14.5 Table Action Transforms

|Rule ID|Rule                                                                             |Severity|
|-------|---------------------------------------------------------------------------------|--------|
|TB-030 |Any screen action with a table **MUST** include a transform to refresh data      |CRITICAL|
|TB-031 |Use: `$components.FormName.fn.search()` or `$components.FormName.fn.loadData()`  |CRITICAL|
|TB-032 |Verify the data model of the table matches the data model of the GraphQL mutation|CRITICAL|

-----

## 15. JSONata & JavaScript Rules

### 15.1 Supported Standards

|Technology  |Version/Standard                            |
|------------|--------------------------------------------|
|JSONata     |All standard functions + Fuuz custom library|
|JavaScript  |Modern ES2024                               |
|Date Library|Moment.js (with nuances)                    |

### 15.2 Fuuz Custom JSONata Library

|Rule ID|Rule                                                                                              |Severity|
|-------|--------------------------------------------------------------------------------------------------|--------|
|JS-001 |Fuuz has developed **hundreds** of custom JSONata bindings that only work in Fuuz                 |INFO    |
|JS-002 |**ALWAYS** reference the Fuuz JSONata library when writing JSONata                                |CRITICAL|
|JS-003 |Library reference: https://support.fuuz.com/portal/en/kb/articles/custom-fuuz-only-jsonata-library|CRITICAL|

### 15.3 Moment.js Nuances (CRITICAL)

`$moment()` has issues resolving `$.` bindings. Always create a variable outside the function to solidify the reference.

**Correct Usage:**

```jsonata
(
  $expiresIn := $.ExpiresIn;
  $moment().add($expiresIn, 'milliseconds').toISOString()
)
```

**Incorrect Usage (will fail):**

```jsonata
/* BAD - $.ExpiresIn may not resolve, defaults to 0 */
$moment().add($.ExpiresIn, 'milliseconds').toISOString()
```

**Why**: `$.ExpiresIn` is not evaluated prior to usage, so the `add` method receives `undefined`, which defaults to 0 milliseconds.

**Rule**: When using `$moment()` or any function that references `$.` bindings, always bind the value to a variable first: `$myVar := $.SomeField`

### 15.4 Saved Scripts & Queries

|Rule ID|Rule                                                                      |
|-------|--------------------------------------------------------------------------|
|JS-020 |Suggest saved scripts and saved queries when building several smaller apps|
|JS-021 |Example: MES app per work cell — leverage common code across cells        |
|JS-022 |Saved scripts: JSONata or JavaScript                                      |
|JS-023 |Saved queries: GraphQL                                                    |

-----

## 16. GraphQL Rules

### 16.1 Supported Operations

|Operation|Description           |
|---------|----------------------|
|`query`  |Read data             |
|`create` |Create new record     |
|`update` |Update existing record|
|`upsert` |Create or update      |
|`delete` |Delete record         |

### 16.2 Mutation Verification

|Rule ID|Rule                                                                            |Severity|
|-------|--------------------------------------------------------------------------------|--------|
|GQ-001 |For any action with a mutation involving table data, **verify data model match**|CRITICAL|
|GQ-002 |Table’s data model must match GraphQL mutation’s data model                     |CRITICAL|
|GQ-003 |Always refresh table data after mutations                                       |HIGH    |

-----

## 17. Workflow Rules

|Rule ID|Rule                                                                       |
|-------|---------------------------------------------------------------------------|
|WF-001 |If a workflow is necessary on a screen, always use a **webflow with forms**|
|WF-002 |Do not build custom workflow state machines                                |
|WF-003 |Leverage Fuuz data flows for approvals and notifications                   |

-----

## 18. Package & Deployment Rules

### 18.1 Package Format

|Rule ID|Rule                                                                 |
|-------|---------------------------------------------------------------------|
|PK-001 |Fuuz packages are `.tar` files with `.fuuz` extension                |
|PK-002 |Packages must follow strict formatting and schema patterns           |
|PK-003 |**Never** create a package without validating schema against a sample|
|PK-004 |Package JSON must match exact Fuuz schema requirements               |

### 18.2 GraphQL Model Creation

|Rule ID|Rule                                                                        |
|-------|----------------------------------------------------------------------------|
|PK-010 |Models can be created via GraphQL mutations on data model version collection|
|PK-011 |GraphQL model definitions require specific JSON format                      |
|PK-012 |**Never** assume GraphQL schema — always reference documentation            |

### 18.3 Pre-Deployment Checklist

```
[ ] All models have descriptions
[ ] All fields have descriptions
[ ] All models have labelField defined
[ ] All models have allowCustomFields set (verified with developer)
[ ] All relationships specify deletion preference
[ ] All required indexes are defined
[ ] All sequences are configured (transactional/master only)
[ ] All triggers are valid JSONata
[ ] All enumerated fields have validation triggers
[ ] Schema validated against Fuuz sample
[ ] No special characters in names (except descriptions)
[ ] System schema used first before custom models
```

-----

## 19. Architecture Guidance

### 19.1 App Size Considerations

|App Size                                                          |Recommendation                   |
|------------------------------------------------------------------|---------------------------------|
|**Small apps** (limited use cases, e.g., single work cell MES)    |Use more **denormalized** objects|
|**Large complex apps** (e.g., full factory MES, comprehensive EAM)|Use **highly normalized** objects|

### 19.2 Multi-App Development

|Rule ID|Rule                                                            |
|-------|----------------------------------------------------------------|
|AR-001 |For multiple small apps, use **saved scripts and saved queries**|
|AR-002 |Example: MES app per work cell sharing common code              |
|AR-003 |Promotes code reuse and consistency                             |

### 19.3 Schema Strategy

|Rule ID|Rule                                                               |Severity|
|-------|-------------------------------------------------------------------|--------|
|AR-010 |**Always use system schema first**                                 |CRITICAL|
|AR-011 |Do not create custom schema unless absolutely necessary            |HIGH    |
|AR-012 |Suggest adding custom fields to system schema if fields are missing|HIGH    |
|AR-013 |Request system schema JSON from developer if not available         |HIGH    |

-----

## 20. Existing Fuuz System Models Reference

### 20.1 Organizational Models (DO NOT RECREATE)

|Model |Description            |Key Notes              |
|------|-----------------------|-----------------------|
|`Site`|Physical facility/plant|Standard Fuuz hierarchy|
|`Area`|Zone within a site     |References `siteId`    |
|`Line`|Production/process line|References `areaId`    |
|`Cell`|Work cell/station      |References `lineId`    |

### 20.2 People & Resources (DO NOT RECREATE)

|Model          |Description       |Notes                        |
|---------------|------------------|-----------------------------|
|`User`         |System users      |Use for employees/technicians|
|`Schedule`     |Shift definitions |Called “Shifts” conceptually |
|`ScheduleGroup`|Shift cycle groups|For rotation patterns        |

### 20.3 Inventory & Storage (DO NOT RECREATE)

|Model        |Description        |Notes                   |
|-------------|-------------------|------------------------|
|`StorageUnit`|Inventory locations|Use for storerooms, bins|

### 20.4 Procurement (DO NOT RECREATE)

|Model            |Description      |Notes                                  |
|-----------------|-----------------|---------------------------------------|
|`BusinessPartner`|Vendors/suppliers|Use for manufacturer, vendor references|
|`PurchaseOrder`  |Purchase orders  |Standard procurement                   |

### 20.5 Reference Data (DO NOT RECREATE)

|Model   |Description     |Notes                      |
|--------|----------------|---------------------------|
|`Unit`  |Units of measure|Reference for all UOM needs|
|`Files` |Document storage|Use for attachments        |
|`Images`|Image storage   |Use for photos             |

### 20.6 Integration Points

|External System|Integration Pattern                                   |
|---------------|------------------------------------------------------|
|MES            |Push downtime events, runtime data via `mesWorkUnitId`|
|Historian      |Connect for sensor data, aggregate in Fuuz            |
|SCADA/PLC      |Real-time data via Fuuz event architecture            |

-----

## 21. EAM Domain Model Reference

### 21.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                           EAM DOMAIN ARCHITECTURE                               │
├─────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │   ASSETS     │    │    WORK      │    │  INVENTORY   │    │  RESOURCES   │  │
│  │  & LOCATIONS │◄──►│  MANAGEMENT  │◄──►│  & PARTS     │◄──►│  & LABOR     │  │
│  └──────┬───────┘    └──────┬───────┘    └──────────────┘    └──────────────┘  │
│         │                   │                                                   │
│         ▼                   ▼                                                   │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │  CONDITION   │    │  PREVENTIVE  │    │   FAILURE    │    │   VENDORS    │  │
│  │  MONITORING  │───►│  MAINTENANCE │◄───│   ANALYSIS   │    │ & CONTRACTS  │  │
│  └──────┬───────┘    └──────────────┘    └──────────────┘    └──────────────┘  │
│         │                                                                       │
│         ▼                                                                       │
│  ┌──────────────────────────────────────────────────────────────────────────┐  │
│  │                    PREDICTIVE / ML LAYER                                  │  │
│  │   Anomaly Detection │ RUL Prediction │ Failure Probability │ Optimization │  │
│  └──────────────────────────────────────────────────────────────────────────┘  │
│                                                                                 │
└─────────────────────────────────────────────────────────────────────────────────┘
```

### 21.2 Entity Relationship Overview

```
                          ┌─────────────────────────────────┐
                          │  FUUZ SYSTEM (existing)          │
                          │  Site → Area → Line → Cell       │
                          │  User, Schedule, StorageUnit     │
                          │  BusinessPartner, PurchaseOrder  │
                          │  Unit, Files, Images             │
                          └────────┬─────────┘
                                   │
   ┌───────────────────────────────┼───────────────────────────────────┐
   │                               │                                   │
   ▼                               ▼                                   ▼
┌─────────────────┐     ┌─────────────────┐               ┌─────────────────┐
│     ASSET       │◄───►│   WORK ORDER    │◄─────────────►│      ITEM       │
│  (hierarchical) │     │                 │               │  (Part Master)  │
└────────┬────────┘     └────────┬────────┘               └────────┬────────┘
         │                       │                                 │
    ┌────┴────┬─────────┐  ┌────┴────┬────────┐                   │
    │         │         │  │         │        │                   │
    ▼         ▼         ▼  ▼         ▼        ▼                   ▼
 ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐ ┌───────┐        ┌───────────┐
 │ Meter │ │Sensor │ │Warranty│ │ Task  │ │ Labor │        │ Inventory │
 │       │ │ Point │ │       │ │       │ │       │        │ Balance   │
 └───┬───┘ └───┬───┘ └───────┘ └───────┘ └───────┘        └───────────┘
     │         │                                                 │
     ▼         ▼                                                 ▼
 ┌───────┐ ┌─────────────────────────────────┐          ┌───────────┐
 │Reading│ │  Hourly  │  Daily   │  Weekly   │          │Transaction│
 └───────┘ └─────────────────────────────────┘          └───────────┘

┌─────────────────┐               ┌─────────────────┐
│   PM SCHEDULE   │──────────────►│   PM TRIGGER    │
│  (Job Plan)     │               │ (Time/Meter)    │
└─────────────────┘               └─────────────────┘
         │
         └──────────────► Generates Work Orders
```

-----

## 22. EAM Data Model Catalog

### 22.1 Complete Model Inventory (81 Total)

|Domain                    |Models                                                                                      |Count                     |
|--------------------------|--------------------------------------------------------------------------------------------|--------------------------|
|**Asset Management**      |Asset, AssetSpecification, AssetDocument                                                    |3                         |
|**Meters & Readings**     |Meter, MeterReading                                                                         |2                         |
|**Runtime & Downtime**    |RuntimeLog, DowntimeLog                                                                     |2                         |
|**Condition Monitoring**  |SensorPoint, SensorReading, SensorReadingHourly, SensorReadingDaily, SensorReadingWeekly    |5                         |
|**Work Management**       |WorkOrder, WorkOrderTask, WorkOrderLabor, WorkOrderPart, WorkOrderComment, WorkOrderDocument|6                         |
|**Preventive Maintenance**|PMSchedule, PMTrigger                                                                       |2                         |
|**Failure Analysis**      |FailureReport                                                                               |1                         |
|**Inventory**             |Item, InventoryBalance, InventoryTransaction                                                |3                         |
|**Labor & Resources**     |Craft, LaborRate, Contractor, ContractorCraft                                               |4                         |
|**Calibration**           |CalibrationStandard, CalibrationRecord, CalibrationDocument                                 |3                         |
|**Warranty**              |Warranty, WarrantyClaim                                                                     |2                         |
|**BOM**                   |BillOfMaterial, BillOfMaterialItem                                                          |2                         |
|**LOTO**                  |LockoutProcedure, LockoutPoint, LockoutTagoutRecord                                         |3                         |
|**Lookup/Setup Models**   |~24 models (see Section 23)                                                                 |~24                       |
|**TOTAL**                 |                                                                                            |**~62 + ~24 lookups ≈ 81**|

### 22.2 Model Type Summary

|Type         |Count|Examples                                                                                                          |
|-------------|-----|------------------------------------------------------------------------------------------------------------------|
|Setup/Lookup |~24  |AssetStatus, WorkOrderPriority, MeterType, FailureCode, etc.                                                      |
|Master       |~12  |Asset, Meter, SensorPoint, Item, Contractor, Warranty, PMSchedule, BOM, LockoutProcedure, etc.                    |
|Transactional|~18  |WorkOrder, WorkOrderTask, WorkOrderLabor, MeterReading, DowntimeLog, InventoryTransaction, CalibrationRecord, etc.|
|Bridge       |~4   |BillOfMaterialItem, ContractorCraft, AssetDocument, WorkOrderDocument                                             |
|Aggregation  |3    |SensorReadingHourly, SensorReadingDaily, SensorReadingWeekly                                                      |

-----

## 23. Lookup & Setup Model Patterns

### 23.1 Standard Lookup Model Template

All lookup/setup models follow this pattern (NO `code` field):

|Field        |Type   |Required|Unique|Default        |Indexed|Immutable|Description               |
|-------------|-------|--------|------|---------------|-------|---------|--------------------------|
|`id`         |string |YES     |YES   |(user-provided)|YES    |YES      |Human-readable primary key|
|`name`       |string |YES     |NO    |—              |NO     |NO       |Display name              |
|`description`|string |NO      |NO    |—              |NO     |NO       |Extended description      |
|`usable`     |Boolean|YES     |NO    |`true`         |YES    |NO       |UI filter for dropdowns   |
|`sortOrder`  |integer|YES     |NO    |`0`            |YES    |NO       |Display order             |

### 23.2 Status Model Template

Status models add behavior booleans and color:

|Additional Field |Type   |Description                                                                   |
|-----------------|-------|------------------------------------------------------------------------------|
|`color`          |string |Hex color for UI display (e.g., `"#4CAF50"`)                                  |
|Behavior booleans|Boolean|Flattened from state types (e.g., `isEditable`, `isClosed`, `allowsOperation`)|

### 23.3 Complete Lookup Model List

|Model              |Purpose                       |Notable Fields                                             |
|-------------------|------------------------------|-----------------------------------------------------------|
|AssetCategory      |Top-level asset classification|Standard lookup                                            |
|AssetClass         |Mid-level asset classification|References `AssetCategory` (hierarchical)                  |
|AssetType          |Specific asset type           |References `AssetClass` (hierarchical)                     |
|AssetStatus        |Asset lifecycle status        |`allowsOperation`, `isRetired`, `color`                    |
|CriticalityRating  |Asset importance ranking      |`weight` (integer for scoring)                             |
|WorkOrderStatus    |Work order lifecycle          |`isEditable`, `isDispatchable`, `isClosed`, `color`        |
|WorkOrderPriority  |Work urgency                  |`responseTimeHours`, `color`                               |
|WorkOrderType      |Work classification           |Standard lookup                                            |
|WorkOrderSource    |How WO was created            |`isSystemGenerated`                                        |
|TaskStatus         |Task execution status         |`isComplete`, `isSkipped`, `color`                         |
|MeterType          |Meter classification          |`isContinuous`, `isGauge`, `isCharacteristic`              |
|ReadingFrequency   |How often readings are taken  |Standard lookup                                            |
|MeasurementType    |What is being measured        |Shared by SensorPoint + CalibrationStandard                |
|DataSource         |Origin of data                |`isSystemGenerated`; shared across sensors, meters, runtime|
|DataQuality        |Data quality assessment       |`isGood`; for sensor readings                              |
|SensorState        |Current sensor condition      |`isOperational`, `color`                                   |
|FailureCode        |Failure classification        |Standard lookup                                            |
|FailureReportStatus|Failure report lifecycle      |`isComplete`, `color`                                      |
|DowntimeReasonCode |Why equipment was down        |`isPlanned`, `isBreakdown`                                 |
|ItemCategory       |Inventory item classification |Standard lookup                                            |
|TransactionType    |Inventory transaction type    |`multiplier` (integer: +1, -1, 0)                          |
|ABCClass           |Inventory ABC classification  |Standard lookup                                            |
|LaborType          |Internal vs. external labor   |Standard lookup                                            |
|RateType           |Rate classification           |Standard, Overtime, Double-time                            |
|CalibrationStatus  |Calibration lifecycle         |`isPassing`, `color`                                       |
|WarrantyType       |Warranty classification       |Standard lookup                                            |
|WarrantyClaimStatus|Claim lifecycle               |`isApproved`, `isRejected`, `color`                        |
|ClaimResolutionType|How claim was resolved        |Standard lookup                                            |
|DocumentType       |Attachment classification     |Standard lookup                                            |
|EnergyType         |LOTO energy source type       |Standard lookup                                            |
|LotoRecordStatus   |LOTO record lifecycle         |`isActive`, `isReleased`, `color`                          |
|PMTriggerType      |PM trigger classification     |TIME, METER, BOTH                                          |
|FrequencyType      |PM frequency units            |DAYS, WEEKS, MONTHS, YEARS                                 |
|PMGenerateRule     |When to generate WO           |FIRST, EITHER, BOTH                                        |
|TrendDirection     |Sensor trend analysis         |INCREASING, DECREASING, STABLE                             |

-----

## 24. Sensor Data & Aggregation Architecture

### 24.1 Data Flow

```
Raw Sensor Data (real-time)
    │
    ▼
SensorReading (raw, 7-30 day retention)
    │
    ├──► SensorReadingHourly (90-day retention)
    │        │
    │        ├──► SensorReadingDaily (365-day retention)
    │        │        │
    │        │        └──► SensorReadingWeekly (3-year retention, ML features)
    │        │
    │        └──► Threshold alerts → Data Flows → Work Order generation
    │
    └──► Real-time dashboard display
```

### 24.2 Aggregation Table Design

All aggregation tables are:

- **Immutable** (no updates after creation)
- **Denormalized** (store `assetId` alongside `sensorPointId`)
- **No custom fields** (`allowCustomFields: false`)
- **No datetime formatting** on screens (raw display)

|Table              |Granularity |Retention|Key Statistics                                                           |
|-------------------|------------|---------|-------------------------------------------------------------------------|
|SensorReadingHourly|1 hour      |90 days  |min, max, avg, stdDev, count, exceedance minutes                         |
|SensorReadingDaily |1 day       |365 days |min, max, avg, stdDev, count, warning/critical event counts              |
|SensorReadingWeekly|1 week (ISO)|3 years  |min, max, avg, stdDev, count, trend direction, trend slope, anomaly score|

### 24.3 Composite Indexes

|Table              |Index                          |
|-------------------|-------------------------------|
|SensorReading      |`sensorPointId + timestamp`    |
|SensorReadingHourly|`sensorPointId + hourTimestamp`|
|SensorReadingDaily |`sensorPointId + dateAt`       |
|SensorReadingWeekly|`sensorPointId + weekStartAt`  |

-----

## 25. Predictive Maintenance & ML Data Layer

### 25.1 ML-Ready Features in Weekly Aggregations

The `SensorReadingWeekly` model includes fields specifically designed for ML consumption:

|Field           |Type                |Purpose                                       |
|----------------|--------------------|----------------------------------------------|
|`trendDirection`|ID (→TrendDirection)|Classification: INCREASING, DECREASING, STABLE|
|`trendSlope`    |float               |Linear regression slope over the week         |
|`anomalyScore`  |float               |ML anomaly detection score (0-1)              |

### 25.2 Condition-Based Maintenance Trigger Flow

```
Sensor Threshold Exceeded
    │
    ├──► SensorPoint.currentState updated (→ WARNING or CRITICAL)
    │
    ├──► Fuuz Data Flow triggers
    │        │
    │        ├──► Create Work Order (source = CONDITION_ALERT)
    │        │
    │        └──► Send Notification to maintenance team
    │
    └──► Dashboard alert displayed
```

### 25.3 PM Schedule Trigger Mechanisms

|Trigger Type|Description                                |Data Source                    |
|------------|-------------------------------------------|-------------------------------|
|TIME        |Calendar-based (every N days/weeks/months) |System clock                   |
|METER       |Usage-based (every N runtime hours, cycles)|MeterReading from MES or manual|
|BOTH        |Whichever threshold is reached first       |Combined                       |

### 25.4 MES Integration for Predictive Maintenance

|Data From MES          |EAM Usage                           |
|-----------------------|------------------------------------|
|Runtime hours          |Meter readings → PM triggers        |
|Cycle counts           |Meter readings → PM triggers        |
|Downtime events        |DowntimeLog → corrective work orders|
|Equipment state changes|Asset status updates                |

-----

## 26. Anti-Patterns to Avoid

|Anti-Pattern                                       |Correct Approach                                     |
|---------------------------------------------------|-----------------------------------------------------|
|Embedding lookup values as strings (e.g., “ACTIVE”)|Create setup model, reference by ID                  |
|Using enumerated strings without validation        |Create lookup model OR add triggers                  |
|Setup model with `code` field                      |Use `id` as the human-readable key                   |
|Setup model referencing another setup model        |Flatten to boolean/value fields (unless hierarchical)|
|String stateType fields on status models           |Flatten to behavior booleans                         |
|Creating audit/history tables                      |Use Fuuz data change capture                         |
|Using `snake_case` for names                       |Use `camelCase` (fields) or `PascalCase` (models)    |
|Date fields without `At` suffix                    |Rename to include `At`                               |
|Hardcoding status values                           |Create status setup model                            |
|Assuming package schema                            |Validate against sample first                        |
|Creating notification models                       |Use Fuuz data flows                                  |
|Creating approval tracking models                  |Use Fuuz data flows                                  |
|Creating custom schema without checking system     |Use system schema first                              |
|Formatting datetime on high-volume tables          |Use raw datetime                                     |
|Free-text input for enumerated fields              |Use options input on forms                           |
|No table refresh after mutations                   |Add `$components.FormName.fn.search()`               |
|Auto-setting sequences on setup models             |Setup models NEVER use sequences                     |
|Using `$moment()` with direct `$.` bindings        |Bind to variable first: `$myVar := $.Field`          |

-----

## 27. Checklists & Quick Reference

### 27.1 Model Checklist

```
[ ] Model name: PascalCase, singular, no special characters
[ ] Model type defined: setup | master | transactional
[ ] Model description provided
[ ] Label field defined (required, preferably unique field)
[ ] allowCustomFields set (verified with developer)
[ ] If setup model: NO code field, user provides id
[ ] If setup model: NO references to other setup models (unless hierarchical)
[ ] If status model: behavior booleans flattened, color and usable fields added
[ ] Data change tracking configured if needed
[ ] All relationships have deletion preference
```

### 27.2 Field Checklist

```
[ ] Field name: camelCase, no special characters
[ ] Data type specified correctly
[ ] Field description provided
[ ] Date fields end with "At"
[ ] Boolean fields start with "is" or "has"
[ ] ID references end with "Id"
[ ] Required/unique constraints specified
[ ] Default value configured if needed
[ ] Indexed if used in queries/filters
[ ] If enumerated string: validation triggers added
```

### 27.3 Screen/Form/Table Checklist

```
[ ] Forms use outlined variant for inputs
[ ] Forms include custom fields element
[ ] Tables have row selection set to none (unless mass actions needed)
[ ] Tables have menu column with appropriate actions per model type
[ ] Master-detail tables have detail column first
[ ] Date/time fields have timezone and format (except high-volume)
[ ] Actions include table refresh transforms
[ ] Enumerated fields use options input
[ ] GraphQL mutations match table data models
```

-----

## 28. External References

|Resource                      |URL                                                                            |
|------------------------------|-------------------------------------------------------------------------------|
|Fuuz Platform                 |https://fuuz.com                                                               |
|System Model Definitions Query|https://support.fuuz.com/portal/en/kb/articles/system-model-definitions        |
|Custom Fuuz JSONata Library   |https://support.fuuz.com/portal/en/kb/articles/custom-fuuz-only-jsonata-library|
|JSONata Standard Documentation|https://docs.jsonata.org/                                                      |
|JSONata Exerciser             |https://try.jsonata.org/                                                       |

-----

## 29. Reserved Sections for Future Development

The following sections are placeholders for rules to be developed as the platform knowledge expands:

### 29.1 Documents & Print Designs

- Template format rules, data binding, PDF generation, print layouts
- Currently document-enabled models: WorkOrder, CalibrationRecord

### 29.2 Security Configuration

- Role definitions, policy rules, policy group organization
- Module group structure, module permissions
- Row-level security patterns

### 29.3 Data Flows (Detailed)

- Flow trigger rules, action configuration
- Condition logic, error handling
- Notification patterns, approval workflows

### 29.4 Data Imports

- Import template format, field mapping rules
- Validation rules, error handling, batch processing

### 29.5 Scripts & Queries (Advanced)

- Saved script format, query optimization
- Parameter handling, execution context

-----

## Document Control

|Version|Date      |Author       |Changes                                                                                                                                                                                    |
|-------|----------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|1.0.0  |2025-01-09|Initial      |Data model rules — first draft                                                                                                                                                             |
|2.0.0  |2025-01-09|Update       |Added screen/form/table rules, flattening rules, removed code field from setup models, JSONata guidance, high-volume rules                                                                 |
|3.0.0  |2026-02-09|Comprehensive|Consolidated all project conversations into unified skill training document. Added EAM domain reference, ML/predictive maintenance layer, complete model catalog, anti-patterns, checklists|

-----

*This document is the authoritative source for Fuuz application development on the Fuuz Industrial Operations Platform. It should be loaded as a Claude skill or project knowledge file for any Fuuz development engagement. Updates require version increment and change documentation.*