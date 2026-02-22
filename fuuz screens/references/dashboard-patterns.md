# Fuuz Dashboard Screen Patterns

> Production-tested patterns for Chart/Visualization components and dashboard architecture.
> Extracted from Tenant Admin (172 components) and Enterprise Admin (219 components) screens.

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
