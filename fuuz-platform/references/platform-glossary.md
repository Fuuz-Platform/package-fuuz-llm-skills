# Fuuz Platform Glossary

Comprehensive definitions of every core concept in the Fuuz Industrial Operations Platform.

---

## Application Structure

### Enterprise
The top-level organizational entity in Fuuz. An enterprise owns one or more tenants and manages licensing, user access, and app installations across the organization.

### Tenant
An isolated environment within an enterprise. Each tenant has its own database, users, and configuration. Fuuz supports 5 tenant types:

| Type | Purpose |
|------|---------|
| **Administration** | Enterprise management: user access, permissions, licensing, app installations. Cannot be manually created. |
| **Application** | Production deployment of a Fuuz app. Limited access to designer tools. |
| **Application Development** | Development environment with full Fuuz designer tools for building apps. |
| **Custom** | Full set of custom application tools available. |
| **Integration** | Tools for integrating between Fuuz apps and external applications. |

### Application
A Fuuz application built within a tenant. Composed of three types of application components: Data Models, Data Flows, and Screens. Each app has its own database.

### ModuleGroup
The top-level organizational unit within an application. Groups related modules together. Defines the application's high-level functional areas. Has an `icon` property for UI display.

### Module
A functional area within a ModuleGroup. Groups related Data Models, Data Flows, and Screens. Every artifact (model, flow, screen) must be assigned to a module via `moduleId`.

---

## Data Layer

### Data Model
Defines a data storage entity with a schema of typed fields. Every data model automatically gets a fully-featured GraphQL API with queries, mutations, and subscriptions. Models are classified as master, setup, or transactional.

### Data Model Type
Classification system for organizing data models:
- **Master** — Core reference entities (assets, products, workcenters)
- **Setup** — Configuration and lookup data (modes, states, event categories)
- **Transactional** — Event/activity data (production logs, telemetry, alarms)

### Data Model Kind
The storage model for a data model. Fuuz only supports **Reference** kind — all models are standalone entities with their own collection. Embedded models are not supported.

### Custom Fields
Runtime-extensible fields that end users can add to a data model without redeploying the schema. Accessed via `_customFields` in queries. Models must have `customFields.exposed: true` to allow this.

### Sequence
An auto-increment generator that produces sequential values for fields. Supports string sequences (with prefix/suffix/zero-padding) and integer sequences. Must be configured before any model field references it.

---

## Logic Layer

### Data Flow
Server-side logic defined in a flow-based programming paradigm. Flows process data through connected nodes (query, transform, mutate, etc.). Five flow types across three execution environments:

| Type ID | Environment | Description | Timeout |
|---------|-------------|-------------|---------|
| **System** | Backend | Server-side processing triggered by schedules, data changes, or direct invocation | 20 minutes |
| **Screen** | Web | Invoked from frontend screens via request/response pattern | 10 minutes |
| **Edge** | Gateway | Runs on-premise via Fuuz Gateway for device interaction | Varies |
| **Integration** | Backend | Moves data between business systems (ERP, CRM, external APIs) | 20 minutes |
| **Document** | Backend | Generates and returns base-64 encoded documents | 20 minutes |

### Saved Transform
A reusable JavaScript or JSONata logic block that can be referenced across multiple flows. Promotes consistency and maintainability.

### Saved Query
A pre-configured GraphQL query that can be reused across flows and screens. Supports Query, Mutation, and Subscription operation types.

---

## UI Layer

### Screen
The user interface component through which end users interact with an app. Defined as JSON with a craft.js-based component tree. Screens can be dashboards, tables, forms, or custom layouts.

### Saved Visualization
A pre-configured FusionCharts visualization that can be reused across screens. Includes chart type, data sourcing, and styling. Managed independently from screen definitions.

---

## Platform Infrastructure

### GraphQL API
Fuuz provides two GraphQL APIs:
- **Application API** — Custom models built by developers. Each app has its own Application API.
- **System API** — Platform models (users, roles, settings, etc.). Same structure across all tenants.

Both use Relay-style connections with `edges[].node` patterns for query results.

### Package
A deployable bundle (`.fuuz` file = tar archive) containing application artifacts. Can include data models, data flows, screens, saved transforms, seed data, settings, and more. Imported via Configuration → Packages.

### Setting
A platform configuration value that controls display, locale, or behavior. 12 system settings include TimeZone, Locale, DateFormat, DateTimeFormat, FloatFormat, IntegerFormat, ThemeMode, and more. Two types:
- **Client** — Applies to the entire tenant
- **User** — Can be overridden per user

### Role
Defines a set of permissions for users. Roles control menu access, screen visibility, and API operation permissions. Assigned to users to control what they can see and do.

### Access Control Policy Group
Groups access control policies together for assignment to roles. Contains one or more individual Access Control Policies.

### Access Control Policy
A fine-grained permission rule that controls access to a specific model and operation (create, read, update, delete). Policies are grouped into Access Control Policy Groups and assigned to roles.

### Data Mapping
A reusable data transformation configuration that maps fields between source and target schemas. Used for structured data import/export between systems. Included in packages via the `dataMappings` array.

### Document Design
A document/report template that generates formatted output (e.g., PDF labels, shipping documents, invoices). Used with Document-type data flows. Included in packages via the `documentDesigns` array.

---

## Device & Integration Layer

### Device Gateway
The Fuuz Gateway runs on-premise to bridge cloud-based flows with local devices (PLCs, sensors, printers). Supports device subscriptions, device functions, and file operations.

### Device Driver
A protocol adapter for communicating with devices. 19 built-in drivers including Ethernet/IP PLC, Modbus TCP, MQTT Client, OPCUA Client, HTTP Client/Server, Microsoft SQL, MySQL, Oracle DB, IBM DB2, SAP RFC, and more.

### Device Driver Type
Categorizes device drivers by protocol family. 13 types: File, HTTP, Modbus, MQTT, MQTT Sparkplug, OPCUA, PCCC PLC, PLC, Printer, Remote System Call, Server, SQL, TCP.

### Connector
An integration adapter for connecting to external systems. 44 built-in connectors including SAP, Salesforce, HTTP, FTP, Plex, Oracle, SQL Server, and more. Each connector has its own credentials and options schema.

---

## Scheduling

### Schedule Group
A collection of schedules that define a shift pattern (e.g., "Basic" with 1st/2nd/3rd shifts).

### Schedule
A named time period within a Schedule Group (e.g., "1st Shift", "2nd Shift"). Linked to Schedule Events that define the actual time windows.

### Schedule Event
A specific recurring time window defined by an RRule (iCalendar recurrence) and duration. Links to a Schedule within a Schedule Group.

### Data Flow Schedule
Links a Data Flow to one or more scheduled frequencies. Controls when and how often a flow runs automatically.

### Data Flow Schedule Frequency
Defines the timing for a Data Flow Schedule using cron expressions and timezone configuration.

---

## Units of Measure

### Unit Type
A category of measurement (e.g., Amount, Area, Currency, Length, Mass, Power, Pressure, Speed, Temperature, Time, Volume, Weight).

### Unit
A specific unit of measure within a Unit Type (e.g., "ea" for each, "kg" for kilograms, "m" for meters). Used by `Measure` and `RatioMeasure` field types.

### Unit Conversion
Defines conversion factors between units within the same Unit Type, enabling automatic unit conversion in queries and displays.
