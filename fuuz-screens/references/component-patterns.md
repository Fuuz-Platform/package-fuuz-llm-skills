# Fuuz Screen Component Patterns

> Detailed component templates, CSS rules, and standard UI patterns for all major Fuuz screen elements.

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

---

### Fuuz Platform Action Button Pattern (Paired Icon + Text)

For **Fuuz platform screens** (admin, system, profile), header action buttons use a **paired elements** pattern: one icon-only button + one text-label button, both inside a container with `box-shadow: 0px 0px 1px 1px #8b5cf6`. This is a Fuuz employee styling workaround that will eventually be replaced with native action button configuration.

```
Container (row, box-shadow: 0px 0px 1px 1px #8b5cf6)
  ├── ActionButton (useIconButton: true, icon: "lock-open", width: 20, height: 20)
  └── ActionButton (useIconButton: true, text: "Change Password", width: auto, height: 20)
```

```json
{
  "ChangePasswordBtnContainer": {
    "type": { "resolvedName": "Container" },
    "isCanvas": true,
    "props": {
      "padding": 2, "margin": 0,
      "flexDirection": "row", "alignItems": "center", "gap": "4px",
      "width": "auto", "height": "auto",
      "borderRadius": 4,
      "style": { "box-shadow": "0px 0px 1px 1px #8b5cf6" }
    },
    "displayName": "Container",
    "custom": { "elementName": "ChangePasswordBtnContainer" },
    "parent": "HeaderActionsRow",
    "hidden": false,
    "nodes": ["ChangePasswordIcon", "ChangePasswordText"],
    "linkedNodes": {}
  },
  "ChangePasswordIcon": {
    "type": { "resolvedName": "ActionButton" },
    "isCanvas": false,
    "props": {
      "useIconButton": true,
      "icon": { "icon": "lock-open", "color": "inherit", "variant": "regular", "size": "icon" },
      "width": 20, "height": 20,
      "action": [{ "type": "transformation", "transformation": "...", "remote": false }],
      "description": "Change password icon trigger"
    },
    "displayName": "Action",
    "custom": { "elementName": "ChangePasswordIcon" },
    "parent": "ChangePasswordBtnContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  },
  "ChangePasswordText": {
    "type": { "resolvedName": "ActionButton" },
    "isCanvas": false,
    "props": {
      "useIconButton": true,
      "text": "Change Password",
      "width": "auto", "height": 20,
      "action": [{ "type": "transformation", "transformation": "...", "remote": false }],
      "description": "Change password text trigger"
    },
    "displayName": "Action",
    "custom": { "elementName": "ChangePasswordText" },
    "parent": "ChangePasswordBtnContainer",
    "hidden": false,
    "nodes": [],
    "linkedNodes": {}
  }
}
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

> ⚠️ **CRITICAL:** Menu columns must NOT have `dataPath` set to a model field name. Using `"dataPath": "actions"` causes the table query to fail with `Field "actions" doesn't exist on type ModelName`. Menu columns generate their own actions and should not bind to a data field. Use an empty string or omit `dataPath` entirely.

```json
"props": {
  "label": " ",
  "format": "menu",
  "sortable": false,
  "dataPath": "",
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

## Action vs Actions Property — Critical Naming Difference

Components use different property names for action arrays. Getting this wrong causes silent failures.

| Component | Property Name | Structure | Example |
|---|---|---|---|
| `ActionButton` | `action` (singular) | Array of action steps | `"action": [{ "type": "form" }, { "type": "mutation" }]` |
| `TableColumn` (menu format) | `actions` (plural) | Array of menu items, each with nested `action` | `"actions": [{ "label": "Edit", "action": [...] }]` |
| `MenuButton` | `actions` (plural) | Array of menu items | `"actions": [{ "text": "Home" }]` |

**ActionButton** uses singular `action` — a flat array of sequential action steps:
```json
{
  "type": { "resolvedName": "ActionButton" },
  "props": {
    "action": [
      { "type": "form", "title": "Create Record", "fields": [...] },
      { "type": "mutation", "api": "Application", "mutation": "createModel", "transforms": {...} },
      { "type": "transformation", "transformation": "$components.Table1.fn.search()", "remote": false }
    ]
  }
}
```

**Menu column** uses plural `actions` — an array of labeled menu items, each containing a singular `action` array:
```json
{
  "props": {
    "dataPath": "",
    "format": "menu",
    "actions": [
      {
        "label": "Edit",
        "icon": "pen-to-square",
        "action": [
          { "type": "form", "transforms": { "pre": "data" }, "fields": [...] },
          { "type": "mutation", ... }
        ]
      },
      {
        "label": "Delete",
        "icon": "trash",
        "action": [
          { "type": "confirmation", "message": { "__transform": "\"Delete \" & data.code & \"?\"" } },
          { "type": "mutation", ... }
        ]
      }
    ]
  }
}
```

---

## Action Chain Data Flow

Action arrays execute **sequentially**. Each step's output becomes available to subsequent steps through specific variable names.

```
Step 1: form      → user fills form → outputs to "data"
Step 2: mutation   → uses "data" → outputs to "mutation" result
Step 3: transformation → can reference "mutation" result, refreshes table
```

### Available Variables at Each Step

| Step Type | Input Available | Output Variable | Description |
|---|---|---|---|
| `form` | `data` (row data from table, if triggered from menu) | `data` (form field values) | User fills fields, outputs as `data` |
| `mutation` | `data` (from previous form or transform) | `mutation` (mutation response) | Executes GraphQL mutation |
| `transformation` | `data`, `mutation` (from prior steps) | — | Runs JSONata expression (e.g., refresh table) |
| `confirmation` | `data` | — (blocks until confirmed) | Shows confirm dialog |
| `navigation` | `data`, `mutation` | — | Navigates to another screen |

### Pre-Transform Usage

- **Form action (step 1 — create new):** No `transforms.pre` needed — form opens empty, outputs field values as `data`
- **Form action (step 1 — edit existing):** Use `"transforms": { "pre": "data" }` to pre-populate form with row data
- **Mutation action (step 2):** `transforms.pre` shapes `data` into the mutation payload format
- **Transformation action (step 3):** Runs a JSONata expression (typically to refresh a table)

### Complete Create Action Chain Example

```json
"action": [
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
        "description": "Unique identifier",
        "validation": { "required": true },
        "query": { "fields": ["code"] }
      },
      {
        "label": "Name",
        "element": "TextInput",
        "type": "text",
        "dataPath": "name",
        "field": "name",
        "description": "Display name",
        "validation": { "required": true },
        "query": { "fields": ["name"] }
      }
    ]
  },
  {
    "type": "mutation",
    "api": "Application",
    "mutation": "createModelName",
    "transforms": {
      "pre": "{\n  \"payload\": [\n    {\n      \"create\": data\n    }\n  ]\n}"
    }
  },
  {
    "type": "transformation",
    "transformation": "$components.TableName.fn.search()",
    "remote": false
  }
]
```

> ⚠️ **CRITICAL:** The mutation payload must be an **array** — `[{ "create": data }]`, NOT `{ "create": data }`. Fuuz mutations expect `[ModelCreatePayloadInput!]!` — the brackets indicate an array type. Even single-record operations require the array wrapper.

### Complete Edit Action Chain Example (from menu column)

```json
{
  "label": "Edit",
  "icon": "pen-to-square",
  "action": [
    {
      "type": "form",
      "submitIcon": "check",
      "submitLabel": "Save",
      "title": { "__transform": "\"Edit \" & data.code" },
      "transforms": { "pre": "data" },
      "fields": [
        {
          "label": "Name",
          "element": "TextInput",
          "type": "text",
          "dataPath": "name",
          "field": "name",
          "description": "Display name",
          "validation": { "required": true },
          "query": { "fields": ["name"] }
        }
      ]
    },
    {
      "type": "mutation",
      "api": "Application",
      "mutation": "updateModelName",
      "transforms": {
        "pre": "{\n  \"payload\": [\n    {\n      \"where\": { \"id\": data.id },\n      \"update\": { \"name\": data.name }\n    }\n  ]\n}"
      }
    },
    {
      "type": "transformation",
      "transformation": "$components.TableName.fn.search()",
      "remote": false
    }
  ]
}
```

### Complete Delete Action Chain Example (from menu column)

```json
{
  "label": "Delete",
  "icon": "trash",
  "action": [
    {
      "type": "confirmation",
      "message": { "__transform": "\"Are you sure you want to delete \" & data.code & \"?\"" },
      "submitIcon": { "icon": "trash", "variant": "solid" },
      "buttonColor": "error"
    },
    {
      "type": "mutation",
      "api": "Application",
      "mutation": "deleteModelName",
      "transforms": {
        "pre": "{\n  \"payload\": [\n    {\n      \"where\": { \"id\": data.id }\n    }\n  ]\n}"
      }
    },
    {
      "type": "transformation",
      "transformation": "$components.TableName.fn.search()",
      "remote": false
    }
  ]
}
```

---

## Form Action Field Configuration

When using `"type": "form"` in an action chain, fields require several properties beyond what basic documentation shows. Missing these properties causes forms to not render or not bind data correctly.

### Required Form Field Properties

| Property | Type | Required? | Description |
|---|---|---|---|
| `label` | string | **Yes** | Field label displayed to user |
| `element` | string | **Yes** | Input component type: `"TextInput"`, `"IntegerInput"`, `"FloatInput"`, `"SelectInput"`, `"DateInput"`, `"DisplayText"` |
| `type` | string | **Yes** | Data type: `"text"`, `"integer"`, `"float"`, `"select"`, `"date"`, `"display"` |
| `dataPath` | string | **Yes** | Path to bind to in the form data |
| `field` | string | **Yes** | Must match `dataPath` for proper binding |
| `query` | object | **Yes** | Must include `"fields": ["fieldName"]` array |
| `description` | string | Recommended | Help text displayed under the field |
| `validation` | object | Recommended | `{ "required": true }` for mandatory fields |

### Optional Form Field Properties

| Property | Type | Description |
|---|---|---|
| `labelPosition` | object | `{ "id": "row", "label": "Left" }` for side-by-side label |
| `justifyContent` | string | `"space-between"` for row layout |
| `target` | object | `{ "dataPath": "fieldName" }` for write target |
| `defaultValue` | any | Default value when form opens empty |

### Form-Level Properties

| Property | Type | Description |
|---|---|---|
| `title` | string/object | Dialog header — static string or `{ "__transform": "\"Edit \" & data.code" }` |
| `submitIcon` | string | Icon for submit button: `"plus"`, `"check"`, `"floppy-disk"` |
| `submitLabel` | string | Submit button text: `"Create"`, `"Save"`, `"Update"` |
| `transforms.pre` | string | Pre-populate form — use `"data"` to fill from row data (for edit forms) |

### Input Element Types for Form Fields

| Element | Type | Use Case |
|---|---|---|
| `TextInput` | `text` | Text strings, codes, names |
| `IntegerInput` | `integer` | Whole numbers |
| `FloatInput` | `float` | Decimal numbers |
| `SelectInput` | `select` | Dropdown selection from model or static options |
| `DateInput` | `date` | Date picker |
| `DateTimeInput` | `datetime` | Date + time picker |
| `Switch` | `switch` | Boolean toggle |
| `DisplayText` | `display` | Read-only display (for view-only forms) |

---

## Mutation Configuration in Screen Designer

The Fuuz Screen Designer provides a **visual GraphQL builder** for configuring mutations in action chains. This is different from manually typing mutation strings.

### How the Visual Builder Works

1. **Select the model** — Choose from the dropdown (e.g., "CapaType")
2. **Select the mutation** — The available mutations appear (createCapaType, updateCapaType, deleteCapaType)
3. **Check return fields** — Select which fields to return after mutation (id, code, name, etc.)
4. **Configure transforms** — Pre/post transforms are configured in separate text fields

### What Gets Generated

The visual builder produces the GraphQL mutation string automatically. The `transforms.pre` field is where you write the JSONata expression that shapes the input data into the mutation payload format.

### Key Points

- Mutation name is **auto-selected** based on model + operation, not manually typed
- Field selection determines what comes back in the response
- The `transforms.pre` JSONata expression is the primary thing you configure manually
- Always ensure the pre-transform wraps the payload in an **array**: `{ "payload": [{ "create": data }] }`

---

## Setup Table Screen Checklist

Quick reference for building a working setup/master table screen:

```
□ Table query.fields array — include all displayed fields, NO "actions" field
□ MenuColumn dataPath — empty string "" or omit entirely
□ Create button action chain:
  1. form (no pre-transform, fields with element+field+query properties)
  2. mutation (pre-transform: {"payload": [{"create": data}]})
  3. transformation ($components.TableName.fn.search())
□ Edit button action chain (in menu):
  1. form (transforms.pre: "data" to pre-populate)
  2. mutation (pre-transform with where + update, wrapped in array)
  3. transformation (table refresh)
□ Delete button (in menu):
  1. confirmation (message with data.code reference)
  2. mutation (pre-transform with where, wrapped in array)
  3. transformation (table refresh)
□ Every column dataPath has corresponding field in query.fields (except menu columns)
□ filterFormName on Table matches the filter Form's elementName
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
