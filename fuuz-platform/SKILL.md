---
name: fuuz-platform
description: Shared platform knowledge for the Fuuz Industrial Operations platform (fuuz.com). Use when requests mention "fuuz platform", "fuuz architecture", "platform architecture", "platform concepts", "system seeded values", "connectors", "device drivers", "device gateway", "platform settings", "tenant types", "fuuz glossary", "fuuz package anatomy", "application structure", "module hierarchy", "moduleGroup", "multi-tenancy", "multi-tenant", "deployment pipeline", "visualization library", "FusionCharts", "SAP", "Plex", "Salesforce", "Epicor", "Netsuite", "Dynamics 365", or when needing cross-cutting platform knowledge that spans data models, flows, and screens. Provides the glossary, architecture reference, package format overview, connector catalog, device driver reference, seeded values, and cross-skill task routing for the Fuuz accelerator suite.
---

# Fuuz Platform Reference

**Version 1.6** | Last Updated: 2026-02-26

Shared platform knowledge and reference material for the Fuuz Industrial Operations platform. This skill provides cross-cutting information used by all other Fuuz skills.

## When to Use This Skill

Use **fuuz-platform** when you need to:
- Look up platform concepts, terminology, or architecture
- Find connector or device driver configurations
- Understand system seeded values (types, settings, enumerations)
- Determine which Fuuz skill to use for a specific task
- Understand the Fuuz package format at a high level
- Configure FusionCharts visualizations
- Understand multi-tenancy, dual API architecture, or deployment pipelines

**This is a reference skill — it does not produce artifacts.** For building data models, flows, or screens, use the corresponding builder skills (fuuz-schema, fuuz-flows, fuuz-screens).

---

## Platform Overview

Fuuz is a cloud-based industrial operations platform for building custom manufacturing, telemetry, and operations applications. Every Fuuz application consists of three component types:

```
ModuleGroup (e.g., "Machine Telemetry Site Application")
└── Module (e.g., "Machine Data App")
    ├── Data Models — Schema (Application API)
    ├── Data Flows — Server-side logic
    └── Screens — User interface
```

Every data model automatically gets a **full GraphQL API** (query, mutate, subscribe). Applications are distributed as **`.fuuz` packages** — tar archives containing manifest, definition, and package-data JSON files.

---

## System vs Custom Schema

**System Schema** — Platform infrastructure consistent across all Fuuz environments:
- Users, Roles, Access Control Policies
- Settings, Screens, Saved Visualizations
- Sequences, Connectors, Device Drivers
- Schedules, Packages, Modules, ModuleGroups
- Each tenant has its own database but identical structure
- Queried via `"api": "system"` — never modified by developers

**Custom Schema** — Developer-created models unique to each application:
- Assets, Products, Workcenters, Production Logs, Telemetry, etc.
- 100% unique per application — each app has its own database
- Queried via `"api": "application"`
- Designed using the **fuuz-schema** skill

Both use Relay-style GraphQL connections:
```graphql
query {
  modelName(where: { ... }, orderBy: { ... }, first: 100) {
    edges { node { id, fieldName } }
  }
}
```

---

## Application Hierarchy

| Level | Example | Purpose |
|-------|---------|---------|
| **Enterprise** | Acme Manufacturing | Organization root |
| **Tenant** | Acme App Dev, Acme Production | Isolated environment with own database |
| **ModuleGroup** | Machine Telemetry Site Application | Top-level app grouping |
| **Module** | Machine Data App | Functional area within an app |
| **Data Model** | Asset, Alarm, OeeHourly | Data schema definitions |
| **Data Flow** | calculateOeeHourly | Backend/web/gateway logic |
| **Screen** | Assets, Dashboard | User interface |

**Tenant Types:** Administration, Application Development, Application, Custom, Integration

→ See `references/platform-glossary.md` for complete definitions of all concepts.

---

## Dual API Architecture

Fuuz exposes two separate GraphQL APIs:

| API | Contains | Access | Schema Owner |
|-----|----------|--------|-------------|
| **Application API** | Custom data models | `"api": "application"` / `"api": "Application"` | Developer (via packages) |
| **System API** | Platform models (40+) | `"api": "system"` / `"api": "System"` | Fuuz platform |

Both APIs use Relay-style connections. Result traversal: `$.modelName.edges[*].node`

→ See `references/platform-architecture.md` for full architecture documentation.

---

## Fuuz Packages

Applications are deployed as `.fuuz` packages (tar archives):

```
MyApp@1.0.0.fuuz
├── manifest.json        — Identity, version, platform compatibility
├── definition.json      — Export selections, dependency ordering
└── package-data.json    — Models, flows, screens, seed data
```

**Package content types:** dataModels, dataFlows, screens, savedTransforms, dataMappings, documentDesigns, data (seed records)

**Deployment ordering** is enforced: ModuleGroup → Module → Sequence → DataModels → Seed Data → Screens/Flows → Schedules

→ See `references/package-anatomy.md` for the complete package guide.
→ See `fuuz schema/references/package-format.md` for detailed JSON structure with annotated examples.

---

## System Seeded Values

The platform includes 100+ pre-configured values across 10 categories:

| Category | Key Values | Count |
|----------|-----------|-------|
| **Application Architecture** | ApplicationComponentType, TenantType | 8 |
| **Data Models** | DataModelType (master/setup/transactional), DataModelKind, CustomFieldType | 19 |
| **Data Flows** | DataFlowType (System/Screen/Edge/Integration/Document) | 11 |
| **Connectors** | 44 built-in integration connectors | 44 |
| **Device Gateway** | DeviceDriverType (13), DeviceDriver (19), SubscriptionType (10), GatewayFunction (6) | 48 |
| **Scheduling** | ScheduleType (cron), SchedulingConstraintType (6) | 7 |
| **Saved Queries** | SavedQueryOperationType (Query/Mutation/Subscription) | 3 |
| **Package Installation** | PackageInstallationTaskType, LogType | 10 |
| **Settings** | 12 system settings (locale, theme, date/time formats) | 12+ |
| **Units of Measure** | UnitType (Amount, Area, Currency, Length, Time, Volume, Weight) | 7 |

→ See `references/system-seeded-values.md` for complete details on every seeded value.

---

## Connectors (44 Built-In)

Pre-built cloud connectors for integrating with external systems from within Fuuz flows:

| Category | Connectors |
|----------|-----------|
| **ERP/MES** | Plex API, Plex Classic, Plex UX, Plex IAM, SAP Cloud, SAP Success Factors, Infor, Epicor, NetsuiteSOAP, NetsuiteREST, Dynamics 365, Arena |
| **CRM/Commerce** | Salesforce, WooCommerce, Magento, Square, Clover, AmazonSPAPI, Channel Advisor, Spiro, Zoho |
| **HR** | ADP, ADP Vista |
| **Cloud** | AWS Lambda, Google API, OpenAI Chat |
| **Database** | Microsoft SQL Server, ODBC |
| **Protocol** | HTTP, FTP, SMTP |
| **Shipping** | UPS, UPS OAuth, Fuuz UPS, FedEx, USPS, C. H. Robinson |
| **EDI** | EDI Nation, TecCom Web Services, TecCom File Upload |
| **Other** | Confluence, QuickBooks, Fuuz, MFGx, Echo |

Connectors are configured in **Configuration → Connectors** and used in flows via `integrateV2` nodes or `$integrate()` bindings.

→ See `references/connectors-reference.md` for the categorized quick-reference with auth types.
→ See `references/system-seeded-values.md` for complete credentials and options schemas.

---

## Device Gateway

The Fuuz Gateway bridges cloud logic with on-premise devices:

**13 Device Driver Types:** File, HTTP, Modbus, MQTT, MQTT Sparkplug, OPC-UA, PCCC PLC, PLC (Ethernet/IP), Printer, Remote System Call, Server, SQL, TCP

**19 Device Drivers:** Specific implementations for each driver type (e.g., Ethernet/IP PLC, Modbus TCP, MQTT Client, OPCUA Client, Microsoft SQL, MySQL, Oracle DB, IBM DB2, SAP RFC, Native Printer, TCP Printer)

**10 Subscription Types:** File System Change, HTTP POST Endpoint, Modbus Values, MQTT Topics, Node Values, Tag Values, and more — all with Store and Forward capability.

**6 Gateway Functions:** Apply Updates, Get Driver Devices, Get Gateway Status, List Log Files, Ping Hosts, Read Log File

→ See `references/connectors-reference.md` for driver types and subscription types.
→ See `references/system-seeded-values.md` for complete driver configurations.

---

## Visualization Library

80+ FusionCharts visualization types for dashboards and data display:

| Category | Chart Types |
|----------|------------|
| **Column/Bar** | Column2D, Column3D, Bar2D, StackedColumn, Marimekko |
| **Line/Area** | Line, Area2D, SplineArea, MultiAxisLine, ZoomLine |
| **Pie/Doughnut** | Pie2D, Pie3D, Doughnut2D, Doughnut3D |
| **Combination** | MSCombi2D, MSCombi3D, StackedColumn2DLine |
| **Scatter/Bubble** | Scatter, Bubble, ZoomScatter |
| **Gauges** | AngularGauge, HLinearGauge, VLEDGauge, Thermometer, Bulb |
| **Widgets** | Funnel, Pyramid, Gantt, Treemap, HeatMap, BoxAndWhisker |
| **Real-Time** | RealTimeArea, RealTimeLine, RealTimeColumn, RealTimeStackedArea |
| **Maps** | 1600+ maps (World, USA, Europe, individual countries) |

Charts are used in fuuz-screens via the **Chart** and **Visualization** components.

→ See `references/visualization-library.md` for complete chart type reference with data format specifications.

---

## Platform Settings

12 system settings control Fuuz behavior and appearance:

| Setting | Input Type | Default | Purpose |
|---------|-----------|---------|---------|
| **DateFormat** | String | `L` | Date display format (Moment.js) |
| **DateTimeFormat** | String | `L LT` | Date & time display format |
| **TimeFormat** | String | `LT` | Time display format |
| **FloatFormat** | String | `0,0.[00000000]` | Decimal number format (Numeral.js) |
| **IntegerFormat** | String | `0,0` | Integer format |
| **Locale** | String | (browser) | Override browser locale |
| **TimeZone** | List | (device) | IANA timezone identifier |
| **AlwaysDisplayTimeZones** | Boolean | `False` | Show timezone on all dates |
| **ThemeMode** | List | `System` | Dark / Light / System |
| **PrimaryColor** | Color | — | Theme primary color |
| **SecondaryColor** | Color | — | Theme secondary color |
| **LogoImageSourceURL** | String | — | Custom branding logo URL |

Settings can be scoped to **Client** (all users in a tenant) or **User** (individual preference).

---

## JSONata in Fuuz

JSONata is the primary expression language used throughout Fuuz in data flow transform nodes, screen element transform props, onChange handlers, and validation expressions. Fuuz extends standard JSONata with 182+ custom platform bindings.

### Critical Gotchas

1. **No `!` operator** — Use `$not(expr)` function instead
2. **`$not()` is a function**, not an operator — `$not($exists(x))` not `!$exists(x)`
3. **`$round` uses banker's rounding** — `$round(2.5)` = 2, `$round(3.5)` = 4
4. **String concatenation uses `&`** — Not `+`. `"hello" & " " & "world"`
5. **Comparison `=` not `==`** — Single equals for comparison. No `===`.
6. **`$now()` and `$millis()` are frozen** — Return same value for entire evaluation
7. **`undefined` propagates** — Missing fields silently drop from results
8. **`in` operator for membership** — `x in [1,2,3]` not `[1,2,3].includes(x)`
9. **Ternary vs Elvis vs Null coalescing** — `? :` (ternary), `?:` (elvis, falsy default), `??` (null/undefined default)
10. **No explicit return** — Last expression evaluated is the result

### Function Summary

| Category | Count | Key Functions |
|----------|-------|---------------|
| **Built-in String** | 21 | `$string`, `$substring`, `$contains`, `$replace`, `$split`, `$join`, `$match`, `$base64encode` |
| **Built-in Numeric** | 12 | `$number`, `$round`, `$floor`, `$ceil`, `$formatNumber`, `$power`, `$random` |
| **Built-in Aggregation** | 4 | `$sum`, `$max`, `$min`, `$average` |
| **Built-in Boolean** | 3 | `$boolean`, `$not`, `$exists` |
| **Built-in Array** | 7 | `$count`, `$append`, `$sort`, `$reverse`, `$distinct`, `$zip` |
| **Built-in Object** | 9 | `$keys`, `$merge`, `$spread`, `$sift`, `$each`, `$type` |
| **Built-in Date/Time** | 4 | `$now`, `$millis`, `$fromMillis`, `$toMillis` |
| **Built-in Higher-Order** | 5 | `$map`, `$filter`, `$reduce`, `$single`, `$sift` |
| **Platform Core Utility** | 10 | `$cuid`, `$uuid`, `$throw`, `$coalesce`, `$wait`, `$retry`, `$tryCatch` |
| **Platform Type Predicates** | 14 | `$isArray`, `$isString`, `$isNil`, `$isNilOrEmpty`, `$isEmpty` |
| **Platform Conversion** | 10 | `$arrayToCsv`, `$csvToArray`, `$parseInt`, `$numeral`, `$jsonToJsonSchema` |
| **Platform String** | 26 | `$startsWith`, `$camelCase`, `$htmlEscape`, `$parseUrl`, `$urlJoin`, `$evalHandlebarsTemplate` |
| **Platform Rich Text** | 6 | `$markdownToHtml`, `$richTextToMarkdown`, `$richTextToHTML` |
| **Platform Object** | 15 | `$path`, `$pick`, `$omit`, `$mergeDeep`, `$flattenObject`, `$objectDiff`, `$jsonParse` |
| **Platform Array** | 21 | `$chunk`, `$groupBy`, `$findFirst`, `$flatMap`, `$partition`, `$all`, `$any` |
| **Platform Parallel** | 5 | `$mapParallel`, `$filterParallel`, `$groupByParallel`, `$timesParallel` |
| **Semver** | 28 | `$semverParse`, `$semverCompare`, `$semverSatisfies`, `$semverSort` |
| **Joins** | 5 | `$innerJoin`, `$leftOuterJoin`, `$leftSemiJoin`, `$leftAntiJoin`, `$crossJoin` |
| **Moment.js** | 7 | `$moment`, `$momentTz`, `$momentDuration`, `$momentMax`, `$momentMin` |
| **XML / EDI** | 8 | `$xmlToJson`, `$jsonToXml`, `$readEDI`, `$writeEDI`, `$parseX12` |
| **Encryption / Network** | 3 | `$encryptHmac`, `$hash`, `$http` |
| **Calendars / Scheduling** | 8 | `$eventsBetween`, `$getAvailabilityBetween`, `$scheduleGroup`, `$schedule` |
| **Unit Conversion** | 3 | `$convertUnit`, `$getConversionFactor`, `$getUnit` |
| **MFGx Application** | 12 | `$query`, `$mutate`, `$executeFlow`, `$integrate`, `$document`, `$executeTransform` |
| **Frontend-Only** | 15 | `$navigateTo`, `$showAlertDialog`, `$showConfirmDialog`, `$writeToClipboard`, `$pdfToJson` |

### Evaluation Contexts

| Feature | Screen Elements | Data Flows |
|---------|----------------|------------|
| Root `$` | Component data (mapPropsToPayload) | Node's current payload |
| `metadata` | User, tenant, settings, URL params | Not available |
| `$components` | Screen component shared state | Not available |
| `$state` | Not available | Full flow state (payload, context, claims) |
| `$appConfig` | Application configuration | Not available |
| UI functions | `$navigateTo`, `$showAlertDialog`, etc. | Not available |

For complete documentation of all operators, all 65 built-in functions, all 182+ platform functions with signatures, programming constructs (variables, lambdas, closures, partial application, regex, chaining), both evaluation contexts with full `$components` and `$state` details, the transform props pattern, and `metadata` fields, see `references/jsonata-reference.md`.

---

## Application Configuration ($appConfig)

Application Configuration provides a way to define shared, reusable configuration values that can be accessed across your entire Fuuz application.

**Available in:** Data Flows, Saved Scripts, Screens (any transform field), Data Model Triggers

**Syntax:** `$appConfig.configId.property`

**Use Cases:**
- **Shared timezone config** — Store a single timezone setting referenced by all flows and screens
- **Default values** — Define default field values used across multiple forms and flows
- **Shared filters** — Common filter criteria reusable across queries
- **Environment-specific settings** — Configuration that varies between development, staging, and production tenants

Example: `$appConfig.timezone.default` returns the configured default timezone string.

---

## Project Documentation Standards

Every Fuuz project must maintain comprehensive internal documentation to ensure supportability and knowledge transfer.

### Required Internal Documentation

| Document | Description |
|----------|-------------|
| **List of all custom flows** | Catalog of every custom data flow in the application with purpose and type |
| **List of all flows required to operate** | All flows needed for the application to run, including shared flows from other modules |
| **Walkthrough for each custom flow** | Step-by-step explanation of what each custom flow does, its trigger, nodes, and expected behavior |
| **List of all custom screens** | Catalog of every screen with purpose, key components, and associated flows |
| **Flowchart of integration process** | Visual diagram showing how data flows between Fuuz and external systems |

### Deployment Rules

- Documentation violations are acceptable while actively working, but all violations **MUST be resolved before production deployment**
- Each deployment creates a deployment record; only the most recent deployment is active and runs
- Deployed versions are **locked** — edits require creating a new version
- Flows can be deactivated without creating a new deployment when needed

---

## MongoDB Data Modeling Guidance

Fuuz uses MongoDB as its underlying database. Follow these principles when designing your data models:

1. **Understand your application before modeling** — Know your query patterns, read/write ratios, and data relationships before designing schemas. Do not blindly normalize like a relational database.
2. **Optimize for query patterns** — Design your data models around how your application reads data. Structure documents to minimize the number of queries needed for common operations.
3. **Balance read/write performance** — Denormalize data for read-heavy workloads. If data is read 100x more than it is written, store computed or duplicated values to avoid expensive joins.
4. **Use correct data types** — Store dates as dates, numbers as numbers, and booleans as booleans. Incorrect types cause query failures and sorting issues.
5. **Limit document size** — MongoDB has a 16 MB document size limit. Design models to avoid unbounded growth (e.g., do not store unlimited arrays of sub-documents).
6. **Create indexes strategically** — Index fields used in `where` clauses and `orderBy` sorts. Over-indexing slows writes; under-indexing slows reads.
7. **Plan for growth** — Consider how data volume will increase over time. Design models and indexes that perform well at scale, not just during initial development.

---

## Cross-Skill Index

Use this quick reference to determine which skill to use:

| Task | Primary Skill | Supporting Skills |
|------|--------------|-------------------|
| Design a data model / schema | **fuuz-schema** | fuuz-platform (seeded values, concepts) |
| Create a Fuuz package file | **fuuz-schema** | — |
| Build a backend data flow | **fuuz-flows** | fuuz-platform (connectors, drivers) |
| Build a web flow for a screen | **fuuz-flows** | fuuz-screens (screen context) |
| Build a gateway flow for devices | **fuuz-flows** | fuuz-platform (device drivers) |
| Create a table/form screen | **fuuz-screens** | — |
| Create a dashboard screen | **fuuz-screens** | fuuz-flows (page load flows) |
| Create an HMI / control panel | **fuuz-screens** | fuuz-flows (real-time data flows) |
| Create a mobile app screen | **fuuz-screens** | fuuz-flows (form submission, scan logic) |
| Build a complete application | **All four** | Schema → Flows → Screens, Platform for reference |
| Look up connectors or drivers | **fuuz-platform** | — |
| Look up system seeded values | **fuuz-platform** | — |
| Configure a chart visualization | **fuuz-platform** | fuuz-screens (Chart components) |
| Understand platform architecture | **fuuz-platform** | — |

→ See `references/cross-skill-index.md` for the full decision matrix, dependency graph, and multi-skill workflows.

---

## Common Multi-Skill Workflows

### Build a New Application
1. **fuuz-platform** — Understand architecture and module hierarchy
2. **fuuz-schema** — Design all data models, relationships, sequences
3. **fuuz-schema** — Generate the schema package
4. **fuuz-flows** — Build backend flows (ETL, aggregations, reactive)
5. **fuuz-flows** — Build web flows for dashboards and custom actions
6. **fuuz-screens** — Create screens (tables, forms, dashboards)
7. **fuuz-screens** — Wire screens to flows via `pageLoadDataFlowIds` and `$executeFlow()`

### Build a Dashboard
1. **fuuz-flows** — Build a web flow (Screen type) that aggregates data into `$state.context`
2. **fuuz-screens** — Create the dashboard screen with Chart/Visualization components
3. **fuuz-platform** — Reference the visualization library for chart configuration

### Set Up Device Integration
1. **fuuz-platform** — Look up device drivers and connector configurations
2. **fuuz-schema** — Design models for device data (telemetry, alarms, data points)
3. **fuuz-flows** — Build gateway flows for device communication
4. **fuuz-flows** — Build backend flows for data processing
5. **fuuz-screens** — Create HMI screens with real-time data display

---

## Resources

### Reference Files in This Skill

| File | Lines | Content |
|------|-------|---------|
| `references/platform-training-guide.md` | ~1,170 | Comprehensive v3.0.0 skill training guide — data model rules, EAM domain reference, field/relationship rules, trigger/index patterns, screen/form/table rules, ML/predictive maintenance layer, anti-patterns, checklists |
| `references/platform-glossary.md` | ~400 | Complete definitions for all platform concepts |
| `references/platform-architecture.md` | ~400 | Dual API, multi-tenancy, deployment pipeline, gateway architecture |
| `references/cross-skill-index.md` | ~350 | Task→skill routing, dependency graph, multi-skill workflows |
| `references/package-anatomy.md` | ~350 | High-level guide to Fuuz packages |
| `references/connectors-reference.md` | ~600 | All 44 connectors categorized with auth types |
| `references/system-seeded-values.md` | ~2,150 | Complete seeded values: all types, connectors, drivers, settings |
| `references/visualization-library.md` | ~2,250 | 80+ FusionCharts types with data formats |
| `references/integration-landscape.md` | ~435 | Complete integration compatibility catalog: 500+ systems across ERP, MES, SCADA, PLCs, robots, CRM, WMS, QMS, CMMS, data platforms, AI/LLM, PLM. Protocol support matrix (REST, OPC UA, MQTT, SOAP, SQL, EDI, Sparkplug B, Modbus, EtherNet/IP). |
| `references/jsonata-bindings-complete.md` | ~540 | Complete reference for all 294 custom JSONata bindings. Categories: Frontend (Clipboard, Navigation, Screen Designer), Backend (EDINation, EDI, Encryption, PDF, File), Shared (Core Utilities, Extended JSONata, Moment, Calendars, Joins, XML, Integration, Parallel, Rich Text, Semver, URL, JSON Schema). |
| `references/graphql-syntax-patterns.md` | ~740 | Complete GraphQL API reference: edges/node pattern, where clause operators, ordering, pagination with cursors, aggregations (all functions including percentile), mutations, DateTime format specifiers, error catalog, best practices. |
| `references/fuuz-platform-development.md` | ~500 | Platform development patterns: node flow architecture, data access patterns, JavaScript vs JSONata decision matrix, duration field handling, screen component hierarchy, context/state management, platform built-ins. |
| `references/graphql-essentials.md` | ~505 | GraphQL quick reference: Application API `equals`/`contains`/`startsWith` filter operators, relationship mutations (connect/disconnect/create nested), GraphQL subscriptions, manufacturing query patterns (work orders, OEE, inventory, production trace), using GraphQL in screens/flows/JSONata ($query/$mutate/$executeSavedQuery), aggregations, best practices, anti-patterns. |
| `references/jsonata-javascript-essentials.md` | ~328 | JSONata & JavaScript quick reference: where JSONata is used in Fuuz (6 contexts), basics quick reference, Fuuz-specific bindings ($moment, $query, $mutate, $urlJoin), Script Editor JavaScript runtime with `$` payload access pattern and `$variableName` context access, diagnostic script, manufacturing patterns (OEE color coding, duration formatting, inventory grouping, shift-aware time), Transformation Explorer testing tool, anti-patterns. |
| `references/jsonata-reference.md` | ~1,050 | Authoritative JSONata reference: core syntax, all operators (path/numeric/comparison/boolean/string/conditional/assignment/chaining/transform), all 65 built-in functions, all 182+ platform functions by package, programming constructs (variables, lambdas, closures, partial application, regex, recursion), both evaluation contexts (Screen Elements and Data Flows), $components documentation, metadata fields, $state documentation, transform props pattern, evaluation pipeline. |

### Reading Order

| Scenario | Start With |
|----------|-----------|
| New to Fuuz | `platform-training-guide.md` → `platform-glossary.md` → `platform-architecture.md` |
| Need to pick a skill | `cross-skill-index.md` |
| Setting up integrations | `connectors-reference.md` → `system-seeded-values.md` |
| Building dashboards | `visualization-library.md` |
| Understanding packages | `package-anatomy.md` |
| Looking up a specific value | `system-seeded-values.md` |
| Writing JSONata expressions | `jsonata-reference.md` |

---

## Related Skills

| Skill | Purpose | When to Use |
|-------|---------|------------|
| **fuuz-schema** | Design data models, generate package files | Creating or modifying the data layer |
| **fuuz-flows** | Build server-side data flow logic | Building backend automation, web APIs, gateway logic |
| **fuuz-screens** | Create user interface screens | Building tables, forms, dashboards, HMIs |

---

## AI Development Rules

When using Claude or other AI tools to build Fuuz applications, follow these rules to ensure reliable, maintainable output:

### General AI Dev Rules

1. **Always explicitly specify the API** — Use `"api": "application"` or `"api": "system"` in every query/mutate node. Never assume the default.
2. **Generate fresh UUIDs** — Every node `id`, `nextNodes` reference, and `elementName` must be a unique UUID. Never reuse IDs from examples.
3. **Test incrementally** — Build and test flows in stages (trigger → first query → verify data → add transform → verify → add mutation). Don't build the entire flow before testing.
4. **Validate every mutation payload** — Always check that the payload matches the model's field names exactly. A single typo causes "unknown field" errors.
5. **Use the platform's visual tools** — The Fuuz Screen Designer has a visual GraphQL builder for mutations. Don't manually type mutation strings in screens.
6. **Export before modifying** — Always export the current state of a flow/screen before making changes. This gives you a rollback point.
7. **Capture context early** — Use `setContext` to store configuration and reference data at the start of flows, not in the middle of processing chains.
8. **Document as you go** — Add descriptive names and descriptions to every flow and node. Future maintainers (including AI) depend on this.
9. **Define inputs and outputs for JSONata formulas** — When using AI to generate JSONata formulas, always define the input data structure and the expected output structure upfront. Never ask AI to just "make the formula" without specifying what goes in and what comes out.
10. **Use AI to generate .fuuz package files** — Claude can generate complete `.fuuz` package files (manifest, definition, package-data) for direct import into the Fuuz platform. This accelerates application scaffolding significantly.
11. **Leverage AI for project artifacts** — AI tools are particularly helpful for:
    - QA comparisons (comparing expected vs actual data)
    - UI mockups and screen layout proposals
    - Documentation generation (flow walkthroughs, data dictionaries)
    - UAT guides and test scripts
    - Sprint delivery documents and release notes

### Troubleshooting Resources

| Issue | Check | Solution |
|---|---|---|
| Query returns empty | Browser DevTools → Network → filter "graphql" | Verify field names, where clause, API selection |
| Mutation fails silently | Check payload structure | Ensure payload is an array: `[{ "create": data }]` |
| Screen doesn't load data | Table `autoLoad` setting | Set `"autoLoad": true` on the Table component |
| Flow times out | Check node count and query volume | Split into master + child flows, reduce `first:` limits |
| JSONata evaluation error | Check for missing `}` or `$` context shift | Use parentheses to capture context: `( $data := $; ... )` |
| JS runtime error | Check for unsupported syntax | No `.toFixed()`, no `let`/`const`, no arrow functions |
| Context undefined | setContext vs mergeContext confusion | `setContext` replaces; `mergeContext` adds. Use merge when prior values needed. |

### Integration Best Practices (Plex, NetSuite, SAP, etc.)

1. **Always use connector authentication** — Never hardcode credentials in flows. Configure connectors in Configuration → Connectors.
2. **Map external IDs** — Store external system IDs alongside Fuuz IDs for reconciliation.
3. **Handle rate limits** — ERP APIs have rate limits. Use batch operations and respect throttling.
4. **Log integration results** — Create audit records for every external API call, especially for orders and transactions.
5. **Use idempotent operations** — Design integrations to be safely re-runnable. Use upsert instead of create where possible.
6. **Test with sandbox environments** — Never point integration flows at production ERP systems during development.
7. **Process in chunks of 500 or less** — Fuuz works best when processing data in chunks of 500 records or fewer. Larger batches risk timeouts and memory issues.
8. **Understand Plex data source types:**
   - **ODBC** — Production data that is 4+ hours behind real-time; suitable for reporting, not real-time operations
   - **Classic Data Sources** — Return strings and XML; require parsing in transforms
   - **UX Data Sources** — Return JSON with typed parameters; preferred for modern integrations
   - **Plex API** — Limited subset of available data; check coverage before committing to this approach
9. **Plan for multi-call joins** — Approximately 90% of the data you need is available in a single API or data source call. The remaining 10% typically requires 2-4 additional calls. Use Fuuz scripting and transforms to join this data together.
10. **Maintain revision history for API writes** — APIs circumvent the ERP's built-in revision history. When writing data via API, always make a secondary call to `Revision History Add` to preserve an audit trail.
11. **NetSuite-specific considerations:**
    - NetSuite only supports standard NIST units of measure
    - NetSuite does not support purchased assemblies — plan alternative modeling
    - Do not enable Lot/Serial tracking in NetSuite without customization — the default behavior is insufficient for most manufacturing use cases

---

## Related Skills — Additional Domain Skills

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| **fuuz-industrial-ops** | Industrial operations patterns (UNS, MES, OEE, alarms, ERP integration) | Building manufacturing/industrial IoT applications |
| **fuuz-ml-telemetry** | Machine learning and telemetry patterns (anomaly detection, forecasting, baselines) | Building predictive maintenance or telemetry analysis features |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | February 2026 | Initial release — platform reference, glossary, architecture, connectors, cross-skill index |
| 1.1 | February 2026 | Added comprehensive platform training guide v3.0.0 (EAM domain, data model rules, ML/predictive layer, anti-patterns, checklists) |
| 1.2 | February 2026 | Added AI development rules, troubleshooting resources, integration best practices, references to new domain skills (fuuz-industrial-ops, fuuz-ml-telemetry) |
| 1.3 | February 2026 | Added Custom JSONata Library Reference (full binding catalog by category), Application Configuration ($appConfig) section, Project Documentation Standards, MongoDB Data Modeling guidance, expanded Integration Best Practices (Plex data sources, chunking, NetSuite caveats, revision history), updated AI Development Rules (JSONata input/output discipline, .fuuz package generation, AI use cases for QA/mockups/UAT/docs) |
| 1.4 | 2026-02-21 | Added 4 new reference files: integration-landscape.md (500+ system integration catalog with protocol matrix), jsonata-bindings-complete.md (all 294 custom JSONata bindings), graphql-syntax-patterns.md (complete GraphQL API reference with aggregations/mutations/error catalog), fuuz-platform-development.md (node flow architecture, data access patterns, JS vs JSONata, duration handling, screen components, debugging) |
| 1.5 | 2026-02-22 | Added 2 new reference files: graphql-essentials.md (Application API filter operators, relationship mutations, subscriptions, manufacturing queries, using GraphQL in JSONata), jsonata-javascript-essentials.md (Script Editor $ access pattern, diagnostic script, manufacturing JSONata patterns, Transformation Explorer) |
| 1.6 | 2026-02-26 | Added comprehensive jsonata-reference.md (authoritative JSONata reference consolidating all operators, 65 built-in functions, 182+ platform functions, programming constructs, both evaluation contexts, $components/$state/$metadata documentation, transform props pattern). Replaced inline Custom JSONata Library Reference section in SKILL.md with condensed summary table pointing to the new reference file. |
