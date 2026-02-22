---
name: fuuz-screens-v1
description: Generate Fuuz Industrial Operations platform screen JSON for dashboards, tables, forms, and reports. Use when requests mention "fuuz screen", "screen designer", "dashboard screen", "table screen", "form screen", "fuuz UI", "screen JSON", "canvas design", or building user interfaces for Fuuz applications. Covers all 59 screen element types with full property documentation (Container, Form, Table, DisplayText, ActionButton, Chart, Icon, Tabs, Dialog, ResizablePanel, and 49 more), layout patterns, data binding with JSONata, FusionCharts visualizations, FontAwesome icons, and responsive flex design. Includes element types reference with required properties to prevent React render errors, dashboard pattern examples from production admin screens, frontend JSONata binding reference, GraphQL query patterns, visualization library reference, and cross-references the fuuz-flows skill for web/backend flows.
---

# Fuuz Screen Designer Skill

## Overview

This skill generates complete, importable Fuuz screen JSON files for the Fuuz Industrial Operations Platform (fuuz.com). Fuuz screens are defined as JSON with a craft.js-based component tree, rendered as React components by the platform's component resolver. Screens are built within the **Application Designer** interface.

**Technology stack:** craft.js (component framework), FusionCharts (visualizations), FontAwesome (icons), Material UI (typography/theming), JSONata (expressions).

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

### Fuuz Element Types Reference
**Project knowledge file:** `FUUZ_ELEMENT_TYPES_REFERENCE.md`
Comprehensive documentation of all 59 observed element types with detailed property tables. Extracted from 1,000+ real elements across 19 production screens. This screen skill incorporates a condensed version in the "Element Types & Properties Reference" section. Consult the full reference for edge-case properties or rarely-used element types.

### Fuuz Admin Dashboard Examples
**Project knowledge files:** `Tenant_App_Admin_Developer_Home.json`, `Enterprise_Admin_Home.json`
Production dashboard screens (172 and 219 components respectively) demonstrating proper styling, pageLoadAction scripting, context-driven data binding, KPI card patterns, and clickable navigation containers. The "Dashboard Screen Patterns" section extracts key patterns from these screens.

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

## Element Types & Properties Reference

This section documents all 59 observed element types with their configuration properties. Source: extracted from 1,000+ real screen elements across 19 production screens. Use `configuration` (or `definition.props`) as the canonical property set.

### Common Properties (All Elements)

Every element inherits these base properties:

| Property | Type | Default | Description |
|---|---|---|---|
| `width` | string | `"100%"` | Element width (`"100%"`, `"auto"`, `"310"`, `"280px"`) |
| `height` | string | `"auto"` | Element height (`"auto"`, `"100%"`, `"110"`, `"40px"`) |
| `padding` | number | `0` | Inner padding in pixels |
| `margin` | number | `0` | Outer margin in pixels |
| `hidden` | boolean/object | `false` | Visibility (static boolean or dynamic `__transform`) |
| `description` | string | — | Purpose documentation. **Only add to:** DisplayText, input elements, ActionButton. **NEVER add to:** Container, Form, Cards, Table, TableColumn, RichText, or layout/structural elements. |

### Common Form Input Properties

All form inputs (TextInput, SelectInput, DateInput, Switch, Checkbox, etc.) share:

| Property | Type | Default | Description |
|---|---|---|---|
| `label` | string | — | Field label displayed to user |
| `formElement` | string | — | **Required.** Parent form's `elementName` |
| `dataPath` | string | — | **Required.** Data model field path for binding |
| `field` | string | — | **Required.** Field name in the data model |
| `validation` | object | `{"required": false}` | Validation rules |
| `variant` | string | `"standard"` | MUI variant (`"standard"`, `"outlined"`) |
| `visible` | boolean | `true` | Visibility flag |
| `type` | string | — | Input type identifier (e.g., `"text"`, `"select"`, `"date"`) |

### Layout Elements

#### Screen (ROOT)

Root-level element. Always `isCanvas: true`. Key props:

| Property | Type | Values | Description |
|---|---|---|---|
| `flexDirection` | string | `"column"` | Primary layout axis |
| `forceFullscreen` | boolean | `true` | Force fullscreen mode |
| `removeOuterMargin` | boolean | `true` | Remove default outer margin |
| `background` | string | `"paper"` | Background theme token |
| `pageLoadAction` | string | — | JSONata expression executed on screen load |
| `intervals` | array | `[]` | Timed interval actions for auto-refresh |
| `flowsDelay` | number | `0` | Delay before executing flows (ms) |
| `data` | object | — | Static data object for screen context |

#### Container (453 observed instances — most common element)

Primary layout building block with full flexbox control:

| Property | Type | Values | Description |
|---|---|---|---|
| `flexDirection` | string | `"column"`, `"row"` | Layout direction |
| `flexWrap` | boolean | `false`, `true` | Wrap children to next line |
| `flexGrow` | boolean | `false`, `true` | Grow to fill available space |
| `flexShrink` | boolean | `false` | Shrink if needed |
| `justifyContent` | string | `"flex-start"`, `"center"`, `"flex-end"`, `"space-between"`, `"space-around"`, `"space-evenly"` | Main-axis alignment |
| `alignItems` | string | `"stretch"`, `"center"`, `"flex-start"`, `"flex-end"` | Cross-axis alignment |
| `justifyItems` | string | `"stretch"`, `"center"`, `"start"`, `"end"` | Grid item alignment |
| `gap` | string | `"0px"` through `"20px"` | Gap between children (**must be string with units**) |
| `background` | string | `"default"`, `"paper"`, `"light"`, `"custom"` | Background theme token |
| `customBackground` | string | — | CSS color when `background` = `"custom"` |
| `shadow` | boolean | `false`, `true` | Drop shadow |
| `borderRadius` | number | `0`–`8` | Corner radius (px) |
| `collapsible` | boolean | `false` | Can be collapsed |
| `collapsedByDefault` | boolean | `false` | Start collapsed |
| `onClickTransform` | boolean | `false` | Enable click handler |
| `onClickActions` | object | — | JSONata transform on click |
| `bordersInput` | object | `{}` | Border configuration |
| `style` | object | `{}` | Custom CSS styles |
| `data` | object | `{}` | Dynamic data binding |

**bordersInput structure:**
```json
{ "borderMode": "single", "border": "1px solid rgba(148, 163, 184, 0.3)" }
```

**onClickActions structure:**
```json
{ "__transform": "$navigateTo('https://example.com', {'newWindow': true, 'replace': false})" }
```

#### TabBar

Tabbed navigation container:

| Property | Type | Values | Description |
|---|---|---|---|
| `tabs` | array | `[{"tabId": 0, "tabTitle": "Label"}]` | Tab definitions |
| `defaultTabIndex` | string | `"0"` | Initially selected tab |
| `orientation` | string | `"horizontal"` | Tab bar orientation |
| `tabWidth` | string | `"100"`, `"160"` | Individual tab width (px) |
| `tabActiveColor` | string | `"primary"`, `"secondary"` | Active tab color |

#### ResizablePanelLayout

Split-panel with draggable divider:

| Property | Type | Values | Description |
|---|---|---|---|
| `axis` | boolean | `false` | Panel axis direction |
| `handle` | string | `"right"` | Handle position |
| `defaultSize` | string | `"400"` | Default panel size (px) |
| `minSize` | string | `"100"` | Minimum size (px) |
| `maxSize` | string | `"800"` | Maximum size (px) |

### Display Elements

#### DisplayText (106 observed instances)

Read-only text with optional data binding. **Must be inside a Form.**

| Property | Type | Values | Description |
|---|---|---|---|
| `label` | string/object | — | Static label or dynamic `__transform` object |
| `fontSize` | string | `"body1"`, `"caption"`, `"h3"`, `"h4"`, `"h5"`, `"h6"` | Value font size |
| `labelFontSize` | string | `"body1"`, `"caption"`, `"subtitle1"`, `"subtitle2"` | Label font size |
| `fontColor` | string | CSS color | Value text color |
| `labelFontColor` | string | `"#64748b"` | Label text color |
| `justifyContent` | string | `"start"`, `"center"`, `"end"`, `"space-between"` | Horizontal alignment |
| `format` | string | `"datetime"`, `"integer"` | Value format |
| `formatString` | string | `"0"`, `"0,0"` | Number format string (numeral.js) |
| `text` | string | — | Static text (overrides data binding) |
| `data` | object | — | Dynamic data binding (`__transform`) |
| `labelPosition` | object | `{"id": "null", "label": "None"}` | Label placement |
| `linkTarget` | object | — | Make value a clickable link |

#### RichText (135 observed instances — most common for static text)

Static rich text using Draft.js content state:

```json
{
  "content": {
    "blocks": [
      {
        "key": "unique-key",
        "text": "Display Text Here",
        "type": "unstyled",
        "depth": 0,
        "inlineStyleRanges": [
          { "offset": 0, "length": 17, "style": "fontfamily-Roboto" },
          { "offset": 0, "length": 17, "style": "fontsize-14" },
          { "offset": 0, "length": 17, "style": "BOLD" }
        ],
        "entityRanges": [],
        "data": { "text-align": "center" }
      }
    ],
    "entityMap": {}
  }
}
```

**Common inline styles:** `fontfamily-Roboto`, `fontsize-10`, `fontsize-12`, `fontsize-14`, `fontsize-24`, `BOLD`, `ITALIC`, `color-<hex>`.

#### Icon (79 observed instances)

FontAwesome icon display:

| Property | Type | Values | Description |
|---|---|---|---|
| `icon` | object | `{"icon": "database", "color": "inherit", "variant": "light", "size": "icon"}` | Icon configuration |
| `color` | string | CSS color (e.g., `"#2563eb"`) | Icon color |
| `size` | string | `"sm"`, `"md"`, `"lg"` | Icon size |
| `variant` | string | `"light"`, `"solid"`, `"brands"` | FontAwesome variant |
| `width` | string | `"18px"`, `"24px"`, `"32px"`, `"48px"` | Width |
| `height` | string | `"18px"`, `"24px"`, `"32px"`, `"48px"` | Height |
| `title` | string | — | Tooltip / accessible title |

#### ProgressBar

| Property | Type | Values | Description |
|---|---|---|---|
| `minNumber` | string | `"0"` | Minimum value |
| `maxNumber` | string | `"100"` | Maximum value |
| `barHeight` | string | `"5"`, `"8"`, `"10"` | Bar height (px) |
| `borderRadius` | string | `"0"`, `"2"`, `"5"` | Bar corner radius |
| `barColor` | object | — | Custom bar color |
| `formElement` | string | — | Parent form reference |
| `dataPath` | string | — | Data binding path |

#### Timer

| Property | Type | Description |
|---|---|---|
| `autoStart` | boolean | Start automatically |
| `countUpMode` | boolean | Count up instead of down |
| `flashWhenExpired` | boolean | Flash when expired |
| `showProgressBar` | boolean | Show progress indicator |

#### EmbeddedWebpage

| Property | Type | Description |
|---|---|---|
| `url` | string | URL to embed |
| `allowScripts` | boolean | Allow JavaScript |
| `allowForms` | boolean | Allow form submission |
| `allowPopups` | boolean | Allow popups |
| `allowSameOrigin` | boolean | Allow same-origin access |

#### Visualization (Saved Visualizations)

| Property | Type | Description |
|---|---|---|
| `visualizationSetting` | string | `"Saved Visualization"` |
| `syncTimeseriesRange` | boolean | Sync time range across visualizations |
| `visualizationTransformRemote` | boolean | Transform data server-side |

### Data Elements

#### Form (65 observed instances)

Data-bound form container. `isCanvas: true`. Wraps inputs and manages data state.

| Property | Type | Description |
|---|---|---|
| `query` | object | **Required.** Data query configuration (see Query Configuration below) |
| `blockNavigationWhenDirty` | boolean | Prevent navigation with unsaved changes |
| `enableConfirmOnDataChanged` | boolean | Show confirmation on data change |
| `visible` | boolean | Visibility flag |

**Form component functions** (called from ActionButtons):
- `$components.Form1.fn.save()` — Save
- `$components.Form1.fn.delete()` — Delete
- `$components.Form1.fn.reset()` — Reset
- `$components.Form1.fn.refresh()` — Reload data

#### Table (4 observed instances)

Data grid with query-driven rows:

| Property | Type | Values | Description |
|---|---|---|---|
| `query` | object | — | **Required.** Data query configuration |
| `selectable` | string | `"none"`, `"multiple"` | Row selection mode |
| `rowMultiSelectWithClick` | boolean | `false`, `true` | Select rows by clicking |
| `enableGrouping` | boolean | `false`, `true` | Enable row grouping |
| `enableCharts` | boolean | `false`, `true` | Built-in charts |
| `showColumnMenu` | boolean | `true` | Column menu |
| `showStatusBar` | boolean | `true` | Status bar |
| `showToolPanels` | boolean | `false`, `true` | Tool panels |
| `hideTableHeader` | boolean | `false` | Hide header row |
| `fontSize` | string | `"14px"` | Cell font size |
| `flexGrow` | boolean | `false` | Grow to fill space |
| `masterDetail` | boolean | `false` | Enable master-detail rows |

#### TableColumn (43 observed instances)

Column definition for a Table:

| Property | Type | Values | Description |
|---|---|---|---|
| `label` | string | — | Column header label |
| `field` | string | — | **Required.** Data model field |
| `dataPath` | string | — | Data binding path |
| `format` | string | `"text"`, `"datetime"`, `"float"`, `"menu"` | Display format |
| `sortable` | boolean | `true` | Enable sorting |
| `editable` | boolean | `false` | Enable inline edit |
| `width` | string | `"50"`, `"100"` | Column width (px) |
| `timeZone` | string | `"setting"` | Timezone for datetime |
| `actions` | object | — | Column action buttons |
| `menuIcon` | string | `"ellipsis-v"` | Icon for menu columns |

#### Cards (2 observed instances)

Repeating card layout. `isCanvas: true`. Each record renders as a card.

| Property | Type | Values | Description |
|---|---|---|---|
| `query` | object | — | **Required.** Data query configuration |
| `flexDirection` | string | `"column"` | Card layout direction |
| `flexWrap` | boolean | `true` | Wrap cards |
| `justifyContent` | string | `"flex-start"` | Card alignment |
| `blockNavigationWhenDirty` | boolean | `true` | Prevent navigation with unsaved |

**Cards with data transform** (Enterprise Admin pattern — transforms query results into card data):
```json
{
  "query": {
    "api": "System",
    "model": "Enterprise",
    "dataPath": "edges",
    "dataTransform": {
      "transform": "($connectors := $$.edges[0].node.metrics.counts.connectors; $each($connectors, function($v, $k) { { \"label\": $k, \"count\": $v } }))",
      "remote": true
    },
    "autoLoad": true,
    "fields": ["id", "metrics"],
    "parameters": "{}"
  }
}
```

**Cards with client-side transform from `$metadata`/context** — Cards that load data from client-side transforms (not a model query) still require a valid query structure with `fields` and `parameters` to prevent the platform from erroring on the auto-generated GraphQL query:
```json
{
  "query": {
    "api": "Application",
    "query": null,
    "dataPath": "edges",
    "dataTransform": { "transform": "([$metadata.userTenants.{\"id\": id, \"tenantName\": tenant.name}])", "remote": false },
    "dataSubscription": { "enabled": false },
    "autoLoad": true,
    "fields": ["id"],
    "parameters": "{\r\n  \"filter\": {\r\n    \"id\": {\r\n      \"_eq\": $metadata.user.id\r\n    }\r\n  },\r\n  \"first\": 1\r\n}"
  }
}
```

#### Chart

| Property | Type | Description |
|---|---|---|
| `chart` | object | **Required.** Full FusionCharts configuration |
| `allowFullscreen` | boolean | Enable fullscreen mode |

#### DataTreeView

Hierarchical tree view:

| Property | Type | Values | Description |
|---|---|---|---|
| `query` | object | — | Data query configuration |
| `maxRecords` | string/number | `"5"`, `10` | Max records per level |
| `maxHeight` | string | `"500px"` | Max height |
| `minHeight` | string | `"200px"` | Min height |
| `expandRoot` | boolean | `false` | Expand root node |
| `expandFirstNode` | boolean | `false` | Expand first child |
| `hideTopLevelRootHeader` | boolean | `false` | Hide root header |

### Action Elements

#### ActionButton (29 observed instances)

| Property | Type | Values | Description |
|---|---|---|---|
| `text` | string/object | — | Button text (static or dynamic `__transform`) |
| `title` | string | — | Button label/tooltip |
| `buttonColor` | string | `"primary"`, `"secondary"`, `"error"` | Color theme |
| `icon` | object | `{"icon": "save", "size": null}` | Button icon |
| `action` | array | — | **Required.** Ordered list of action steps |
| `useIconButton` | boolean | `false`, `true` | Icon-only button |
| `disabled` | boolean/object | `false` | Static or dynamic disable |
| `disableHover` | boolean | `false` | Disable hover effect |
| `disableRipple` | boolean | `false` | Disable click ripple |

**Action types in action array:**

1. **transformation** — Execute JSONata:
```json
{ "type": "transformation", "transformation": "$components.Form1.fn.save()", "remote": false }
```

2. **confirmation** — Show confirm dialog:
```json
{
  "type": "confirmation",
  "message": { "__transform": "\"Are you sure?\"" },
  "submitIcon": { "icon": "trash", "variant": "solid" },
  "buttonColor": "error"
}
```

3. **navigation** — Navigate:
```json
{ "type": "navigation", "navigationType": "Back", "back": true }
```

### Specialized Input Elements

| Element | Type String | Key Properties |
|---|---|---|
| `TextInput` | `"text"` | Standard text input |
| `Password` | `"password"` | Masked password input |
| `IntegerInput` | `"integer"` | Whole number, has `target` |
| `FloatInput` | `"float"` | Decimal number |
| `NumberInput` | `"number"` | Generic number |
| `SliderInput` | `"slider"` | `marks`, `includeNumberField` |
| `DateInput` | `"date"` | `timeOfDay`, `target` |
| `DateTimeInput` | `"datetime"` | Combined date + time |
| `DateRangeInput` | `"datetimeRange"` | Start/end date range |
| `TimeInput` | `"time"` | Time-only picker |
| `TimeZoneInput` | `"timeZone"` | `isClearable`, `multiselect`, `searchPredicate` |
| `DurationInput` | `"duration"` | `mode`, `enabledUnits`, `showDurationString` |
| `ColorInput` | `"color"` | Color picker |
| `AddressInput` | `"address"` | Structured address fields |
| `MarkdownInput` | `"markdown"` | Markdown editor with preview |
| `RichTextInput` | `"richText"` | WYSIWYG editor |
| `CodeEditorInput` | `"codeEditor"` | `mode`, `stringify` |
| `JSONInput` | `"json"` | `showEditor` |
| `JSONSchemaInput` | `"jsonSchema"` | JSON Schema editor |
| `JSONataInput` | `"jsonata"` | `snippets` |
| `TransformInput` | `"transform"` | Transform editor |
| `GraphQLBuilderInput` | `"graphqlBuilder"` | `api`, `mode`, `rootNodeType` |
| `WrappedGraphQLPredicate` | `"graphqlWhere"` | WHERE clause builder |
| `FileUpload` | `"file"` | `dataFormat: "base64"` |
| `SVGInput` | `"svg"` | SVG editor |
| `Image` | `"image"` | Image display/input |
| `ScanTextInput` | `"scanText"` | `allowManualInput`, `beepOnScan`, `clearOnFocus`, `scanTimeoutMilliseconds` |
| `MeasureInput` | `"measure"` | Unit-of-measure |
| `RatioMeasureInput` | `"ratioMeasure"` | Ratio measurement |
| `RRuleInput` | `"rrule"` | `enableTimeZone`, `showRuleString` |
| `ArrayInput` | `"array"` | Array values |
| `CustomFieldsInput` | — | `dataModel`, `dataPath: "_customFields"`, `expandGroups` |

### SelectInput Extended Properties

| Property | Type | Values | Description |
|---|---|---|---|
| `api` | string | `"Application"` | API source |
| `dataModel` | string | — | Data model for options |
| `labelPath` | string | `"name"` | Field to display as label |
| `selectFields` | array | `["name"]` | Fields returned on selection |
| `orderByField` | string | `"name"` | Sort field |
| `isClearable` | boolean | `true` | Allow clearing |
| `multiselect` | boolean | `false` | Multi-select |
| `searchPredicate` | string | `"contains"` | Search mode |
| `stateTracking` | string | `"full"`, `"id"` | State tracking mode |
| `optionLimit` | string | `"5"` | Max visible options |
| `dialogMode` | string | `"never"` | Dialog display mode |
| `alwaysReloadOptions` | boolean | `false` | Reload on every open |

### Query Configuration (Used by Form, Table, Cards, DataTreeView)

```json
{
  "api": "Application",
  "model": "DataModelName",
  "query": null,
  "dataPath": "edges[0]",
  "dataTransform": { "transform": null, "remote": true },
  "dataSubscription": { "enabled": false },
  "autoLoad": true,
  "parameters": "{ \"filter\": { \"id\": { \"_eq\": id } }, \"first\": 1 }",
  "fields": ["id"],
  "filterPredicate": {},
  "disableDataChangeIndicator": false
}
```

| Property | Type | Description |
|---|---|---|
| `api` | string | `"Application"` or `"System"` — GraphQL endpoint |
| `model` | string | Data model name |
| `query` | string/null | Custom GraphQL (null = auto-generated) |
| `dataPath` | string | `"edges[0]"` (single) or `"edges"` (list) |
| `autoLoad` | boolean | Auto-load on mount |
| `parameters` | string | JSONata filter/sort/pagination expression |
| `fields` | array | Fields to select (minimum: `["id"]`) |
| `filterPredicate` | object | Additional filter predicate (always `{}` if unused) |
| `dataTransform` | object | `{ "transform": null, "remote": true }` |
| `dataSubscription` | object | `{ "enabled": false }` |

### Validation Object

```json
{
  "required": true,
  "unique": {
    "api": "Application",
    "model": "Screen",
    "excludeId": true,
    "byFields": [{ "field": "name", "dataPath": "name" }]
  }
}
```

---

## Screen (ROOT) Configuration

### Dashboard Screen ROOT

```json
{
  "ROOT": {
    "type": { "resolvedName": "Screen" },
    "isCanvas": true,
    "props": {
      "padding": 0,
      "margin": 0,
      "flexDirection": "column",
      "flexWrap": false,
      "alignItems": "stretch",
      "justifyContent": "flex-start",
      "width": "100%",
      "height": "auto",
      "hidden": false,
      "flexGrow": false,
      "shadow": false,
      "borderRadius": 0,
      "flowsDelay": 0,
      "removeOuterMargin": true,
      "background": "paper",
      "forceFullscreen": true,
      "intervals": [],
      "pageLoadDataFlowIds": [],
      "screenDataFlowIds": [],
      "description": "Description of this dashboard screen"
    },
    "displayName": "Screen",
    "custom": {
      "elementName": "Screen",
      "elementCounts": {},
      "template": "dashboard-type",
      "version": "0.0.1"
    },
    "parent": null,
    "hidden": false,
    "nodes": ["HeaderContainer", "BrandSeparator", "MainContent"],
    "linkedNodes": {}
  }
}
```

**Dashboard-only props:**
- `pageLoadAction` — JSONata expression executed on page load (inline script)
- `pageLoadDataFlowIds` — Array of web flow IDs to execute on load
- `screenDataFlowIds` — Array of web flow IDs available to the screen
- `intervals` — Auto-refresh configurations: `[{"interval": 60, "action": "$components.RefreshBtn.fn.execute()"}]`

### Table/Form Screen ROOT

```json
{
  "ROOT": {
    "type": { "resolvedName": "Screen" },
    "isCanvas": true,
    "props": {
      "padding": 0,
      "margin": 0,
      "flexDirection": "column",
      "flexWrap": false,
      "alignItems": "stretch",
      "justifyContent": "flex-start",
      "width": "100%",
      "height": "100%",
      "hidden": false,
      "flexGrow": false,
      "shadow": false,
      "borderRadius": 0,
      "flowsDelay": 0,
      "removeOuterMargin": true,
      "background": "paper"
    },
    "displayName": "Screen",
    "custom": {
      "elementName": "Screen",
      "elementCounts": {},
      "dataModel": "ModelName",
      "template": "master"
    },
    "parent": null,
    "hidden": false,
    "nodes": ["ContainerOuter"],
    "linkedNodes": {}
  }
}
```

**Key differences:**
- `height: "100%"` (not `"auto"`) — fills viewport for table scrolling
- No `forceFullscreen`, no `intervals`, no `pageLoadAction`
- `custom.dataModel` specifies the bound data model
- `custom.template` values: `"master"` (CRUD table), `"history"` (read-only log), `"form"` (detail form), `"setup"` (simple config table)
- Form screens use `alignItems: "center"` on ROOT (tables use `"stretch"`)
- Form screens may include `custom.module` for categorization: `{ "id": "orchestration", "group": { "id": "system" } }`

---

## Standard Box-Shadow Values

Always use these exact shadow values. Using inconsistent shadows degrades the platform's visual consistency.

| Use Case | `box-shadow` Value |
|----------|-------------------|
| Section cards (main content areas) | `2px 2px 6px #8b5cf6` |
| Individual item cards (inside Cards component) | `2px 2px 2px #8b5cf6` |
| Action button containers (header, Fuuz internal) | `0px 0px 1px 1px #8b5cf6` |

**Section-level cards** get the larger dispersed shadow. **Individual clickable cards** inside a Cards element get the tighter shadow. **Header action button wrappers** (Fuuz internal paired button pattern) get the outline-style shadow.

---

## CSS Property Formatting Rules

### Gap — MUST be string with units
```json
"gap": "16px"    ← CORRECT
"gap": 16        ← WRONG (breaks layout)
"gap": "16"      ← WRONG
```

### Width — string with units for fixed sizes
```json
"width": "280px"  ← CORRECT fixed width
"width": "100%"   ← CORRECT percentage
"width": "auto"   ← CORRECT auto-sizing
"width": "0px"    ← CORRECT for flexGrow containers (let flex decide)
"width": "280"    ← WRONG (ambiguous)
```

### Small numeric widths (under 100) — bare numbers OK
```json
"width": 45       ← OK for small elements like icons
"height": 32      ← OK
```

### Padding/Margin — numeric (pixels implied)
```json
"padding": 16     ← CORRECT
"margin": 6       ← CORRECT
```

### Border Radius — numeric
```json
"borderRadius": 8  ← CORRECT
```

---

## Container Component

The workhorse layout component. Always `isCanvas: true`.

### Full Container Template

```json
{
  "MyContainer": {
    "type": { "resolvedName": "Container" },
    "isCanvas": true,
    "props": {
      "padding": 0,
      "margin": 0,
      "flexWrap": false,
      "flexDirection": "row",
      "justifyContent": "flex-start",
      "alignItems": "center",
      "width": "100%",
      "height": "auto",
      "hidden": false,
      "flexGrow": false,
      "flexShrink": false,
      "shadow": false,
      "borderRadius": 0,
      "collapsible": false,
      "collapsedFooter": false,
      "collapsedSize": 0,
      "collapsedByDefault": false,
      "onClickTransform": false,
      "layout": "flex",
      "bordersInput": {},
      "justifyItems": "stretch",
      "style": {},
      "data": {},
      "gap": "16px"
    },
    "displayName": "Container",
    "custom": {
      "elementName": "MyContainer"
    },
    "parent": "ROOT",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Common Container Patterns

**Card (shadow + border + rounded):**
```json
"props": {
  "padding": 6,
  "flexDirection": "column",
  "justifyContent": "space-between",
  "alignItems": "stretch",
  "width": "320",
  "height": "120",
  "flexGrow": true,
  "shadow": true,
  "borderRadius": 8,
  "bordersInput": {
    "borderMode": "single",
    "border": "2px solid #2563eb"
  },
  "background": "paper",
  "gap": "8px"
}
```

**Row layout (responsive wrap):**
```json
"props": {
  "flexDirection": "row",
  "justifyContent": "space-between",
  "alignItems": "center",
  "flexWrap": true,
  "width": "100%",
  "gap": "16px"
}
```

**Column layout (stacked):**
```json
"props": {
  "flexDirection": "column",
  "justifyContent": "flex-start",
  "alignItems": "stretch",
  "width": "100%",
  "flexGrow": true,
  "gap": "12px"
}
```

**Grow-to-fill column (for table body areas):**
```json
"props": {
  "flexDirection": "column",
  "width": "0px",
  "height": "100%",
  "flexGrow": true
}
```

**Brand separator bar (gradient accent):**
```json
"props": {
  "padding": 0,
  "margin": 0,
  "flexDirection": "column",
  "width": "100%",
  "height": "4px",
  "flexGrow": false,
  "style": {
    "background-image": "linear-gradient(90deg, #39005a, #5b30df, #03caaf)"
  }
}
```

### Background Options

| Value | Appearance |
|---|---|
| `"paper"` | Theme paper color (white in light mode) |
| `"default"` | Theme default background |
| `"light"` | Light gray background |
| `"custom"` | Uses `customBackground` color string (static or dynamic) |
| `"primary.dark"` | Theme primary dark color |
| `"primary.main"` | Theme primary color |

`customBackground` can be a static hex string (`"#f0f0f0"`) or a dynamic `__transform` object.

### Border Modes

```json
"bordersInput": {
  "borderMode": "single",
  "border": "1px solid #e2e8f0"
}

"bordersInput": {
  "borderMode": "multi",
  "border": "0px undefined ",
  "borderLeft": "0.5px solid #5b30df",
  "borderBottom": "0px none "
}

"bordersInput": {
  "borderMode": "full",
  "border": "0px none "
}
```

---

## Form Component

Forms provide data context for child components. **Required wrapper for any DisplayText with data bindings.**

### Form Bound to Data Model (for table/form screens)

```json
{
  "MyForm": {
    "type": { "resolvedName": "Form" },
    "isCanvas": true,
    "props": {
      "query": {
        "api": "Application",
        "query": null,
        "dataPath": "edges[0]",
        "dataTransform": {
          "transform": null,
          "remote": true
        },
        "dataSubscription": { "enabled": false },
        "autoLoad": false,
        "fields": ["id", "code", "name"],
        "disableDataChangeIndicator": false,
        "dataChangeIndicatorSize": "normal",
        "model": "ModelName",
        "filterPredicate": {}
      },
      "blockNavigationWhenDirty": false,
      "enableConfirmOnDataChanged": false,
      "padding": 0,
      "margin": 0,
      "width": "100%",
      "height": "auto",
      "visible": true,
      "description": "Description of this form's data context"
    },
    "displayName": "Form",
    "custom": { "elementName": "MyForm" },
    "parent": "ParentNodeId",
    "hidden": false,
    "nodes": ["FormContentContainer"],
    "linkedNodes": {}
  }
}
```

### Form for Dashboard Context Display

For dashboards where data comes from screen context (set by page load flow), use `autoLoad: false`:

```json
{
  "MetricForm": {
    "type": { "resolvedName": "Form" },
    "isCanvas": true,
    "props": {
      "query": {
        "api": "Application",
        "query": "",
        "dataPath": "",
        "dataTransform": { "transform": null, "remote": true },
        "dataSubscription": { "enabled": false },
        "autoLoad": false,
        "fields": [],
        "disableDataChangeIndicator": false,
        "dataChangeIndicatorSize": "normal",
        "model": "",
        "filterPredicate": {}
      },
      "blockNavigationWhenDirty": false,
      "enableConfirmOnDataChanged": false,
      "padding": 0,
      "margin": 0,
      "width": "100%",
      "height": "auto",
      "visible": true
    },
    "displayName": "Form",
    "custom": { "elementName": "MetricForm" },
    "parent": "MetricCard",
    "hidden": false,
    "nodes": ["MetricValueContainer"],
    "linkedNodes": {}
  }
}
```

### Passive Form (Data from pageLoadAction / Screen Context)

For forms that receive their data from `pageLoadAction` via `$components.Screen.fn.setContext()` and bind inputs to `$components.Screen.context.*`, use `autoLoad: true` with a harmless dummy filter. **Do NOT set `autoLoad: false`** — this causes platform errors when the form has no model query.

```json
{
  "ProfileForm": {
    "type": { "resolvedName": "Form" },
    "isCanvas": true,
    "props": {
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
      },
      "blockNavigationWhenDirty": false,
      "enableConfirmOnDataChanged": false,
      "padding": 0,
      "margin": 0,
      "width": "100%",
      "height": "auto",
      "visible": true
    },
    "displayName": "Form",
    "custom": { "elementName": "ProfileForm" },
    "parent": "ParentContainer",
    "hidden": false,
    "nodes": ["FormContentContainer"],
    "linkedNodes": {}
  }
}
```

---

## DisplayText Component

For dynamic text bound to data. Must be inside a Form.

```json
{
  "OeeValueText": {
    "type": { "resolvedName": "DisplayText" },
    "isCanvas": false,
    "props": {
      "fontSize": "h4",
      "textAlign": "left",
      "fontWeight": "bold",
      "color": "#2563eb",
      "formElement": "OeeForm",
      "description": "Displays the current OEE percentage value",
      "width": "auto",
      "height": "auto",
      "data": {
        "__transform": "$state.context.oee ? $string($round($state.context.oee * 100, 1)) & '%' : '--'",
        "__cacheKey": "oeeValue",
        "__dynamicFields": {
          "payload": [],
          "context": ["state.context.oee"]
        }
      }
    },
    "displayName": "DisplayText",
    "custom": { "elementName": "OeeValueText" },
    "parent": "OeeValueContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Data Binding Structure

**CRITICAL:** Every DisplayText `data` binding must include the full triplet: `__transform`, `__cacheKey`, and `__remote`. Omitting `__cacheKey` or `__remote` causes unreliable rendering for `$metadata.*` and `$components.Screen.context.*` bindings.

```json
// ❌ WRONG — Missing __cacheKey and __remote:
"data": { "__transform": "$metadata.user.email" }

// ✅ CORRECT — Full triplet (minimal, no dynamic fields needed):
"data": {
  "__transform": "$metadata.user.email",
  "__cacheKey": "email",
  "__remote": false
}

// ✅ CORRECT — Full triplet with __dynamicFields for $state.context bindings:
"data": {
  "__transform": "JSONata expression",
  "__cacheKey": "uniqueKey",
  "__remote": false,
  "__dynamicFields": {
    "payload": [],
    "context": ["state.context.fieldName"]
  }
}
```

**`__cacheKey`** — Short unique identifier string for this binding. Used by the platform for caching and reactivity.

**`__remote`** — `false` for client-side transforms (reading metadata, context, form state). `true` only when the transform executes server-side.

**`__transform`** — JSONata expression for the display value:
- Screen context: `$state.context.fieldName`
- String literals: `'Static Text'`
- Concatenation: `$state.context.name & ' - ' & $state.context.code`
- Conditionals: `$state.context.value ? $state.context.value : 'N/A'`
- Formatting: `$momentDuration($state.context.mtbfMs, 'milliseconds').format('h[h] m[m]')`
- Math: `$string($round($state.context.oee * 100, 1)) & '%'`

**`__dynamicFields.context`** — MUST list all `$state.context.*` fields used in the transform. Omitting a field breaks reactivity. Use the pattern `"state.context.topLevelField"` (no leading `$`).

### Font Size Values (MUI Typography Variants)

`h1`, `h2`, `h3`, `h4`, `h5`, `h6`, `subtitle1`, `subtitle2`, `body1`, `body2`, `caption`, `overline`

### Font Colors

- Hex codes: `"#1e293b"`, `"#10b981"`, `"#64748b"`, `"#ffffff"`
- Theme: `"primary.main"`, `"primary.dark"`, `"secondary.main"`, `"text.primary"`, `"text.secondary"`

---

## RichText Component

For static formatted text (no data binding). Uses Draft.js content blocks.

```json
{
  "TitleText": {
    "type": { "resolvedName": "RichText" },
    "isCanvas": false,
    "props": {
      "width": "auto",
      "height": "100%",
      "content": {
        "blocks": [
          {
            "key": "title-block",
            "text": "Dashboard Title",
            "type": "unstyled",
            "depth": 0,
            "inlineStyleRanges": [
              { "offset": 0, "length": 15, "style": "fontfamily-Roboto" },
              { "offset": 0, "length": 15, "style": "fontsize-14" },
              { "offset": 0, "length": 15, "style": "BOLD" },
              { "offset": 0, "length": 15, "style": "color-#64748b" }
            ],
            "entityRanges": [],
            "data": {}
          }
        ],
        "entityMap": {}
      }
    },
    "displayName": "Text",
    "custom": { "elementName": "TitleText" },
    "parent": "HeaderContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

**Inline style types:** `fontfamily-Roboto`, `fontsize-14`, `BOLD`, `ITALIC`, `UNDERLINE`, `color-#hexcode`, `color-rgb(r,g,b)`

---

## ActionButton Component

**CRITICAL:** resolvedName is `ActionButton`, displayName is `Action`.

```json
{
  "RefreshButton": {
    "type": { "resolvedName": "ActionButton" },
    "isCanvas": false,
    "props": {
      "margin": 0,
      "buttonColor": "primary",
      "icon": {
        "icon": "arrows-rotate",
        "color": "inherit",
        "variant": "regular",
        "size": "icon"
      },
      "width": "auto",
      "height": "auto",
      "disabled": false,
      "hidden": false,
      "useIconButton": false,
      "disableHover": false,
      "disableRipple": false,
      "action": [
        {
          "type": "transformation",
          "transformation": "(\n  $components.Screen.fn.reloadFlows()\n)",
          "remote": false
        }
      ],
      "title": "Refresh Dashboard",
      "description": "Reloads all dashboard data flows"
    },
    "displayName": "Action",
    "custom": { "elementName": "RefreshButton" },
    "parent": "ActionContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Icon-Only Button

Set `"useIconButton": true` and specify `icon` prop:

```json
"props": {
  "useIconButton": true,
  "icon": {
    "icon": "magnifying-glass",
    "color": "info",
    "variant": "regular",
    "size": "icon"
  },
  "width": "24px",
  "height": "24px"
}
```

### Button Colors

`primary`, `secondary`, `error`, `warning`, `info`, `success`

### Common Action Patterns

**Set screen context:**
```jsonata
(
  $context := {"key": "value"};
  $components.Screen.fn.setContext($context)
)
```

**Navigate:**
```jsonata
$navigate('/screen-path?param=' & $state.context.id)
```

**Table search:**
```jsonata
$components.TableName.fn.search()
```

**Reload flows:**
```jsonata
$components.Screen.fn.reloadFlows()
```

**Open dialogue screen:**
```jsonata
$components.DialogName.fn.open()
```

**Table CRUD actions (for detail view in table row menu):**
```json
"action": [
  {
    "type": "form",
    "submitIcon": "check",
    "submitLabel": "Close",
    "title": { "__transform": "\"Record: \" & data.code" },
    "transforms": { "pre": "data" },
    "fields": [
      {
        "label": "Code",
        "element": "DisplayText",
        "type": "display",
        "labelPosition": { "id": "row", "label": "Left" },
        "justifyContent": "space-between",
        "description": "Record identifier",
        "field": "code",
        "dataPath": "code",
        "target": { "dataPath": "code" }
      }
    ]
  }
]
```

### Fuuz Platform Action Button Pattern (Paired Icon + Text)

For **Fuuz platform screens** (admin, system, profile), header action buttons use a **paired elements** pattern: one icon-only button + one text-label button, both inside a container with `box-shadow: 0px 0px 1px 1px #8b5cf6`. This is a Fuuz employee styling workaround that will eventually be replaced with native action button configuration.

```
Container (row, box-shadow: 0px 0px 1px 1px #8b5cf6)
  ├── ActionButton (useIconButton: true, icon: "lock-open", width: 20, height: 20)
  └── ActionButton (useIconButton: true, text: "Change Password", width: auto, height: 20)
```

**Rule:** For non-Fuuz (customer) screens, use standard single action buttons unless instructed otherwise. The paired pattern is specifically for Fuuz internal platform screens.

---

## Icon Component

Uses FontAwesome icons.

```json
{
  "StatusIcon": {
    "type": { "resolvedName": "Icon" },
    "isCanvas": false,
    "props": {
      "icon": {
        "icon": "gauge-high",
        "color": "inherit",
        "variant": "light",
        "size": "icon"
      },
      "title": "OEE Gauge",
      "height": "24px",
      "width": "24px",
      "color": "#2563eb",
      "variant": "light",
      "size": "md",
      "description": "Icon indicating OEE metric"
    },
    "displayName": "Icon",
    "custom": { "elementName": "StatusIcon" },
    "parent": "HeaderRow",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Icon Variants

`light`, `regular`, `solid`, `thin`, `duotone`

### Common FontAwesome Icons

**Dashboard:** `gauge`, `gauge-high`, `chart-line`, `chart-bar`, `chart-pie`, `tachometer-alt`
**Actions:** `arrows-rotate`, `plus`, `trash`, `pen-to-square`, `floppy-disk`, `xmark`, `magnifying-glass`, `filter`, `download`, `upload`, `gear`
**Status:** `circle-check`, `triangle-exclamation`, `circle-xmark`, `circle-info`, `bell`, `shield`
**Navigation:** `arrow-left`, `arrow-right`, `chevron-down`, `chevron-up`, `bars`, `ellipsis-vertical`
**Manufacturing:** `industry`, `gears`, `wrench`, `clock`, `stopwatch`, `bolt`, `temperature-half`

---

## FlowButton Component (Mobile/Flow Triggers)

Triggers data flows. Used primarily in mobile screens for submit/cancel actions.

```json
{
  "SubmitButton": {
    "type": { "resolvedName": "FlowButton" },
    "isCanvas": false,
    "props": {
      "margin": 4,
      "buttonColor": "green",
      "icon": {
        "__transform": "\"\"",
        "__dynamicFields": { "payload": null, "context": [] },
        "__cacheKey": "", "__remote": false
      },
      "width": "50%",
      "height": "100%",
      "disabled": {
        "__transform": "$isNilOrEmpty($components.Form1.formState.values.inventory.number)",
        "__dynamicFields": {
          "payload": null,
          "context": ["components.Form1.formState.values.inventory.number"]
        },
        "__cacheKey": "", "__remote": false
      },
      "hidden": false,
      "useIconButton": false,
      "payload": {},
      "text": "Submit",
      "customTextSize": 24
    },
    "displayName": "Flow",
    "custom": { "elementName": "SubmitButton" },
    "parent": "ContainerFooter",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

**Key props:** `buttonColor` (green/amber/red/primary), `text`, `customTextSize`, `payload`, `disabled` (dynamic transform)

---

## MenuButton Component (Mobile Navigation)

Hamburger/dropdown menu for mobile screens:

```json
{
  "Menu1": {
    "type": { "resolvedName": "MenuButton" },
    "isCanvas": false,
    "props": {
      "margin": 0,
      "isFlow": false,
      "actions": [{ "text": "Home" }],
      "elementName": "Menu Button",
      "height": "100%",
      "width": "100%",
      "menuIcon": "bars",
      "iconSize": "large"
    },
    "displayName": "Menu",
    "custom": { "elementName": "Menu1" },
    "parent": "ContainerNavigation",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

---

## Table Component

Tables bind to data models and render paginated, sortable, filterable grids.

```json
{
  "DataTable": {
    "type": { "resolvedName": "Table" },
    "isCanvas": true,
    "props": {
      "margin": 0,
      "width": "100%",
      "height": "0px",
      "flexGrow": true,
      "query": {
        "api": "Application",
        "query": null,
        "dataPath": "edges",
        "dataTransform": {
          "transform": "JSONata transform or null",
          "remote": true
        },
        "dataSubscription": { "enabled": false },
        "autoLoad": true,
        "fields": ["id", "code", "name", "relatedModel.id", "relatedModel.name"],
        "disableDataChangeIndicator": false,
        "model": "DataModelName",
        "parameters": "{\n  \"orderBy\": [\n    { \"field\": \"occurAt\", \"direction\": \"desc\" }\n  ]\n}"
      },
      "selectable": "none",
      "showToolPanels": true,
      "showStatusBar": true,
      "enableGrouping": true,
      "enableCharts": true,
      "showColumnMenu": true,
      "enableRowDragging": false,
      "disableColumnDragging": false,
      "masterDetail": false,
      "hideTableHeader": false,
      "description": "Description of data shown in this table",
      "defaultSort": [
        { "field": "ColumnNodeId", "direction": "desc" }
      ],
      "filterFormName": "FormFilterPanel"
    },
    "displayName": "Table",
    "custom": { "elementName": "DataTable" },
    "parent": "ContainerBody",
    "hidden": false,
    "nodes": ["Column1", "Column2", "Column3"],
    "linkedNodes": {}
  }
}
```

### Table Data Transform (JSONata)

Common pattern for computed columns:

```jsonata
[edges.node~>|$|(
  $total := $coalesce([quantityGood, 0]) + $coalesce([quantityScrap, 0]);
  $yield := $total > 0 ? $round(quantityGood / $total * 100, 1) : 0;
  {
    "totalQuantity": $total,
    "yieldPercent": $string($yield) & "%"
  }
)|]
```

### TableColumn Component

```json
{
  "ColumnName": {
    "type": { "resolvedName": "TableColumn" },
    "isCanvas": false,
    "props": {
      "label": "Column Header",
      "dataPath": "fieldName",
      "format": "text",
      "sortable": true,
      "width": "120px",
      "hidden": false,
      "description": "Description of this column data"
    },
    "displayName": "Table Column",
    "custom": { "elementName": "ColumnName" },
    "parent": "DataTable",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

**Column formats:** `text`, `number`, `datetime`, `date`, `boolean`, `link`, `menu`, `json`, `image`, `badge`

**DateTime column with formatting:**
```json
"props": {
  "label": "Occurred At",
  "dataPath": "occurAt",
  "format": "datetime",
  "formatString": "MM/DD/YYYY h:mm A",
  "timeZone": "setting",
  "sortable": true,
  "width": "170px"
}
```

**Menu column (row actions):**
```json
"props": {
  "label": " ",
  "format": "menu",
  "sortable": false,
  "dataPath": "actions",
  "width": "50px",
  "actions": [ ... ]
}
```

**Relationship column:**
```json
"props": {
  "label": "Workcenter",
  "dataPath": "workcenter.name",
  "format": "text",
  "sortable": true
}
```

---

## ResizablePanelLayout (Filter Sidebar)

Used for filter panels in table screens.

```json
{
  "ResizableFilterPanel": {
    "type": { "resolvedName": "ResizablePanelLayout" },
    "isCanvas": true,
    "props": {
      "handle": "right",
      "toggleHotkey": "",
      "defaultSize": 320,
      "axis": false,
      "minSize": 0,
      "maxSize": 320
    },
    "displayName": "Resizable Panel",
    "custom": { "elementName": "ResizableFilterPanel" },
    "parent": "ContainerOuter",
    "hidden": false,
    "nodes": ["ContainerFilterOuter"],
    "linkedNodes": {}
  }
}
```

---

## Filter Input Components

### SelectInput (Dropdown)

```json
{
  "SelectWorkcenter": {
    "type": { "resolvedName": "SelectInput" },
    "isCanvas": false,
    "props": {
      "padding": 4,
      "width": "100%",
      "height": "auto",
      "validation": { "required": false },
      "uniqueValidationEnabled": false,
      "visible": true,
      "type": "select",
      "label": "Workcenter",
      "dialogMode": "never",
      "multiselect": false,
      "isClearable": true,
      "selectAll": true,
      "searchPredicate": "contains",
      "api": "Application",
      "stateTracking": "full",
      "optionLimit": 5,
      "optionLimitFlex": 5,
      "alwaysReloadOptions": false,
      "formElement": "FormFilterPanel",
      "variant": "filled",
      "disabled": false,
      "description": "Filter by workcenter",
      "dataModel": "Workcenter",
      "selectFields": ["name"],
      "labelPath": "name",
      "orderByField": "name",
      "orderByDirection": "asc",
      "query": {
        "fields": ["workcenter.id", "workcenter.name"]
      },
      "dataPath": "workcenter",
      "target": { "dataPath": null },
      "additionalFilter": {
        "_and": [{ "active": { "_eq": true } }]
      }
    },
    "displayName": "Select",
    "custom": { "elementName": "SelectWorkcenter" },
    "parent": "FilterFormContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

**Key SelectInput props:**
- `variant`: `"filled"` for filter panels, `"outlined"` for mobile/form inputs
- `stateTracking`: `"full"` for complete state management
- `additionalFilter`: GraphQL `_and` array to restrict options (e.g., active-only)
- `dataModel`: The model to load options from
- `selectFields`: Fields to show in the dropdown
- `labelPath`: Which field to display as the option label
- `orderByField`/`orderByDirection`: Option sorting

### OptionsInput (Radio/Checkbox)

```json
{
  "OptionsStatus": {
    "type": { "resolvedName": "OptionsInput" },
    "isCanvas": false,
    "props": {
      "padding": 4,
      "width": "100%",
      "type": "options",
      "label": "Status",
      "multiselect": false,
      "isClearable": true,
      "formElement": "FormFilterPanel",
      "variant": "filled",
      "description": "Filter by active/inactive status",
      "options": [
        { "id": true, "label": "Active" },
        { "id": false, "label": "Inactive" }
      ],
      "target": { "dataPath": "isActive._eq" },
      "dataPath": "isActive",
      "field": "isActive"
    },
    "displayName": "Options",
    "custom": { "elementName": "OptionsStatus" },
    "parent": "FilterFormContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### DateRangeInput

```json
{
  "DateRangeOccurAt": {
    "type": { "resolvedName": "DateRangeInput" },
    "isCanvas": false,
    "props": {
      "padding": 4,
      "width": "100%",
      "label": "Date Range",
      "formElement": "FormFilterPanel",
      "variant": "filled",
      "description": "Filter by occurrence date range",
      "target": { "dataPath": "occurAt" },
      "field": "occurAt"
    },
    "displayName": "Date Range",
    "custom": { "elementName": "DateRangeOccurAt" },
    "parent": "FilterFormContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Switch (Toggle Filter)

```json
{
  "SwitchActive": {
    "type": { "resolvedName": "Switch" },
    "isCanvas": false,
    "props": {
      "padding": 3,
      "width": "100%",
      "height": "40",
      "validation": { "required": false },
      "visible": true,
      "type": "switch",
      "label": "Active Only",
      "target": { "dataPath": null },
      "dataPath": "isActive",
      "formElement": "FormFilterPanel",
      "alignItems": "center"
    },
    "displayName": "Switch",
    "custom": { "elementName": "SwitchActive" },
    "parent": "FilterFormContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Switch for Binary/Boolean Settings

For binary/boolean settings (dark mode, enabled/disabled, yes/no), **use `Switch` not `OptionsInput`**. Switch supports `label` (right/true label) and `labelLeft` (left/false label) for clear dual-state display.

```json
{
  "DarkModeSwitch": {
    "type": { "resolvedName": "Switch" },
    "isCanvas": false,
    "props": {
      "padding": 3,
      "width": "100%",
      "height": "40",
      "validation": { "required": false },
      "visible": true,
      "type": "switch",
      "label": "Light Mode",
      "labelLeft": "Dark Mode",
      "dataPath": "themeModeMine",
      "formElement": "ProfileForm",
      "defaultValue": true,
      "data": {
        "__transform": "$components.Screen.context.darkMode",
        "__cacheKey": "darkModeCurrent",
        "__remote": false
      },
      "description": "Toggle between light and dark theme mode"
    },
    "displayName": "Switch",
    "custom": { "elementName": "DarkModeSwitch" },
    "parent": "SettingsContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

**When to use Switch vs OptionsInput:**
- **Switch**: Binary choices (on/off, true/false, enable/disable, light/dark)
- **OptionsInput**: Multiple choices (3+ options), non-binary selections

### Checkbox (Boolean Filter)

```json
{
  "CheckboxArchived": {
    "type": { "resolvedName": "Checkbox" },
    "isCanvas": false,
    "props": {
      "padding": 3,
      "width": "100%",
      "height": "40",
      "validation": { "required": false },
      "visible": true,
      "type": "checkbox",
      "label": "Include Archived",
      "target": { "dataPath": null },
      "dataPath": "includeArchived",
      "formElement": "FormFilterPanel",
      "alignItems": "center"
    },
    "displayName": "Checkbox",
    "custom": { "elementName": "CheckboxArchived" },
    "parent": "FilterFormContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### GraphQL Where Input (Advanced Filter)

```json
{
  "AdvancedFilter": {
    "type": { "resolvedName": "WrappedGraphQLPredicate" },
    "isCanvas": false,
    "props": {
      "padding": 4,
      "width": "100%",
      "type": "graphqlWhere",
      "dataPath": "predicate",
      "target": { "dataPath": "_and.0" },
      "formElement": "FormFilterPanel",
      "label": "Advanced Filter",
      "description": "Advanced GraphQL where clause for complex filtering"
    },
    "displayName": "GraphQL Where Input",
    "custom": { "elementName": "AdvancedFilter" },
    "parent": "FilterFormContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

---

## Screen Runtime Context

Every Fuuz screen has a runtime context object available to all JSONata expressions. Understanding this structure is essential for writing transforms, conditional styling, and data binding. This context is accessible via `$metadata`, `$components`, `$data`, and related bindings.

### Context Structure Overview

```
{
  metadata: { ... },       // Platform, user, tenant, screen, settings, enterprise info
  components: { ... },     // Every component on the screen with fn, data, formState
  data: [ ... ],           // Current data array (table rows / query results)
  currentView: { ... },    // Active table view configuration
  flowEngine: { ... },     // Flow engine state
  initialData: [ ... ],    // Original data before any transforms
  urlParameters: { ... }   // URL parameters passed to the screen
}
```

### metadata Object

Available via `$metadata` in JSONata expressions:

```json
{
  "urlParameters": {},
  "querystring": {
    "Workcenters": { "WorkcenterTable": { "view": "Default" } }
  },
  "user": {
    "id": "ckzzmdqs8000y0jwx2o5qhwmi",
    "firstName": "Craig",
    "lastName": "Scott",
    "middleName": "",
    "email": "cscott@mfgx.io",
    "environments": [
      { "id": "build", "name": "Build", "url": "https://build.mfgx.fuuz.app" }
    ],
    "accessTypeId": "administrator"
  },
  "tenant": {
    "id": "fuuzOverflow",
    "name": "Fuuz Overflow",
    "tenantTypeId": "custom"
  },
  "userTenants": [
    {
      "id": "...",
      "accessTypeId": "administrator",
      "tenant": { "id": "...", "name": "...", "tenantTypeId": "custom" }
    }
  ],
  "screen": {
    "id": "workcenter",
    "forceFullscreen": false,
    "version": {
      "id": "cmlbkd55qeg36018tbrfwdllc",
      "number": "0.0.13",
      "createdAt": "2026-02-07T00:16:12.638Z"
    },
    "latestVersion": { "id": "...", "label": "0.0.13" }
  },
  "environment": "build",
  "platformVersion": "2026.2.0.766",
  "settings": {
    "AlwaysDisplayTimeZones": false,
    "DateFormat": "L",
    "DateTimeFormat": "L LT",
    "FloatFormat": "0,0.[00000000]",
    "IntegerFormat": "0,0",
    "Locale": null,
    "LogoImageSourceURL": "",
    "PrimaryColor": "#006FBA",
    "SecondaryColor": "#709CBA",
    "ThemeMode": "Light",
    "TimeFormat": "LT",
    "TimeZone": "America/Detroit"
  },
  "enterprise": {
    "id": "mfgx",
    "name": "MFGx",
    "maintenanceMode": false,
    "platformVersion": "2026.2.0.766",
    "environment": "build",
    "domain": "build.mfgx.fuuz.app"
  },
  "userRoles": []
}
```

**Common metadata access patterns in transforms:**
- `$metadata.user.id` — Current user ID
- `$metadata.user.firstName & " " & $metadata.user.lastName` — User display name
- `$metadata.user.accessTypeId` — Access level (`"administrator"`, `"standard"`, etc.)
- `$metadata.tenant.id` — Current tenant/app ID
- `$metadata.tenant.name` — Tenant display name
- `$metadata.screen.id` — Current screen ID
- `$metadata.screen.version.number` — Screen version
- `$metadata.settings.TimeZone` — Configured timezone
- `$metadata.settings.PrimaryColor` — Theme primary color
- `$metadata.settings.ThemeMode` — `"Light"` or `"Dark"`
- `$metadata.enterprise.environment` — `"build"`, `"production"`, etc.
- `$metadata.enterprise.maintenanceMode` — Boolean maintenance flag
- `$metadata.querystring` — URL query parameters parsed by component
- `$metadata.urlParameters` — URL route parameters

### components Object

Every component on the screen is available via `$components.<elementName>`:

```json
{
  "FormFilterPanel": {
    "data": {},
    "initialData": null,
    "defaultValues": {},
    "fn": {},
    "query": { "api": "Application", "model": "Workcenter" },
    "formState": {
      "dirty": false,
      "dirtyFields": {},
      "errors": {},
      "hasValidationErrors": false,
      "invalid": false,
      "modified": { "code": false, "cell": false },
      "pristine": true,
      "submitting": false,
      "submitFailed": false,
      "submitSucceeded": false,
      "touched": { "code": false, "cell": false },
      "valid": true,
      "values": {},
      "visited": { "code": false, "cell": false }
    }
  },
  "WorkcenterTable": {
    "data": null,
    "initialData": null,
    "defaultValues": { "cell": {}, "line": {}, "currentWorkOrder": {}, "scheduleGroup": {} },
    "fn": {},
    "currentView": {
      "name": "Default",
      "rowLimit": 500,
      "isDefault": true,
      "columns": [
        { "colId": "TableColumnActions", "hide": false, "rowGroup": false },
        { "colId": "TableColumnCode", "hide": false, "sort": "asc", "sortIndex": 0 }
      ],
      "origin": "system"
    }
  },
  "Screen": {
    "fn": {},
    "context": {}
  }
}
```

**Key component access patterns:**
- `$components.Screen.context` — Screen-level context set by `pageLoadAction`
- `$components.Screen.fn.setContext($obj)` — Set screen context
- `$components.Screen.fn.pageLoadFn()` — Re-execute pageLoadAction
- `$components.Form1.data` — Current form data
- `$components.Form1.fn.save()` — Save form
- `$components.Form1.fn.delete()` — Delete record
- `$components.Form1.fn.refresh()` — Reload form data
- `$components.Form1.fn.reset()` — Reset form to initial state
- `$components.Form1.formState.dirty` — Whether form has unsaved changes
- `$components.Form1.formState.valid` — Whether form passes validation
- `$components.Form1.formState.values` — Current form field values
- `$components.Table1.fn.search()` — Refresh table query
- `$components.Table1.data` — Current table data array
- `$components.Table1.currentView` — Active view configuration
- `$components.Cards1.fn.search()` — Refresh cards query
- `$components.Cards1.data` — Current cards data

### Transform Payload (Row-Level Context)

Every transform receives a `payload` containing the current row data. Inside table column transforms, `$data` refers to the current row:

```json
{
  "data": {
    "id": "wc-bmx-c2",
    "code": "WC-BMX-C2",
    "name": "Batch Mixing Workcenter 2",
    "description": "Primary production workcenter for Batch Mixing Cell 2",
    "cell": {
      "id": "cmmk01grpn00lfnbxmlc",
      "code": "BMX-C2",
      "name": "Batch Mixing Cell 2"
    },
    "line": {
      "id": "cmmk01grpn00lfmggxm4",
      "code": "BMIX",
      "name": "Batch Mixing"
    },
    "currentWorkOrder": {
      "id": "wo-mke-0368",
      "code": "WO-MKE-0368",
      "status": "Completed"
    },
    "isActive": true,
    "createdAt": "2026-01-05T16:00:30.920Z",
    "createdByUser": {
      "id": "ckzzmdqs8000y0jwx2o5qhwmi",
      "firstName": "Craig",
      "lastName": "Scott",
      "email": "cscott@mfgx.io"
    },
    "createdByDisplay": "Craig Scott",
    "activeColor": "#2e7d32",
    "activeDisplay": "Active",
    "modeColor": "#757575",
    "stateColor": "#757575"
  }
}
```

In transforms, access via:
- `$data.id` — Current row ID
- `$data.code` — Current row code
- `$data.cell.name` — Nested relationship field
- `$data.isActive` — Boolean field
- `$data.activeColor` — Computed display field (from dataTransform)

### Transform Trace (Debugging)

Every transform execution produces a trace object for debugging:

```json
{
  "runtime": 725,
  "property": "style",
  "id": "wc-bmx-c2",
  "column": "TableColumnActive",
  "dynamicPropPathComputeTime": 0,
  "dynamicPropPaths": [],
  "dynamicProps": {}
}
```

- `runtime` — Execution time in ms (watch for slow transforms)
- `property` — Which prop this transform applies to (`"style"`, `"data"`, `"hidden"`, etc.)
- `id` — The data record ID being processed
- `column` — Which column (for table column transforms)

### Transform Result

Each transform returns a result that gets applied to the target property:

```json
{ "color": "#2e7d32" }
```

For style transforms, the result is a CSS style object. For data transforms, the result is the transformed data value.

### Data Array

The `data` array contains the current query results (table rows, form data, etc.). Each row includes:
- All queried fields from the data model
- Nested relationship objects (cell, line, currentWorkOrder, createdByUser, etc.)
- Computed display fields added by `dataTransform` (createdByDisplay, activeColor, activeDisplay, etc.)

### initialData vs data

- `initialData` — The raw query results before any client-side transforms
- `data` — The current state after transforms and user edits

This distinction is important for forms: `formState.dirty` compares current `data` against `initialData` to detect changes.

### Preferred Data Distribution Pattern: pageLoadAction → setContext → bind

For screens that display read-only data from multiple sources, centralize data loading in `pageLoadAction` and distribute via `$components.Screen.fn.setContext()`:

```
pageLoadAction (JSONata):
  1. Execute GraphQL queries via $query()
  2. Transform/format results
  3. Call $components.Screen.fn.setContext({ key1: value1, key2: value2 })

DisplayText elements bind via:
  data.__transform: "$components.Screen.context.key1"
  data.__cacheKey: "uniqueCacheKey"
  data.__remote: false
```

**This pattern avoids:**
- Nested Forms with individual query subscriptions
- Redundant GraphQL calls for data that can be fetched once
- Complex per-element query configurations

**Use individual Form/Table queries only when:**
- The element needs a live, independent query subscription
- The data is truly independent of other screen data
- The element requires server-side `dataTransform` on query results

### Practical Examples

**Conditional styling based on row data:**
```jsonata
/* TableColumn style transform — color based on active status */
$data.isActive ? {"color": "#2e7d32"} : {"color": "#d32f2f"}
```

**Accessing metadata in pageLoadAction:**
```jsonata
(
  $tz := $metadata.settings.TimeZone;
  $userId := $metadata.user.id;
  $tenantId := $metadata.tenant.id;
  /* Use in queries or display */
)
```

**Checking form state before action:**
```jsonata
/* Disable save button when form is pristine or invalid */
$components.Form1.formState.pristine or $not($components.Form1.formState.valid)
```

**Dynamic visibility based on user access:**
```jsonata
/* Hide admin-only section for non-admins */
$metadata.user.accessTypeId != "administrator"
```

**Reading URL parameters:**
```jsonata
$metadata.querystring.Workcenters.WorkcenterTable.view
```

---

## Frontend JSONata Bindings for Screens

This section covers the JSONata bindings most relevant to screen design. All screen expressions use JSONata — JavaScript is only available in web flows. For the full 294-binding reference, consult the `fuuz_jsonata_bindings_skill.md` project knowledge file.

### Binding Context Rules

1. All custom bindings use the `$` prefix (e.g., `$uuid()`, `$moment()`).
2. **Frontend bindings** — Only available in Screen Designer expressions (action chains, `__transform`, event handlers).
3. **Shared bindings** — Available in both Screen Designer and Data Flows.
4. **Backend bindings** — Only available in server-side Data Flows (not in screens).
5. Standard JSONata from [docs.jsonata.org](https://docs.jsonata.org/) is fully supported alongside custom bindings.
6. Component bindings use `$components.{ComponentName}.fn.{method}()` — replace with the actual component `elementName`.

### Component Bindings (Frontend Only)

These are the primary bindings for screen interactivity. Replace `{Table}`, `{Form}`, `{Container}`, `{ActionButton}` with actual component element names.

**Table Operations:**

| Binding | Purpose |
|---|---|
| `$components.{Table}.fn.search({rowsPerPage: 500})` | Execute table search using filter form values. `rowsPerPage` optional. |
| `$components.{Table}.fn.loadData({filter: {field: {_eq: value}}, rowLimit: 500})` | Load data with explicit filter (bypasses filter form). |
| `$components.{Table}.fn.setData([{id, ...fields}])` | Overwrite entire table data with custom array. `id` required per row. |
| `$components.{Table}.fn.addRows([{id, ...fields}])` | Append rows to end of table. |
| `$components.{Table}.fn.updateRows([{id, ...fields}])` | Match by `id` and update existing rows. |
| `$components.{Table}.fn.upsertRows([{id, ...fields}])` | Add new rows and update existing by `id`. |
| `$components.{Table}.fn.deleteRows([id1, id2])` | Delete rows by ID array. |
| `$components.{Table}.fn.selectRow([id1, id2])` | Select specific rows by ID. |
| `$components.{Table}.fn.deselectRow([id1, id2])` | Deselect specific rows. |
| `$components.{Table}.fn.toggleRowSelection([id1, id2])` | Toggle selection state. |
| `$components.{Table}.fn.selectAllRows()` | Select all rows regardless of filtering/grouping. |
| `$components.{Table}.fn.deselectAllRows()` | Clear all selections. |
| `$components.{Table}.fn.selectAllFiltered()` | Select only filtered rows. |
| `$components.{Table}.fn.deselectAllFiltered()` | Clear filtered selections. |
| `$components.{Table}.fn.setSelectedRows([id1, id2])` | Set exact selection by IDs. |

**Form Operations:**

| Binding | Purpose |
|---|---|
| `$components.{Form}.fn.setValue(field, data)` | Set a form field value. `field` is the data path. |
| `$components.{Form}.fn.focus(field)` | Set cursor focus to a field. |
| `$components.{Form}.fn.blur(field)` | Remove cursor focus from a field. |
| `$components.{Form}.fn.loadData()` | Reload form data from the bound data model. |
| `$components.{Form}.fn.getUpdateMutation()` | Generate GraphQL update mutation (requires data model). |
| `$components.{Form}.fn.getUpdatePayload()` | Get the mutation payload for form changes. |
| `$components.{Form}.fn.validate()` | Validate form and return error array. |
| `$components.{Form}.fn.save(isSaveAll?)` | Save form. Default saves modified fields only; pass `true` to save all. |
| `$components.{Form}.fn.getVariables()` | Get all form variables. |
| `$components.{Form}.fn.delete()` | Delete the form record. |

**Container Operations:**

| Binding | Purpose |
|---|---|
| `$components.{Container}.fn.hide()` | Hide a container. |
| `$components.{Container}.fn.show()` | Show a container. |
| `$components.{Container}.fn.toggle()` | Toggle container visibility. |
| `$components.{Container}.fn.resetHidden()` | Reset all hidden fields in container. |

**Cross-Component Execution:**

| Binding | Purpose |
|---|---|
| `$components.{ActionButton}.fn.execute({field: value})` | Execute another action button's action chain. Optional parameters passed as object. |

### Navigation & Dialog Bindings (Frontend Only)

| Binding | Purpose |
|---|---|
| `$navigateTo(pathname, options)` | Navigate to a page. Options: `{newWindow: true, replace: true}` |
| `$navigateBack()` | Navigate back one page. |
| `$navigateReload()` | Reload the current page. |
| `$addNotificationMessage(title, options)` | Show notification snackbar. Options: `{severity: "success"\|"error"\|"warning"\|"info"}` |
| `$showAlertDialog(message, options)` | Show alert dialog. Options: `{title, icon, buttonColor}` |
| `$showConfirmDialog(message, options)` | Show confirm dialog. Options: `{title, delayTimeInSeconds, icon, buttonColor}` |
| `$showFormDialog(object)` | Show form dialog from JSON definition. |
| `$writeToClipboard(data)` | Write data to clipboard. |
| `$readFromClipboard()` | Read data from clipboard. |

### Data & Integration Bindings (Shared — Usable in Screens)

These are available in both screens and flows, commonly used in action chain transformations and dynamic data props.

**GraphQL Operations (used with Fuuz GraphQL syntax):**

| Binding | Example |
|---|---|
| `$query(object)` | `$query({"statement": "query { workcenter { edges { node { id name } } } }", "variables": {}})` |
| `$mutate(object)` | `$mutate({"statement": "mutation CreateWC($payload: [WorkcenterCreatePayloadInput!]!) { createWorkcenter(payload: $payload) { id } }", "variables": {"payload": [{"create": {"name": "WC-1"}}]}})` |

**Flow & Transform Execution:**

| Binding | Example |
|---|---|
| `$executeFlow(flowId, payload)` | `$executeFlow("flow-uuid", {"workcenterId": id})` — Call a request/response flow and return result. |
| `$executeTransform(transformId, payload)` | `$executeTransform("transform-uuid", data)` — Execute a saved transform. |

**Device Communication:**

| Binding | Example |
|---|---|
| `$executeDeviceFunction(object)` | `$executeDeviceFunction({"deviceId": "dev-1", "functionId": "readTag", "request": {}})` |
| `$executeDeviceGatewayFunction(object)` | `$executeDeviceGatewayFunction({"deviceGatewayId": "gw-1", "functionId": "writeTag", "request": {}})` |

**External Integration:**

| Binding | Example |
|---|---|
| `$integrate(object)` | `$integrate({"Request": {"connectionName": "SAP", "payload": []}})` |

### Commonly Used Shared Utilities in Screen Expressions

| Binding | Purpose |
|---|---|
| `$uuid()` | Generate a V4 UUID (for new record IDs). |
| `$cuid()` | Generate a CUID. |
| `$moment(string?, format?, timezone?)` | Create Moment.js object. Chain `.format()`, `.add()`, `.subtract()`, `.diff()`, `.startOf()`, `.endOf()`. |
| `$momentTz(string?, format?, timezone?)` | Create Moment.js with explicit timezone: `$momentTz("2024-01-01", "YYYY-MM-DD", "America/Detroit")`. |
| `$momentTzGuess()` | Guess user's timezone from browser (frontend only — returns UTC in backend). |
| `$momentMax(array)` | Return most distant future from array of moment objects. |
| `$momentMin(array)` | Return most distant past from array of moment objects. |
| `$now()` | Current UTC timestamp in ISO 8601 string. |
| `$log(message, meta?, level?)` | Log to console. Levels: error, warn, info, verbose, debug, silly. |
| `$throw(message, object?)` | Halt transform execution with error. |
| `$coalesce(array)` | Return first non-null/undefined value. |
| `$wait(ms)` | Pause execution for milliseconds. |
| `$jsonParse(string)` | Parse JSON string to object. |
| `$jsonStringify(value, options?)` | Serialize to JSON string. Options: `{space: 2}`. |
| `$numeral(value)` | Numeral.js object for formatting: `$numeral(0.85).format("0.0%")`. |
| `$markdownToHtml(string)` | Convert markdown to HTML (for RichText content). |
| `$richTextToPlainText(object)` | Convert RawDraftContent to plain text. |
| `$getCalendar(object)` | Get production calendar by `{id}` or `{name}`. |
| `$document(object, options?)` | Generate PDF: `$document({"documentDesignId": id, "payload": {}}, {"returnContent": true})`. |

**Type Checking (use in conditional transforms):**

| Binding | Purpose |
|---|---|
| `$isNilOrEmpty(value)` | Check if null, undefined, or empty string/array/object. |
| `$isNil(value)` | Check if null or undefined. |
| `$isEmpty(value)` | Check if empty array, object, or string. |
| `$isArray(value)` | Check if value is an array. |
| `$isString(value)` | Check if value is a string. |
| `$isNumber(value)` | Check if value is a number. |
| `$isObject(value)` | Check if value is an object. |
| `$isBoolean(value)` | Check if value is a boolean. |

**Object Manipulation (use in action chain pre-transforms):**

| Binding | Purpose |
|---|---|
| `$path(object, string)` | Get value at nested path: `$path(obj, "parent.child")`. |
| `$omit(object, keys)` | Remove properties from object. |
| `$pick(object, keys)` | Keep only specified properties. |
| `$mergeDeep(arrayOfObjects)` | Deep merge multiple objects. |
| `$has(object, path)` | Check if property exists at path. |
| `$values(object)` | Get array of object values. |
| `$toPairs(object)` | Convert to `[[key, value], ...]` array. |
| `$fromPairs(array)` | Convert `[[key, value], ...]` to object. |
| `$flattenObject(object, options?)` | Flatten nested object to single level. Options: `{delimiter: ".", safe: true}`. |
| `$objectDiff(obj1, obj2, options?)` | Compare two objects, return differences. Options: `{detailed: true}`. |

**Array Manipulation (use in table data transforms and mapping):**

| Binding | Purpose |
|---|---|
| `$groupBy(array, function)` | Group array by key function. |
| `$indexBy(array, function)` | Like groupBy but keeps only last match per key. |
| `$uniq(array)` | Remove duplicates (by reference for objects). |
| `$uniqBy(array, function)` | Remove duplicates by custom key function. |
| `$chunk(array, size)` | Split array into chunks. |
| `$flatten(array, depth?)` | Flatten nested arrays. |
| `$partition(array, function)` | Split into two arrays: [truthy, falsy]. |
| `$first(array)` | Get first element. |
| `$last(array)` | Get last element. |
| `$findFirst(array, function)` | Find first element matching predicate. |
| `$findLast(array, function)` | Find last element matching predicate. |
| `$all(array, function)` | True if all elements satisfy predicate. |
| `$any(array, function)` | True if any element satisfies predicate. |
| `$flatMap(array, function)` | Map then flatten result. |

**String Utilities (use in display transforms):**

| Binding | Purpose |
|---|---|
| `$startCase(string)` | Convert to Start Case. |
| `$camelCase(string)` | Convert to camelCase. |
| `$upperFirst(string)` | Capitalize first character. |
| `$startsWith(string, pattern)` | Check if string starts with pattern. |
| `$endsWith(string, pattern)` | Check if string ends with pattern. |
| `$pluralize(string, locale)` | Pluralize a word. |
| `$singularize(string, locale)` | Singularize a word. |
| `$htmlEscape(string)` | Escape HTML special characters. |

**CSV & Data (use in export/import actions):**

| Binding | Purpose |
|---|---|
| `$arrayToCsv(array, options?)` | Convert array of objects to CSV string. |
| `$csvToArray(string, options?)` | Parse CSV string to array of objects. |

**SQL-Style Joins (use in complex table data assembly):**

| Binding | Purpose |
|---|---|
| `$innerJoin(left, right, leftKey, rightKey, joinedKey, options?)` | Inner join two arrays. |
| `$leftOuterJoin(left, right, leftKey, rightKey, joinedKey, options?)` | Left outer join. |
| `$leftAntiJoin(left, right, leftKey, rightKey)` | Keep left items with no right match. |
| `$leftSemiJoin(left, right, leftKey, rightKey)` | Keep left items that have a right match. |
| `$crossJoin(left, right, joinedKey, options?)` | Cross join all combinations. |

**Calendar & Scheduling (use in production screen expressions):**

| Binding | Purpose |
|---|---|
| `$eventsBetween(moment1, moment2, calendar)` | Get calendar events between two dates. |
| `$getEventsAt(moment, calendar)` | Get events occurring at a specific time. |
| `$availableAt(moment, calendar)` | Check if any available event exists at the moment. |
| `$getAvailabilityBetween(moment1, moment2, calendar)` | Get events categorized by availability. |

### Fuuz JSONata Platform-Specific Constraints

Several JSONata patterns that work in standard JSONata do **not** work in the Fuuz runtime. Follow these rules to avoid platform errors.

**Working patterns in Fuuz:**
```jsonata
/* Block expressions with variable assignment */
(
  $var := expression;
  $result
)

/* Function definitions */
$fn := function($s) {(
  $r := $replace($s, /pattern/, "replacement");
  $uppercase($substring($r, 0, 1)) & $substring($r, 1)
)};

/* Direct .{} mapping (final expression) */
$metadata.userTenants.{
  "id": id,
  "field": value
}
```

**Broken patterns in Fuuz — AVOID:**
```jsonata
/* Bare ternary with .{} mapping — FAILS */
$count(arr) > 0 ? arr.{ "id": id } : [{ "id": "none" }]

/* $map with function — may cause "map is not a function" */
$map($metadata.userRoles, function($r) { ... })

/* $append for array coercion — unreliable in Cards transform */
$append([], $metadata.userRoles)
```

**Rules for Fuuz Cards/Table/List transforms:**
1. Always wrap the entire expression in `()` block expression
2. Use `.{}` mapping syntax, NOT `$map()`
3. Wrap mapping result in `[]` array literal to guarantee array output
4. Avoid bare ternary + mapping combinations
5. Handle empty arrays via container `hidden` property with `$isNilOrEmpty()`, not inline conditionals

### Screen Expression Patterns

**Dynamic text with state context:**
```
$state.context.oee ? $numeral($state.context.oee).format("0.0%") : "--"
```

**Conditional container background (status color):**
```
$state.context.status = "Running" ? "#4CAF50" : $state.context.status = "Down" ? "#F44336" : "#9E9E9E"
```

**Table search after action (in action chain transformation step):**
```
$components.Table1.fn.search()
```

**Navigate to form screen with route parameter:**
```
$navigateTo("/app/workcenter/" & id)
```

**Show notification after save:**
```
$addNotificationMessage("Record saved successfully", {"severity": "success"})
```

**Query data inline (for select options or lookups):**
```
$query({"statement": "query { workcenter(where: {isActive: {_eq: true}}) { edges { node { id name } } } }"}).workcenter.edges.node
```

**Execute flow and use result:**
```
$executeFlow("oee-calc-flow-id", {"workcenterId": $state.context.workcenterId})
```

**Disabled button when no rows selected:**
```
$count($components.Table1.selectedRows) = 0
```

**Dynamic delete confirmation message:**
```
"Delete " & $count($components.Table1.selectedRows) & " records?"
```

**Format date/time for display (user timezone):**
```
$moment(data.createdAt).format("MM/DD/YYYY hh:mm A")
```

**Calculate time elapsed (duration display):**
```
$moment().diff($moment(data.startAt), "minutes") & " min"
```

**Conditional icon color based on threshold:**
```
data.value >= 85 ? "#4CAF50" : data.value >= 60 ? "#FFC107" : "#F44336"
```

**Build filter payload from form values (action chain pre-transform):**
```jsonata
{
  "payload": {
    "create": $mergeDeep([
      data,
      {"createdAt": $now(), "id": $uuid()}
    ])
  }
}
```

**Transform table data for chart consumption:**
```jsonata
$components.Table1.data.{
  "label": name,
  "value": $string($round(oee * 100, 1))
}
```

**Aggregate selected rows for bulk action:**
```jsonata
{
  "payload": $components.Table1.selectedRows.{
    "where": {"id": id},
    "update": {"status": "Completed", "completedAt": $now()}
  }
}
```

**Join two datasets in a transform (e.g., enrich table data):**
```jsonata
$innerJoin(
  $state.context.workOrders,
  $state.context.products,
  "productId",
  "id",
  "product"
)
```

**Calendar-aware expression (check if workcenter is scheduled):**
```jsonata
$availableAt($moment(), $getCalendar({"id": data.calendarId}))
  ? "Scheduled" : "Off-Schedule"
```

---

## GraphQL Patterns for Screen Data

Screens interact with the Fuuz GraphQL API through Table/Form model bindings, action chain mutations, and `$query()`/`$mutate()` calls. For the full syntax reference, consult `Fuuz_GraphQL_Syntax___Patterns.md`. Key patterns for screen use:

### Edges/Node Pattern
All collection queries return `{ edges: [{ node: {...} }] }`. Tables handle this automatically when bound to a data model. In `$query()` calls, unwrap with: `$query(...).modelName.edges.node`.

### Table Model Query
When a Table component has `model: "workcenter"`, the platform generates a GraphQL query automatically. The `fields` array in `query` props controls which fields are fetched (including relationship paths like `site.name`). The `parameters` prop maps filter form values to `where` clause predicates.

### Where Clause Operators for Filters
```
_eq       — Equals
_gte/_lte — Greater/less than or equal
_gt/_lt   — Greater/less than
_in       — In list: ["Active", "InProgress"]
_isNull   — Null check (CRITICAL: not _is_null)
_and/_or  — Logical combinations
```

### Action Chain Mutations
Create and update mutations follow the Fuuz payload pattern:
```jsonata
/* Create — wrap with {create: {...}} */
{"payload": {"create": data}}

/* Update — wrap with {where: {...}, update: {...}} */
{"payload": {"where": {"id": id}, "update": data}}

/* Bulk update from selected rows */
{"payload": $components.Table1.selectedRows.{"where": {"id": id}}}
```

### Ordering
```graphql
orderBy: { field: createdAt, direction: desc }
```
CRITICAL: Field name is an enum — no quotes around field name.

### Aggregations (for dashboard data)
Available aggregate functions: `count`, `minimum`, `maximum`, `average`, `sum`, `standardDeviation`, `first`, `last`, `median`, `uniqueCount`, `percentile`.
CRITICAL: `minimum`, `maximum`, `first`, `last` return arrays — unwrap with `[0]`.

### Pagination
Use `first: N` to limit results. For cursor-based pagination, use `after: $cursor` with `pageInfo { hasNextPage endCursor }`.

### DateTime Format Specifiers
Some fields support format conversion in queries:
```graphql
scheduledStartAt(format: milliseconds)  /* Get as epoch ms */
scheduledEndAt(format: iso)             /* Get as ISO string (default) */
```

### Relationships & Nested Queries
Query related entities inline — no separate calls needed:
```graphql
workcenter {
  edges {
    node {
      id
      name
      site { id name timezone }           /* many-to-one */
      workOrders(where: { status: { _eq: "Active" } }) {  /* one-to-many filtered */
        edges { node { id code status } }
      }
    }
  }
}
```

### Screen-Specific GraphQL Patterns

**$query() for inline data fetching (action chains or transforms):**
```jsonata
(
  $result := $query({
    "statement": "query GetActive($where: WorkcenterWhereInput) { workcenter(where: $where, orderBy: {field: name, direction: asc}) { edges { node { id code name isActive } } } }",
    "variables": {"where": {"isActive": {"_eq": true}}}
  });
  $result.workcenter.edges.node
)
```

**$mutate() for action chain operations:**
```jsonata
$mutate({
  "statement": "mutation Update($payload: [WorkcenterUpdatePayloadInput!]!) { updateWorkcenter(payload: $payload) { id name } }",
  "variables": {
    "payload": [{
      "where": {"id": data.id},
      "update": {"name": data.name, "isActive": data.isActive}
    }]
  }
})
```

**Aggregation query for dashboard metrics:**
```jsonata
(
  $stats := $query({
    "statement": "query Stats($where: TelemetryRawWhereInput) { telemetryRaw(where: $where, groupBy: [dataPointId]) { edges { node { dataPointId _aggregate { value { count average minimum maximum } } } } } }",
    "variables": {"where": {"_and": [{"recordedAt": {"_gte": $state.context.startTime}}, {"recordedAt": {"_lte": $state.context.endTime}}]}}
  });
  $stats.telemetryRaw.edges.node.{
    "dataPointId": dataPointId,
    "count": _aggregate.value.count,
    "avg": _aggregate.value.average,
    "min": _aggregate.value.minimum[0],
    "max": _aggregate.value.maximum[0]
  }
)
```

---

## Chart Component (FusionCharts)

For inline FusionCharts visualizations embedded directly in a screen. The Fuuz visualization engine is powered by FusionCharts — all chart configurations follow a JSON schema where `chart` properties control appearance/behavior, and data structure objects (`colorRange`, `dials`, `dataset`, `categories`, `trendlines`, etc.) define the data payload. For the complete chart property reference, consult `Fuuz_Visualization_Library_Reference.md`.

```json
{
  "OeeGaugeChart": {
    "type": { "resolvedName": "Chart" },
    "isCanvas": false,
    "props": {
      "chartType": "angulargauge",
      "width": "100%",
      "height": "200px",
      "formElement": "OeeForm",
      "description": "Angular gauge showing current OEE percentage",
      "data": {
        "__transform": "{ 'chart': { 'caption': 'OEE', 'lowerLimit': '0', 'upperLimit': '100', 'showValue': '1', 'theme': 'fusion' }, 'colorRange': { 'color': [{ 'minValue': '0', 'maxValue': '60', 'code': '#F44336' }, { 'minValue': '60', 'maxValue': '85', 'code': '#FFC107' }, { 'minValue': '85', 'maxValue': '100', 'code': '#4CAF50' }] }, 'dials': { 'dial': [{ 'value': $string($round($state.context.oee * 100, 1)) }] } }",
        "__cacheKey": "oeeGauge",
        "__dynamicFields": {
          "payload": [],
          "context": ["state.context.oee"]
        }
      }
    },
    "displayName": "Chart",
    "custom": { "elementName": "OeeGaugeChart" },
    "parent": "ChartContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Chart Data Binding

Chart data is provided via a `__transform` expression that builds the FusionCharts data structure. The transform must return the complete JSON object expected by the chart type. Common data structures by chart category:

**Gauge charts** (angulargauge, bulb, hled, cylinder, thermometer):
```jsonata
{
  "chart": { "caption": "Temperature", "lowerLimit": "0", "upperLimit": "200", "numberSuffix": "°F" },
  "colorRange": { "color": [
    { "minValue": "0", "maxValue": "100", "code": "#4CAF50" },
    { "minValue": "100", "maxValue": "150", "code": "#FFC107" },
    { "minValue": "150", "maxValue": "200", "code": "#F44336" }
  ]},
  "dials": { "dial": [{ "value": $string(data.temperature) }] }
}
```

**Single-series charts** (column2d, bar2d, line, area2d, pie2d, doughnut2d):
```jsonata
{
  "chart": { "caption": "Production by Line", "xAxisName": "Line", "yAxisName": "Units" },
  "data": $state.context.production.{ "label": name, "value": $string(quantity) }
}
```

**Multi-series charts** (mscolumn2d, msline, msarea, mscombi2d):
```jsonata
{
  "chart": { "caption": "OEE Trend", "xAxisName": "Date", "yAxisName": "%" },
  "categories": [{ "category": $state.context.dates.{ "label": label } }],
  "dataset": [
    { "seriesname": "Availability", "data": $state.context.availability.{ "value": $string(value) } },
    { "seriesname": "Performance", "data": $state.context.performance.{ "value": $string(value) } },
    { "seriesname": "Quality", "data": $state.context.quality.{ "value": $string(value) } }
  ]
}
```

**Real-time charts** (realtimeline, realtimecolumn, realtimearea):
```jsonata
{
  "chart": { "caption": "Live Temperature", "yAxisMaxValue": "200", "refreshInterval": "2" },
  "categories": [{ "category": [{ "label": $moment().format("HH:mm:ss") }] }],
  "dataset": [{ "data": [{ "value": $string(data.currentTemp) }] }]
}
```

**CRITICAL:** All chart `value` fields must be strings — use `$string()` to convert numbers.

### Chart Component Props

| Prop | Type | Description |
|---|---|---|
| `chartType` | string | FusionCharts type identifier (e.g., `"angulargauge"`, `"column2d"`, `"msline"`). |
| `width` | string | Chart width (e.g., `"100%"`, `"400px"`). |
| `height` | string | Chart height (e.g., `"200px"`, `"300px"`). |
| `formElement` | string | Name of parent Form component (for data context). |
| `data` | `__transform` object | JSONata expression returning the complete FusionCharts data object. |
| `description` | string | Element description (required per design principles). |

### Common Chart Types for Manufacturing

| Chart Type | Use Case |
|---|---|
| `angulargauge` | OEE, Availability, Performance, Quality gauges |
| `column2d` / `bar2d` | Hourly production, downtime by event |
| `mscolumn2d` / `msbar2d` | Multi-series comparison (shift vs shift, line vs line) |
| `line` / `msline` | Trend lines (OEE over time, temperature trends) |
| `stackedcolumn2d` | Time allocation (run vs downtime vs planned) |
| `stackedcolumn2dline` | Stacked with cumulative line (Pareto analysis) |
| `doughnut2d` / `pie2d` | Loss distribution, quality breakdown, rejection reasons |
| `heatmap` | Hourly × daily OEE heatmap, machine utilization matrix |
| `gantt` | State timeline, shift schedule, work order scheduling |
| `bulb` | Simple status indicator (running/stopped/alarm) |
| `realtimeline` | Live sensor data, real-time process monitoring |
| `radar` | Multi-dimensional KPI comparison |
| `pareto` | Defect analysis, downtime Pareto |
| `waterfall2d` | Time loss waterfall, yield analysis |
| `treemap` | Production hierarchy breakdown |

---

## Visualization Component (Saved Visualizations)

The Fuuz Visualization Library provides 80+ chart types powered by FusionCharts. Charts can be used in screens two ways:

1. **Inline Chart component** — Configure `chartType`, `chart` properties, and data structures directly in the screen JSON (see Chart Component above).
2. **Saved Visualization component** — Reference a pre-built visualization by ID. The visualization is configured and maintained in the Fuuz Visualization Designer, then embedded in screens. This is the **preferred approach** for reusable, maintainable charting.

For the complete chart configuration reference (all chart types, properties, data structures), consult `Fuuz_Visualization_Library_Reference.md`.

### Saved Visualization Component

```json
{
  "SavedViz": {
    "type": { "resolvedName": "Visualization" },
    "isCanvas": false,
    "props": {
      "visualizationId": "saved-viz-id",
      "width": "100%",
      "height": "300px",
      "description": "Saved OEE trend visualization"
    },
    "displayName": "Visualization",
    "custom": { "elementName": "SavedViz" },
    "parent": "ChartContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
```

### Visualization Component Props

| Prop | Type | Description |
|---|---|---|
| `visualizationId` | string | ID of the saved visualization in the platform. |
| `width` | string | Component width (e.g., `"100%"`, `"500px"`). |
| `height` | string | Component height (e.g., `"300px"`). |
| `description` | string | Element description. |

### When to Use Saved Visualizations vs Inline Charts

| Scenario | Approach |
|---|---|
| Standard KPI dashboard with reusable charts | Saved Visualization — configure once, embed in multiple screens |
| One-off gauge or indicator embedded in a form | Inline Chart — simpler, self-contained |
| Chart data driven by `$state.context` from a page load flow | Either — saved viz can bind to screen context; inline Chart uses `__transform` |
| Complex multi-series chart needing frequent tuning | Saved Visualization — easier to iterate without editing screen JSON |
| Real-time charts (`realtimeline`, `realtimecolumn`, etc.) | Inline Chart — typically bound to live data via intervals and `$state.context` |

### Visualization Library Chart Categories

The full library (documented in `Fuuz_Visualization_Library_Reference.md`) includes:

| Category | Chart Types | Typical Manufacturing Use |
|---|---|---|
| Gauges & Indicators | `angulargauge`, `hled`, `bulb`, `cylinder`, `hbullet`, `thermometer`, `vbullet`, `vled` | OEE/KPI gauges, tank levels, status indicators |
| Single-Series | `area`, `bar2d`, `column2d`, `line`, `logcolumn2d`, `logline` | Hourly production, single-metric trends |
| Multi-Series | `msarea`, `msbar2d`, `mscolumn2d`, `msline`, `multiaxisline`, `msstepline` | Shift comparison, multi-workcenter overlay |
| Stacked | `stackedcolumn2d`, `stackedbar2d`, `stackedarea2d`, `stackedcolumn2dline` | Time allocation (run/down/planned), loss breakdown |
| Combination | `mscombi2d`, `mscombidy2d`, `mscolumn3dlinedy` | Column + line overlays (production + OEE trend) |
| Scroll | `scrollcolumn2d`, `scrollline2d`, `scrollcombi2d`, `scrollstackedcolumn2d` | Large date-range trends with scrollable axis |
| Pie & Doughnut | `pie2d`, `doughnut2d`, `multilevelpie` | Downtime category distribution, quality loss types |
| Scatter & Bubble | `scatter`, `bubble`, `selectscatter` | Correlation analysis, parameter clustering |
| Real-Time | `realtimeline`, `realtimecolumn`, `realtimearea`, `realtimelinedy` | Live telemetry, running process values |
| Specialty | `gantt`, `heatmap`, `radar`, `candlestick`, `boxandwhisker2d`, `treemap`, `sankey`, `sunburst` | Schedule timelines, hourly OEE heatmaps, SPC |
| Spark | `sparkline`, `sparkcolumn`, `sparkwinloss` | Inline micro-charts in table cells or metric cards |
| Spline | `spline`, `msspline`, `mssplinearea` | Smooth curve variants of line/area charts |
| Zoom | `zoomline`, `zoomlinedy`, `zoomscatter` | Large dataset exploration with zoom/pan |

### Dashboard Layout with Mixed Chart/Visualization Components

Dashboards often combine inline charts (for dynamic state-bound data) with saved visualizations (for standard KPI views):

```
ChartsRow (row, wrap, gap:16px)
├── GaugeContainer (flexGrow, width: 200px)
│   └── OeeGauge (Chart, angulargauge, data from $state.context)
├── TrendContainer (flexGrow, width: 400px)
│   └── OeeTrend (Visualization, saved-viz-id for weekly OEE trend)
├── ParetoContainer (flexGrow, width: 400px)
│   └── DowntimePareto (Chart, pareto, data from $state.context)
└── HeatmapContainer (flexGrow, width: 400px)
    └── UtilizationHeatmap (Visualization, saved-viz-id for utilization matrix)
```

---

## Dashboard Screen Patterns (from Production Admin Screens)

These patterns are extracted from the Fuuz Tenant Admin Home and Enterprise Admin Home screens — production dashboards with 172 and 219 components respectively. They demonstrate proper styling, layout hierarchy, pageLoadAction scripting, and context-driven data binding.

### Dashboard Architecture Pattern — Centralized Data Flow

**CRITICAL PATTERN:** For screens that display read-only data from multiple sources (user metadata, settings, computed values), **always** prefer the `pageLoadAction → setContext → bind` pattern over individual Form/Table queries. This reduces GraphQL calls and centralizes data formatting logic.

Dashboard screens use this flow:

1. **`pageLoadAction`** — JSONata script that runs on screen load:
   - Executes one or more `$query()` calls to fetch aggregate data
   - Transforms/formats results
   - Calls `$components.Screen.fn.setContext({ ... })` to make data available
2. **Form wrappers** — `autoLoad: false` forms that bind DisplayText elements to context data
3. **DisplayText with `__transform`** — Reads values from `$components.Screen.context.*` with full triplet (`__transform`, `__cacheKey`, `__remote: false`)
4. **Clickable containers** — `onClickTransform: true` with `onClickActions.__transform` for navigation

**Data binding pattern from context:**
```
DisplayText elements bind via:
  data.__transform: "$components.Screen.context.fieldName"
  data.__cacheKey: "uniqueKey"
  data.__remote: false
```

### pageLoadAction Pattern (Tenant Admin Example)

```jsonata
(
  /* Define GraphQL query — use aliases for multiple calls to same model */
  $statement := 'query AdminHomeData {
    dataModel { total }
    dataModelDeployed: dataModel(where: { currentDataModelDeploymentId: { _isNull: false } }) { total }
    screen { total }
    screenDeployed: screen(where: { currentDeploymentId: { _isNull: false } }) { total }
    user { total }
    userActive: user(where: { active: { _eq: true } }) { total }
    deviceGateway { total }
    gatewayConnected: deviceGateway(where: { socketActive: { _eq: true } }) { total }
  }';

  /* Execute query */
  $result := $query({"statement": $statement});

  /* Helper to safely return 0 for null values */
  $n := function($v) { $v ? $v : 0 };

  /* Process metrics */
  $dmTotal := $n($result.dataModel.total);
  $usrTotal := $n($result.user.total);
  $usrActive := $n($result.userActive.total);

  /* Build structured context object */
  $context := {
    'dataModels': { 'total': $dmTotal, 'deployed': $n($result.dataModelDeployed.total) },
    'users': { 'total': $usrTotal, 'active': $usrActive, 'inactive': $usrTotal - $usrActive },
    'gateways': { 'total': $n($result.deviceGateway.total), 'connected': $n($result.gatewayConnected.total) },
    'lastRefresh': $now()
  };

  /* Set screen context — all DisplayText elements read from this */
  $components.Screen.fn.setContext($context);
)
```

**Key patterns in pageLoadAction:**
- Wrap entire expression in parentheses `( ... )`
- Use `$query()` with `"api": "system"` for system-level data
- Use aliases in GraphQL for multiple filters on same model
- Always define a null-safe helper: `$n := function($v) { $v ? $v : 0 }`
- Use `$numeral().format()` for number formatting in context
- End with `$components.Screen.fn.setContext($context)`

### System API Query in pageLoadAction (Enterprise Admin Pattern)

```jsonata
$sysResult := $query({
  "api": "system",
  "statement": $systemStatement,
  "variables": {
    "where": { "id": { "_in": [$metadata.tenant.id] } }
  }
});
```

### KPI Card Container Pattern

Standard metric card (310×110px, shadow, colored border):

```json
{
  "AdminCardDataModels": {
    "type": { "resolvedName": "Container" },
    "isCanvas": true,
    "props": {
      "padding": 8, "margin": 8,
      "flexDirection": "column", "justifyContent": "space-between", "alignItems": "stretch",
      "width": "310", "height": "110",
      "flexGrow": true, "flexShrink": false, "flexWrap": true,
      "shadow": true, "borderRadius": 8,
      "background": "paper", "gap": "8px",
      "bordersInput": { "borderMode": "single", "border": "2px solid #2563eb" },
      "style": { "box-shadow": "2px 2px 6px #8b5cf6" },
      "layout": "flex",
      "onClickTransform": false,
      "collapsible": false, "collapsedFooter": false, "collapsedSize": 0, "collapsedByDefault": false,
      "justifyItems": "stretch",
      "description": "Data Models KPI card"
    },
    "displayName": "Container",
    "custom": { "elementName": "AdminCardDataModels" },
    "parent": "AdminMetricsRow1",
    "hidden": false,
    "nodes": ["AdminCardDataModelsContent"],
    "linkedNodes": {}
  }
}
```

**Card color coding convention:**
- `#2563eb` (blue border) — Primary/foundational metrics
- `#8b5cf6` (purple shadow) — Accent shadow
- `#10b981` (green text) — Healthy/positive values
- `#d97706` (amber text) — Warning/draft counts
- `#dc2626` (red text) — Critical/offline counts

### Dashboard DisplayText with Context Binding

**Large metric value (h3, green):**
```json
{
  "fontSize": "h3",
  "fontColor": "#10b981",
  "formElement": "AdminDataModelsForm",
  "dataPath": "dataModels",
  "data": {
    "__transform": "$components.Screen.context.dataModels.label",
    "__dynamicFields": { "payload": [], "context": ["components.Screen.context.dataModels.label"] },
    "__cacheKey": "dm-label"
  },
  "description": "Deployed count - data models currently deployed"
}
```

**Secondary metric with conditional formatting (h4, amber):**
```json
{
  "fontSize": "h4",
  "fontColor": "#d97706",
  "formElement": "AdminDataModelsForm",
  "dataPath": "dataModelLabels",
  "data": {
    "__transform": "$components.Screen.context.dataModels.draft > 0 ? \" / \" & $components.Screen.context.dataModels.draft : \" / 0\"",
    "__dynamicFields": { "payload": [], "context": ["components.Screen.context.dataModels.draft"] }
  },
  "label": {
    "__transform": "$components.Screen.context.dataModels.deployed",
    "__dynamicFields": { "payload": null, "context": ["components.Screen.context.dataModels.deployed"] },
    "__cacheKey": "dmodelDeployedValue",
    "__remote": false,
    "__fallbackValue": "0 / "
  },
  "description": "Deployed / Draft count"
}
```

### Clickable Navigation Container

```json
{
  "props": {
    "onClickTransform": true,
    "onClickActions": {
      "__transform": "$navigateTo('https://academy.fuuz.com', {'newWindow': true, 'replace': false})"
    },
    "style": { "cursor": "pointer" },
    "description": "Link to Fuuz Academy"
  }
}
```

### Dashboard Refresh Action Button

```json
{
  "type": { "resolvedName": "ActionButton" },
  "props": {
    "text": "Refresh",
    "buttonColor": "primary",
    "useIconButton": true,
    "icon": { "icon": "arrows-rotate" },
    "action": [
      {
        "type": "transformation",
        "transformation": "($components.Screen.fn.pageLoadFn(); $components.RecentActivityCards.fn.search())",
        "remote": false
      }
    ],
    "description": "Refresh all dashboard metrics"
  }
}
```

### CRITICAL: Array Wrapping for Data Transforms

Any `dataTransform` that should produce an array — for Cards, Tables, or any list-rendering component — **must** wrap the mapping expression in `[]`. In JSONata, when a `.{}` mapping resolves to only **one** matching record, it returns a **single object** instead of an array. Components that iterate over results will error.

```jsonata
// ❌ WRONG — Single-result returns object, not array:
$metadata.userTenants.{
  "id": id,
  "tenantName": tenant.name
}

// ✅ CORRECT — Always wrap in [] and () for block scoping:
(
  [$metadata.userTenants.{
    "id": id,
    "tenantName": tenant.name
  }]
)
```

**Rule:** Always wrap mapping expressions in `[]` to guarantee array output regardless of whether 0, 1, or many records match. The entire expression should also be wrapped in `()` for block scoping.

### Cards with Data Transform (Enterprise Pattern)

Cards element that transforms query results into dynamic card data:

```json
{
  "query": {
    "api": "System",
    "model": "Enterprise",
    "dataPath": "edges",
    "dataTransform": {
      "transform": "($connectors := $$.edges[0].node.metrics.counts.connectors; $each($connectors, function($v, $k) { { \"label\": $k, \"count\": $v } }))",
      "remote": true
    },
    "autoLoad": true,
    "fields": ["id", "metrics"],
    "parameters": "{}"
  },
  "hidden": {
    "__transform": "$isNilOrEmpty($components.Cards8.data)",
    "__dynamicFields": { "payload": null, "context": ["$components.Cards8.data"] },
    "__cacheKey": "",
    "__remote": false,
    "__fallbackValue": true
  }
}
```

Note the `hidden` property uses a dynamic transform to hide the Cards component when no data is available.

### Dynamic Visibility Pattern

Any element can be dynamically hidden using a `__transform` on the `hidden` property:

```json
{
  "hidden": {
    "__transform": "$components.Screen.context.gettingStarted.showSection = false",
    "__dynamicFields": {
      "payload": null,
      "context": ["components.Screen.context.gettingStarted.showSection"]
    },
    "__cacheKey": "show-getting-started",
    "__remote": false,
    "__fallbackValue": true
  }
}
```

### Dashboard Layout Hierarchy

```
ROOT (Screen) — column, paper, removeOuterMargin:true, forceFullscreen:true
├── HeaderContainer — row, flexWrap:true, padding:6
│   ├── TitleGroup — row, alignItems:center, gap:8px
│   │   ├── Icon — app icon
│   │   └── TitleText — RichText with screen title
│   └── ActionGroup — row, gap:4px
│       ├── RefreshButton — ActionButton
│       └── OtherActions — ActionButtons
├── BrandSeparator — height:4px, gradient background
├── MetricsRow1 — row, flexWrap:true, gap:8px, padding:8
│   ├── KPICard1 (310×110, shadow, colored border)
│   │   ├── CardHeader — row, space-between
│   │   │   ├── Label — RichText
│   │   │   └── Icon — colored icon
│   │   └── CardForm (autoLoad:false)
│   │       └── ValueContainer — row
│   │           ├── MainValue — DisplayText h3 green
│   │           └── SecondaryValue — DisplayText h4 amber/red
│   ├── KPICard2 ...
│   └── KPICard3 ...
├── MetricsRow2 — same pattern
├── ContentSection — column, padding:8
│   ├── SectionHeader — RichText
│   └── SectionContent — Cards/Table/DataTreeView
└── HelpSection — row, flexWrap:true
    ├── HelpCard1 — clickable container with onClickActions
    └── HelpCard2 ...
```

---

## Screen Layout Templates

### Template 1: Dashboard Screen

```
ROOT (Screen, column, forceFullscreen, pageLoadFlows)
├── HeaderContainer (row, space-between, wrap)
│   ├── TitleGroup (row, gap:12px)
│   │   └── TitleForm → TitleText (RichText)
│   └── RefreshGroup (row, gap:8px)
│       ├── TimestampForm → TimestampText (DisplayText)
│       └── RefreshButton (ActionButton)
├── BrandSeparator (gradient bar, 4px height)
└── MainContent (column, flexGrow, gap:20px)
    ├── MetricsRow (row, wrap, gap:16px)
    │   ├── MetricCard1 (card container)
    │   │   ├── CardHeader (row, icon + label)
    │   │   └── CardForm → ValueText (DisplayText)
    │   ├── MetricCard2 ...
    │   └── MetricCard3 ...
    ├── ChartsRow (row, wrap, gap:16px)
    │   ├── ChartContainer1 (flexGrow)
    │   │   └── Chart or Visualization
    │   └── ChartContainer2 (flexGrow)
    │       └── Chart or Visualization
    └── DetailRow (row, wrap, gap:16px)
        └── ...
```

### Template 2: Table Screen (Master/History/Setup)

```
ROOT (Screen, column, height:100%, background:paper)
└── ContainerOuter (row, wrap, height:0px, flexGrow)
    ├── ResizableFilterPanel (ResizablePanelLayout, defaultSize:300, maxSize:300)
    │   └── ContainerFilterOuter (column, 100%, flexShrink:true)
    │       ├── ContainerFilterPanelHeader (column, 40px, light bg, shadow)
    │       │   ├── ContainerFilterPanelHeaderInner (row, space-between, 35px)
    │       │   │   ├── FilterPanelTitle (RichText, "Filter Panel", bold, blue)
    │       │   │   └── SearchButtonContainer (30px)
    │       │   │       └── ActionSearch (ActionButton, magnifying-glass, $components.Table1.fn.search())
    │       │   └── BrandBandSeparator (gradient, 4px)
    │       └── FormFilterPanel (Form, autoLoad:false)
    │           └── FilterContainer (column, flexGrow)
    │               ├── SelectInput × N (filter dropdowns, variant:filled, width:100%)
    │               ├── Switch (toggle filter)
    │               ├── Checkbox (boolean filter)
    │               └── WrappedGraphQLPredicate (advanced filter, always last)
    └── ContainerBody (column, flexGrow, width:0px, borderLeft: 0.5px solid #5b30df)
        ├── TopActionSkeleton (row, 35px, light bg, shadow)
        │   ├── ContainerActionsStandard (row, 100px, space-between)
        │   │   ├── ActionCreateSingle (plus, success/green)
        │   │   └── ActionDeleteMultiple (trash-list, error/red)
        │   └── ContainerActionsCustom (row, 250px)
        │       ├── Custom action buttons...
        │       └── ActionWikiView (book-open)
        ├── BrandBandSeparator (gradient, 4px)
        └── Table1 (Table, model binding, height:0px, flexGrow)
            ├── MenuColumn (format:menu, 40px, ellipsis-v)
            ├── ImageColumn (format:image, for Master screens)
            ├── DataColumns (name, code, description, etc.)
            ├── CreatedAt (format:datetime, timeZone:setting)
            ├── CreatedByUser (format:text, data transform: firstName & lastName)
            ├── UpdatedAt (format:datetime, timeZone:setting)
            └── UpdatedByUser (format:text, data transform: firstName & lastName)
```

### Template 3: Form Screen — Standard System Page (with Sidebar)

**IMPORTANT: Sidebar Restrictions** — The 280px fixed sidebar pattern is for **admin dashboard home screens** (Tenant_App_Admin, Enterprise_Admin) and **standard system pages** (model detail/edit screens). Profile screens and detail pages should use **inline sections** with row-based layouts (side-by-side cards) instead. Only use the sidebar pattern when the skill reference examples explicitly show it for that screen type.

```
ROOT (Screen, column, alignItems:center, height:100%)
├── ScreenHeaderandActions (column, 40px)
│   ├── HeaderBar (row, space-between, 35px, default bg, shadow, borderBottom)
│   │   └── HeaderContent (row)
│   │       ├── TitleSection (row, back button + title + status indicator)
│   │       └── ActionsSection (row, save/edit/delete buttons)
│   └── BrandBandSeparator (gradient, 4px)
└── MainContainer (row, flexGrow, height:0px, overflow:hidden)
    ├── MainContentForm (column, padding:12, flexGrow, width:0px, scrollable)
    │   └── FormContent (Form, model binding)
    │       └── Sections with input fields...
    └── SideBarContainer (column, 280px, grid layout, columns:1, gap:12px, default bg)
        ├── EssentialDetails (card, borderRadius:4)
        │   ├── Header (grid, icon + "Details" title + help button, borderBottom)
        │   └── Form → Detail fields display
        ├── RelatedModels (card)
        │   ├── Header (grid, icon + title)
        │   └── Related data lists/tables
        └── QuickCustomActionsContainer (card)
            ├── Header (grid, bolt icon + "Quick Actions")
            └── Action buttons (View History, etc.)
```

### Template 4: Form Screen — Standard System Dialogue (no Sidebar)

```
ROOT (Screen, column, alignItems:center, height:100%)
├── ScreenHeaderandActions (column, 40px)
│   ├── HeaderBar (row, space-between, 35px, default bg, shadow)
│   │   └── HeaderContent (row, title + actions)
│   └── BrandBandSeparator (gradient, 4px)
└── MainContainer (row, flexGrow, height:0px, overflow:hidden)
    └── MainContentForm (column, padding:12, flexGrow, width:0px, scrollable)
        └── FormContent (Form, model binding)
            └── Sections with input fields...
```

### Template 5: Mobile Screen

```
ROOT (Screen, column, alignItems:center, height:100%, background:paper)
└── ContainerOuter (column, space-between, 100%, overflow:hidden)
    ├── ContainerHeader (row, space-between, 40px, padding:4)
    │   ├── TitleContainer (row, flexGrow)
    │   │   └── ScreenName (RichText, bold, fontsize-18)
    │   └── ContainerNavigation (column, 50px)
    │       └── MenuButton (bars icon, large)
    ├── BrandBandSeparator (gradient, 4px)
    ├── ContainerBody (column, flexGrow, height:0px, scrollable, scrollbar-width:thin)
    │   └── Form1 (Form, autoLoad:false)
    │       └── ContainerInnerBody (column, padding:6, flexGrow)
    │           ├── TextInput (height:120px, dataFontSize:24, variant:outlined)
    │           ├── TextInput (height:120px, dataFontSize:24, variant:outlined)
    │           └── SelectInput (height:120, dataFontSize:24, variant:outlined)
    ├── BrandBandSeparator (gradient, 4px)
    └── ContainerFooter (row, space-between, 60px)
        ├── SubmitButton (FlowButton, green, 50%, customTextSize:24)
        └── CancelButton (FlowButton, amber, 50%, customTextSize:24)
```

---

## Table Screen Variant Standards

### Master Screen
- `selectable: "single"` — row selection for edit/delete
- `autoLoad: false` — loads via filter search
- `showToolPanels: true`, `showStatusBar: true`, `enableGrouping: true`
- `showColumnMenu: false`, `disableColumnDragging: true`, `disableDragLeaveHidesColumns: true`
- `enableCharts: false`
- Includes image column, menu column with Edit/Delete/Info actions
- Action bar: Create + Delete buttons
- Default sort: by name ascending (`"field": "TableColumn1", "direction": "asc"`)

### History Screen
- `selectable: "none"` — read-only
- `autoLoad: true` — loads data immediately
- `showToolPanels: true`, `enableGrouping: true`, `enableCharts: true`
- `showColumnMenu: true`
- No image column, no edit/delete in menu
- Default sort: by createdAt descending (`"direction": "desc"`)
- Extra GraphQL Where Input in filter panel (`_and.0` targeting)

### Setup Screen
- `selectable: "none"` — simple list
- `autoLoad: true`
- `showToolPanels: false`, `showStatusBar: false`, `enableGrouping: false`
- `showColumnMenu: false`, `disableColumnDragging: true`, `disableDragLeaveHidesColumns: true`
- `enableCharts: false`
- Minimal columns, compact layout
- Default sort: by name ascending

---

## Standard UI Patterns

### BrandBandSeparator

A consistent branding element used throughout all screen types:

```json
{
  "BrandBandSeparator": {
    "type": { "resolvedName": "Container" },
    "isCanvas": true,
    "props": {
      "padding": 0, "margin": 0,
      "flexDirection": "column", "width": "100%", "height": "4px",
      "flexGrow": false, "flexShrink": false, "shadow": true,
      "borderRadius": 0, "layout": "flex",
      "style": {
        "background-image": "linear-gradient(90deg, #39005a,#5b30df, #03caaf)"
      }
    },
    "displayName": "Container",
    "custom": { "elementName": "BrandBandSeparator" }
  }
}
```

### ContainerBody Left Border (Table Screens)

Separates filter panel from data body:
```json
"bordersInput": {
  "borderMode": "multi",
  "border": "0px undefined ",
  "borderLeft": "0.5px solid #5b30df"
}
```

### Top Action Bar (Table Screens)

```json
{
  "TopActionSkeleton": {
    "props": {
      "flexDirection": "row", "justifyContent": "flex-start", "alignItems": "center",
      "width": "100%", "height": "35px",
      "flexGrow": false, "flexShrink": false, "shadow": true,
      "background": "light",
      "bordersInput": {
        "borderMode": "multi", "border": "0px undefined ",
        "borderLeft": "0px undefined ", "borderBottom": "0px none "
      }
    }
  }
}
```

### Scrollable Content Container

Standard pattern for scrollable form/mobile bodies:
```json
"style": {
  "overflow-y": "auto",
  "overflow-x": "hidden",
  "scrollbar-width": "thin",
  "scrollbar-color": "#5b30df #e2e8f0"
}
```

### Status Indicator Pill (Form Screens)

```json
{
  "StatusIndicatorContainer": {
    "props": {
      "flexDirection": "row", "justifyContent": "space-around", "alignItems": "center",
      "width": "70", "height": "24",
      "borderRadius": 12, "background": "custom", "customBackground": "#dcfce7"
    },
    "nodes": ["StatusDot", "StatusLabel"]
  },
  "StatusDot": {
    "type": { "resolvedName": "Container" },
    "props": {
      "width": "10", "height": "10", "borderRadius": 5,
      "background": "custom", "customBackground": "#2e7d32"
    }
  },
  "StatusLabel": {
    "type": { "resolvedName": "RichText" },
    "props": { "width": "40", "content": { "blocks": [{ "text": "Active", "inlineStyleRanges": [{ "style": "fontsize-12" }] }] } }
  }
}
```

### Sidebar Section Header (Form Screens)

Each sidebar card uses a consistent header pattern with grid layout:
```json
{
  "SectionHeader": {
    "props": {
      "flexDirection": "row", "alignItems": "center",
      "width": "100%", "height": "40",
      "layout": "grid", "columns": 2, "gap": "5px",
      "bordersInput": {
        "borderMode": "multi", "border": "0px undefined ",
        "borderBottom": "1px solid #e2e8f0"
      }
    },
    "nodes": ["SectionIcon", "SectionTitle"]
  }
}
```

Icons for sidebar sections: `circle-info` (details), `bolt` (quick actions), `link` (related models)

---

## Action Chain Patterns

### Create Record (Master Table)

```json
"action": [
  {
    "type": "form",
    "submitIcon": "plus",
    "fields": [
      {
        "name": "label", "label": "Label", "type": "text",
        "dataPath": "label", "description": "The label of the new record.",
        "validation": { "required": true },
        "query": { "fields": ["label"] }
      }
    ]
  },
  {
    "type": "mutation",
    "api": "Application",
    "transforms": { "pre": "{\n  \"payload\": {\n    \"create\": data\n  }\n}" },
    "message": { "__transform": "mutation.label & \" created successfully.\"" }
  },
  {
    "type": "transformation",
    "transformation": "$components.Table1.fn.search()",
    "remote": false,
    "message": { "__transform": "data ? \"Data updated successfully\" : null" }
  }
]
```

### Delete Selected Records (Master Table)

```json
"action": [
  {
    "type": "confirmation",
    "buttonColor": "error",
    "submitIcon": { "icon": "trash", "variant": "solid" },
    "disabled": {
      "__transform": "$count($components.Table1.selectedRows) = 0",
      "__dynamicFields": { "context": ["components.Table1.selectedRows"] }
    },
    "message": {
      "__transform": "\"Delete \" & $count($components.Table1.selectedRows) & \" records?\""
    }
  },
  {
    "type": "mutation",
    "api": "Application",
    "transforms": {
      "pre": "{\n  \"payload\": $components.Table1.selectedRows.{\n    \"where\": {\n      \"id\": id\n    }\n  }\n}"
    }
  },
  {
    "type": "transformation",
    "remote": false,
    "transformation": "$components.Table1.fn.search()"
  }
]
```

### Table Row Menu Actions

```json
{
  "format": "menu",
  "dataPath": "actions",
  "width": "40px",
  "menuIcon": "ellipsis-v",
  "iconSize": "small",
  "actions": [
    {
      "text": "Information",
      "icon": { "icon": "circle-info", "color": "info", "variant": "regular", "size": "small" },
      "action": [{ "type": "confirmation", "message": { "__transform": "rowData.description" } }]
    },
    {
      "text": "Edit",
      "icon": { "icon": "pencil", "color": "warning", "variant": "regular", "size": "small" }
    },
    {
      "text": "Delete",
      "icon": { "icon": "trash", "color": "error", "variant": "regular", "size": "small" }
    }
  ]
}
```

---

## TableColumn Data Transform (Related Entities)

For displaying related entity fields (e.g., createdByUser name):

```json
{
  "TableColumn7": {
    "type": { "resolvedName": "TableColumn" },
    "props": {
      "label": "Created By User",
      "format": "text",
      "sortable": true,
      "dataPath": "createdByUser.id",
      "field": "createdByUser.id",
      "description": "The user who created this record.",
      "editable": false,
      "query": {
        "fields": ["createdByUser.id", "createdByUser.firstName", "createdByUser.lastName"]
      },
      "data": {
        "__transform": "data.createdByUser.firstName & \" \" & data.createdByUser.lastName",
        "__dynamicFields": { "payload": null, "context": [] },
        "__cacheKey": "",
        "__remote": false
      }
    }
  }
}
```

**Standard audit columns** (include in all table screens):
- `createdAt` (format: datetime, timeZone: "setting")
- `createdByUser` (format: text, data transform for full name)
- `updatedAt` (format: datetime, timeZone: "setting")
- `updatedByUser` (format: text, data transform for full name)

---

## Mobile Screen Standards

- **Input height:** Fixed `120px` (prevents layout shift from validation errors)
- **Font sizes:** `dataFontSize: 24`, `labelFontSize: 15` for touch readability
- **Input variant:** `"outlined"` (visual indicator of editable fields)
- **Footer:** Fixed `60px` height with FlowButton components (50% width each)
- **Scrollable body:** `overflow-y: auto`, `scrollbar-width: "thin"`
- **ContainerOuter:** `justifyContent: "space-between"` to anchor footer
- **MenuButton:** `"bars"` icon, `"large"` iconSize for hamburger menu
- **SelectInput filters:** Always include `additionalFilter` for active-only records: `{ "_and": [{ "active": { "_eq": true } }] }`

---

## Container Grid Layout

Containers support grid layout in addition to flex:

```json
{
  "props": {
    "layout": "grid",
    "columns": 2,
    "rows": 3,
    "gap": "12px",
    "justifyItems": "start"
  }
}
```

Grid is used for: sidebar sections, section headers with icon+title, multi-column form fields.

---

## Container Background Options

| Value | Effect |
|---|---|
| `"paper"` | Theme paper color (white in light mode) |
| `"default"` | Theme default background |
| `"light"` | Light gray background |
| `"custom"` | Use `customBackground` color string |

`customBackground` can be static (`"#f0f0f0"`) or dynamic (a `__transform` expression).

---

## Standard Box-Shadow Values (Layout Templates)

| Use Case | `box-shadow` Value |
|----------|-------------------|
| Section cards (main content areas) | `2px 2px 6px #8b5cf6` |
| Individual item cards (inside Cards component) | `2px 2px 2px #8b5cf6` |
| Action button containers (header, Fuuz internal) | `0px 0px 1px 1px #8b5cf6` |

---

## Responsive Design Patterns

### Flex Container Responsiveness

**Grow to fill:** Set `width: "0px"` + `flexGrow: true` for containers that should share space equally.

**Wrap on small screens:** Set `flexWrap: true` on row containers so cards stack vertically when space is limited.

**Minimum card width:** Use fixed `width` on cards (e.g., `"280px"`) with `flexGrow: true` so they grow on large screens but wrap on small ones.

**Full-height tables:** Use `height: "0px"` + `flexGrow: true` on the table and its parent containers to fill available viewport height.

### Example: Responsive Metric Cards Row

```json
{
  "MetricsRow": {
    "props": {
      "flexDirection": "row",
      "flexWrap": true,
      "justifyContent": "space-between",
      "alignItems": "center",
      "width": "100%",
      "gap": "16px"
    }
  },
  "MetricCard": {
    "props": {
      "flexDirection": "column",
      "width": "280",
      "height": "120",
      "flexGrow": true,
      "shadow": true,
      "borderRadius": 8
    }
  }
}
```

---

## Common Errors and Solutions

| Error | Cause | Solution |
|---|---|---|
| Blank white screen / React error boundary | Missing required property | Check elementId, name, parent, query.api, query.model, dataPath |
| Component not found / render error | Wrong `resolvedName` | Use exact names: `ActionButton`, `DisplayText`, `Container` |
| Layout not rendering | Gap as number | Use `"gap": "16px"` (string with units) |
| Width not applied | Missing units | Use `"width": "280px"` not `"280"` |
| DisplayText not updating | Missing `__dynamicFields` | List ALL `$state.context.*` / `$components.Screen.context.*` fields in context array |
| Screen fails to load | Missing ROOT props | Include `flowsDelay: 0`, `removeOuterMargin: true` |
| Child not rendering | Node ID mismatch | Exact match between parent `nodes` array and child key |
| Table not filling height | Wrong container sizing | Use `height: "0px"` + `flexGrow: true` chain |
| Filter not connected | Missing `filterFormName` | Set `filterFormName` on Table to match form's `elementName` |
| Form input not working | Missing `formElement` | Set `formElement` on every input to its parent Form's `elementName` |
| "Cannot read property of null" | Null/empty transform | Never use `null` for `data`, `style`, `bordersInput`, `filterPredicate` — use `{}` |
| Form crashes on mount | Missing `query` object | Even `autoLoad: false` forms need a full `query` object with `api`, `model` |
| Data binding shows nothing | Missing `dataPath` on input/display | Every bound element needs `dataPath` matching the model field |
| Save does nothing | Missing `field` on input | Input elements need `field` property for write-back |
| DisplayText label renders "[object Object]" | Dynamic label without `__fallbackValue` | Add `__fallbackValue` to dynamic label transforms |
| Cards element invisible | Empty data with no hidden handler | Use dynamic `hidden` with `$isNilOrEmpty()` check |
| Parameters error | Empty `parameters` set to null | Use `parameters: "{}"` (empty JSONata object string), never null |
| DisplayText not rendering metadata/context values | `data` binding missing `__cacheKey` and/or `__remote` | Always include full triplet: `__transform`, `__cacheKey`, `__remote: false` |
| Array transform returns single object instead of array | Mapping expression (`.{}`) with one result returns object, not array | Wrap mapping in `[]` array literal: `[$metadata.items.{ "id": id }]` |
| Cards element errors on auto-generated query | Empty `fields` and `parameters` on Cards with client-side `dataTransform` | Include `fields: ["id"]` and dummy `parameters` with a filter (e.g., `id _eq $metadata.user.id`) |
| Passive Form errors on load | `autoLoad: false` with empty parameters | Keep `autoLoad: true` with dummy filter (`id _eq "system"`) and `dataTransform: { "remote": true }` |
| Description on Container/Form/Cards causes issues | `description` added to structural/layout elements | Only add `description` to DisplayText, input elements, and ActionButton — never to Container, Form, Cards, Table, RichText |

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
