---
name: fuuz-screen-design
description: >
  Build, modify, and troubleshoot screen designs (UI) for the Fuuz Industrial Operations Platform (fuuz.com).
  Trigger this skill whenever the user mentions Fuuz screens, screen JSON, screen elements, UI components in Fuuz,
  form design, table design, containers, action buttons, filter panels, or any visual layout work within the Fuuz
  application designer. Also trigger when analyzing or generating Fuuz screen JSON exports, working with screen
  context, or designing manufacturing UI like OEE dashboards, work order forms, inventory screens, or operator interfaces.
---

# Fuuz Screen Design Skill

> **Purpose:** Guide Claude in creating, analyzing, and modifying screen designs for the Fuuz Industrial Operations Platform. Screens in Fuuz are JSON-based definitions that describe UI layouts, data bindings, actions, and styling.

## 1. Architecture Overview

Fuuz screens are exported and imported as JSON. Every screen is a tree of **elements** nested inside a root `Screen` element. Each element has a `type`, configuration properties, optional children, and optional scripts/actions.

### Screen JSON Structure
```json
{
  "name": "MyScreen",
  "type": "Screen",
  "elements": [ /* child elements */ ],
  "context": {},
  "scripts": []
}
```

### Element Hierarchy
```
Screen
├── Container (layout wrapper)
│   ├── Form (data-bound input group)
│   │   ├── TextInput
│   │   ├── SelectInput
│   │   ├── DateInput
│   │   └── NumberInput
│   ├── Table (data grid)
│   ├── Chart (visualization)
│   └── ActionButton (user interaction)
├── TabBar (multi-tab navigation)
│   ├── Tab
│   └── Tab
└── Container
    └── DisplayText (read-only output)
```

## 2. Element Types Reference

Fuuz has **59 known element types** across these categories:

### Layout Elements
| Type | Purpose | Can Contain Children |
|------|---------|---------------------|
| `Screen` | Root element, one per screen | Yes |
| `Container` | Generic layout wrapper with flex/grid | Yes |
| `TabBar` | Tab navigation container | Yes (Tab children) |
| `Tab` | Individual tab panel | Yes |
| `ResizableFilterPanel` | Collapsible side panel for filters | Yes |
| `Divider` | Visual separator | No |

### Form Input Elements
| Type | Purpose | Key Properties |
|------|---------|---------------|
| `TextInput` | Text entry | `dataPath`, `placeholder`, `required`, `maxLength` |
| `NumberInput` | Numeric entry | `dataPath`, `min`, `max`, `step`, `decimalPlaces` |
| `SelectInput` | Dropdown selection | `dataPath`, `options`, `query`, `multiple` |
| `DateInput` | Date/datetime picker | `dataPath`, `dateFormat`, `includeTime` |
| `SwitchInput` | Boolean toggle | `dataPath` |
| `CheckboxInput` | Boolean checkbox | `dataPath` |
| `TextAreaInput` | Multi-line text | `dataPath`, `rows` |
| `ColorInput` | Color picker | `dataPath` |
| `FileInput` | File upload | `dataPath`, `accept`, `maxSize` |
| `RichTextInput` | Rich text editor (Draft.js) | `dataPath` |
| `SliderInput` | Range slider | `dataPath`, `min`, `max` |
| `RadioGroup` | Radio button group | `dataPath`, `options` |
| `AutocompleteInput` | Search-as-you-type | `dataPath`, `query` |

### Display Elements
| Type | Purpose |
|------|---------|
| `DisplayText` | Read-only text, supports JSONata transforms |
| `RichText` | Formatted display text with styling |
| `Icon` | Font Awesome Pro icon display |
| `Image` | Image display |
| `Badge` | Status/count badge |
| `Avatar` | User avatar display |
| `ProgressBar` | Progress indicator |
| `Sparkline` | Inline mini-chart |

### Data Elements
| Type | Purpose | Key Config |
|------|---------|-----------|
| `Form` | Data-bound input container | `query`, `dataSource`, `api` |
| `Table` | AG Grid data table | `query`, `columns`, `pagination` |
| `Chart` | FusionCharts visualization | `chartType`, `query`, `dataSource` |
| `Calendar` | Calendar/scheduler view | `query`, `eventMapping` |
| `Kanban` | Kanban board | `query`, `columnMapping` |

### Action Elements
| Type | Purpose |
|------|---------|
| `ActionButton` | Button with configurable action pipeline |
| `MenuButton` | Dropdown menu with multiple actions |
| `FloatingActionButton` | FAB with quick actions |
| `IconButton` | Compact icon-only button |

## 3. Standard Form Design Pattern (MainFormContainer)

This is the canonical layout for single-record CRUD screens in Fuuz:

```
Screen
├── EssentialDetails (immutable: Type, ID, Code, Label)
├── MainFormContainer (auto-scroll Y, data-bound)
│   ├── PrimaryContent (general editable fields)
│   ├── CodeContent (JSON, JSONata, code fields)
│   ├── SettingsContent (boolean switches/checkboxes)
│   └── CustomFieldContent (user-defined custom fields)
├── QuickActions (contextual action buttons)
└── SaveAction (disabled when form is clean or required fields null)
```

### Key Rules
- **EssentialDetails** are read-only — things like Type, ID, Code, Label that identify the record
- **MainFormContainer** scrolls only in Y direction to minimize navigation
- **PrimaryContent** is always the first card — general editable fields go here
- **CodeContent** holds structured data fields (JSON editors, JSONata expressions)
- **SettingsContent** contains only boolean values as switches or checkboxes
- **SaveAction** must be disabled when form `isDirty === false` OR any required input is null
- Designer should arrange cards within MainFormContainer based on user preferences

## 4. Screen Context (State Management)

Screen Context is a session-scoped state object shared across all components on a single screen. It does not persist across navigation or page refresh.

### Context Functions
```javascript
// Replace entire context
$components.Screen.fn.setContext($data)

// Merge fields into existing context (non-destructive)
$components.Screen.fn.mergeContext({"newField": "value"})

// Set a single field
$components.Screen.fn.setContextValue("fieldName", "value")

// Delete a specific field
$components.Screen.fn.deleteContextValue("fieldName")
```

### Accessing Context
- In JSONata transforms: `$context.fieldName`
- In component queries: reference context values as query variables
- In browser console (debug only): `context.components.Screen.context`

**Important:** The top-level `context` object in browser dev tools is a debug accessor. The actual Screen Context lives at `context.components.Screen.context`.

## 5. Data Binding & Queries

### Form Data Binding
Forms connect to data via the `query` property using GraphQL:
```json
{
  "type": "Form",
  "query": {
    "api": "Application",
    "operation": "query",
    "name": "workOrder",
    "variables": { "where": { "id": { "equals": "$context.selectedId" } } }
  }
}
```

### Input Data Paths
Each input's `dataPath` maps to a field in the form's data response:
```json
{ "type": "TextInput", "dataPath": "workOrderNumber", "label": "WO #" }
```

### Table Queries
Tables use similar GraphQL queries with pagination support:
```json
{
  "type": "Table",
  "query": {
    "api": "Application",
    "operation": "query",
    "name": "workOrders",
    "variables": {
      "where": { "status": { "equals": "In Progress" } },
      "orderBy": [{ "createdAt": "desc" }],
      "take": 50
    }
  }
}
```

### Data Transforms
DisplayText and other elements use JSONata transforms for computed display:
```json
{
  "type": "DisplayText",
  "transform": "$formatNumber(oeePercentage, '#0.0') & '%'"
}
```

## 6. Action Pipelines

ActionButtons execute a sequence of steps (pipeline):
```json
{
  "type": "ActionButton",
  "label": "Complete Work Order",
  "actions": [
    { "type": "confirm", "message": "Are you sure?" },
    { "type": "mutate", "mutation": "updateWorkOrder", "variables": { "status": "Complete" } },
    { "type": "notify", "message": "Work order completed", "severity": "success" },
    { "type": "refresh", "target": "WorkOrderTable" }
  ]
}
```

### Common Action Types
- `mutate` — Execute a GraphQL mutation
- `query` — Run a query and store result
- `confirm` — Show confirmation dialog
- `notify` — Display toast notification
- `navigate` — Navigate to another screen
- `refresh` — Refresh a component's data
- `setContext` — Update screen context
- `openDialog` — Open a modal dialog
- `executeFlow` — Trigger a data flow
- `download` — Download a file

## 7. Styling & Layout

### Container Sizing
```json
{
  "type": "Container",
  "sizing": { "width": "100%", "height": "auto", "minHeight": "200px" },
  "spacing": { "padding": "16px", "margin": "8px", "gap": "12px" },
  "flex": { "direction": "row", "wrap": "wrap", "justify": "space-between", "align": "center" }
}
```

### Color Transforms (Common for KPI/OEE)
```jsonata
$oee < 60 ? "red" : $oee < 85 ? "orange" : "green"
```

### ISA-101 Compliance
For industrial dashboards, follow ISA-101 HMI standards:
- Use color sparingly and meaningfully (red=alarm, yellow=warning, green=normal)
- Large, readable fonts for operator distance viewing
- Consistent layout patterns across screens
- Status indicators over decorative elements

## 8. Component Library Reference

Fuuz screens can use these external libraries:
- **Font Awesome Pro** — Icons (`fa-solid`, `fa-regular`, `fa-light`, `fa-duotone`)
- **AG Grid** — Table/data grid component
- **FusionCharts** — Chart visualizations
- **Stimulsoft** — Report/document rendering
- **Draft.js** — Rich text editing

## 9. Common Anti-Patterns to Avoid

- **Never hardcode IDs** — Use dynamic references from context or query results
- **Never nest Forms inside Forms** — One Form per data scope
- **Never put editable fields in EssentialDetails** — Those are read-only
- **Never skip required field validation** on SaveAction
- **Never use inline styles when Fuuz theme tokens are available**
- **Avoid deep nesting** — Flatten container hierarchy where possible (max 4 levels)
- **Never assume screen context persists** across navigation — it's session-scoped per screen

## 10. Screen Design Checklist

When creating or reviewing a Fuuz screen:
1. ✅ Root element is `Screen` with a meaningful name
2. ✅ Forms are connected to appropriate GraphQL queries
3. ✅ All inputs have `dataPath` mapped to response fields
4. ✅ Required fields are marked and validated
5. ✅ Save button disabled when form is clean or required fields null
6. ✅ Immutable fields (ID, Type, Code) in EssentialDetails, not editable areas
7. ✅ Color coding follows ISA-101 for industrial dashboards
8. ✅ MainFormContainer scrolls Y-only
9. ✅ Action pipelines include confirmation for destructive operations
10. ✅ Screen context used for cross-component communication (not hidden fields)
