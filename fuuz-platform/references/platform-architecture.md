# Fuuz Platform Architecture

Technical architecture of the Fuuz Industrial Operations Platform.

---

## System Overview

Fuuz is a cloud-based industrial operations platform that enables building custom manufacturing, telemetry, and operations applications. The platform provides:

- **Auto-generated GraphQL APIs** for every data model
- **Flow-based programming** for server-side logic
- **Screen designer** for building reactive UIs
- **Device gateway** for on-premise device integration
- **Package system** for deploying and distributing applications

---

## Dual API Architecture

Fuuz exposes two separate GraphQL APIs:

### Application API
- Contains **custom data models** created by developers
- Each application/tenant has its own unique Application API
- Schema is defined by the developer and deployed via packages
- Accessed in flows via `"api": "application"` and in screens via `"api": "Application"`

### System API
- Contains **platform models** consistent across all Fuuz environments
- Includes: User, Role, AccessControlPolicy, Setting, Screen, Visualization, Sequence, Schedule, Connector, DeviceDriver, and 40+ more
- Schema is managed by the platform — developers query it but don't modify it
- Accessed in flows via `"api": "system"` and in screens via `"api": "System"`

Both APIs use **Relay-style connections**:
```graphql
query {
  modelName(where: { ... }, orderBy: { ... }, first: 100) {
    edges {
      node {
        id
        fieldName
      }
    }
  }
}
```

Result traversal: `$.modelName.edges[*].node` or `$.modelName.edges[0].node` (single record).

---

## Multi-Tenancy Model

```
Enterprise
├── Administration Tenant (1 per enterprise)
│   └── Enterprise-wide: users, roles, licensing, app installations
├── Application Development Tenant(s)
│   └── Full designer tools: build models, flows, screens
├── Application Tenant(s)
│   └── Production deployment: limited designer access
├── Custom Tenant(s)
│   └── Full custom application tools
└── Integration Tenant(s)
    └── External system integration tools
```

**Key principles:**
- Each tenant has its **own database** — complete data isolation
- System schema is **identical in structure** across all tenants but **separate instances**
- Custom schema is **100% unique** per application tenant
- Users belong to the enterprise and are granted access to specific tenants

---

## Application Component Architecture

Every Fuuz application is composed of three types of components, organized in a hierarchy:

```
ModuleGroup (e.g., "Machine Telemetry Site Application")
└── Module (e.g., "Machine Data App")
    ├── Data Models — Define the data schema (Application API)
    │   ├── Master models (Asset, Product, Workcenter)
    │   ├── Setup models (Mode, State, EventCategory)
    │   └── Transactional models (ProductionLog, Alarm, TelemetryRaw)
    │
    ├── Data Flows — Define server-side logic
    │   ├── System (backend) flows — schedules, data change triggers
    │   ├── Screen (web) flows — request/response for UI
    │   └── Gateway flows — on-premise device communication
    │
    └── Screens — Define the user interface
        ├── Table screens — CRUD data grids
        ├── Form screens — Record detail views
        ├── Dashboard screens — KPIs, charts, metrics
        └── Custom screens — HMI, mobile, control panels
```

---

## Package Deployment Pipeline

Applications are distributed and deployed as `.fuuz` packages:

```
Development Tenant                    Production Tenant
┌─────────────────┐                  ┌─────────────────┐
│ Design models    │                  │                 │
│ Build flows      │  ── Export ──→   │  ── Import ──→  │
│ Create screens   │    (.fuuz)      │   Package UI    │
│ Configure seeds  │                  │                 │
└─────────────────┘                  └─────────────────┘
```

### Package Structure
```
MyApp@1.0.0.fuuz (tar archive)
├── manifest.json        — Identity, version, dependencies
├── definition.json      — Export selections, ordering metadata
└── package-data.json    — Content: models, flows, screens, seed data
```

### Package Content Types

| Key in package-data.json | Artifact Type | Description |
|--------------------------|--------------|-------------|
| `dataModels` | Data Models | Schema definitions with fields and metadata |
| `dataFlows` | Data Flows | Flow definitions with node configurations |
| `screens` | Screens | Screen JSON with component trees and routes |
| `savedTransforms` | Saved Transforms | Reusable transform scripts |
| `dataMappings` | Data Mappings | Data mapping configurations |
| `documentDesigns` | Document Designs | Document templates |
| `data` | Seed Data | Pre-populated records (settings, modes, reference data) |

### Deployment Ordering
The platform enforces dependency ordering during import:
1. **ModuleGroup** records (`insertEarly: true`)
2. **Module** records (`insertEarly: true`)
3. **Sequence** records (`insertEarly: true`)
4. **Data Models** (schema deployment)
5. **Seed data** (reference records, settings, roles)
6. **Screens and Flows** (depend on models existing)
7. **Schedule configurations** (depend on flows existing)

---

## Device Gateway Architecture

The Fuuz Gateway bridges cloud-based logic with on-premise devices:

```
Cloud (Fuuz Platform)                    On-Premise
┌──────────────────┐                    ┌──────────────────┐
│ Gateway Flows    │ ◄── WebSocket ──► │ Fuuz Gateway     │
│ (flow nodes)     │                    │ ├── Device Driver │
│                  │                    │ │   ├── PLC       │
│ Screen bindings  │                    │ │   ├── Sensor    │
│ ($executeDevice  │                    │ │   └── Printer   │
│   Function)      │                    │ └── Subscriptions │
└──────────────────┘                    └──────────────────┘
```

### Device Communication Flow
1. Gateway flow or screen action initiates a device function call
2. Request routes through the Fuuz Gateway WebSocket connection
3. Gateway uses the appropriate Device Driver to communicate with the device
4. Response returns through the same channel

### Device Driver Types (13 categories)
File, HTTP, Modbus, MQTT, MQTT Sparkplug, OPC, PLC (Allen-Bradley, Siemens, etc.), Serial, S7, TCP, and more.

### Device Subscription Types
- **Value Change** — Trigger when a device value changes
- **Interval** — Poll at regular intervals
- **Event** — Trigger on device events

---

## Scheduling Architecture

Fuuz supports automatic flow execution via a scheduling system:

```
Schedule Group (e.g., "Basic Shifts")
├── Schedule (e.g., "1st Shift")
│   └── Schedule Event (RRule + Duration: "M-F 6AM-2PM")
├── Schedule (e.g., "2nd Shift")
│   └── Schedule Event (RRule + Duration: "M-F 2PM-10PM")
└── Schedule (e.g., "3rd Shift")
    └── Schedule Event (RRule + Duration: "Su-F 10PM-6AM")

Data Flow Schedule
├── Links a flow to schedule(s)
└── Data Flow Schedule Frequency
    └── Cron expression + timezone (e.g., "*/5 * * * *" America/Detroit)
```

Schedule Events use iCalendar RRule format for recurrence patterns and ISO 8601 Duration for time windows.

---

## Connector Architecture

44 built-in connectors for integrating with external systems:

| Category | Connectors |
|----------|-----------|
| **ERP/MES** | SAP, Plex, Epicor, Oracle |
| **Database** | SQL Server, PostgreSQL, MySQL, MongoDB, IBM DB2 |
| **Cloud** | AWS (S3, Lambda, SQS, SNS), Azure, Salesforce |
| **Protocol** | HTTP, FTP, SFTP, MQTT, AMQP, gRPC |
| **File** | CSV, Excel, Local File |
| **Email** | SMTP, Microsoft 365 |

Each connector has:
- **Credentials Schema** — Authentication configuration (API keys, OAuth, username/password)
- **Options Schema** — Connection configuration (endpoints, timeouts, retry logic)

Connectors are configured in Fuuz and used in flows via `integrateV2` nodes or `$integrate()` bindings.
