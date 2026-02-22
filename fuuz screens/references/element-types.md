# Fuuz Element Types & Properties Reference

> Extracted from SKILL.md - All 59 observed element types with configuration properties.
> Source: 1,000+ real screen elements across 19 production screens.

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
| `field` | string | — | **Required.** Field name in the data model for write-back |
| `validation` | object | `{"required": false}` | Validation rules |
| `variant` | string | `"standard"` | MUI variant (`"standard"`, `"outlined"`, `"filled"`) |
| `visible` | boolean | `true` | Visibility flag |
| `type` | string | — | Input type identifier (e.g., `"text"`, `"select"`, `"date"`) |
| `target` | object | — | Write target override: `{ "dataPath": "fieldName" }` |
| `query` | object | — | Data binding config: `{ "fields": ["fieldName"] }` — required for form action fields |

### Form Action Field Properties (Action Chain Forms)

When form fields appear inside `"type": "form"` action steps (e.g., create/edit dialogs triggered by ActionButton), they require additional properties beyond what model-bound forms need:

| Property | Type | Required? | Description |
|---|---|---|---|
| `element` | string | **Yes** | Input component type: `"TextInput"`, `"IntegerInput"`, `"FloatInput"`, `"SelectInput"`, `"DateInput"`, `"DisplayText"` |
| `field` | string | **Yes** | Must match `dataPath` for proper data binding |
| `query` | object | **Yes** | Must include `{ "fields": ["fieldName"] }` — required for data binding in action chain forms |
| `description` | string | Recommended | Help text displayed under the field |

> ⚠️ Missing `element`, `field`, or `query.fields` on form action fields causes forms to not render or not bind data. These properties are often omitted in simplified examples but are always required for working forms.

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
