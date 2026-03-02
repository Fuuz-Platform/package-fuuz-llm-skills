# Fuuz Seed Data Patterns Reference

The `data` array in `package-data.json` contains seed and reference records that are pre-populated when a package is imported into a Fuuz environment. These records establish the foundational configuration — prerequisites, lookup values, default settings, and reference data — that models and flows depend on at runtime.

---

## Data Array Entry Structure

Each entry in the `data` array represents one model type and its seed records:

```json
{
  "api": "application",
  "modelName": "ModelName",
  "payload": [
    { "record1": "..." },
    { "record2": "..." }
  ],
  "metadata": { "insertEarly": true }
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `api` | String | Yes | Always `"application"` for custom data models. |
| `modelName` | String | Yes | PascalCase model name exactly matching the model definition (e.g., `"AlarmState"`, `"UnitType"`). |
| `payload` | Array | Yes | Array of record objects to insert. Each object contains the field values for one record. |
| `metadata` | Object | No | Import behavior flags. Use `{ "insertEarly": true }` for prerequisite records only. |

---

## Ordering Rules

**CRITICAL:** The `data` array MUST be ordered by dependency. Records that are referenced by other records must appear first. The platform processes entries sequentially — a record that references a foreign key to another model will fail if the target record has not been inserted yet.

### Standard Ordering (from MachineTelemetryApp)

| Order | Category | Model Names | `insertEarly` |
|-------|----------|-------------|----------------|
| 1 | Prerequisites | `ModuleGroup` | Yes |
| 2 | Prerequisites | `Module` | Yes |
| 3 | Prerequisites | `Sequence` | Yes |
| 4 | Setup / Reference | `AlarmState`, `ScheduleType`, `ScheduleGroup` | No |
| 5 | Lookup Data | `Mode`, `State`, `EventCategory`, `Event` | No |
| 6 | Schedule | `Schedule`, `ScheduleEvent` | No |
| 7 | Settings | `Setting` | No |
| 8 | Access Control | `Role`, `AccessControlPolicyGroup` | No |
| 9 | Units | `UnitType`, `Unit` | No |
| 10 | Device | `DeviceDriverType`, `DeviceDriver` | No |
| 11 | Master Data | `Workcenter`, `Product`, `Site`, `Area`, `Line`, `Cell`, `Asset`, `DataPoint` | No |
| 12 | Transactional Data | `WorkOrder`, `OeeDaily`, `OeeHourly`, `ProductionLog`, `WorkcenterHistory` | No |
| 13 | Schedule Exceptions | `ScheduleGroupExceptionEvent` | No |
| 14 | Flow Schedules | `DataFlowSchedule`, `DataFlowScheduleFrequency` | No |

**Rules:**
- Prerequisites (ModuleGroup, Module, Sequence) are always positions 1-3 with `insertEarly: true`
- Setup/reference and lookup models come before master data that references them via FK
- Master data comes before transactional data that references it
- `UnitType` must come before `Unit` (Unit references UnitType)
- `DataFlowSchedule` must come before `DataFlowScheduleFrequency` (frequency references schedule)
- `DataFlowSchedule` and `DataFlowScheduleFrequency` are always LAST in the data array

---

## insertEarly Metadata

The `insertEarly` flag tells the platform to process these records before all other data entries, regardless of array position. It is used exclusively for the three prerequisite types.

```json
"metadata": { "insertEarly": true }
```

| Model | insertEarly | Reason |
|-------|-------------|--------|
| `ModuleGroup` | Yes | Must exist before Modules can reference it |
| `Module` | Yes | Must exist before data models reference it via `moduleId` |
| `Sequence` | Yes | Must exist before model fields auto-generate sequence values |
| All other models | No (omit metadata or use `{}`) | Processed in standard array order |

**IMPORTANT:** Only ModuleGroup, Module, and Sequence use `insertEarly: true`. All other seed data entries either omit the `metadata` field entirely or use an empty object. Applying `insertEarly` to non-prerequisite models can cause unpredictable import ordering.

---

## Record Format by Category

### Prerequisites

These records use `insertEarly: true` and establish the organizational structure for the package.

**ModuleGroup:**

```json
{
  "api": "application",
  "modelName": "ModuleGroup",
  "payload": [
    {
      "id": "machineTelemetrySiteApplication",
      "name": "Machine Telemetry Site Application",
      "icon": "g",
      "description": "Manufacturing telemetry.",
      "_customFields": { "_externalId": null }
    }
  ],
  "metadata": { "insertEarly": true }
}
```

**Module:**

```json
{
  "api": "application",
  "modelName": "Module",
  "payload": [
    {
      "id": "machineDataApp",
      "name": "Machine Data App",
      "description": "Core data models.",
      "moduleGroupId": "machineTelemetrySiteApplication",
      "_customFields": { "_externalId": null }
    }
  ],
  "metadata": { "insertEarly": true }
}
```

**Sequence:**

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
    }
  ],
  "metadata": { "insertEarly": true }
}
```

See `references/prerequisites.md` for full prerequisite documentation.

---

### Setup / Reference Data

Lookup tables and configuration values that other models reference via foreign keys. These records use meaningful, human-readable IDs.

**AlarmState** (workflow states):

```json
{
  "api": "application",
  "modelName": "AlarmState",
  "payload": [
    {
      "id": "ACTIVE",
      "code": "ACTIVE",
      "name": "Active",
      "description": "Alarm triggered, not yet acknowledged.",
      "_customFields": { "_externalId": null }
    },
    {
      "id": "ACKNOWLEDGED",
      "code": "ACKNOWLEDGED",
      "name": "Acknowledged",
      "description": "Alarm acknowledged by operator.",
      "_customFields": { "_externalId": null }
    },
    {
      "id": "CLEARED",
      "code": "CLEARED",
      "name": "Cleared",
      "description": "Alarm condition resolved.",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**ScheduleType:**

```json
{
  "api": "application",
  "modelName": "ScheduleType",
  "payload": [
    {
      "id": "cron",
      "name": "Cron",
      "description": "Cron-based schedule.",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**ScheduleGroup:**

```json
{
  "api": "application",
  "modelName": "ScheduleGroup",
  "payload": [
    {
      "id": "basic",
      "name": "Basic",
      "description": "Standard shift schedule group.",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**Schedule** (references ScheduleGroup):

```json
{
  "api": "application",
  "modelName": "Schedule",
  "payload": [
    {
      "id": "first",
      "code": "First",
      "sortOrder": null,
      "name": "1st Shift",
      "description": null,
      "scheduleGroupId": "basic",
      "_customFields": { "_externalId": null }
    },
    {
      "id": "second",
      "code": "Second",
      "sortOrder": null,
      "name": "2nd Shift",
      "description": null,
      "scheduleGroupId": "basic",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**ScheduleEvent** (references Schedule, uses RRule for recurrence):

```json
{
  "api": "application",
  "modelName": "ScheduleEvent",
  "payload": [
    {
      "id": "cmk00kowx2c6n016cdi743spl",
      "label": "DailyFirst",
      "rrule": "DTSTART:20260104T060000Z\nRRULE:FREQ=WEEKLY;INTERVAL=1;BYDAY=MO,TU,WE,TH,FR",
      "duration": "PT8H",
      "scheduleId": "first",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

---

### Settings

Application-level configuration parameters.

**Setting:**

```json
{
  "api": "application",
  "modelName": "Setting",
  "payload": [
    {
      "id": "DateFormat",
      "name": "Date Format",
      "settingInputTypeId": "String",
      "description": "Moment.js format string for dates.",
      "defaultValue": "L",
      "listValues": null,
      "_customFields": { "_externalId": null }
    },
    {
      "id": "TimeFormat",
      "name": "Time Format",
      "settingInputTypeId": "String",
      "description": "Moment.js format string for times.",
      "defaultValue": "LTS",
      "listValues": null,
      "_customFields": { "_externalId": null }
    }
  ]
}
```

| Field | Description |
|-------|-------------|
| `id` | PascalCase setting identifier. |
| `settingInputTypeId` | Input type: `"String"`, `"Int"`, `"Boolean"`, `"List"`. |
| `defaultValue` | Default value as a string. |
| `listValues` | Array of allowed values for `"List"` type settings. `null` for other types. |

---

### Access Control

Roles and access policy groups that govern user permissions.

**Role:**

```json
{
  "api": "application",
  "modelName": "Role",
  "payload": [
    {
      "id": "devUser",
      "name": "Development User",
      "description": "Full access development role.",
      "menu": null,
      "homeScreenId": null,
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**AccessControlPolicyGroup:**

```json
{
  "api": "application",
  "modelName": "AccessControlPolicyGroup",
  "payload": [
    {
      "id": "cmkcsccl1213l016ffs5e81dd",
      "name": "Full Access",
      "description": "Unrestricted access to all models.",
      "roleId": "devUser",
      "moduleGroupId": "machineTelemetrySiteApplication",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**Note:** `AccessControlPolicyGroup` references both `Role` (via `roleId`) and `ModuleGroup` (via `moduleGroupId`), so both must be inserted before it.

---

### Units

Measurement unit types and their individual units.

**UnitType** (must come before Unit):

```json
{
  "api": "application",
  "modelName": "UnitType",
  "payload": [
    {
      "id": "Amount",
      "label": "Amount",
      "description": "Quantities",
      "_customFields": { "_externalId": null }
    },
    {
      "id": "Duration",
      "label": "Duration",
      "description": "Time durations",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**Unit** (references UnitType via `unitTypeId`):

```json
{
  "api": "application",
  "modelName": "Unit",
  "payload": [
    {
      "id": "ea",
      "unitTypeId": "Amount",
      "name": "Each",
      "code": "ea",
      "_customFields": { "_externalId": null }
    },
    {
      "id": "min",
      "unitTypeId": "Duration",
      "name": "Minutes",
      "code": "min",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

---

### Lookup Data

Enumeration-style records with boolean behavioral flags used by the platform for classification and runtime logic.

**Mode** (production modes with behavioral flags):

```json
{
  "api": "application",
  "modelName": "Mode",
  "payload": [
    {
      "id": "production",
      "code": "PROD",
      "name": "Production",
      "description": "Normal production.",
      "countAsDowntime": false,
      "countAsProductionTime": true,
      "_customFields": { "_externalId": null }
    },
    {
      "id": "idle",
      "code": "IDLE",
      "name": "Idle",
      "description": "Machine idle, no production.",
      "countAsDowntime": true,
      "countAsProductionTime": false,
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**State** (machine states):

```json
{
  "api": "application",
  "modelName": "State",
  "payload": [
    {
      "id": "running",
      "code": "RUN",
      "name": "Running",
      "description": "Machine is operating.",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**EventCategory** (event classification):

```json
{
  "api": "application",
  "modelName": "EventCategory",
  "payload": [
    {
      "id": "machineEvent",
      "code": "MACH",
      "name": "Machine Event",
      "description": "Events originating from machine signals.",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

---

### Schedule Configuration

Exception events that override the standard schedule.

**ScheduleGroupExceptionEvent** (references ScheduleGroup):

```json
{
  "api": "application",
  "modelName": "ScheduleGroupExceptionEvent",
  "payload": [
    {
      "id": "cmk00kowx2c6n016cdi7newyr",
      "label": "New Year's Day",
      "rrule": "DTSTART:20260101T000000Z\nRRULE:FREQ=YEARLY;INTERVAL=1",
      "duration": "PT24H",
      "scheduleGroupId": "basic",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

---

### Flow Schedules (Always Last)

Data flow schedule entries and their frequencies. These must be the last entries in the data array because they reference data flows that are defined in the `dataFlows` section of the package.

**DataFlowSchedule:**

```json
{
  "api": "application",
  "modelName": "DataFlowSchedule",
  "payload": [
    {
      "id": "cmkcsddgj214d016f2ej38gfh",
      "name": "Raw Telemetry Data Generation",
      "description": null,
      "active": true,
      "inputSchema": {},
      "dataFlowId": "telemetryGeneratorScheduler",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**DataFlowScheduleFrequency** (references DataFlowSchedule and ScheduleType):

```json
{
  "api": "application",
  "modelName": "DataFlowScheduleFrequency",
  "payload": [
    {
      "id": "cmkcsdvyw214k016f3j72frr7",
      "name": "5 Minute Frequency",
      "active": true,
      "valid": true,
      "lastExecution": null,
      "payload": null,
      "status": null,
      "config": {
        "cronString": "*/5 * * * *",
        "timezone": "America/Detroit"
      },
      "dataFlowScheduleId": "cmkcsddgj214d016f2ej38gfh",
      "scheduleTypeId": "cron",
      "_customFields": { "_externalId": null }
    }
  ]
}
```

**CRITICAL:** `DataFlowScheduleFrequency` must always appear after `DataFlowSchedule` in the data array because it references the schedule via `dataFlowScheduleId`.

---

## Common Seed Data Patterns

### Schema-Only Packages

Schema-only packages contain data model definitions and their prerequisites. The `data` array is minimal:

```json
"data": [
  { "api": "application", "modelName": "ModuleGroup", "payload": [...], "metadata": { "insertEarly": true } },
  { "api": "application", "modelName": "Module", "payload": [...], "metadata": { "insertEarly": true } },
  { "api": "application", "modelName": "Sequence", "payload": [...], "metadata": { "insertEarly": true } }
]
```

These three entries are the minimum required seed data for any package that defines data models.

### Full Application Packages

Full application packages include prerequisites plus all reference/configuration data needed for the application to function:

| Category | Typical Models | Purpose |
|----------|---------------|---------|
| Prerequisites | ModuleGroup, Module, Sequence | Organizational structure and code generation |
| Setup data | AlarmState, ScheduleType, ScheduleGroup | Workflow states and classification values |
| Schedule config | Schedule, ScheduleEvent | Shift definitions and recurrence patterns |
| Settings | Setting | Application configuration parameters |
| Access control | Role, AccessControlPolicyGroup | User permissions and role assignments |
| Units | UnitType, Unit | Measurement units for Measure/RatioMeasure fields |
| Device config | DeviceDriverType, DeviceDriver | Hardware/protocol driver definitions |
| Lookup data | Mode, State, EventCategory, Event | Operational classification enumerations |
| Flow schedules | DataFlowSchedule, DataFlowScheduleFrequency | Automated flow execution timing |

### Packages with No Custom Seed Data

If a package defines models but does not need to pre-populate any records beyond prerequisites, the `data` array contains only the three prerequisite entries. The payload for models with no seed data is simply not included in the array — do not add entries with empty `payload` arrays.

---

## Tips

### When to Include Seed Data

| Include seed data when... | Leave empty when... |
|---------------------------|---------------------|
| The model defines a fixed set of lookup values (states, types, categories) | Users will create all records manually at runtime |
| The application requires default configuration to function (settings, schedules) | The model is purely transactional (production logs, telemetry) |
| Access control roles must be pre-configured | The model represents user-generated content |
| Unit types and units are needed for Measure fields | The model's records are environment-specific |

### ID Conventions for Seed Data Records

| Record Type | ID Format | Examples |
|-------------|-----------|---------|
| Prerequisites | camelCase descriptive | `machineTelemetrySiteApplication`, `machineDataApp`, `alarmCode` |
| Setup/lookup models | UPPER_CASE or camelCase meaningful ID | `ACTIVE`, `ACKNOWLEDGED`, `production`, `idle` |
| Settings | PascalCase | `DateFormat`, `TimeFormat` |
| Units | Lowercase abbreviation | `ea`, `min`, `kg`, `m` |
| Schedule records | Lowercase descriptive | `first`, `second`, `basic` |
| System-generated records | CUID format | `cmk00kowx2c6n016cdi743spl` |

**IMPORTANT:** Use meaningful, human-readable IDs wherever possible. CUIDs should only be used for records where the ID has no semantic meaning (e.g., ScheduleEvent, DataFlowSchedule, DataFlowScheduleFrequency, AccessControlPolicyGroup).

### The _customFields Pattern

Every seed data record must include the `_customFields` object:

```json
"_customFields": { "_externalId": null }
```

This is required by the platform for external system integration mapping. Set `_externalId` to `null` when the record does not originate from an external system.

### Seed Data and Model Triggers

If a model has a `create` trigger that sets the `id` from the `code` field (common for setup models), the seed data `id` should match what the trigger would produce. For example, if the trigger is `$ ~> | $ | { "id": code } |`, and the code is `"ACTIVE"`, then set `"id": "ACTIVE"` in the seed data.

### Transactional Data in Seed Records

Transactional models (WorkOrder, ProductionLog, OeeHourly, etc.) typically do not include seed data in production packages. Seed records for transactional models are only included in demo or sample packages to provide example data for testing and demonstration.
