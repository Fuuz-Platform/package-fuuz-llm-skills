# Fuuz Cross-Skill Index

Decision guide for determining which Fuuz skill(s) to use for any given task. This index covers all four skills in the Fuuz accelerator suite.

---

## Skill Overview

| Skill | Purpose | Output |
|-------|---------|--------|
| **fuuz-schema** | Design data models, generate package files | Fuuz package JSON (manifest + definition + package-data) |
| **fuuz-flows** | Build server-side data flow logic | Flow JSON (node configurations, transforms) |
| **fuuz-screens** | Create user interface screens | Screen JSON (component tree, design) |
| **fuuz-platform** | Shared platform reference material | Reference documentation (no generated artifacts) |

---

## Task → Skill Decision Matrix

### "What are you building?"

| Task | Primary Skill | Supporting Skills |
|------|--------------|-------------------|
| Design a new data model / schema | **fuuz-schema** | fuuz-platform (for seeded values, platform concepts) |
| Create a Fuuz package file | **fuuz-schema** | — |
| Build a backend data flow (scheduled, reactive) | **fuuz-flows** | fuuz-platform (for connectors, drivers) |
| Build a web flow for a screen | **fuuz-flows** | fuuz-screens (for understanding screen context) |
| Build a gateway flow for devices | **fuuz-flows** | fuuz-platform (for device drivers, connectors) |
| Create a table/form screen | **fuuz-screens** | — |
| Create a dashboard screen | **fuuz-screens** | fuuz-flows (for page load flows) |
| Create an HMI / control panel | **fuuz-screens** | fuuz-flows (for real-time data flows) |
| Create a mobile app screen | **fuuz-screens** | fuuz-flows (for form submission flows) |
| Build a complete application | **All four** | Schema → Flows → Screens, Platform for reference |
| Look up a connector or device driver | **fuuz-platform** | — |
| Look up system seeded values | **fuuz-platform** | — |
| Configure a FusionCharts visualization | **fuuz-platform** | fuuz-screens (for Chart/Visualization components) |
| Understand platform architecture | **fuuz-platform** | — |
| Look up field types for a model | **fuuz-schema** | — |
| Understand GraphQL query patterns | **fuuz-flows** (backend) or **fuuz-screens** (frontend) | — |

### "What stage am I at?"

| Development Stage | Skill(s) |
|-------------------|----------|
| 1. Planning the data layer | **fuuz-schema** + **fuuz-platform** (for glossary, architecture) |
| 2. Designing models and relationships | **fuuz-schema** |
| 3. Generating the schema package | **fuuz-schema** |
| 4. Building backend automation | **fuuz-flows** |
| 5. Building screen-supporting flows | **fuuz-flows** + **fuuz-screens** (for understanding what the screen needs) |
| 6. Creating user interfaces | **fuuz-screens** + **fuuz-flows** (for companion flows) |
| 7. Configuring visualizations | **fuuz-platform** (viz library) + **fuuz-screens** (Chart components) |
| 8. Setting up device integration | **fuuz-flows** + **fuuz-platform** (device drivers, connectors) |

---

## Artifact Dependency Graph

```
                    ┌──────────────────┐
                    │  fuuz-platform   │
                    │  (reference)     │
                    └────────┬─────────┘
                             │ provides shared knowledge to
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │ fuuz-schema │  │ fuuz-flows  │  │ fuuz-screens│
    │ (models)    │  │ (logic)     │  │ (UI)        │
    └──────┬──────┘  └──────┬──────┘  └──────┬──────┘
           │                │                │
           │  models feed   │  flows feed    │
           ├───────────────►│───────────────►│
           │                │◄───────────────┤
           │                │  screens call  │
           │                │  flows         │
           │                │                │
    ┌──────▼──────────────▼────────────────▼──────┐
    │           Fuuz Application                   │
    │  Package (.fuuz) = models + flows + screens  │
    └──────────────────────────────────────────────┘
```

**Data flows:**
1. **Schema → Flows**: Models define the data structures that flows query and mutate
2. **Schema → Screens**: Models define the data that screens display and edit
3. **Flows → Screens**: Web flows provide computed data to screens via `$state.context`
4. **Screens → Flows**: Screens call flows via `$executeFlow()` for custom actions

---

## Common Multi-Skill Workflows

### Workflow 1: Build a New Application from Scratch

1. **fuuz-platform** — Understand the platform architecture and module hierarchy
2. **fuuz-schema** — Design all data models, relationships, sequences, seed data
3. **fuuz-schema** — Generate the schema package and import into Fuuz
4. **fuuz-flows** — Build backend flows (ETL, aggregations, reactive updates)
5. **fuuz-flows** — Build web flows for dashboard data and custom screen actions
6. **fuuz-screens** — Create screens (tables, forms, dashboards)
7. **fuuz-screens** — Wire screens to web flows via `pageLoadDataFlowIds` and `$executeFlow()`

### Workflow 2: Add a New Feature to an Existing App

1. **fuuz-schema** — Design new models or extend existing ones
2. **fuuz-schema** — Generate an updated package with the new models
3. **fuuz-flows** — Build flows that use the new models
4. **fuuz-screens** — Create or update screens for the new feature

### Workflow 3: Build a Dashboard

1. **fuuz-flows** — Build a web flow (Screen type) that aggregates data into `$state.context`
2. **fuuz-screens** — Create the dashboard screen with Chart/Visualization components
3. **fuuz-platform** — Reference the visualization library for chart configuration

### Workflow 4: Set Up Device Integration

1. **fuuz-platform** — Look up device drivers and connector configurations
2. **fuuz-schema** — Design models for device data (telemetry, alarms, data points)
3. **fuuz-flows** — Build gateway flows for device communication
4. **fuuz-flows** — Build backend flows for data processing and aggregation
5. **fuuz-screens** — Create HMI screens with real-time data display

---

## File-Level Cross-References

### When fuuz-schema references other skills:
- `fuuz flows/SKILL.md` — For understanding how flows use model names in query/mutate nodes
- `fuuz screens/SKILL.md` — For understanding how screens bind to models via `query.model`
- `fuuz-platform/references/system-seeded-values.md` — For system model types, field types, connectors

### When fuuz-flows references other skills:
- `fuuz schema/SKILL.md` — For data model design guidance when building queries
- `fuuz screens/SKILL.md` — For understanding screen context when building web flows
- `fuuz-platform/references/system-seeded-values.md` — For connector and driver configurations

### When fuuz-screens references other skills:
- `fuuz flows/SKILL.md` — For building companion web flows (required for dashboards, HMIs, mobile)
- `fuuz schema/SKILL.md` — For understanding data model structure when configuring data bindings
- `fuuz-platform/references/visualization-library.md` — For FusionCharts configuration

### When fuuz-platform references other skills:
- `fuuz schema/SKILL.md` — For data model design (directs users there for model building)
- `fuuz flows/SKILL.md` — For flow building (directs users there for flow development)
- `fuuz screens/SKILL.md` — For screen creation (directs users there for UI development)
