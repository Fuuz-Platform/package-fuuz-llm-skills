# Fuuz Screen Layout Templates & Standards

> Ready-to-use layout templates, table screen variants, standard UI patterns,
> action chain patterns, mobile standards, and responsive design guidance.

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
    "transforms": { "pre": "{\n  \"payload\": [\n    {\n      \"create\": data\n    }\n  ]\n}" },
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
  "dataPath": "",
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

## Standard Box-Shadow Values

| Use Case | `box-shadow` Value |
|----------|-------------------|
| Section cards (main content areas) | `2px 2px 6px #8b5cf6` |
| Individual item cards (inside Cards component) | `2px 2px 2px #8b5cf6` |
| Action button containers (header, Fuuz internal) | `0px 0px 1px 1px #8b5cf6` |

Always use these exact values. Section-level cards get the larger dispersed shadow. Individual clickable cards inside a Cards element get the tighter shadow. Header action button wrappers get the outline-style shadow.

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

