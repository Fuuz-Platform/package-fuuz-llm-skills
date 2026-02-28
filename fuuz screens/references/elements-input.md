# Input

Form-bound input elements. All require placement inside a Form element (`requiresForm: true`).

Most input elements share standard field sets defined in the overview (`_overview.md`). Each element below only lists **custom fields** beyond those shared sets.

**Standard Shared Sets:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS` variants), Validation (`VALIDATION_FIELDS` variants), Advanced (`ADVANCED_FIELDS`), Behavior (`BEHAVIOR_FIELDS`), defaultProps.

---

## Text Inputs

### Text (`TextInput`)
**Input Type:** `text`

Basic text input.

**Sections:** Basic (`makeBasicFields({filterType:'String'})`), Display (`DISPLAY_FIELDS_WITH_FONT_SIZE`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `multiline` | checkbox | Basic | Enable multiline. |
| `inputMode` | options | Basic | Keyboard hint. Options: text \| decimal \| numeric \| tel \| search \| email \| url \| none. |

**Custom Defaults:** `{ variant: "standard" }`

---

### Password (`Password`)
**Input Type:** `password`

Password input with optional policy validation.

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS_WITH_FONT_SIZE`), Validation (`VALIDATION_FIELDS` + custom), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `validation.passwordPolicy` | checkbox | Validation | Apply password policy. |

**Custom Defaults:** `{ variant: "standard", validation: { required: true, passwordPolicy: false } }`

---

### Scan Text (`ScanTextInput`)
**Input Type:** `scanText`

Scanner/barcode text input.

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS_WITH_VARIANT` + custom), Behavior (custom), Validation (`VALIDATION_FIELDS` + `LENGTH_VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `hideIcons` | switch | Display | Hide indicator icons. |
| `terminatorCharacters` | combobox (multi) | Behavior | End-of-scan signals. Options: enter \| tab \| space \| custom. |
| `customTerminators` | array | Behavior | Custom suffix strings. Visible when custom selected. |
| `prefixString` | text | Behavior | Prefix to match at scan start. |
| `stripPrefixString` | checkbox | Behavior | Remove prefix from value. |
| `allowManualInput` | checkbox | Behavior | Allow keyboard entry. |
| `scanTimeoutMilliseconds` | integer | Behavior | Scan timeout in ms. Visible when manual input disabled. |
| `clearable` | checkbox | Behavior | Show clear button. |
| `clearOnFocus` | checkbox | Behavior | Auto-clear on focus. |
| `autoFocus` | checkbox | Behavior | Auto-focus on page load. |
| `beepOnScan` | checkbox | Behavior | Beep on scan. |

**Custom Defaults:** `{ variant: "standard", hideIcons: false, terminatorCharacters: ["enter","tab"], allowManualInput: true, scanTimeoutMilliseconds: 200, clearable: true, autoFocus: false, clearOnFocus: true, beepOnScan: false }`

---

## Numeric Inputs

### Number (`NumberInput`)
**Input Type:** `number`

Number input.

**Sections:** Basic (`makeBasicFields({defaultValueProps:{type:'number'}})`), Display (`DISPLAY_FIELDS_WITH_FONT_SIZE`), Behavior (`BEHAVIOR_FIELDS`), Validation (`VALIDATION_FIELDS_WITH_NUMBER_VALIDATION`), Advanced (`ADVANCED_FIELDS`)

No custom fields. **Custom Defaults:** `{ variant: "standard", validation: { disallowNegative: false, disallowDecimals: false } }`

---

### Integer (`IntegerInput`)
**Input Type:** `integer`

Integer number input (no decimals). Same structure as Number.

**Sections:** Basic (`makeBasicFields({filterType:'Int'})`), Display (`DISPLAY_FIELDS_WITH_FONT_SIZE`), Behavior (`BEHAVIOR_FIELDS`), Validation (`VALIDATION_FIELDS_WITH_NUMBER_VALIDATION`), Advanced (`ADVANCED_FIELDS`)

No custom fields. **Custom Defaults:** `{ variant: "standard", validation: { disallowNegative: false, disallowDecimals: false } }`

---

### Decimal (`FloatInput`)
**Input Type:** `float`

Decimal number input. Same structure as Number/Integer.

**Sections:** Basic (`makeBasicFields({filterType:'Float'})`), Display (`DISPLAY_FIELDS_WITH_FONT_SIZE`), Behavior (`BEHAVIOR_FIELDS`), Validation (`VALIDATION_FIELDS_WITH_NUMBER_VALIDATION`), Advanced (`ADVANCED_FIELDS`)

No custom fields. **Custom Defaults:** `{ variant: "standard", validation: { disallowNegative: false, disallowDecimals: false } }`

---

### Slider (`SliderInput`)
**Input Type:** `slider`

Slider input.

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS`), Behavior (custom), Validation (`VALIDATION_FIELDS` + custom), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `marks` | json | Behavior | Slider marks. |
| `includeNumberField` | checkbox | Behavior | Include adjacent number field. |
| `validation.minValue` | number | Validation | Minimum value. |
| `validation.maxValue` | number | Validation | Maximum value. |

---

## Date & Time Inputs

### Date (`DateInput`)
**Input Type:** `date`

Date input.

**Sections:** Basic (`makeBasicFields({filterType:'Date'})` + custom), Display (`DISPLAY_FIELDS_WITH_VARIANT`), Behavior (custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `timeOfDay` | combobox | Basic | Time of day. Options: now \| startOf \| endOf. |
| `formatString` | text | Behavior | Moment.js format string. |
| `timeZone` | transform (smartInput: combobox) | Behavior | IANA timezone, "setting", or "device". |

**Custom Defaults:** `{ variant: "standard", timeOfDay: "now" }`

---

### Time (`TimeInput`)
**Input Type:** `time`

Time input. Same format fields as Date but no timeOfDay.

**Sections:** Basic (`makeBasicFields({filterType:'Time'})`), Display (`DISPLAY_FIELDS_WITH_VARIANT`), Behavior (formatString + timeZone), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Defaults:** `{ variant: "standard" }`

---

### Date & Time (`DateTimeInput`)
**Input Type:** `datetime`

Date and time input. Same format fields as Time.

**Sections:** Basic (`makeBasicFields({filterType:'DateTime'})`), Display (`DISPLAY_FIELDS_WITH_VARIANT`), Behavior (formatString + timeZone), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Defaults:** `{ variant: "standard" }`

---

### Date Range (`DateRangeInput`)
**Input Type:** `datetimeRange`

Date range picker.

**Sections:** Basic (`makeBasicFields({filterType:'DateTime'})`), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

No custom fields. No Behavior section.

---

### Duration (`DurationInput`)
**Input Type:** `duration`

Duration input (ISO 8601).

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS_WITH_VARIANT` + custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `enabledUnits` | options (multi) | Display | Units to show. Options: years \| months \| days \| hours \| minutes \| seconds. |
| `showHumanizedDuration` | checkbox | Display | Show human-readable string. |
| `showDurationString` | checkbox | Display | Show ISO 8601 string. |

**Custom Defaults:** `{ variant: "standard", showHumanizedDuration: false, showDurationString: false }`

---

## Boolean Inputs

### Checkbox (`Checkbox`)
**Input Type:** `checkbox`

Checkbox input.

**Sections:** Basic (`makeBasicFields({filterType:'Boolean'})`), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

No custom fields.

---

### Switch (`Switch`)
**Input Type:** `switch`

Toggle switch.

**Sections:** Basic (`makeBasicFields({filterType:'Boolean'})` + custom), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `labelLeft` | text | Basic | Label on the left side of the switch. |

---

## Selection Inputs

### Select (`SelectInput`)
**Input Type:** `select`

Data-model driven select dropdown.

**Sections:** Basic (`makeBasicFields({filterType:/Node$/})`), Display (`DISPLAY_FIELDS_WITH_FONT_SIZE_WITHOUT_VARIANT`), Data (custom), Behavior (custom), Validation (`VALIDATION_FIELDS`), Advanced (custom extends `ADVANCED_FIELDS`)

**Custom Fields (Data):**

| Field | Type | Description |
|-------|------|-------------|
| `api` | combobox | API. Options: Application \| System. |
| `dataModel` | combobox | Data model for options (dynamic). |
| `selectFields` | graphql | Fields shown in selection popup. |
| `orderByField` | text | Sort field. |
| `orderByDirection` | combobox | Sort direction. Options: asc \| desc. |
| `optionLimit` | integer | Max options displayed. |
| `optionLimitFlex` | integer | Extra options flex. |

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `dialogMode` | options | Dialog mode. Options: never \| always \| advanced. |
| `multiselect` | checkbox | Allow multiple selections. |
| `isClearable` | checkbox | Allow clearing. |
| `selectAll` | checkbox | Enable Select All. Visible when multiselect. |
| `searchPredicate` | options | Filter. Options: contains \| startsWith \| endsWith \| eq. |
| `linkTarget` | transform | Navigation target. |
| `alwaysReloadOptions` | checkbox | Always reload on open. |

**Custom Fields (Advanced):**

| Field | Type | Description |
|-------|------|-------------|
| `additionalFields` | graphql | Extra query fields not shown in UI. |
| `additionalFilter` | transform | Additional data filter. |
| `autoFill` | checkbox | Auto-fill when one result. |
| `autoClear` | checkbox | Auto-clear on filter change. |
| `labelPath` | text | Label path. |
| `stateTracking` | options | State tracking. Options: full \| id. |

**Custom Defaults:** `{ dialogMode: "never", multiselect: false, isClearable: true, selectAll: true, searchPredicate: "contains", api: "Application", stateTracking: "full", optionLimit: 5, optionLimitFlex: 5, alwaysReloadOptions: false }`

---

### Options (`OptionsInput`)
**Input Type:** `options`

Select with pre-defined option list (not data-model driven).

**Sections:** Basic (`makeBasicFields({filterType:/Node$/})`), Display (`DISPLAY_FIELDS_WITH_FONT_SIZE_WITHOUT_VARIANT`), Data (custom), Behavior (custom), Validation (`VALIDATION_FIELDS`), Advanced (custom extends `ADVANCED_FIELDS`)

**Custom Fields (Data):**

| Field | Type | Description |
|-------|------|-------------|
| `options` | transform / fieldGroup | Array of {id, label} options. |
| `optionLimit` | integer | Max options. |
| `optionLimitFlex` | integer | Options flex. |
| `selectFields` | json | Visible popup fields. |

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `multiselect` | checkbox | Allow multiple selections. |
| `isClearable` | checkbox | Allow clearing. |
| `selectAll` | checkbox | Enable Select All. |
| `searchPredicate` | options | Filter. Options: contains \| startsWith \| endsWith \| eq. |
| `linkTarget` | transform | Navigation target. |

**Custom Fields (Advanced):**

| Field | Type | Description |
|-------|------|-------------|
| `labelPath` | text | Label path. |
| `stateTracking` | options | State tracking. Options: full \| id. |

**Custom Defaults:** `{ multiselect: false, isClearable: true, selectAll: true, searchPredicate: "contains", stateTracking: "id", optionLimit: 5, optionLimitFlex: 5 }`

---

### Time Zone (`TimeZoneInput`)
**Input Type:** `timeZone`

IANA time zone select.

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS`), Behavior (custom), Validation (required only), Advanced (custom extends `ADVANCED_FIELDS`)

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `multiselect` | checkbox | Allow multiple selections. |
| `isClearable` | checkbox | Allow clearing. |
| `selectAll` | checkbox | Enable Select All. |
| `searchPredicate` | options | Filter predicate. |

**Custom Defaults:** `{ isClearable: true, multiselect: false, searchPredicate: "contains", stateTracking: "id" }`

---

## Color & Styling

### Color (`ColorInput`)
**Input Type:** `color`

Color picker input.

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS_WITH_VARIANT`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

No custom fields. **Custom Defaults:** `{ variant: "standard" }`

---

### Icon Picker (`IconPicker`)
**Input Type:** `icon`

FontAwesome icon picker.

**Sections:** Basic (custom), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Basic):**

| Field | Type | Description |
|-------|------|-------------|
| `totalItems` | slider | Query size (12–24). |
| `simplified` | checkbox | Simplified mode. |
| `hideColor` | checkbox | Hide color UI. |
| `hideSize` | checkbox | Hide size UI. |
| `hideVariant` | checkbox | Hide variant UI. |
| `variant` | combobox | Default variant. Options: solid \| light \| regular \| duotone. |
| `color` | combobox | Default color. |
| `size` | combobox | Default size. |

**Custom Defaults:** `{ color: "primary" }`

---

## Complex/Structured Inputs

### Address (`AddressInput`)
**Input Type:** `address`

Address input.

**Sections:** Basic (`makeBasicFields({filterType:'AddressNode'})`), Display (`DISPLAY_FIELDS_WITH_VARIANT`), Validation (`VALIDATION_FIELDS` + custom), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `validation.schema` | json | Validation | JSON schema for validation. |

**Custom Defaults:** `{ variant: "standard" }`

---

### Measure (`MeasureInput`)
**Input Type:** `measure`

Value + unit of measurement input.

**Sections:** Basic (`makeBasicFields({filterType:'MeasureNode'})`), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS` + custom), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `validation.schema` | json | Validation | JSON schema for validation. |

---

### Ratio Measure (`RatioMeasureInput`)
**Input Type:** `ratioMeasure`

Ratio measure input (numerator/denominator with units).

**Sections:** Basic (`makeBasicFields({filterType:'RatioMeasureNode'})`), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

No custom fields.

---

### Array (`ArrayInput`)
**Input Type:** `array`

Repeatable array of typed items.

**Sections:** Basic (custom), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS` + custom)

Note: No Advanced section.

**Custom Fields (Basic):**

| Field | Type | Description |
|-------|------|-------------|
| `field` | graphql | Data model field picker. |
| `label` | transform | Label. |
| `itemType` | transform / options | Item type. Options: text \| integer \| float \| checkbox \| address \| date \| time \| datetime. |
| `itemProps` | transform | Properties passed to each item. |
| `dataPath` | text | Data path. |
| `defaultValue` | transform | Default value (should be array). |
| `formElement` | node | Parent form. |
| `description` | text | Helper text. |

**Custom Fields (Validation):**

| Field | Type | Description |
|-------|------|-------------|
| `validation.minItems` | integer | Minimum items. |
| `validation.maxItems` | integer | Maximum items. |

---

### Custom Fields (`CustomFieldsInput`)
**Input Type:** `objectFieldGroup` (dynamic)

Auto-displays configured custom fields from a data model. Custom component, not GenericInput.

**Sections:** Basic (custom), Behavior (custom), Display (`DISPLAY_FIELDS`), Advanced (disabled only)

**Custom Fields (Basic):**

| Field | Type | Description |
|-------|------|-------------|
| `field` | graphql | Data model field (filter: `_CustomFieldsNode$`). |
| `dataPath` | text | Path to _customFields. |
| `model` | options | Data model for custom fields. |
| `label` | text | Field group label. |

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `expandGroups` | switch | Auto-expand groups on load. |

**Custom Defaults:** `{ label: "Custom Fields" }`

---

## Rich Text & Code Editors

### Rich Text (`RichTextInput`)
**Input Type:** `richText`

Rich text editor.

**Sections:** Basic (`makeBasicFields({filterType:'RichText'})`), Display (`DISPLAY_FIELDS` + custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `defaultToDisplayMode` | checkbox | Display | Default to read-only mode. |

---

### Markdown (`MarkdownInput`)
**Input Type:** `markdown`

Markdown editor.

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS` + custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `defaultToDisplayMode` | checkbox | Display | Default to display mode. |

---

### Code Editor (`CodeEditorInput`)
**Input Type:** `codeEditor`

Generic code editor.

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS`), Editor (custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Editor):**

| Field | Type | Description |
|-------|------|-------------|
| `mode` | transform / options | Language. Options: HTML \| CSS \| Python \| SQL \| Text \| XML. |
| `collapsible` | switch | Make collapsible. |
| `collapsedByDefault` | switch | Collapse by default. Visible when collapsible. |

**Custom Defaults:** `{ mode: "text" }`

---

### JSONata (`JSONataInput`)
**Input Type:** `jsonata`

JSONata expression editor.

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS`), Behavior (custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `smartInput` | json | Smart input config. |
| `collapsible` | switch | Make collapsible. |
| `collapsedByDefault` | switch | Collapse by default. |

---

### Transform (`TransformInput`)
**Input Type:** `transform`

Transform expression editor (JSONata with smart input).

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS`), Behavior (custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `smartInput` | json | Smart input config. |
| `advanced` | switch | Show payload and cache key. |

---

## JSON & Schema Inputs

### JSON (`JSONInput`)
**Input Type:** `json`

JSON editor with optional schema validation.

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS` + custom), Editor (custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Display):**

| Field | Type | Description |
|-------|------|-------------|
| `direction` | options | Layout direction. Options: horizontal \| vertical. |

**Custom Fields (Editor):**

| Field | Type | Description |
|-------|------|-------------|
| `dataModel` | options | Data model for auto-loading JSON schema. |
| `showSchemaFields` | switch | Toggle schema fields. |
| `schema` | transform | JSON schema. Visible when showSchemaFields. |
| `collapsible` | switch | Make collapsible. |
| `collapsedByDefault` | switch | Collapse by default. Visible when collapsible. |
| `showEditor` | switch | Show code editor. |
| `editorOptions` | json | Monaco editor options. |

**Custom Defaults:** `{ showEditor: true }`

---

### JSON Schema (`JSONSchemaInput`)
**Input Type:** `jsonSchema`

JSON Schema editor.

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS`), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

No custom fields.

---

## GraphQL Inputs

### GraphQL Builder (`GraphQLBuilderInput`)
**Input Type:** `graphqlBuilder`

Visual GraphQL query/mutation builder.

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS`), Behavior (custom), Validation (`VALIDATION_FIELDS`), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `api` | options | API. Options: Application \| System. |
| `baseModel` | options | Base data model. |
| `mutationType` | options | Mutation type. Options: create \| update \| upsert \| delete. |
| `mode` | options | Output mode. Options: query \| mutation \| ast \| fields. |
| `rootNodeType` | options | Root node. Options: connection \| nodeFields. |
| `singleSelect` | switch | Single field selection only. |
| `filterType` | text | Filter by field type. |
| `rootLevelOnly` | switch | Root-level fields only. |
| `excludeAuditFields` | switch | Exclude audit fields. |
| `excludeAggregate` | switch | Exclude _aggregate fields. |
| `aggregateOnly` | switch | Show aggregate only. |
| `autoOpenBaseFields` | switch | Auto-expand root level. |
| `argModes.inline` | switch | Enable inline args. |
| `argModes.json` | switch | Enable JSON args. |
| `argModes.variable` | switch | Enable variable args. |
| `argModes.transform` | switch | Enable transform args. |
| `enableBaseModelChange` | switch | Allow model change. |
| `enableVariablesDrawer` | switch | Enable variables drawer. |
| `enableCodeDrawer` | switch | Enable code drawer. |
| `enableFilter` | switch | Enable field filter. |

**Custom Defaults:** `{ api: "Application", mode: "query", rootNodeType: "connection", singleSelect: false, enableBaseModelChange: true, enableFilter: true }`

---

### GraphQL Where (`WrappedGraphQLPredicate`)
**Input Type:** `graphqlWhere`

Visual GraphQL WhereInput predicate builder.

**Sections:** Basic (`makeBasicFields({labelRequired:false, fields:{field:false, predicate:false}})`), Display (`DISPLAY_FIELDS`), Advanced (`ADVANCED_FIELDS`)

No custom fields. **Custom Defaults:** `{ dataPath: "predicate", target: { dataPath: "_and.0" } }`

---

## File & Media Inputs

### File (`FileUpload`)
**Input Type:** `file`

File upload input.

**Sections:** Basic (partial `BASIC_FIELDS` — no predicate/formElement), Display (`DISPLAY_FIELDS`), Behavior (custom), Validation (`VALIDATION_FIELDS`)

Note: No Advanced section.

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `multiple` | checkbox | Allow multiple uploads. |
| `accept` | array | Accepted MIME types. Default: `["text/csv"]`. |

---

### Image (`Image`)
**Input Type:** `image`

Image display. **Category: Display.**

**Sections:** Basic (`makeBasicFields({filterType:'ImageNode'})` + custom), Display (`DISPLAY_FIELDS`), Advanced (custom)

**Custom Fields (Basic):**

| Field | Type | Description |
|-------|------|-------------|
| `title` | transform | Image title. |
| `size` | combobox | Size. Options: auto \| tiny \| small \| medium \| large. |

**Custom Fields (Advanced):**

| Field | Type | Description |
|-------|------|-------------|
| `fields` | json | Additional query fields. |
| `src` | transform | Image source path. |
| `thumbnailSrc` | transform | Thumbnail source path. |

---

### PDF (`PDFViewer`)
**Input Type:** `pdfViewer`

PDF viewer. **Category: Display.** Custom component.

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS`), Behavior (custom), Advanced (fields only)

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `pdf` | transform / combobox | PDF source (Fuuz file ID, URL, or base64). |
| `zoom` | transform / combobox | Zoom level. |
| `page` | transform / integer | Page number. |
| `showHeader` | transform (smartInput: checkbox) | Show viewer header. |
| `enableDownload` | transform (smartInput: checkbox) | Show download button. Visible when header shown. |
| `enablePrint` | transform (smartInput: checkbox) | Show print button. Visible when header shown. |
| `enableZoomControls` | transform (smartInput: checkbox) | Show zoom controls. Visible when header shown. |
| `enablePageControls` | transform (smartInput: checkbox) | Show page controls. Visible when header shown. |
| `showFilename` | transform (smartInput: checkbox) | Show filename. Visible when header shown. |

**Custom Defaults:** `{ showHeader: true, enableDownload: true, enablePrint: true, enableZoomControls: true, enablePageControls: true, showFilename: true, zoom: "pageFit", page: 1 }`

---

### SVG (`SVGInput`)
**Input Type:** `svg`

SVG display. **Category: Display.** Does not require a form.

**Sections:** Basic (custom), Display (`DISPLAY_FIELDS`)

**Custom Fields (Basic):**

| Field | Type | Description |
|-------|------|-------------|
| `label` | text | Label. |
| `description` | text | Helper text. |
| `labelAlignment` | options | Label alignment. Options: left \| center \| right. |
| `inputSVG` | transform / html | SVG string to render. |

---

## Display-Type Inputs

### Text Display (`DisplayText`)
**Input Type:** `display`

Read-only text display. **Category: Display.**

**Sections:** Basic (`makeBasicFields`), Display (custom), Behavior (custom), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Display):**

| Field | Type | Description |
|-------|------|-------------|
| `justifyContent` | combobox | Horizontal alignment. |
| `alignTextItems` | combobox | Vertical alignment. |
| `labelPosition` | combobox | Label position. Options: column (above) \| row (left) \| null (none). |
| `labelFontSize` | combobox | Label typography variant (h1–h6, body1, etc.). |
| `labelFontColor` | color | Label color. |
| `fontSize` | combobox | Data typography variant. |
| `fontColor` | color | Data color. |

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `format` | combobox | Display format. Options: address \| boolean \| date \| datetime \| integer \| float \| measure \| ratioMeasure \| time \| markdown \| duration. |
| `durationMode` | options | Duration mode. Options: humanize \| abbreviated \| time. Visible when format = duration. |
| `timeUnit` | options | Duration unit. Visible when abbreviated. |
| `humanizeThreshold` | integer | Units to render. Visible when humanize. |
| `formatString` | text | Format string. |
| `timeZone` | transform | Time zone. |
| `linkTarget` | transform | Navigation target. |
| `linkProps` | json | Link properties. |
| `text` | text | Static text. |

**Custom Defaults:** `{ justifyContent: "start", alignTextItems: "start", labelFontSize: "body1", fontSize: "body1" }`

---

### Progress Bar (`ProgressBar`)
**Input Type:** `progressBar`

Progress bar display. **Category: Display.**

**Sections:** Basic (`makeBasicFields`), Display (`DISPLAY_FIELDS` + custom), Behavior (custom), Advanced (`ADVANCED_FIELDS`)

**Custom Fields (Display):**

| Field | Type | Description |
|-------|------|-------------|
| `barHeight` | slider (0–20) | Bar height. |
| `borderRadius` | slider (0–20) | Border radius. |
| `barColor` | transform (smartInput: color) | Bar color. |
| `rootColor` | transform (smartInput: color) | Background color. |

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `minNumber` | transform (smartInput: number) | Minimum value in range. |
| `maxNumber` | transform (smartInput: number) | Maximum value in range. |

**Custom Defaults:** `{ barHeight: 8, borderRadius: 0, minNumber: 0, maxNumber: 100 }`

---

### Visualization (`Visualization`)
**Input Type:** `visualization`

Visualization chart display. **Category: Display.** **Flags:** isVisualization.

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS`), Behavior (custom)

Note: No Validation or Advanced sections.

**Custom Fields (Behavior):**

| Field | Type | Description |
|-------|------|-------------|
| `visualizationSetting` | combobox | Mode. Options: Saved Visualization \| Chart Type. |
| `visualizationName` | combobox | Saved visualization name. |
| `visualizationChartType` | combobox | Chart type. |
| `visualizationConfiguration` | json | Chart configuration. |
| `visualizationTransform` | jsonata | Data transform. |
| `autoRefreshSeconds` | integer | Auto-refresh interval (seconds). |
| `visualizationTransformRemote` | checkbox | Run transform remotely. |
| `visualizationParameters` | transform | Parameters for payload. |
| `syncTimeseriesRange` | checkbox | Sync time range across timeseries charts. |

**Custom Defaults:** `{ height: "400px", visualizationTransformRemote: true, syncTimeseriesRange: false }`

---

## Recurrence

### RRule (`RRuleInput`)
**Input Type:** `rrule`

Recurrence rule input (iCal RRule spec).

**Sections:** Basic (`BASIC_FIELDS`), Display (`DISPLAY_FIELDS` + custom), Validation (`VALIDATION_FIELDS`), Advanced (custom + `ADVANCED_FIELDS`)

**Custom Fields:**

| Field | Type | Section | Description |
|-------|------|---------|-------------|
| `showRuleString` | checkbox | Display | Show generated RRule string. |
| `enableTimeZone` | checkbox | Advanced | Allow time zone customization. |

**Custom Defaults:** `{ showRuleString: false, enableTimeZone: false }`
