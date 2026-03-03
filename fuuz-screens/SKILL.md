---
name: fuuz-screens
description: "Generate Fuuz screen JSON for dashboards, tables, forms, and reports. Trigger on: fuuz screen, screen designer, dashboard, table screen, form screen, Fuuz UI, screen JSON, canvas design, or Fuuz UI construction. Covers 75 elements in 5 categories (Layout 12, Input 41, Data 8, Display 4, Interaction 10) with full property docs for Container, Form, Table, DisplayText, ActionButton, Chart, Icon, Tabs, Dialog, and ResizablePanel. Includes Screen Designer concepts: Craft.js architecture, element flags, shared field sets, field type glossary, and dual-mode transform editing. Defines required props to prevent React render errors, JSONata context ($components, $appConfig, metadata), FusionCharts, FontAwesome, responsive flex layouts, dashboard patterns, and links to fuuz-graphql, fuuz-jsonata, and fuuz-flows."
---

# Fuuz Screen Designer Skill

**Version 1.4 | 2026-02-26**

### Version History

| Version | Date | Changes |
|---|---|---|
| 1.4 | 2026-02-26 | Added Screen Designer Concepts section (Craft.js architecture, canvas elements, forms, data providers, input elements). Added element flags table. Added shared field sets documentation (makeBasicFields, DISPLAY_FIELDS variants, VALIDATION_FIELDS variants, ADVANCED_FIELDS, BEHAVIOR_FIELDS, LENGTH_VALIDATION_FIELDS). Added default props specification. Added field type glossary. Added transform fields dual-mode explanation. Updated element category counts (Layout 12, Input 41, Data 8, Display 4, Interaction 10 = 75 total). Added shared data fields (READ_PREFERENCE_INPUT, Common Data Query Pattern, Data Subscription Pattern). Added comprehensive screen element JSONata context documentation ($, metadata paths, $components shared state, $appConfig, frontend-only functions, transform props pattern). Added critical JSONata gotchas for screen expressions. |
| 1.3 | 2026-02-22 | Added screen-essentials.md reference: MainFormContainer standard pattern, action pipeline structure (10 action types), screen context functions, ISA-101 industrial compliance, component library reference, design checklist |
| 1.2 | 2026-02-21 | Added OEE dashboard domain reference (formulas, state classifications, time windows, calculation patterns, GraphQL queries). Added Stimulsoft Reports reference for document design (.mrt files). Documentation bake-in: all external reference content now included in skill package. |
| 1.1 | February 2026 | Critical fixes from Screen-Skill-Improvements training doc: menu column dataPath fix, mutation payload array structure, action vs actions property comparison, form action field required properties, action chain execution order and data flow, pre-transform usage rules, table query field rules, common errors section, setup table CRUD checklist. Added Mobile App Design Guidelines, Inline Web Flows note. Fixed incorrect examples in layout-templates reference. |
| 1.0 | Initial | Original skill with element types, component patterns, layout templates, dashboard patterns, frontend JSONata bindings, GraphQL patterns, common errors, visualization library, and system seeded values references. |

---

## Overview

This skill generates complete, importable Fuuz screen JSON files for the Fuuz Industrial Operations Platform (fuuz.com). Fuuz screens are defined as JSON with a craft.js-based component tree, rendered as React components by the platform's component resolver. Screens are built within the **Application Designer** interface.

**Technology stack:** craft.js (component framework), FusionCharts (visualizations), FontAwesome (icons), Material UI (typography/theming), JSONata (expressions).

---

## Screen Designer Concepts

The screen designer uses **Craft.js** to provide a drag-and-drop visual editor. Users compose screens by placing **elements** onto a canvas.

- A **Screen** is the root canvas element. It contains child elements arranged in a layout hierarchy.
- A **Canvas Element** (`canvas: true`) can contain child elements. Examples: Screen, Container, Accordion, Form, Table, Cards.
- A **Form** (`isForm: true`) provides data context to child input elements. Forms query a data model and expose field values. Input elements inside a form bind to fields via `dataPath`.
- A **Data Provider** (`isDataProvider: true`) queries data and exposes it to child elements. Forms and Tables are data providers.
- An **Input Element** (`requiresForm: true`) must be placed inside a Form. It binds to a form field via `dataPath` and supports validation, transforms, and onChange handlers.

### Element Flags

| Flag | Meaning |
|---|---|
| `canvas` | Can contain child elements |
| `isContainer` | Generic container (accepts most child types) |
| `isForm` | Provides form data context to children |
| `isDataProvider` | Queries data and provides it to children |
| `isTable` | Table-specific data provider |
| `isCard` | Card-specific layout |
| `isButton` | Button element |
| `requiresForm` | Must be placed inside a Form |
| `excludeFromToolbox` | Not user-addable (e.g., Screen root element) |

### Element Categories

| Category | Count | Description |
|---|---|---|
| Layout | 12 | Containers, screens, tabs, accordions |
| Input | 41 | Form-bound input fields |
| Data | 8 | Forms, tables, cards, data trees |
| Display | 4 | Charts, timers, calendars, event console |
| Interaction | 10 | Buttons (flow, action, split, menu, custom) |

**Total: 75 element types across 5 categories.**

### Shared Field Sets (Input Elements)

Most input elements reuse standard field sets. Each element's documentation only lists **custom fields** beyond these sets, and notes which shared sets it uses.

#### Basic Fields (`makeBasicFields`)

Generated by `makeBasicFields({ filterType, defaultValueProps, labelRequired, fields: { field, predicate } })`. Parameters control which optional fields are included.

| Field | Type | Description |
|---|---|---|
| `field` | graphql (field picker) | Data model field binding. Auto-sets dataPath and label on change. Optional (controlled by `fields.field` param). |
| `dataPath` | text | Data path within form data. Required. |
| `defaultValue` | json | Default value for the input. |
| `predicate` | combobox | Filter predicate. Options: `_contains`, `_containsObject`, `_eq`, `_endsWith`, `_gt`, `_gte`, `_has`, `_in`, `_isNull`, `_lt`, `_lte`, `_startsWith`. Optional (controlled by `fields.predicate` param). |
| `formElement` | node | Reference to the parent form element. |
| `label` | transform (smartInput: text) | Display label. Required by default. |
| `description` | text | Helper text (multiline). |

#### Display Fields (`DISPLAY_FIELDS`)

| Field | Type | Description |
|---|---|---|
| `padding` | slider (0-96, step 8) | Element padding. |
| `width` | text | Element width. Required. |
| `height` | text | Element height. Required. |
| `alignItems` | combobox | Alignment. Options: `start`, `end`, `center`. |
| `visible` | transform (smartInput: checkbox) | Visibility based on logic. |

**Variants:**
- `DISPLAY_FIELDS_WITH_VARIANT` -- adds `variant` (options: standard | outlined | filled)
- `DISPLAY_FIELDS_WITH_FONT_SIZE` -- adds variant + font size fields
- `DISPLAY_FIELDS_WITH_FONT_SIZE_WITHOUT_VARIANT` -- adds font size fields without variant (for react-select based inputs)

#### Font Size Fields (`FONT_SIZE_FIELDS`)

| Field | Type | Description |
|---|---|---|
| `labelFontSize` | slider (8-48, step 4) | Label font size. Clearable. |
| `dataFontSize` | slider (8-48, step 4) | Data/input font size. Clearable. |

#### Validation Fields (`VALIDATION_FIELDS`)

| Field | Type | Description |
|---|---|---|
| `validation.required` | checkbox | Whether the field is required. |
| `uniqueValidationEnabled` | checkbox | Enable unique validation. |
| `validation.unique.api` | options | API for unique check (Application | System). Visible when unique enabled. |
| `validation.unique.model` | text | Data model for unique check. Visible when unique enabled. |
| `validation.unique.excludeId` | checkbox | Exclude current record from unique check. Visible when unique enabled. |
| `validation.unique.byFields` | fieldGroup | Fields to check uniqueness by. Sub-fields: `field` (text, required), `dataPath` (text). Visible when unique enabled. |
| `validation.transform` | jsonata | Custom validation transform. Should return error message string or undefined. |

**Variant:** `VALIDATION_FIELDS_WITH_NUMBER_VALIDATION` -- adds: `validation.minValue` (transform/number), `validation.maxValue` (transform/number), `validation.maxDigits` (transform/integer), `validation.disallowNegative` (transform/checkbox), `validation.disallowDecimals` (transform/checkbox)

#### Length Validation Fields (`LENGTH_VALIDATION_FIELDS`)

| Field | Type | Description |
|---|---|---|
| `validation.minLength` | integer | Minimum length. |
| `validation.maxLength` | integer | Maximum length. |

#### Advanced Fields (`ADVANCED_FIELDS`)

| Field | Type | Description |
|---|---|---|
| `disabled` | transform (smartInput: checkbox) | Disabled state based on logic. |
| `onChange` | jsonata | Transform run on every value change. |
| `data` | transform | Data transformations for this field. |
| `fields` | json | Additional query fields (array of dataPaths). |
| `targetDataPath` | text | Target data path override. |

#### Behavior Fields (`BEHAVIOR_FIELDS`)

| Field | Type | Description |
|---|---|---|
| `formatString` | text | Numeric format string (e.g., "0.000"). |
| `step` | transform (smartInput: integer) | Step value for the input. |

### Default Props (Input Elements)

All input elements start with these default property values:

```json
{
  "padding": 8,
  "width": "100%",
  "height": "auto",
  "validation": { "required": false },
  "uniqueValidationEnabled": false,
  "visible": true
}
```

### Shared Data Fields

#### Read Preference (`READ_PREFERENCE_INPUT`)

Used by Table and Cards in their Data section.

| Field | Type | Description |
|---|---|---|
| `query.readPreference` | combobox | Server read preference. Options: `primaryPreferred` (default, consistent data), `secondary` (offload reads, may be stale). |

#### Common Data Query Pattern

Used by Form, Table, and Cards. Fields typically appear in a "Data" section:

| Field | Type | Description |
|---|---|---|
| `query.api` | combobox | API source. Options: Application | System. |
| `query.model` | options (dynamic) | Data model. Options queried from model definitions. |
| `query.autoLoad` | checkbox | Auto-load data on mount. |
| `query.parameters` | jsonata | Parameters transform. |
| `query.filterPredicate` | graphqlWhere | Filter predicate with transform support. |
| `query.fields` | json | Additional query fields. |

#### Data Subscription Pattern

Used by Form, Table, and Cards in their Advanced section:

| Field | Type | Description |
|---|---|---|
| `query.dataSubscription.enabled` | checkbox | Enable real-time data subscription. |
| `query.dataSubscription.topics` | fieldGroup | Subscription topics. Sub-field: `topic` (text, required). Visible when enabled. |
| `query.dataSubscription.filterTransform` | jsonata | Filter incoming subscription data. Visible when enabled. |
| `query.dataSubscription.valueTransform` | jsonata | Transform subscription values. Visible when enabled. |

### Field Type Glossary

| Type | Description |
|---|---|
| `text` | Text input field |
| `slider` | Numeric slider with min/max/step |
| `checkbox` | Boolean checkbox |
| `switch` | Boolean toggle switch |
| `options` | Single-select radio/button options |
| `combobox` | Dropdown select (single or multi) |
| `transform` | JSONata expression with smartInput for simple mode |
| `jsonata` | Pure JSONata expression editor |
| `fieldGroup` | Repeatable group of sub-fields |
| `border` | Border configuration input |
| `color` | Color picker |
| `icon` | Icon picker |
| `integer` | Integer number input |
| `action` | Action step builder (array of action steps) |
| `chart` | Chart configuration editor |
| `duration` | Duration picker |
| `json` | JSON editor |
| `graphqlWhere` | GraphQL where clause builder |
| `graphql` | GraphQL field picker |
| `node` | Element reference picker |

### Transform Fields (Dual-Mode Editing)

Fields with `type: 'transform'` support dual-mode editing:
- **Simple mode** -- renders a widget matching the `smartInput.type` (e.g., checkbox, text, color, integer). Users set a simple static value.
- **Advanced mode** -- full JSONata expression editor. Users write dynamic expressions.

This allows users to set simple static values or write dynamic expressions. In documentation, noted as `transform (smartInput: <type>)`.

---

## When to Use This Skill

- Creating new Fuuz screens (dashboards, tables, forms, reports)
- Editing or extending existing Fuuz screen JSON
- Building responsive layouts with flex containers
- Configuring data bindings, queries, and JSONata transforms
- Integrating FusionCharts visualizations
- Setting up filter panels, action bars, and navigation
- Debugging React render errors in Fuuz screens

---

## Related Skills & References

This skill works in concert with other Fuuz platform skills. Consult these when a screen requires capabilities beyond static layout and data binding:

### Fuuz Data Flow Builder (fuuz-flows skill)
**Location:** `/mnt/skills/user/fuuz-flows/SKILL.md` (or `/mnt/skills/organization/fuuz-flows/SKILL.md`)
**When to use:** Any screen that needs dynamic server-side logic, real-time data processing, or complex orchestration requires companion web flows (type "Screen") or backend flows. This is generally required for functional applications such as:
- **HMI / Control Panel screens** — Need web flows to aggregate real-time telemetry, compute OEE, manage workcenter state transitions, and push updates via intervals/subscriptions.
- **Mobile apps** — Need web flows for form submission logic, barcode/scan processing, inventory transactions, and offline-capable data sync.
- **Dashboard screens** — Need page load flows (`pageLoadDataFlowIds`, `screenDataFlowIds`) to fetch and transform data into `$state.context.*` for display.
- **Screens with custom actions** — Any ActionButton or FlowButton that calls `$executeFlow()` requires a corresponding request/response flow.
- **Screens with device integration** — Use `$executeDeviceFunction()` or `$executeDeviceGatewayFunction()` bindings which communicate with gateway flows.

**Always read the fuuz-flows SKILL.md first** when the user's request involves building flows to support a screen. Generate the screen JSON with this skill, and the supporting flows with the fuuz-flows skill. Typical scenarios requiring companion flows:

| Screen Type | Flow Type | Purpose |
|---|---|---|
| HMI / Control Panel | Web Flow (Screen type) | Real-time telemetry aggregation, OEE computation, workcenter state transitions, interval-based data refresh |
| Mobile App | Web Flow (Screen type) + Backend Flow | Form submission logic, scan/barcode processing, inventory transactions, validation |
| Dashboard | Web Flow (Screen type) | Page load data aggregation via `pageLoadDataFlowIds` / `screenDataFlowIds`, populating `$state.context.*` |
| Any screen with custom actions | Web Flow (Request/Response) | ActionButton or FlowButton calls `$executeFlow(flowId, payload)` |
| Device integration screens | Gateway Flow | `$executeDeviceFunction()` / `$executeDeviceGatewayFunction()` calls |
| Screens with external API calls | Backend Flow | `$integrate()` calls to SAP, ERP, or third-party APIs |

### Fuuz Data Model Designer (fuuz-schema skill)
**Location:** `/mnt/skills/user/fuuz-schema/SKILL.md` (or `/mnt/skills/organization/fuuz-schema/SKILL.md`)
**When to use:** When designing or understanding data models that screens bind to. The schema skill defines the data layer — model types (master/setup/transactional), field types, relationships, and Fuuz package generation. Consult when:
- Configuring screen `query.model` bindings — understand model names, field types, and available fields
- Building form screens — know which fields are required (`!` suffix), which have default values, and relationship field patterns
- Understanding data relationships — how FK fields (`entityId`) and relation fields (`entity`) connect models
- Planning a new feature — design the data models first (fuuz-schema), then build flows (fuuz-flows), then create screens (fuuz-screens)

### Fuuz Platform Reference (fuuz-platform skill)
**Location:** `/mnt/skills/user/fuuz-platform/SKILL.md` (or `/mnt/skills/organization/fuuz-platform/SKILL.md`)
**When to use:** For shared platform knowledge that spans all skills. Consult for:
- **Platform glossary** — Definitions of Tenant, Enterprise, ModuleGroup, Module, and other core concepts
- **Connector reference** — All 44 built-in connectors with auth types (used in screens via `$integrate()`)
- **Device driver reference** — On-premise device drivers (used in screens via `$executeDeviceFunction()`)
- **System seeded values** — Complete reference for all platform types, settings, and enumerations
- **Cross-skill routing** — Determine which skill to use for any given task
- **Visualization library** — FusionCharts configuration for Chart and Visualization screen components

### Fuuz JSONata Bindings Reference
**Project knowledge file:** `fuuz_jsonata_bindings_skill.md`
All 294 custom JSONata bindings available in the platform. This screen skill includes a curated subset of frontend-relevant bindings below (see "Frontend JSONata Bindings for Screens"). For the full reference including backend-only bindings, consult the bindings skill. Key points:
- **Frontend bindings** work only in Screen Designer expressions (action chains, `__transform`, event handlers).
- **Shared bindings** work in both screens and data flows — use for `$query()`, `$mutate()`, `$moment()`, `$numeral()`, etc.
- **Backend bindings** work only in data flows — never reference in screen JSON.
- Standard JSONata from [docs.jsonata.org](https://docs.jsonata.org/) is fully supported alongside custom bindings.

### Fuuz Visualization Library Reference
**Project knowledge file:** `Fuuz_Visualization_Library_Reference.md`
Complete chart configuration reference for all 80+ FusionCharts chart types used in the platform. Powered by FusionCharts, configured via JSON with `chart` properties and data structure objects (`colorRange`, `dials`, `dataset`, `categories`, `trendlines`, etc.). Two usage modes on screens:
- **Inline Chart component** — Embed a `Chart` component directly on the screen with `chartType`, chart properties, and data bound via `__transform`.
- **Saved Visualization component** — Reference a pre-configured visualization by `visualizationId`. The saved visualization handles its own data sourcing, chart type, and styling. Prefer saved visualizations when the chart is reused across screens or maintained independently.

Chart categories: Gauges & Indicators (angulargauge, hled, bulb, cylinder, thermometer, vled), Single-Series (area, bar, column, line), Multi-Series (msarea, msbar2d, mscolumn2d, msline, multiaxisline), Stacked (stackedcolumn2d, stackedarea2d, stackbar2d), Combination (mscombi2d, mscombidy2d), Scroll (scrollcolumn2d, scrollline2d), Pie & Doughnut (pie, doughnut2d), Scatter & Bubble (scatter, bubble), Real-Time (realtimeline, realtimecolumn), Specialty (gantt, heatmap, radar, treemap, sankey, waterfall2d, candlestick, pareto), Spark (sparkline, sparkcolumn), Spline, Zoom (zoomline, zoomscatter), and Time Series.

### Fuuz GraphQL Syntax & Patterns
**Project knowledge file:** `Fuuz_GraphQL_Syntax___Patterns.md`
Query and mutation patterns for the Fuuz GraphQL API. Covers edges/node pattern, where clause operators, ordering, pagination, aggregations, and mutations. Use when:
- Configuring Table/Form `model` queries and `parameters` (filter predicates).
- Writing action chain mutations (create, update, delete records).
- Writing `$query()` / `$mutate()` calls in screen expressions or flow transforms.
- Building aggregation queries for dashboard metrics.
- Critical syntax reminders: `_isNull` not `_is_null`, unquoted `orderBy` field names, `minimum[0]`/`maximum[0]` unwrap.

### Fuuz System Seeded Values Reference
**Project knowledge file:** `Fuuz_System_Seeded_Values_Reference.md`
Reference for all system-seeded lookup values (modes, statuses, event types, etc.) used in filters, selects, and conditional logic.

### Fuuz Stimulsoft Reports
**Reference file:** `references/stimulsoft-reports.md`
**When to use:** When the user needs printable reports, document designs, or report templates (.mrt files). Stimulsoft is a separate reporting engine from the Screen Designer. Covers: report architecture, band types, expressions, data dictionary, 7 report types, aggregates, formatting, charts, cross-tabs, interactive features, dashboards, export formats, and Fuuz-specific integration guidance. Do NOT confuse Stimulsoft reports with Fuuz screens.

### Fuuz Element Types Reference
**Project knowledge file:** `FUUZ_ELEMENT_TYPES_REFERENCE.md`
Comprehensive documentation of all 75 element types with detailed property tables. Extracted from 1,000+ real elements across 19 production screens. This screen skill incorporates a condensed version in the "Element Types & Properties Reference" section. Consult the full reference for edge-case properties or rarely-used element types.

### Fuuz Admin Dashboard Examples
**Project knowledge files:** `Tenant_App_Admin_Developer_Home.json`, `Enterprise_Admin_Home.json`
Production dashboard screens (172 and 219 components respectively) demonstrating proper styling, pageLoadAction scripting, context-driven data binding, KPI card patterns, and clickable navigation containers. The "Dashboard Screen Patterns" section extracts key patterns from these screens.

---

## Reference Files Directory

This skill includes detailed reference files in the `references/` directory. **Read relevant reference files before generating screens.** The SKILL.md contains the core rules and structure — reference files contain the detailed patterns and examples.

| File | Lines | Content |
|---|---|---|
| `references/element-types.md` | ~463 | All 75 element types with property tables |
| `references/component-patterns.md` | ~1,500+ | CSS rules, Container, Form, DisplayText, RichText, ActionButton, Icon, FlowButton, MenuButton, Table, ResizablePanel, Filter components, **Action vs Actions property naming, Action Chain Data Flow, Form Action Field Configuration, Mutation Visual Builder, Complete CRUD action chain examples, Setup Table Screen Checklist** |
| `references/screen-runtime-context.md` | ~313 | Runtime metadata, components, transform payload/trace/result, practical examples |
| `references/frontend-jsonata-bindings.md` | ~339 | Curated frontend JSONata bindings (navigation, data, formatting, component functions) |
| `references/graphql-patterns.md` | ~120 | Screen-specific GraphQL query/mutation patterns |
| `references/dashboard-patterns.md` | ~488 | Chart/Visualization components, pageLoadAction, KPI cards, dashboard architecture |
| `references/layout-templates.md` | ~507 | Screen layout templates (Dashboard, Table, Form, Mobile), table variants, action chains, responsive patterns |
| `references/common-errors.md` | ~130 | Quick-reference troubleshooting for render errors, data binding issues, action chain/mutation errors, and debugging tips |
| ~~`references/visualization-library.md`~~ | ~2,246 | **Located in `fuuz-platform` skill** (`fuuz-platform/references/visualization-library.md`). Complete FusionCharts reference (80+ chart types). |
| ~~`references/system-seeded-values.md`~~ | ~2,156 | **Located in `fuuz-platform` skill** (`fuuz-platform/references/system-seeded-values.md`). System-seeded lookup values. |
| `references/oee-dashboard-domain.md` | ~520 | OEE/TEEP/MTBF/MTTR formulas, state classifications, time windows, calculation patterns, GraphQL queries |
| `references/stimulsoft-reports.md` | ~877 | Stimulsoft report architecture, band types, expressions, 7+ report types, Fuuz integration |
| `references/screen-essentials.md` | ~293 | Screen quick reference: MainFormContainer standard pattern (EssentialDetails/PrimaryContent/CodeContent/SettingsContent/CustomFieldContent/QuickActions/SaveAction), action pipelines with 10 action types (mutate/query/confirm/notify/navigate/refresh/setContext/openDialog/executeFlow/download), screen context functions (setContext/mergeContext/setContextValue/deleteContextValue), ISA-101 compliance for industrial dashboards, component library reference (AG Grid, FusionCharts, Stimulsoft, Draft.js), design checklist |

**When to read which reference file:**

| Task | Required Reference Files |
|---|---|
| Any screen generation | `element-types.md`, `component-patterns.md` |
| Dashboard screen | + `dashboard-patterns.md`, `screen-runtime-context.md`, `frontend-jsonata-bindings.md` |
| Table/form screen | + `layout-templates.md`, `graphql-patterns.md` |
| Debugging render errors or action chain issues | `common-errors.md` |
| Adding charts/visualizations | `dashboard-patterns.md`, + load **fuuz-platform** for `visualization-library.md` |
| Filter/select with seeded values | Load **fuuz-platform** for `system-seeded-values.md` |
| Writing JSONata expressions | `frontend-jsonata-bindings.md`, `screen-runtime-context.md` |
| Mobile app screen | + `layout-templates.md`, review Mobile App Design Guidelines section in SKILL.md |
| Standard CRUD form screen | `screen-essentials.md` (MainFormContainer pattern), `component-patterns.md` |
| Action button pipelines | `screen-essentials.md` (action pipeline types), `component-patterns.md` |
| Industrial HMI/operator screen | `screen-essentials.md` (ISA-101 compliance), `dashboard-patterns.md` |

---

## Critical Design Principles

These principles come directly from Fuuz platform requirements and must be followed in every screen:

1. **Responsive design** — Use proper flex methodologies so screens adapt to different device sizes. Avoid fixed pixel widths except for small elements; prefer `flexGrow: true` and percentage widths.
2. **Proper naming** — ALL screen elements must have descriptive `elementName` values in `custom`. Use PascalCase (e.g., `OeeMetricsRow`, `FilterPanelHeader`).
3. **Descriptions on display/input/action elements only** — The `description` property must ONLY be added to: `DisplayText`, input elements (`OptionsInput`, `TimeZoneInput`, `Switch`, `TextInput`, etc.), and `ActionButton`. NEVER add `description` to `Container`, `Form`, `Cards`, `Table`, `TableColumn`, `RichText`, or any layout/structural element.
4. **JSONata only in screens** — Screens directly use JSONata for expressions. JavaScript is only available via web flows (data flows of type "Screen").
5. **Dashboards use page load flows/scripts** — Only dashboard-type screens should use `pageLoadAction`, `pageLoadDataFlowIds`, or `screenDataFlowIds`. 
6. **Tables and forms bind to data models** — Table and form screens bind directly to data models via `model` property, with `parameters` and `dataTransform` for data manipulation.
7. **Prefer dialogue screens over nested action forms** — For complex interactions use dialogue screens and web flows. Embedded form objects (action type `"form"`) are acceptable only for simple read-only detail views.
8. **FusionCharts for visualizations** — Use as either inline Chart components on the page or as saved visualizations referenced by ID.
9. **Avoid nested Forms** — Prefer consolidating queries into the `pageLoadAction` and distributing data via `$components.Screen.fn.setContext()`. Inputs can bind to the outer form and read computed values from Screen context. The **only** valid reason for a nested Form is when an element requires a live, independent query subscription that cannot be served by pageLoadAction.
10. **Use Switch for binary settings** — For boolean/binary settings (dark mode, enabled/disabled, yes/no), use `Switch` not `OptionsInput`. Switch supports `label` (right/true label) and `labelLeft` (left/false label) for clear dual-state display.
11. **Mutation payloads are always arrays** — All Fuuz GraphQL mutations expect `[ModelPayloadInput!]!` — the payload must be an array even for single record operations: `{ "payload": [{ "create": data }] }` NOT `{ "payload": { "create": data } }`.
12. **Menu column dataPath must be empty** — Menu columns (format: "menu") must NOT have `dataPath` set to a model field name. Use `"dataPath": ""` or omit it. Setting `"dataPath": "actions"` causes query failures.
13. **Action vs actions property naming** — `ActionButton` uses singular `action` (array of steps). Menu columns use plural `actions` (array of labeled menu items, each containing a singular `action` array). Getting this wrong causes silent failures.
14. **Form action fields need complete properties** — Form fields in action chains (create/edit dialogs) require: `element` (component type), `field` (model field name), `dataPath`, and `query.fields` array. Missing any of these causes forms to not render or not bind data.
15. **Mobile screens: minimal elements** — On mobile, keep inputs, filters, and action elements to the absolute bare minimum. If a user does not need it 80%+ of the time, do not include it. No audio output support; use visual indicators. For bulk scanning, accumulate scans client-side and submit in batch.
16. **Inline web flows supported** — Screens can use inline web flows embedded directly within screen actions, rather than always requiring separate standalone flow artifacts. Use for simple one-off operations; prefer standalone flows for complex or reusable logic.

---

## Critical Required Properties — NEVER Omit

Missing or misconfigured properties are the #1 cause of React render errors in Fuuz screens. The platform will show a blank white screen or a generic React error boundary with no actionable details. **Always include these properties on every element:**

### Mandatory on EVERY Element

| Property | Location | Why Required |
|---|---|---|
| `elementId` (as node key) | Top-level key in design | The component tree uses this as the unique identifier. Missing = orphaned node. |
| `custom.elementName` | `custom` object | Must match the node key exactly. The runtime resolver uses this to find components. |
| `type.resolvedName` | `type` object | Must be an exact match from the component registry. Typos = render crash. |
| `parent` | Top-level | Must reference the parent element's key. Orphaned elements crash the tree walk. |
| `nodes` | Top-level (array) | Must list all direct child element keys. Missing child = invisible element. |
| `description` | `props` | **Only on DisplayText, input elements, and ActionButton.** Purpose documentation. NEVER add to Container, Form, Cards, Table, TableColumn, RichText, or layout elements. |
| `isCanvas` | Top-level | `true` for containers that hold children (Screen, Container, Form, Cards). `false` for leaf elements. |
| `hidden` | Top-level | Always set explicitly (`false` or a dynamic `__transform`). Omitting can cause undefined behavior. |

### Mandatory on Data-Bound Elements (Form, Table, Cards, DisplayText, Inputs)

| Property | Location | Why Required |
|---|---|---|
| `query.api` | `props.query` | `"Application"` or `"System"` — determines which GraphQL endpoint to use. Missing = no data. |
| `query.model` | `props.query` | Data model name. Missing = query fails silently. |
| `query.dataPath` | `props.query` | `"edges[0]"` (single record) or `"edges"` (list). Missing = data exists but nothing renders. |
| `query.fields` | `props.query` | Array of field names. At minimum `["id"]`. Missing = empty response. |
| `dataPath` | `props` (inputs) | Field binding path for form inputs and DisplayText. Missing = value never populates. |
| `formElement` | `props` (inputs) | Parent form's `elementName`. Missing = input is disconnected from form state. |
| `field` | `props` (inputs) | Data model field name for write-back. Missing = saves ignore this input. |

### Transform Properties — NEVER Leave Empty/Null

Empty or null transforms cause React errors. Always close with a valid value:

```jsonc
// ❌ WRONG — These cause React crashes:
"data": null
"data": ""
"onClickActions": null
"__transform": null
"dataTransform": { "transform": null }    // OK only inside query objects
"parameters": null

// ✅ CORRECT — Safe empty values:
"data": {}
"data": ""                                 // empty string OK for data prop
"onClickActions": {}
"__transform": "\"\""                      // empty string expression
"dataTransform": { "transform": null, "remote": true }  // null OK here with remote
"parameters": "{}"                         // empty JSONata object
"bordersInput": {}                         // empty object, not null
"style": {}                                // empty object, not null
"filterPredicate": {}                      // empty object, not null
```

### Form autoLoad Rules

| Scenario | `autoLoad` | Why |
|---|---|---|
| Form bound to data model (table/form screens) | `true` | Load record on mount using query parameters |
| Form used as context display (dashboards) | `false` | Data comes from screen context, not a query |
| Passive form (data from pageLoadAction / context) | `true` | **Must be `true`** with a dummy filter — see below |
| Form with no model binding (layout wrapper) | `false` | No query to execute |

**Dashboard context display forms** (autoLoad: false) still need a valid `query` object with at minimum `api` and `model` — even if the form never queries. Omitting the query object entirely causes errors.

```json
{
  "query": {
    "api": "Application",
    "query": "",
    "dataPath": "",
    "dataTransform": { "transform": null, "remote": true },
    "dataSubscription": { "enabled": false },
    "autoLoad": false,
    "fields": [],
    "disableDataChangeIndicator": false,
    "model": "",
    "filterPredicate": {}
  }
}
```

**Passive forms** (receiving data from `pageLoadAction` / Screen context, no direct model query) **must** keep `autoLoad: true` with a harmless dummy filter. Setting `autoLoad: false` with empty parameters causes platform errors.

```json
{
  "query": {
    "api": "Application",
    "query": null,
    "dataPath": null,
    "dataTransform": { "remote": true },
    "dataSubscription": { "enabled": false },
    "autoLoad": true,
    "parameters": "{\r\n  \"filter\": {\r\n    \"id\": {\r\n      \"_eq\": \"system\"\r\n    }\r\n  },\r\n  \"first\": 1\r\n}",
    "fields": [],
    "disableDataChangeIndicator": true,
    "filterPredicate": {}
  }
}
```

### DisplayText Data Binding — Full Triplet Required

Every DisplayText `data` object binding to `$metadata.*` or `$components.Screen.context.*` **must** include all three properties: `__transform`, `__cacheKey`, and `__remote`. Omitting `__cacheKey` or `__remote` causes unreliable rendering.

```json
// ❌ WRONG — Missing __cacheKey and __remote:
"data": { "__transform": "$metadata.user.email" }

// ✅ CORRECT — Full triplet:
"data": {
  "__transform": "$metadata.user.email",
  "__cacheKey": "email",
  "__remote": false
}
```

**Rule:** `__cacheKey` should be a short unique identifier string. `__remote` is `false` for client-side transforms. For transforms that also use `__dynamicFields`, the full triplet is still required alongside `__dynamicFields`.

### DisplayText Dynamic Label Pattern

When a DisplayText label itself is dynamic (not just the value), the `label` property takes a transform object:

```json
{
  "label": {
    "__transform": "$components.Screen.context.users.active",
    "__dynamicFields": { "payload": null, "context": ["components.Screen.context.users.active"] },
    "__cacheKey": "user-activeNo",
    "__remote": false,
    "__fallbackValue": "0"
  }
}
```

Always include `__fallbackValue` on dynamic labels to prevent null/undefined rendering.

---

## Screen JSON Structure

Every Fuuz screen follows this top-level structure:

```json
[
  {
    "name": "Screen Display Name",
    "tabTitle": "Browser Tab Title",
    "type": "canvas",
    "props": {
      "design": {
        "ROOT": { ... },
        "NodeId1": { ... },
        "NodeId2": { ... }
      }
    }
  }
]
```

**Key rules:**
- Outer wrapper is an array `[{ ... }]`
- `type` must be `"canvas"`
- All component nodes live inside `props.design`
- `ROOT` is the required top-level Screen component
- Node IDs should be descriptive PascalCase names (preferred) or UUIDs

---

## Node Structure

Every node in the design tree follows this pattern:

```json
{
  "NodeId": {
    "type": {
      "resolvedName": "ComponentType"
    },
    "isCanvas": true,
    "props": { ... },
    "displayName": "ComponentType",
    "custom": {
      "elementName": "NodeId"
    },
    "parent": "ParentNodeId",
    "hidden": false,
    "nodes": ["ChildNodeId1", "ChildNodeId2"],
    "linkedNodes": {}
  }
}
```

**CRITICAL rules:**
- `resolvedName` must exactly match a registered Fuuz component name
- `displayName` should match `resolvedName` for consistency
- `parent` must reference a valid node ID (or `null` for ROOT only)
- Every child's key must appear in its parent's `nodes` array
- `custom.elementName` must match the node's key
- Parent-child relationships must be bidirectional and consistent

---

## Component Registry

### Valid Component Types (resolvedName values)

| resolvedName | displayName | isCanvas | Purpose |
|---|---|---|---|
| `Screen` | `Screen` | true | Root component (ROOT node only) |
| `Container` | `Container` | true | Flex/grid layout container |
| `Form` | `Form` | true | Data context wrapper for inputs and display elements |
| `DisplayText` | `DisplayText` | false | Text display with optional data binding |
| `RichText` | `Text` | false | Static rich text (Draft.js content blocks) |
| `ActionButton` | `Action` | false | Clickable button with transformation actions |
| `Table` | `Table` | true | Data grid with query binding |
| `TableColumn` | `Table Column` | false | Column definition within a Table |
| `Chart` | `Chart` | false | FusionCharts visualization wrapper |
| `Visualization` | `Visualization` | false | Saved visualization reference |
| `Image` | `Image` | false | Image display |
| `Icon` | `Icon` | false | FontAwesome icon display |
| `Tabs` | `Tabs` | true | Tabbed container |
| `Dialog` | `Dialog` | true | Modal dialog |
| `ResizablePanelLayout` | `Resizable Panel` | true | Collapsible/resizable sidebar panel |
| `SelectInput` | `Select` | false | Dropdown select input |
| `TextInput` | `Text Input` | false | Text input field |
| `DateRangeInput` | `Date Range` | false | Date range picker |
| `OptionsInput` | `Options` | false | Radio/checkbox option group |
| `WrappedGraphQLPredicate` | `GraphQL Where Input` | false | Advanced GraphQL filter builder |
| `Switch` | `Switch` | false | Toggle switch input |
| `Checkbox` | `Checkbox` | false | Checkbox input |
| `FlowButton` | `Flow` | false | Button that triggers a data flow |
| `MenuButton` | `Menu` | false | Hamburger/dropdown menu button |
| `ScanInput` | `Scan` | false | Barcode/QR code scanner input (mobile) |
| `Markdown` | `Markdown` | false | Markdown content display |
| `JSON` | `JSON` | false | JSON display/editor |
| `CustomFields` | `Custom Fields` | false | Dynamic custom fields component |
| `CustomColumn` | `Custom Column` | true | Custom rendered table column |

**CRITICAL naming traps:**

| WRONG resolvedName | CORRECT resolvedName |
|---|---|
| `Action` | `ActionButton` |
| `Text` | `DisplayText` or `RichText` |
| `Box` | `Container` |
| `ResizableFilterPanel` | `ResizablePanelLayout` |

---

## Screen Element JSONata Context

JSONata expressions in screen elements (transform props, onChange handlers, validation expressions, table column data/style) receive a specific evaluation context. Understanding this context is essential for writing correct screen expressions.

### Root Input (`$`)

The root `$` is the component-specific data, assembled by each component's `mapPropsToPayload`. It varies by component type:
- **Form inputs**: current form field values
- **Table columns**: current row data
- **Action buttons**: data context of the triggering element

### `metadata` -- Platform Metadata

Available in all frontend JSONata expressions. Assembled from Redux state.

| Path | Description |
|---|---|
| `metadata.user` | Current user object -- `.id`, `.firstName`, `.lastName`, `.email`, `.environments`, `.accessTypeId` |
| `metadata.tenant` | Current tenant -- `.id`, `.name`, `.tenantTypeId` |
| `metadata.userTenants` | All tenants the user belongs to -- `[].id`, `[].tenant.id`, `[].tenant.name` |
| `metadata.userRoles` | Roles -- `[].role.id`, `[].role.name`, `[].role.description`, `[].role.homeScreenId` |
| `metadata.screen` | Current screen metadata |
| `metadata.environment` | Environment name (from enterprise) |
| `metadata.platformVersion` | Platform version string |
| `metadata.settings` | Localization: `.DateFormat`, `.TimeFormat`, `.DateTimeFormat`, `.IntegerFormat`, `.FloatFormat`, `.Locale`, `.TimeZone`, `.AlwaysDisplayTimeZones` |
| `metadata.enterprise` | Enterprise -- `.id`, `.name`, `.domain`, `.maintenanceMode`, `.platformVersion`, `.environment` |
| `metadata.urlParameters` | URL path parameters (e.g., `:id` from `/screens/:id`) |
| `metadata.querystring` | URL query string parameters |

### `$components` -- Screen Component Shared State

Hierarchical store of all named screen components. Each component registers:

```
$components.<Name>.data     -- Component's current data
$components.<Name>.fn       -- Component's callable functions
$components.<Name>.context  -- Component-specific context (e.g., row data in tables)
```

#### Table Component (`$components.MyTable`)

| Path | Description |
|---|---|
| `.data` | Current table row data |
| `.fn.loadData({filter, rowLimit})` | Reload data from backend |
| `.fn.search({rowsPerPage})` | Search with filter form |
| `.fn.setRows(array)` | Overwrite all rows |
| `.fn.addRows(array)` | Append rows |
| `.fn.updateRows(array)` | Update existing rows by id |
| `.fn.upsertRows(array)` | Add or update rows by id |
| `.fn.deleteRows(array)` | Delete rows by id |
| `.fn.selectRow(array)` | Select rows |
| `.fn.deselectRow(array)` | Deselect rows |
| `.fn.toggleRowSelection(array)` | Toggle selection |
| `.fn.selectAllRows()` | Select all rows |
| `.fn.deselectAllRows()` | Deselect all rows |
| `.fn.selectAllFiltered()` | Select all filtered rows |
| `.fn.deselectAllFiltered()` | Deselect all filtered rows |
| `.fn.setSelectedRows(array)` | Set exact selection |
| `.fn.setData(data)` | Set table data directly |

#### Form Component (`$components.MyForm`)

| Path | Description |
|---|---|
| `.data` | Current form field values |
| `.fn.setValue(field, value)` | Set a field value |
| `.fn.focus(field)` | Focus a field |
| `.fn.blur(field)` | Blur a field |
| `.fn.loadData()` | Reload form data |
| `.fn.validate()` | Validate and return errors |
| `.fn.save(saveAll?)` | Save the form |
| `.fn.delete()` | Delete form data |
| `.fn.disableField(field)` | Disable a field |
| `.fn.enableField(field)` | Enable a field |
| `.fn.getUpdateMutation()` | Get GraphQL mutation |
| `.fn.getUpdatePayload()` | Get mutation variables |
| `.fn.getVariables()` | Get all form variables |

#### Container Component (`$components.MyContainer`)

| Path | Description |
|---|---|
| `.fn.hide()` | Hide the container |
| `.fn.show()` | Show the container |
| `.fn.toggle()` | Toggle visibility |
| `.fn.resetHidden()` | Reset hidden fields |
| `.fn.collapse()` | Collapse the container |
| `.fn.expand()` | Expand the container |

#### Screen Element (`$components.MyScreen`)

| Path | Description |
|---|---|
| `.context` | Screen context data |
| `.fn.hide()` | Hide |
| `.fn.show()` | Show |
| `.fn.toggle()` | Toggle visibility |
| `.fn.showLoading()` | Show loading indicator |
| `.fn.hideLoading()` | Hide loading indicator |
| `.fn.executePageLoadAction()` | Re-execute page load action |

#### TabBar Component (`$components.MyTabBar`)

| Path | Description |
|---|---|
| `.tabIndex` | Current tab index (number) |
| `.tabs` | Array of tab context objects |
| `.currentTab` | Current tab context object |
| `.fn.setTab(index)` | Switch to tab by index |
| `.fn.enableTab(index)` | Enable a tab |
| `.fn.disableTab(index)` | Disable a tab |
| `.fn.showTab(index)` | Show a tab |
| `.fn.hideTab(index)` | Hide a tab |

#### Widget Element (`$components.MyWidget`)

| Path | Description |
|---|---|
| `.fn.setParams(params)` | Set parameters for the embedded screen |

#### Action / Flow / Split Button (`$components.MyButton`)

| Path | Description |
|---|---|
| `.fn.execute(payload?)` | Execute the button's action with optional payload |

#### Cards Component (`$components.MyCards`)

| Path | Description |
|---|---|
| `.data` | Array of card data objects |
| `.fn.loadData()` | Load/reload card data |
| `.fn.search()` | Search cards using filter form |

### `$appConfig` -- Application Configuration

The tenant's application configuration object. Available in all local (non-remote) frontend evaluations.

### Frontend-Only Functions

These functions are only available in browser context (screen element expressions):

| Function | Description |
|---|---|
| `$navigateTo(pathname, options?)` | Navigate to URL. Options: `{newWindow, replace}` |
| `$navigateBack()` | Navigate back in history |
| `$navigateReload()` | Reload current screen |
| `$showAlertDialog(message, options?)` | Show alert dialog |
| `$showConfirmDialog(message, options?)` | Show confirmation dialog (returns boolean) |
| `$showFormDialog(formProps)` | Show form dialog |
| `$showScreenDialog(props)` | Show screen dialog |
| `$retrieveFileContent(fileId, encoding?)` | Download and decode a file by ID |
| `$addNotificationMessage(title, options?)` | Show UI notification |
| `$writeToClipboard(data)` | Write to clipboard |
| `$readFromClipboard()` | Read from clipboard |

### Transform Props Pattern

Screen element props that accept JSONata use this object structure:

```json
{
  "__transform": "expression or { id: savedTransformId }",
  "__remote": false,
  "__language": "JSONata",
  "__dynamicFields": {
    "payload": ["fieldA", "fieldB"],
    "context": ["metadata.user"]
  },
  "__cacheKey": "unique-key",
  "__refreshInterval": 5000,
  "__fallbackValue": "default"
}
```

| Key | Description |
|---|---|
| `__transform` | JSONata expression string, or `{id}` / `{name}` for saved transform |
| `__remote` | `true` to evaluate server-side (default `false`) |
| `__language` | `"JSONata"` (default) or `"JavaScript"` |
| `__dynamicFields` | Paths in payload/context that the transform depends on. When values at these paths change, the transform re-executes and the component re-renders. Shape: `{ payload: ["data.createdByUser.id"], context: [] }`. Set to `false` to evaluate once only. |
| `__cacheKey` | Unique key for storing the transform result, scoped to the element. |
| `__refreshInterval` | Milliseconds between automatic re-evaluations |
| `__fallbackValue` | Default value while transform is pending |

---

## Critical JSONata Gotchas for Screen Expressions

These are the most common JSONata mistakes when writing screen element expressions. Review before writing any non-trivial expression.

1. **No `!` operator** -- Use `$not(expr)` function instead. `$not($exists(x))` not `!$exists(x)`.
2. **`$not()` is a function**, not an operator -- Always call it as `$not(expression)`.
3. **Ternary vs Elvis vs Null Coalescing** -- `expr ? truthy : falsy` (ternary), `expr ?: default` (elvis: default when falsy), `expr ?? default` (null coalescing: default when null/undefined only). These are three distinct operators.
4. **String concatenation uses `&`** -- Not `+`. Write `"hello" & " " & "world"`.
5. **Comparison uses `=` not `==`** -- Single equals for comparison. No `===` or `==` in JSONata.
6. **`in` operator for membership** -- `x in [1,2,3]` not `[1,2,3].includes(x)`.
7. **`$round` uses banker's rounding** -- Rounds to nearest even on .5 boundary. `$round(2.5)` = 2, `$round(3.5)` = 4.
8. **`$now()` and `$millis()` are frozen** -- Return same value for entire evaluation (consistent timestamps).
9. **No explicit return** -- Last expression evaluated is the result.
10. **`undefined` propagates** -- Accessing a missing field returns `undefined`, which silently drops from results rather than erroring.
11. **Singleton array equivalence** -- JSONata treats `[42]` and `42` as interchangeable in path expressions.

---


## Common Errors Quick Reference

See `references/common-errors.md` for the full troubleshooting guide. The most critical rules:

| Symptom | Cause | Fix |
|---|---|---|
| Blank white screen / React error | Missing required property (elementId, type, parent, nodes) | Check Output Checklist structure requirements |
| "Cannot read property of null" | Transform property set to `null` instead of `{}` | Use `{}` for empty style/data/bordersInput/filterPredicate |
| Form crashes on mount | Missing `query` object (even with autoLoad: false) | Always include complete query object with api + model |
| Data binding shows nothing | Missing `dataPath` on form input or DisplayText | Set dataPath to field name |
| DisplayText shows "[object Object]" | Dynamic label missing `__fallbackValue` | Add `__fallbackValue` to label transform object |

### Common Screen Errors & Solutions

| Error | Cause | Fix |
|---|---|---|
| "Field 'X' doesn't exist on type" | Column or query references non-existent field | Remove field from `query.fields` or table column |
| "Syntax Error: Unexpected Name" | Wrong mutation format | Check mutation name matches model's auto-generated mutation |
| "Variable not provided" | Payload structure mismatch | Ensure payload is wrapped in array `[{create: data}]` |
| Table shows spinner forever | Query failing silently | Check browser console for GraphQL errors |
| Button spins but nothing happens | Action chain error | Check each step's pre-transform; verify mutation name |
| Form fields don't populate on edit | Missing pre-transform on form step | Add `transforms.pre: "data"` to form action |

---

## Menu Column Critical Fix

Menu columns should NOT have a `dataPath` that maps to a data field. Using `"dataPath": "actions"` causes query failures because "actions" doesn't exist on the data model.

```json
// ❌ WRONG — causes "Field 'actions' doesn't exist on type"
"MenuColumn": {
  "props": {
    "dataPath": "actions",
    "format": "menu"
  }
}

// ✅ CORRECT
"MenuColumn": {
  "props": {
    "dataPath": "",
    "format": "menu",
    "width": "50px",
    "actions": [...]
  }
}
```

**Rule:** Menu columns generate their own actions and should not bind to a data field. Every column `dataPath` must have a corresponding field in `query.fields` array, **except** menu columns.

---

## Mutation Payload Structure

Fuuz mutations expect array payloads — `[ModelNameCreatePayloadInput!]!` — even for single record operations.

**Create mutation pre-transform:**
```json
{
  "transforms": {
    "pre": "{\n  \"payload\": [\n    {\n      \"create\": data\n    }\n  ]\n}"
  }
}
```

**Update mutation pre-transform:**
```json
{
  "transforms": {
    "pre": "{\n  \"payload\": [\n    {\n      \"where\": { \"id\": data.id },\n      \"update\": data\n    }\n  ]\n}"
  }
}
```

**Delete mutation pre-transform:**
```json
{
  "transforms": {
    "pre": "{\n  \"payload\": [\n    {\n      \"where\": { \"id\": data.id }\n    }\n  ]\n}"
  }
}
```

**Key rule:** The payload must always be an **array** `[{create: data}]`, not just `{create: data}`.

---

## Action vs Actions Property

Different components use different property names for actions:

| Component | Property | Pattern |
|---|---|---|
| `ActionButton` | `action` (singular) | `"action": [{ "type": "form" }, { "type": "mutation" }]` |
| `TableColumn` menu | `actions` (plural) | `"actions": [{ "label": "Edit", "action": [...] }]` |
| `FlowButton` | `action` (singular) | `"action": [{ "type": "executeFlow" }]` |
| `MenuButton` | `actions` (plural) | `"actions": [{ "label": "Option", "action": [...] }]` |

**ActionButton** uses singular `action` — an array of sequential steps.
**Menu-style components** use plural `actions` — an array of menu items, each with its own nested `action` array.

---

## Form Action Field Configuration

Form actions within action chains require complete field configuration:

```json
{
  "type": "form",
  "submitIcon": "plus",
  "submitLabel": "Create",
  "title": "Create Record",
  "fields": [
    {
      "label": "Code",
      "element": "TextInput",
      "type": "text",
      "dataPath": "code",
      "field": "code",
      "description": "Field help text",
      "validation": { "required": true },
      "query": { "fields": ["code"] }
    }
  ]
}
```

**Required field properties:**
| Property | Required | Description |
|---|---|---|
| `label` | Yes | Display label |
| `element` | Yes | Component type: `TextInput`, `IntegerInput`, `FloatInput`, `SelectInput`, `Switch`, `DateTimeInput` |
| `type` | Yes | Data type: `text`, `integer`, `float`, `select`, `boolean`, `datetime` |
| `dataPath` | Yes | Field binding path |
| `field` | Yes | Must match dataPath for proper binding |
| `query.fields` | Yes | Array of field names for data binding |
| `validation` | No | Validation rules like `{ "required": true }` |
| `description` | No | Help text shown below the field |

---

## Action Chain Data Flow

Action arrays execute sequentially. Each step's output becomes available to subsequent steps:

```json
"action": [
  { "type": "form" },           // Step 1: User fills form → outputs to 'data'
  { "type": "mutation" },       // Step 2: Uses 'data' → outputs to 'mutation'
  { "type": "transformation" }  // Step 3: Can reference 'mutation' result
]
```

**Variable availability at each step:**
| Step | Available Variables | Common Use |
|---|---|---|
| `form` | None (first step) | Collect user input |
| `mutation` | `data` (form output) | Send data to server |
| `transformation` | `data`, `mutation` | Refresh table: `$components.TableName.fn.search()` |

**Pre-transform rules:**
- Form actions (step 1): No pre-transform needed — form data automatically becomes `data`
- Mutation actions (step 2): Pre-transform shapes `data` into mutation payload format
- Transformation actions (step 3): Pre-transform can reference `mutation` result

---

## Setup Table CRUD Checklist

When building a setup/reference table screen with Create/Edit/Delete:

- [ ] Table `query.fields` array contains NO "actions" field
- [ ] MenuColumn `dataPath` is empty string `""` or omitted
- [ ] Create button action chain:
  1. `form` action (no pre-transform)
  2. `mutation` action (pre-transform: `{"payload": [{"create": data}]}`)
  3. `transformation` action (`$components.TableName.fn.search()`)
- [ ] Edit button action chain:
  1. `form` action (with `transforms.pre: "data"` to populate form)
  2. `mutation` action (pre-transform with `where` + `update`)
  3. `transformation` action (table refresh)
- [ ] Delete button action chain:
  1. `mutation` action (pre-transform with `where`)
  2. `transformation` action (table refresh)

---

## Mobile App Design Guidelines

When designing screens intended for mobile devices (phones, tablets, handheld scanners), follow these additional constraints beyond the standard mobile template:

### Reduced Real Estate
- Follow standard mobile UI guidelines: minimal chrome, large touch targets, single-column layouts
- Keep inputs, filters, and action elements to the absolute bare minimum -- if a user does not need an element 80%+ of the time, do not include it
- Avoid large tables or data grids on mobile; prefer card-based lists or summary views
- Avoid excessive action buttons; consolidate actions into a single MenuButton where possible

### No Audio Output
- Fuuz mobile apps do not support audio output
- Use visual indicators instead of sounds (color changes, icons, status text, toast notifications via `$addNotificationMessage()`)
- This is especially important for noisy factory/warehouse environments where audio would be unreliable anyway

### Bulk Scanning Pattern
- For bulk scan workflows (e.g., scanning multiple inventory items): add each scan result to a **client-side list** first, then submit the full list when the user is done
- Do NOT submit each scan individually -- this causes excessive network calls and poor UX on unreliable connections
- Use `$components.Screen.fn.setContext()` to accumulate scanned items in screen context, then submit the batch via `$executeFlow()` or mutation

### Full Screen / Kiosk Mode
- Use Fuuz full screen mode for dedicated station screens
- Use Chrome kiosk mode (`--kiosk` flag) for locked-down terminal deployments
- Install as a Chrome App (Add to Home Screen / PWA) for a native-like mobile experience

### Mobile Screen Sizing Reference
- Input height: `120px` (prevents layout shift from validation errors)
- Font sizes: `dataFontSize: 24`, `labelFontSize: 15`
- Input variant: `"outlined"` for visual clarity
- Footer height: `60px` with FlowButton components
- Use `justifyContent: "space-between"` on outer container to anchor footer

---

## Inline Web Flows

Screens can now use **inline web flows** -- embedded directly within screen actions rather than requiring separate standalone flows. This means action chains on buttons and menu items can invoke flow logic inline without the user needing to create, name, and manage a separate flow artifact.

This is useful for:
- Simple data transformations that do not warrant a full standalone flow
- Quick one-off operations triggered by action buttons
- Reducing the number of flow artifacts in the application

For complex or reusable logic, standalone flows (referenced via `$executeFlow()` or `pageLoadDataFlowIds`) are still preferred. See the fuuz-flows skill for full flow creation guidance.

---

## Full JSON Output Required

**ALWAYS output the complete screen JSON.** Fuuz screens cannot be partially updated via import — the platform replaces the entire screen definition on import. There is no merge or patch mechanism. This means:

- **Never output "just the changed section"** — The user needs the full `[{ "name": "...", "type": "canvas", "props": { "design": { ... } } }]` every time.
- **Never say "update the X section with..."** — Output the entire JSON file with the change incorporated.
- **For edits to existing screens** — Take the user's existing JSON, apply the requested changes, and output the complete modified JSON.
- **Save as `.json` file** — Always save output as a downloadable `.json` file the user can import directly.

### How Users Import Screen JSON

There are two methods to get the JSON into Fuuz:

**Method 1: Application Designer Import (recommended for new screens)**
1. Go to **Application Designer** (Configuration → Application Designer)
2. Open or create a screen
3. Use **File → Import JSON** to upload the `.json` file
4. The screen renders immediately in the designer canvas

**Method 2: Screen Version Paste (for updating existing screens)**
1. Go to **Screens** list (Configuration → Screens)
2. Select the target screen (or create a new one)
3. Go to the **Versions** tab
4. Either create a new version or edit an existing version
5. Paste the full JSON into the version text editor
6. Save the version

Both methods require the complete JSON structure — partial JSON will fail or overwrite the screen with only the provided elements.

### Exception: Single Element Definition Editing

If the user only needs to update a **single element** (not nested children), you can provide just that element's definition JSON. The user can apply it in the Screen Designer:

1. Click the element in the designer canvas
2. Click the **edit icon** at the top of the property panel
3. Select **"Definition"**
4. Paste the updated element JSON

**Limitations:** This only works for a single, flat element — it does not support nesting. If the element contains children (e.g., a Container with child elements), you must output the full screen JSON instead.

When providing a single element definition, output the node's complete definition object:
```json
{
  "type": { "resolvedName": "DisplayText" },
  "isCanvas": false,
  "props": { ... },
  "displayName": "DisplayText",
  "custom": { "elementName": "MyDisplayText" },
  "parent": "ParentElementName",
  "hidden": false,
  "nodes": [],
  "linkedNodes": {}
}
```

---

---
## Output Checklist

When generating a Fuuz screen, verify:

**Structure & Identity (NEVER omit):**
- ✅ Outer wrapper is `[{ ... }]` array with `name`, `tabTitle`, `type: "canvas"`
- ✅ Every element has `custom.elementName` matching its node key
- ✅ Every element has `type.resolvedName` from the valid component registry
- ✅ Every element has `parent` referencing its parent (null only for ROOT)
- ✅ Every element has `nodes` array listing direct children (empty `[]` for leaf elements)
- ✅ Every element has `isCanvas` set explicitly (true for containers, false for leaves)
- ✅ Every element has `hidden` set explicitly (false or dynamic transform)
- ✅ `description` populated on DisplayText, input elements, and ActionButton ONLY (never on Container, Form, Cards, Table, TableColumn, RichText, or layout elements)

**ROOT Configuration:**
- ✅ ROOT has `flowsDelay: 0`, `removeOuterMargin: true`, `background: "paper"`, `forceFullscreen: true`
- ✅ ROOT has `intervals: []` (even if empty)
- ✅ `elementCounts` in ROOT custom reflects actual component counts

**Data Binding (NEVER omit on data elements):**
- ✅ Every Form has a complete `query` object with `api`, `model`, `dataPath`, `fields`, `filterPredicate: {}`
- ✅ Every Table/Cards has `query` with `dataPath: "edges"` and `fields: ["id"]` minimum
- ✅ Every DisplayText is inside a Form and has `formElement` set
- ✅ Every form input has `formElement`, `dataPath`, and `field` set
- ✅ All `__dynamicFields.context` arrays are complete and accurate

**Empty Value Safety (NEVER use null):**
- ✅ `data` is `{}` not `null` when unused
- ✅ `style` is `{}` not `null` when unused
- ✅ `bordersInput` is `{}` not `null` when unused
- ✅ `filterPredicate` is `{}` not `null` when unused
- ✅ `parameters` is `"{}"` not `null` when unused
- ✅ `onClickActions` is `{}` not `null` when `onClickTransform: false`

**Layout & Styling:**
- ✅ Gap values are strings with units: `"16px"`
- ✅ Fixed widths include units where needed: `"280px"`
- ✅ All parent ↔ child relationships are bidirectional
- ✅ Node IDs are descriptive PascalCase
- ✅ Layout is responsive (flexGrow, flexWrap where appropriate)
- ✅ BrandBandSeparator present (4px gradient, shadow:true)

**Screen Type Specific:**
- ✅ Dashboard screens use `pageLoadAction` with `$components.Screen.fn.setContext()`
- ✅ Table/form screens use model binding with `autoLoad: true`
- ✅ Dashboard forms use `autoLoad: false`
- ✅ Table screens include standard audit columns
- ✅ Mobile inputs use `height:120px`, `dataFontSize:24`, `variant:outlined`
- ✅ JSON is valid and parseable

---

## Screen Architecture Guide

This section helps determine the full set of artifacts needed for a screen request. Fuuz screens rarely exist in isolation — functional screens typically require companion flows, GraphQL queries, and JSONata expressions working together.

### Architecture Decision Matrix

| Screen Request | Screen Skill | Flows Needed? | GraphQL Needed? | Visualization Needed? |
|---|---|---|---|---|
| Simple CRUD table (master/setup) | ✅ Table template | ❌ Not usually — Table binds to model directly | ✅ Auto-generated by table model binding | ❌ |
| History/audit table | ✅ History template | ❌ | ✅ Auto-generated | ❌ |
| Form screen (view/edit record) | ✅ Form template | ❌ Not usually — Form binds to model | ✅ Auto-generated | ❌ |
| Dashboard with KPI metrics | ✅ Dashboard template | ✅ **Web Flow (Screen type)** for page load data | ✅ Aggregation queries in flow | ✅ Gauges + charts |
| HMI / Control Panel | ✅ Dashboard or custom | ✅ **Web Flow** for real-time data, state transitions | ✅ Telemetry + state queries | ✅ Real-time gauges |
| Mobile app screen | ✅ Mobile template | ✅ **Web Flow** for form submission, scan processing | ✅ Mutations in flow | ❌ Usually |
| Screen with custom actions | ✅ Any template | ✅ **Web Flow (Request/Response)** per `$executeFlow()` | ✅ Queries/mutations in flow | ❌ |
| Device integration screen | ✅ Any template | ✅ **Gateway Flow** for device communication | ✅ In flow transforms | ❌ |

### How the Pieces Connect

```
┌─────────────────────────────────────────────────────────┐
│  SCREEN (this skill)                                     │
│  Screen JSON → Layout, Components, Action Chains         │
│                                                          │
│  Uses:                                                   │
│  ├── JSONata bindings (Frontend + Shared)                │
│  │   ├── $components.*.fn.*() — component interaction    │
│  │   ├── $navigateTo(), $addNotificationMessage() — UI   │
│  │   ├── $query(), $mutate() — inline GraphQL            │
│  │   ├── $executeFlow() — call companion web flows       │
│  │   ├── $moment(), $numeral() — formatting              │
│  │   └── $state.context.* — read flow-provided data      │
│  │                                                       │
│  ├── GraphQL (via model binding or $query/$mutate)       │
│  │   ├── Table/Form auto-query from model + fields       │
│  │   ├── Action chain mutations (create/update/delete)   │
│  │   └── Inline $query() for lookups and aggregations    │
│  │                                                       │
│  └── Visualizations                                      │
│      ├── Inline Chart component (chartType + __transform)│
│      └── Saved Visualization component (visualizationId) │
├─────────────────────────────────────────────────────────┤
│  FLOWS (fuuz-flows skill)                                │
│  Web Flows (Screen type) → populate $state.context       │
│  Web Flows (Request/Response) → called by $executeFlow() │
│  Backend Flows → server-side processing                  │
│  Gateway Flows → device communication                    │
│                                                          │
│  Uses:                                                   │
│  ├── JSONata bindings (Backend + Shared)                 │
│  ├── GraphQL queries/mutations (via $query/$mutate)      │
│  └── External integrations ($integrate, $http)           │
└─────────────────────────────────────────────────────────┘
```

### When to Generate Flows Alongside Screens

**Always generate companion flows** (use the fuuz-flows skill) when:
1. The screen has `pageLoadDataFlowIds` or `screenDataFlowIds` — needs a web flow (Screen type) to populate `$state.context.*`.
2. Any ActionButton or FlowButton calls `$executeFlow(flowId, payload)` — needs a web flow (Request/Response type).
3. The screen requires real-time data refresh — needs a web flow with interval scheduling.
4. The screen involves device communication — needs a gateway flow.
5. The screen has complex business logic (validation rules, multi-step transactions, computed values) — better in flows than inline JSONata.

**Flows are generally NOT needed** when:
1. The screen is a simple CRUD table bound to a data model.
2. The screen is a simple form bound to a data model with standard save/delete actions.
3. All interactivity is handled by built-in component functions (`$components.Table1.fn.search()`, `$components.Form1.fn.save()`).

### Screen Complexity Levels

**Level 1 — Static Layout (Screen skill only)**
Simple table or form screens. Model-bound, filter panel, standard CRUD actions. No flows needed.

**Level 2 — Enhanced Screen (Screen skill + inline GraphQL/JSONata)**
Table/form screens with custom action chains, inline `$query()` calls for dynamic selects, conditional visibility, custom transforms. Still no flows needed.

**Level 3 — Data-Driven Screen (Screen skill + Web Flows)**
Dashboards, HMIs, control panels. Page load flows aggregate data into `$state.context.*`. Charts/visualizations display computed metrics. **Use the fuuz-flows skill** to generate companion web flows.

**Level 4 — Full Application (Screen skill + Web Flows + Gateway Flows + Backend Flows)**
Mobile apps, multi-screen workflows, device-integrated HMIs. Multiple flow types, external integrations, real-time updates. **Use the fuuz-flows skill** for all flow artifacts.

---

## Working with This Skill

### Step-by-step process for generating a screen:

1. **Determine screen type** — Dashboard, table, form, mobile, or hybrid
2. **Identify data sources** — Data models, web flows, screen context
3. **Determine if flows are needed** — See "When Flows Are Required" below
4. **Plan layout** — Sketch the component tree using the templates above
5. **Generate JSON** — Build from ROOT down, maintaining parent-child integrity
6. **Validate** — Run through the output checklist
7. **Output full JSON as `.json` file** — Always output the COMPLETE screen JSON as a downloadable file. Never output partial snippets — the user needs the full `[{ "name": "...", "type": "canvas", "props": { "design": { ... } } }]` to import via Application Designer or paste into a Screen Version.

### When flows are required (read fuuz-flows skill):

Screens often require companion web flows or backend flows for full functionality. **Read the fuuz-flows SKILL.md** (`/mnt/skills/user/fuuz-flows/SKILL.md`) before generating flows.

| Screen Type | Flow Requirement |
|---|---|
| **Simple table/form (CRUD)** | No flows needed — Table/Form bind directly to data model, action chains handle create/update/delete. |
| **Dashboard with KPIs** | Web flow required — Page load flow fetches, aggregates, and transforms data into `$state.context.*`. |
| **HMI / Control Panel** | Web flows + backend flows — Real-time data aggregation, state machine logic, device communication, interval refresh. |
| **Mobile app** | Web flows — Form submission logic, scan/barcode processing, inventory transactions, validation beyond simple form rules. |
| **Screen with custom buttons** | Web flow per button — Any FlowButton or ActionButton calling `$executeFlow()` needs a request/response flow. |
| **Screen with device integration** | Gateway flow — `$executeDeviceFunction()` / `$executeDeviceGatewayFunction()` require configured device gateway flows. |
| **Screen with external API calls** | Backend flow — `$integrate()` calls require connection configuration and may need a flow for orchestration. |

### For dashboard screens:
- Build the page load flow/script first (or reference existing web flows)
- Use `$state.context.*` bindings in DisplayText components
- Charts can bind to context data via `__transform`
- Use `intervals` for auto-refresh
- Saved Visualizations are preferred for reusable charts; inline Charts for one-off indicators

### For table screens:
- Identify the data model and its fields (including relationships)
- Set up `fields` array on the table query to include all needed paths
- Use `dataTransform` for computed columns
- Configure filter panel with appropriate inputs per filterable field
- Set `filterFormName` on the Table to link to the filter Form
- Use `$query()` or `$mutate()` in action chains for operations beyond standard CRUD

### For form screens:
- Bind Form to data model with `autoLoad: true`
- Use `parameters` for route-based filtering (e.g., `"filter": {"id": {"_eq": "routeParam.id"}}`)
- Include Save and Cancel action buttons
- Group related fields into section containers
- **Standard Page (model detail/edit):** Use sidebar (280px) with EssentialDetails, RelatedModels, QuickActions sections
- **Dialogue Page:** No sidebar — full-width form content only
- **Profile/Settings screens:** Use inline sections with row-based layouts (side-by-side cards), **NOT** the 280px sidebar pattern. The sidebar is for admin dashboards and model detail screens only.
- Header always includes BrandBandSeparator below action bar
- Main content uses branded scrollbar: `"scrollbar-color": "#5b30df #e2e8f0"`

### For mobile screens:
- Use `justifyContent: "space-between"` on outer container to anchor footer
- Fixed-height inputs (120px) to prevent layout shift from validation errors
- Large font sizes: `dataFontSize: 24`, `labelFontSize: 15`
- Use `variant: "outlined"` on inputs for visual clarity
- FlowButton components for submit/cancel in footer — these typically call `$executeFlow()` requiring web flows
- MenuButton for navigation (hamburger menu)
- SelectInput should include `additionalFilter` to restrict to active records

### For HMI / control panel screens:
- Start by designing the web flows for data aggregation (read fuuz-flows skill)
- Screen uses `screenDataFlowIds` and `intervals` for real-time refresh
- Display live values with DisplayText bound to `$state.context.*`
- Use inline Chart components (gauges, real-time lines) for live indicators
- ActionButtons with `$executeFlow()` for operator commands (start/stop, mode change)
- Device bindings (`$executeDeviceFunction`) for PLC/gateway reads and writes
- Status containers use dynamic `customBackground` transforms for visual state indication

### Architecture decision guide:

**"Can I do this with just a screen, or do I need a flow?"**
- Reading/displaying model data → Screen only (Table/Form model binding)
- CRUD operations → Screen only (action chains with mutation steps)
- Computed values, aggregations, multi-model joins → Web flow needed
- Real-time data, auto-refresh → Web flow + `screenDataFlowIds` + `intervals`
- External API calls → Backend flow + `$integrate()` or `$executeFlow()`
- Device/PLC communication → Gateway flow + `$executeDeviceFunction()`
- Complex validation or business logic → Web flow called from `$executeFlow()`

### elementCounts tracking:
The ROOT `custom.elementCounts` object should count components using their **displayName** as the key:
- `Container`, `Action` (for ActionButton), `Resizable Panel`, `Tabs`, `Form`, `Select` (for SelectInput)
- `Switch`, `Checkbox`, `Flow` (for FlowButton), `Menu` (for MenuButton)
- `Table`, `Table Column`, `Text` (for RichText), `Display` (for DisplayText)
- `Icon`, `Scan` (for ScanInput), `GraphQL Builder`, `GraphQL Where Input`
