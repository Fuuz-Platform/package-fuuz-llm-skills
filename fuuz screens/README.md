# Fuuz Screen Designer Skill (`fuuz-screens-v1`)

Generate complete, importable Fuuz screen JSON for the [Fuuz Industrial Operations Platform](https://fuuz.com). Covers dashboards, tables, forms, mobile apps, HMI/control panels, and reports.

## Overview

This skill produces production-ready screen JSON that can be imported directly into the Fuuz Application Designer. It covers all 59 screen element types, layout patterns, data binding with JSONata, FusionCharts visualizations, FontAwesome icons, and responsive flex design.

**Technology stack:** craft.js (component framework), FusionCharts (visualizations), FontAwesome (icons), Material UI (typography/theming), JSONata (expressions), GraphQL (data layer).

## When to Use

Trigger this skill when requests mention:

- `fuuz screen`, `screen designer`, `screen JSON`, `canvas design`
- `dashboard screen`, `table screen`, `form screen`, `mobile screen`
- `fuuz UI`, building user interfaces for Fuuz applications
- Debugging React render errors in Fuuz screens

## File Structure

```
fuuz-screens-v1/
├── README.md                          ← You are here
├── SKILL.md                           (644 lines)   Core rules, required properties,
│                                                     JSON structure, architecture guide,
│                                                     output checklist
└── references/
    ├── element-types.md               (463 lines)   All 59 element types with full
    │                                                 property tables
    ├── component-patterns.md        (1,139 lines)   CSS rules, Container, Form,
    │                                                 DisplayText, RichText, ActionButton,
    │                                                 Icon, FlowButton, MenuButton, Table,
    │                                                 ResizablePanel, Filter components
    ├── screen-runtime-context.md      (313 lines)   Runtime metadata, components object,
    │                                                 transform payload/trace/result,
    │                                                 practical examples
    ├── frontend-jsonata-bindings.md   (339 lines)   Curated frontend JSONata bindings
    │                                                 (navigation, data access, formatting,
    │                                                 component functions)
    ├── graphql-patterns.md            (120 lines)   Screen-specific GraphQL query and
    │                                                 mutation patterns
    ├── dashboard-patterns.md          (488 lines)   Chart & Visualization components,
    │                                                 pageLoadAction, KPI cards, dashboard
    │                                                 architecture from production screens
    ├── layout-templates.md            (507 lines)   5 screen templates (Dashboard, Table,
    │                                                 Form Standard, Form Dialogue, Mobile),
    │                                                 table variants, action chains,
    │                                                 responsive patterns
    ├── common-errors.md                (87 lines)   Quick-reference troubleshooting for
    │                                                 render errors and data binding issues
    ├── visualization-library.md     (2,246 lines)   Complete FusionCharts reference
    │                                                 (80+ chart types with properties
    │                                                 and data structures)
    └── system-seeded-values.md      (2,156 lines)   System-seeded lookup values (modes,
                                                      statuses, event types, connectors)
```

**Total:** 11 files, ~8,500 lines of documentation.

## Quick Start

1. **Read `SKILL.md`** — Contains the core rules that apply to every screen: required properties, JSON structure, component registry, output checklist, and architecture decision guide.

2. **Read the relevant reference files** based on what you're building:

| Task | Required Reference Files |
|---|---|
| Any screen | `element-types.md`, `component-patterns.md` |
| Dashboard | + `dashboard-patterns.md`, `screen-runtime-context.md`, `frontend-jsonata-bindings.md` |
| Table / Form | + `layout-templates.md`, `graphql-patterns.md` |
| Adding charts | `dashboard-patterns.md`, `visualization-library.md` |
| Writing JSONata | `frontend-jsonata-bindings.md`, `screen-runtime-context.md` |
| Filter with seeded values | `system-seeded-values.md` |
| Debugging errors | `common-errors.md` |

3. **Generate the screen JSON** — Build from ROOT down, following the templates and patterns.

4. **Validate** — Run through the Output Checklist in SKILL.md before delivering.

## Companion Skills

### fuuz-flows (Data Flow Builder)

Screens rarely exist in isolation. Functional screens often require companion data flows for server-side logic, real-time data, and complex orchestration.

| Screen Type | Flows Needed? |
|---|---|
| Simple CRUD table/form | No — binds directly to data model |
| Dashboard with KPIs | Yes — Web Flow (Screen type) for pageLoadAction data |
| HMI / Control Panel | Yes — Web Flows + Backend Flows for real-time data |
| Mobile app | Yes — Web Flows for form submission, scan processing |
| Custom action buttons | Yes — Web Flow per `$executeFlow()` call |
| Device integration | Yes — Gateway Flow for PLC/device communication |

**Always read the `fuuz-flows` SKILL.md** when generating flows to support a screen.

### fuuz-schema (Data Model Designer)

Consult when building screens that bind to custom data models. The schema skill defines model types, field types, and relationship patterns that determine how screens query and display data.

### fuuz-platform (Platform Reference)

Consult for shared platform knowledge: connector configurations (used via `$integrate()`), device driver details (used via `$executeDeviceFunction()`), system seeded values, FusionCharts visualization library, and cross-skill task routing.

## Critical Rules Summary

These are the most common causes of screen failures. Full details in SKILL.md.

### Required on EVERY Element

- `elementId` (node key), `custom.elementName` (must match key)
- `type.resolvedName` (exact match from component registry)
- `parent`, `nodes`, `isCanvas`, `hidden`, `description`

### Required on Data-Bound Elements

- `query.api`, `query.model`, `query.dataPath`, `query.fields`
- `dataPath`, `formElement`, `field` (on form inputs)

### Never Use Null

- `data` → use `{}`
- `style` → use `{}`
- `bordersInput` → use `{}`
- `filterPredicate` → use `{}`
- `parameters` → use `"{}"`
- `onClickActions` → use `{}`

### Output Format

Always output **complete screen JSON** as a downloadable `.json` file:

```json
[
  {
    "name": "Screen Name",
    "tabTitle": "Browser Tab Title",
    "type": "canvas",
    "props": {
      "design": {
        "ROOT": { ... },
        "NodeId1": { ... }
      }
    }
  }
]
```

Users import via **Application Designer → File → Import JSON** or by pasting into a **Screen Version** editor.

## Screen Complexity Levels

**Level 1 — Static Layout** (this skill only)
Simple table or form screens. Model-bound, filter panel, standard CRUD.

**Level 2 — Enhanced Screen** (this skill + inline GraphQL/JSONata)
Custom action chains, conditional visibility, dynamic selects.

**Level 3 — Data-Driven** (this skill + `fuuz-flows`)
Dashboards, HMIs. Page load flows populate `$state.context.*`.

**Level 4 — Full Application** (this skill + multiple flow types)
Mobile apps, multi-screen workflows, device integration, external APIs.

## Version History

| Version | Date | Changes |
|---|---|---|
| 1.0 | 2026-02-08 | Initial release — 59 element types, 10 reference files, production dashboard patterns, screen runtime context, complete component templates |

## Source Materials

This skill was built from:

- **19 production screens** — Element types and property tables extracted from 1,000+ real components
- **Tenant Admin Dashboard** (172 components) — pageLoadAction, KPI cards, context binding patterns
- **Enterprise Admin Dashboard** (219 components) — System API queries, Cards transforms, navigation
- **Fuuz Platform documentation** — GraphQL syntax, JSONata bindings, visualization library, system seeded values
- **Screen runtime context** — Full metadata, components, transform payload/trace structures from live screens

---

*Built for the [Fuuz Industrial Operations Platform](https://fuuz.com). For platform documentation, visit [fuuz.com](https://fuuz.com).*
