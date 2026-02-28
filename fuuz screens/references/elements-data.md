# Data

Data provider elements for forms, tables, cards, and tree views.

---

### Form (`Form`)

Provides form context for child elements. Input elements bind to form fields via `dataPath`.

**Flags:** canvas, isForm, isDataProvider
**Exposed Functions:** `fn.setValue`, `fn.focus`, `fn.blur`, `fn.disableField`, `fn.enableField`, `fn.validate`, `fn.save`, `fn.delete`

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `description` | text (multiline) | Helper text. |
| `blockNavigationWhenDirty` | checkbox | Warn before navigating with unsaved changes. |

**Data** (expanded): Uses Common Data Query Pattern (see overview) plus:

| Field | Type | Description |
|-------|------|-------------|
| `titleContext` | transform | Screen title context transform. |

**Validation**:

| Field | Type | Description |
|-------|------|-------------|
| `validation.transform` | jsonata | Form validation. Returns object of errors keyed by field data paths, or undefined. |

**Advanced**: Uses Data Subscription Pattern (see overview) plus:

| Field | Type | Description |
|-------|------|-------------|
| `query.dataTransform.transform` | jsonata | Transform loaded data. |
| `query.disableDataChangeIndicator` | checkbox | Disable data change indicator. |
| `query.selectedDataChangeIndicator` | options | Indicator mode. Options: Refresh \| New Tab. |
| `dataChangeIndicatorSize` | options | Indicator size. Options: small \| normal. |
| `enableConfirmOnDataChanged` | checkbox | Require confirmation to overwrite when document changed since opened. |

**Defaults:** `{ query: { api: "Application", dataPath: "edges[0]", autoLoad: true, parameters: '{ "filter": { "id": { "_eq": id } }, "first": 1 }', fields: ["id"] }, blockNavigationWhenDirty: true, enableConfirmOnDataChanged: false }`

---

### Table (`Table`)

Data table with configurable columns, sorting, filtering, grouping, and row selection.

**Flags:** canvas, isTable, isDataProvider
**Exposed Functions:** `fn.search`, `fn.selectRow`, `fn.deselectRow`, `fn.toggleRowSelection`, `fn.selectAllRows`, `fn.deselectAllRows`, `fn.selectAllFiltered`, `fn.deselectAllFiltered`, `fn.setSelectedRows`, `fn.setRows`, `fn.setData`, `fn.addRows`, `fn.updateRows`, `fn.upsertRows`, `fn.deleteRows`
**Exposed State:** `selectedRows`, `currentView`, `data`

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `filterFormName` | node / text | Filter form element for search binding. |
| `tableName` | text | Table header name. |
| `description` | text (multiline) | Helper text. |

**Display**:

| Field | Type | Description |
|-------|------|-------------|
| `margin` | slider (0–96, step 4) | Element margin. |
| `width` | text | Element width. |
| `height` | text | Element height. |
| `fontSize` | text | Table font size. |
| `flexGrow` | checkbox | Expand to fill space. |
| `hideTableHeader` | checkbox | Hide table header bar. |

**Data** (expanded): Uses Common Data Query Pattern (see overview).

**Behavior**:

| Field | Type | Description |
|-------|------|-------------|
| `selectable` | combobox | Row selection mode. Options: none \| single \| multiple. |
| `rowMultiSelectWithClick` | checkbox | Multi-select rows with click. |
| `defaultSort` | transform (smartInput: fieldGroup) | Default sort. Sub-fields: `field` (combobox), `direction` (asc \| desc). |
| `showToolPanels` | checkbox | Show tool panels. |
| `showStatusBar` | checkbox | Show status bar. |
| `enableGrouping` | checkbox | Enable row grouping. |
| `enableCharts` | checkbox | Enable charts. |
| `showColumnMenu` | checkbox | Show column header menu. |
| `disableColumnDragging` | checkbox | Disable column reorder by drag. |
| `disableDragLeaveHidesColumns` | checkbox | Disable hiding columns by dragging out. |
| `enableRowDragging` | checkbox | Enable row drag sorting. |
| `enableRowDragMultiRow` | checkbox | Multi-row drag. Visible when enableRowDragging + selectable = multiple. |
| `hideRowWhileDragging` | checkbox | Hide row during drag. Visible when enableRowDragging. |
| `enableRowDragEntireRow` | checkbox | Drag from anywhere on row. Visible when enableRowDragging. |
| `enableRowDraggingBetweenTables` | checkbox | Drag rows between tables. Visible when enableRowDragging. |
| `tableDropZoneList` | fieldGroup | Drop zone table names. Visible when drag between tables. Sub-field: `tableName` (text). |
| `removeRowFromSrcGrid` | checkbox | Remove source row after drop. Visible when drag between tables. |
| `masterDetail` | checkbox | Enable master/detail expandable rows. |
| `detailHeight` | slider (0–600, step 50) | Detail section height. Visible when masterDetail. |
| `limitToOneActiveDetail` | checkbox | Limit to 1 expanded detail. Visible when masterDetail. |
| `titleContext` | transform | Title context transform. |
| `onDataChangeTransform` | jsonata | Transform on data change. |

**Validation**:

| Field | Type | Description |
|-------|------|-------------|
| `validation.transform` | jsonata | Table validation transform. |

**View**:

| Field | Type | Description |
|-------|------|-------------|
| `views` | fieldGroup (label via `name`) | Named views. Sub-fields: `name` (text), `rowLimit` (integer, default 500), `isDefault` (checkbox), `description` (text), `columns` (combobox, multiselect). |

**Advanced**: Uses Data Subscription Pattern (see overview) plus:

| Field | Type | Description |
|-------|------|-------------|
| `query.dataTransform.transform` | jsonata | Transform loaded data. |
| `query.dataTransform.remote` | checkbox | Run transform remotely. |
| `query.disableDataChangeIndicator` | checkbox | Disable data change indicator. |
| `query.selectedDataChangeIndicator` | options | Indicator mode. Options: refresh \| newTab. |

**Defaults:** `{ margin: 8, width: "100%", height: "auto", selectable: "multiple", showToolPanels: true, showStatusBar: true, enableGrouping: true, enableCharts: true, showColumnMenu: true, enableRowDragging: false, masterDetail: false }`

---

### Table Column (`TableColumn`)

Configures a single column on its parent table. Renders as a placeholder in the designer; at runtime, registers field definition with parent table.

**Flags:** isTableColumn

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `field` | graphql (field picker) | Data model field. Auto-sets dataPath, label, description, format on change. |
| `dataPath` | text | Column data path. Required. |
| `label` | text | Column header label. Required. |
| `description` | text (multiline) | Helper text. |

**Display**:

| Field | Type | Description |
|-------|------|-------------|
| `width` | text | Column width. |
| `style` | transform (smartInput: json) | Cell styling. Properties: background, color, fontSize, fontWeight (normal \| lighter \| bold \| bolder), alignItems, justifyContent. |
| `headerAlign` | combobox | Header alignment. Options: center \| left \| right. |
| `wrapText` | switch | Wrap text. Visible when format != action. |
| `showColumnSuppressMenu` | checkbox | Show column header menu. |

**Behavior**:

| Field | Type | Description |
|-------|------|-------------|
| `format` | options | Output format. Options: action \| address \| checkmark \| date \| datetime \| dialogList \| duration \| float \| flow \| icon \| image \| integer \| json \| markdown \| measure \| menu \| ratioMeasure \| richText \| text \| time. |
| `durationMode` | options | Duration display. Options: humanize \| abbreviated \| time. Visible when format = duration. |
| `timeUnit` | options | Abbreviated unit. Options: millisecond \| second \| minute \| hour \| day \| week \| month \| year. Visible when duration + abbreviated. |
| `humanizeThreshold` | integer | Units to render (0–7). Visible when duration + humanize. |
| `formatString` | text | Format string (Moment.js or Numeral.js). Visible for measure, ratioMeasure, integer, float, date, datetime, time. |
| `timeZone` | transform (smartInput: combobox) | Time zone (IANA, "setting", or "device"). Visible for date, datetime, time. |
| `linkTarget` | transform (smartInput: text) | Navigation target for column link. |
| `query.fields` | json | Additional query fields. |
| `actions` | fieldGroup | Action buttons. Visible when format = action. Sub-fields: title, text, useIconButton, icon, buttonColor, customButtonColor, disabled, hidden, action. |
| `flows` | fieldGroup | Flow buttons. Visible when format = flow. Sub-fields: title, text, useIconButton, icon, buttonColor, customButtonColor, disabled, hidden, dataFlowId, payload. |
| `menuIcon` | options | Menu icon. Visible when format = menu. Options: ellipsis-v \| ellipsis-h \| bars \| grip-lines. |
| `editable` | checkbox | Make column editable. |

**Validation**:

| Field | Type | Description |
|-------|------|-------------|
| `validation.transform` | jsonata | Column validation. |

**Advanced**:

| Field | Type | Description |
|-------|------|-------------|
| `data` | transform (smartInput: json) | Custom column data. |
| `sortable` | switch | Allow sorting. |
| `sortAsNumber` | checkbox | Sort as number. |

**Defaults:** `{ label: "Column", format: "text", sortable: true }`

---

### Custom Fields Column (`CustomFieldsTableColumn`)

Automatically displays configured custom fields as grouped columns.

**Flags:** isTableColumn, isDynamicTableColumn

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `field` | graphql (field picker) | Data model field. Filters to `_CustomFieldsNode` types. |
| `dataPath` | text | Path to _customFields. Required. |
| `dataModel` | options | Data model for custom fields. Dynamic options. Required. |
| `label` | text | Column header. Required. |

**Behavior**:

| Field | Type | Description |
|-------|------|-------------|
| `expandGroups` | switch | Auto-expand column groups on load. |

**Defaults:** `{ label: "Custom Fields", expandGroups: false }`

---

### Dynamic Column (`DynamicTableColumn`)

Configures one or more dynamic columns from a transform expression.

**Flags:** isTableColumn, isDynamicTableColumn

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `dataPath` | text | Data path. Required. |
| `label` | text | Column header. Required. |

**Behavior**:

| Field | Type | Description |
|-------|------|-------------|
| `columns` | transform | Array of column definition objects. |
| `query.fields` | json | Additional query fields. |

**Defaults:** `{ label: "Dynamic Column" }`

---

### Cards (`Cards`)

Form canvas that renders a card per data row. Provides form context to child input elements.

**Flags:** canvas, isForm, isCard, isDataProvider
**Exposed Functions:** `fn.loadData`, `fn.search`

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `filterFormName` | node / text | Filter form for search binding. |
| `description` | text (multiline) | Helper text. |
| `blockNavigationWhenDirty` | checkbox | Warn before navigating with unsaved changes. |
| `padding` | slider (0–96, step 4) | Container padding. |
| `margin` | slider (0–96, step 4) | Container margin. |
| `background` | combobox | Background. Options: accent \| primary \| light \| default \| paper \| dark \| custom. |
| `customBackground` | color | Custom background. Visible when custom. |
| `shadow` | checkbox | Box shadow. |
| `borderRadius` | slider (0–96, step 4) | Corner rounding. |
| `width` | text | Container width. |
| `height` | text | Container height. |
| `flexDirection` | combobox | Options: column \| row \| row-reverse \| column-reverse. |
| `flexWrap` | checkbox | Wrap children. |
| `alignItems` | combobox | Options: flex-start \| flex-end \| center \| stretch \| baseline. |
| `justifyContent` | combobox | Options: flex-start \| flex-end \| start \| end \| left \| right \| center \| space-between \| space-around \| space-evenly. |
| `hidden` | transform (smartInput: checkbox) | Hide container. |
| `flexGrow` | checkbox | Expand to fill space. |

**Data** (expanded): Uses Common Data Query Pattern (see overview) without filterPredicate.

**Validation**:

| Field | Type | Description |
|-------|------|-------------|
| `validation.transform` | jsonata | Form validation. |

**Advanced**: Uses Data Subscription Pattern (see overview) plus:

| Field | Type | Description |
|-------|------|-------------|
| `query.dataTransform.transform` | jsonata | Transform loaded data. |

**Defaults:** `{ padding: 8, margin: 0, flexDirection: "column", flexWrap: true, alignItems: "stretch", justifyContent: "flex-start", width: "100%", height: "auto", blockNavigationWhenDirty: true }`

---

### Scheduling Config (`SchedulingConfiguration`)

Scheduler display and editing element.

**Flags:** isDataProvider

Note: Conditionally excluded from toolbox when scheduling is excluded.

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `description` | text (multiline) | Helper text. |

**Data**:

| Field | Type | Description |
|-------|------|-------------|
| `scheduler.schedulingConfigurationId` | transform (smartInput: select, model: SchedulingConfiguration) | Configuration to load when run ID not provided. |
| `scheduler.schedulingConfigurationRunId` | transform | Configuration run ID for viewing before committing. |

---

### Data Tree (`DataTreeView`)

Tree view of model data with expandable related data.

**Flags:** canvas, isDataProvider

**Basic**:

| Field | Type | Description |
|-------|------|-------------|
| `label` | transform (smartInput: text) | Tree view label. |
| `description` | text (multiline) | Helper text. |

**Display**:

| Field | Type | Description |
|-------|------|-------------|
| `minWidth` | text | Minimum width. |
| `maxWidth` | text | Maximum width before scroll. |
| `minHeight` | text | Minimum height. |
| `maxHeight` | text | Maximum height before scroll. |
| `visible` | transform (smartInput: checkbox) | Visibility. |

**Data**:

| Field | Type | Description |
|-------|------|-------------|
| `query.api` | options | API. Options: Application \| System. |
| `query.model` | options | Data model. Dynamic options. |
| `query.filterPredicate` | graphqlWhere | Filters. |
| `maxRecords` | integer | Max records (1–100). |

**Advanced**:

| Field | Type | Description |
|-------|------|-------------|
| `expandRoot` | transform (smartInput: checkbox) | Auto-expand root level. |
| `expandFirstNode` | transform (smartInput: checkbox) | Auto-expand first node. |
| `collapseFiltersDefault` | transform (smartInput: checkbox) | Collapse filters when parent expanded. |
| `hideTopLevelRootHeader` | transform (smartInput: checkbox) | Hide top-level pagination/search. |
| `disabled` | transform (smartInput: checkbox) | Disable tree view. |

**Defaults:** `{ query: { api: "Application" }, maxRecords: 5, expandRoot: false, expandFirstNode: false, maxWidth: "100%", maxHeight: "500px", visible: true }`
