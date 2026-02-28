---
name: fuuz-system-schema
description: Complete System API GraphQL schema reference for the Fuuz Industrial Operations platform (fuuz.com). Use when requests mention looking up system model fields, querying system entities (User, Role, Tenant, Connection, Connector, DataFlow, DataModel, Screen, Device, DeviceGateway, Module, Package, Calendar, Schedule, Document, Export, Notification, Visualization, Sequence, Setting, Tag, Channel), writing GraphQL queries/mutations against system API models, or needing exact field names and types for platform-level models. Contains 158 system model definitions with 2,676 fields. Do NOT use for application-specific models (WorkOrder, Inventory, Asset, Workcenter, Product, SalesOrder, etc.) — those vary per customer/partner deployment and are not included here.
---

# Fuuz System Schema Reference

**Version 1.1.0** | Last Updated: 2026-02-26

Complete System API GraphQL schema — the built-in models that exist in every Fuuz environment.

## System Models vs Application Models

**System models** (158 models — included in this skill):
- Exist in every Fuuz environment
- Define the platform infrastructure (users, flows, screens, models, connections, packages, devices, etc.)
- Are consistent across all tenants

**Application models** (NOT included):
- Created by customers/partners for specific applications
- Include domain-specific entities (WorkOrder, Inventory, Asset, Workcenter, Product, SalesOrder, etc.)
- Vary by deployment/tenant
- When a user needs to work with application models, ask them to provide their Application API schema or data model documentation
- Application models connect to system models via standard GraphQL relationships (e.g., a custom `WorkOrder` model might reference the system `User` model via `createdByUser`)

## References

### `references/system-schema.json`

Complete System API schema containing **158 model definitions** with **2,676 fields** total.

Structure:
```json
{
  "data": {
    "modelDefinitions": [
      {
        "name": "ModelName",
        "description": "What this model represents",
        "fields": [
          { "name": "fieldName", "type": "FieldType", "description": "..." }
        ]
      }
    ]
  }
}
```

### `references/fuuz-models.md`

Authoritative markdown reference of all 158 system models with full field definitions in table format. This is the canonical source from which system-schema.json is derived.

## System Model Categories (158 models)

| Category | Count | Key Models |
|----------|-------|------------|
| **Access Control** | 3 | AccessControlPolicy, AccessControlPolicyGroup |
| **Application** | 11 | ApplicationConfiguration, ApplicationComponentType, ApplicationMetadata, ApplicationTraceMetadata, ApplicationSpanEventLog |
| **Calendar** | 8 | Calendar, CalendarEvent, CalendarEventGroup, CalendarEventType, CalendarResource, CalendarType |
| **Channel/Tag** | 5 | Channel, ChannelStatus, Tag, TagValue, TagValueQuality |
| **Connection** | 2 | Connection, Connector |
| **Data Flow** | 15 | DataFlow, DataFlowVersion, DataFlowDeployment, DataFlowElement, DataFlowSchedule, DataFlowType, DataFlowMCPToolConfiguration |
| **Data Mapping** | 4 | DataMapping, DataMappingVersion, DataMappingDeployment, DataMappingTestCase |
| **Data Model** | 13 | DataModel, DataModelElement, DataModelVersion, DataModelDeployment, DataModelCustomField, DataModelKind, DataModelType |
| **Device/Gateway** | 11 | Device, DeviceDriver, DeviceGateway, DeviceSubscription, DeviceGatewayFunction |
| **Document** | 6 | DocumentDesign, DocumentDesignVersion, DocumentRender, DocumentRenderFormat |
| **Export** | 5 | ExportConfiguration, ExportFormat, ExportHistory, ExportStatus |
| **External App** | 2 | ExternalApplication, ExternalApplicationConfiguration |
| **File/Image** | 2 | File, Image |
| **Integration** | 3 | IntegrationRequest, IntegrationRequestLog, IntegrationRequestStatus |
| **Module** | 2 | Module, ModuleGroup |
| **Notification** | 3 | NotificationChannel, NotificationChannelRecipient, NotificationCommunication |
| **Package** | 13 | InstalledPackage, PackageDefinition, PackageInstallation, PackageInstallationLog, PackageBuilderState |
| **Role** | 4 | Role, RoleAccessControlPolicyGroup, RoleExternalApplicationConfiguration, RoleSettingValue |
| **Route** | 1 | Route |
| **Saved Query/Transform** | 6 | SavedQuery, SavedQueryParameter, SavedTransform, SavedTransformVersion, SavedTransformDeployment |
| **Schedule** | 10 | Schedule, ScheduleEvent, ScheduleGroup, ScheduleType, SchedulingConfiguration, SchedulingConfigurationRun |
| **Screen** | 5 | Screen, ScreenVersion, ScreenDeployment, ScreenElement, ScreenElementQuery |
| **Sequence** | 2 | Sequence, SequenceNumber |
| **Setting** | 4 | Setting, SettingType, SettingInputType, SettingSettingType |
| **Unit** | 4 | Unit, UnitConversion, UnitType, Measure, RatioMeasure |
| **User** | 8 | User, UserRole, UserAccessControlPolicy, UserFavorite, UserSettingValue, UserRoutePreference |
| **Visualization** | 2 | Visualization, VisualizationType |
| **Other** | 4 | Address, Recipient, ScriptLanguage, Topic |

## Common System Field Types

| Type | Description |
|------|-------------|
| `ID!` | Non-nullable unique identifier |
| `String` / `String!` | Optional / required text |
| `Int` / `Float` | Numeric types |
| `Boolean` / `Boolean!` | True/false |
| `DateTime` / `DateTime!` | ISO timestamp |
| `JSON` / `JSON!` | Arbitrary JSON data |
| `[RelatedModel!]` | Array relationship (has-many) |
| `RelatedModel` / `RelatedModel!` | Object relationship (belongs-to) |

## Auto-Generated System Fields

Every model automatically includes these fields (do NOT query them unless needed):

- `id` (ID!) — Primary key
- `createdAt` / `updatedAt` (DateTime!) — Timestamps
- `createdByUserId` / `updatedByUserId` (ID!) — User references
- `createdByUser` / `updatedByUser` (User!) — User relationships
- `_trace` (ApplicationTraceMetadata) — Trace metadata

## Usage Pattern

When you need system model info:

1. Read `references/system-schema.json`
2. Find the model by `name` in the `modelDefinitions` array
3. Use the `fields` array to get exact field names and types for your GraphQL query/mutation

Example — find fields for the `Connection` model:
```jsonata
$models := $read('references/system-schema.json').data.modelDefinitions;
$connection := $models[name = 'Connection'];
$connection.fields.name  /* returns array of all field names */
```

## Companion Skills

| Skill | Relationship |
|-------|-------------|
| **fuuz-flows** | Flows query/mutate these system models via GraphQL nodes |
| **fuuz-screens** | Screens display system model data via query bindings |
| **fuuz-schema** | Design custom models that relate to system models |
| **fuuz-platform** | Platform architecture, seeded values, connectors |

## Important Notes

- This schema is **consistent across all Fuuz environments** (unlike custom/application models)
- The schema is **read-only reference** — it describes the API, not how to modify it
- Field types use GraphQL notation: `!` = non-nullable, `[Type!]` = array of non-nullable
- Some models have 30+ fields — only query the fields you actually need
- Models like WorkOrder, Inventory, Asset, Product, etc. are **application models**, not system models
