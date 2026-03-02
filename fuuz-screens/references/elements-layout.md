# Layout

Container and structural elements for organizing screen content.

---

### Accordion (`ScreenAccordion`)

Expandable/collapsible section with optional header actions.

**Flags:** canvas, isContainer

**Basic** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `label` | transform (smartInput: text) | Accordion header title. |

**Display** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `width` | text | Element width. |
| `height` | text | Element height. |
| `margin` | slider (0–96, step 4) | Element margin. |

**Behavior** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `disabled` | transform (smartInput: checkbox) | Prevents expanding/collapsing. |
| `hidden` | transform (smartInput: checkbox) | Hide the accordion. |
| `defaultExpanded` | checkbox | Expanded when first rendered. |

**Actions** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `actions` | fieldGroup (label via `title`) | Header action buttons. Sub-fields: `title` (text), `text` (text), `useIconButton` (checkbox, default true), `icon` (icon), `buttonColor` (combobox: primary \| secondary \| error \| red \| green \| amber), `customButtonColor` (color), `disabled` (transform), `hidden` (transform), `action` (action). |

**Advanced** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `renderOnExpansion` | checkbox | Mount content only on first expansion. |
| `disposeOnCollapse` | checkbox | Unmount content when collapsed. |

**Defaults:** `{ label: "Accordion", renderOnExpansion: true, width: "100%", height: "auto" }`

---

### Accordion Group (`ScreenAccordionGroup`)

Container for accordions. Only one can be expanded at a time when exclusive.

**Flags:** canvas, isContainer

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `exclusive` | checkbox | Only one accordion expanded at a time. |
| `defaultExpandedId` | text | Element name of the default expanded accordion. |

**Display** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `width` | text | Element width. |
| `height` | text | Element height. |

**Behavior** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `hidden` | transform (smartInput: checkbox) | Hide the accordion group. |

**Defaults:** `{ exclusive: true, width: "100%", height: "auto" }`

---

### Container (`Container`)

Block element that can contain other elements. Supports flex and grid layouts.

**Flags:** canvas, isContainer
**Exposed Functions:** `fn.hide`, `fn.show`, `fn.toggle`, `fn.resetHidden`, `fn.collapse`, `fn.expand`, `fn.toggleCollapse`, `fn.showLoading`, `fn.hideLoading`

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `padding` | slider (0–96, step 4) | Container padding. |
| `margin` | slider (0–96, step 4) | Container margin. |
| `background` | combobox | Background color. Options: accent \| primary \| light \| default \| paper \| dark \| custom. |
| `customBackground` | transform (smartInput: color) | Custom background. Visible when background = custom. |
| `shadow` | checkbox | Box shadow. |
| `width` | text | Container width. |
| `height` | text | Container height. |
| `flexGrow` | checkbox | Expand to fill available space. |
| `flexShrink` | checkbox | Shrink to fit available space. |

**Behavior** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `hidden` | transform (smartInput: checkbox) | Hide container and contents. |
| `collapsible` | checkbox | Enable collapsible behavior. |
| `collapsedFooter` | checkbox | Separate footer section for collapse. Visible when collapsible. |
| `collapsedSize` | slider (0–1000) | Collapsed height in px. Visible when collapsible. |
| `collapsedByDefault` | checkbox | Start collapsed. Visible when collapsible. |
| `onClickTransform` | checkbox | Enable on-click transform. |
| `onClickActions` | transform | Transform to run on click. Visible when onClickTransform. |

**Border** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `bordersInput` | border (mode: full) | Border configuration. |
| `borderRadius` | slider (0–96, step 4) | Corner rounding. |

**Layout** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `layout` | options | Layout mode. Options: flex \| grid. |
| `columns` | transform (smartInput: integer) | Grid columns. Visible when grid. |
| `rows` | transform (smartInput: integer) | Grid rows. Visible when grid. |
| `gap` | text | Grid gap. Visible when grid. |
| `justifyItems` | options | Grid horizontal justify. Options: start \| center \| end. Visible when grid. |
| `flexWrap` | checkbox | Wrap children. Visible when flex. |
| `flexDirection` | options | Options: column \| row \| row-reverse \| column-reverse. Visible when flex. |
| `justifyContent` | options | Options: flex-start \| flex-end \| start \| end \| left \| right \| center \| space-between \| space-around \| space-evenly. Visible when flex. |
| `alignItems` | options | Options: flex-start \| center \| flex-end \| stretch \| baseline. Dynamic labels based on layout/direction. |

**Advanced** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `style` | transform (smartInput: json) | Additional CSS styles (camelCase). |

**Defaults:** `{ flexWrap: true, flexDirection: "column", justifyContent: "flex-start", alignItems: "stretch", layout: "flex", width: "100%", height: "auto" }`

---

### Resizable Panel (`ResizablePanelLayout`)

Resizable panel that adjusts its size dynamically.

**Flags:** canvas

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `axis` | switch | Panel axis. Vertical / Horizontal. |
| `handle` | options | Handle position. Options: left \| right (vertical) or top \| bottom (horizontal). |
| `defaultSize` | integer (step 10) | Default panel size in px. |
| `maxSize` | integer (step 10) | Maximum size in px. |
| `minSize` | integer (step 10) | Minimum size in px. |
| `toggleHotkey` | text | Hotkey to toggle panel (e.g., "alt+1"). |

**Defaults:** `{ handle: "right", defaultSize: 400, axis: false, minSize: 100, maxSize: 800 }`

---

### Grid Cell (`GridCell`)

Wrapper for grid cell properties inside a grid container.

**Flags:** canvas, isGridCell

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `colSpan` | transform (smartInput: integer) | Number of columns this cell spans. |
| `rowSpan` | transform (smartInput: integer) | Number of rows this cell spans. |

**Advanced** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `style` | transform (smartInput: json) | Additional CSS styles. |

**Defaults:** `{ style: {} }`

---

### Button Group (`ButtonsGroup`)

Container for grouping buttons together.

**Flags:** canvas, isButtonGroups

**Display** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `width` | text | Group width. |
| `height` | text | Group height. |
| `verticalOrientation` | switch | Horizontal / Vertical orientation. |
| `margin` | slider (0–96, step 4) | Group margin. |
| `buttonGroupCustomColor` | transform (smartInput: color) | Custom background color. |
| `buttonGroupTextColor` | transform (smartInput: color) | Custom text color for all buttons. |

**Defaults:** `{ margin: 6, width: "200px", height: "auto" }`

---

### Text (`RichText`)

Text block for displaying static rich text content.

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `description` | text (multiline) | Helper text. |
| `content` | richText | Rich text content to display. |

**Display** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `width` | text | Element width. |
| `height` | text | Element height. |

**Defaults:** `{ width: "100%", height: "auto" }`

---

### Screen (`Screen`)

Root screen element. Cannot be added from toolbox — automatically created as the canvas root.

**Flags:** canvas, isContainer, excludeFromToolbox
**Exposed Functions:** `fn.hide`, `fn.show`, `fn.toggle`, `fn.resetHidden`, `fn.showLoading`, `fn.hideLoading`, `fn.setContextValue`, `fn.deleteContextValue`, `fn.setContext`, `fn.mergeContext`, `fn.executePageLoadAction`, `fn.return`

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `padding` | slider (0–96, step 4) | Screen padding. |
| `margin` | slider (0–96, step 4) | Screen margin. Visible when removeOuterMargin != true. |
| `removeOuterMargin` | switch | Remove outer margin, fill entire viewport. |
| `background` | combobox | Options: accent \| primary \| light \| default \| paper \| dark \| custom. |
| `customBackground` | color | Custom background. Visible when background = custom. |
| `shadow` | checkbox | Box shadow. |
| `borderRadius` | slider (0–96, step 4) | Corner rounding. |
| `width` | text | Screen width. |
| `height` | text | Screen height. |
| `flexDirection` | combobox | Options: column \| row \| row-reverse \| column-reverse. |
| `flexWrap` | checkbox | Wrap children. |
| `alignItems` | combobox | Options: flex-start \| flex-end \| center \| stretch \| baseline. |
| `justifyContent` | combobox | Options: flex-start \| flex-end \| start \| end \| left \| right \| center \| space-between \| space-around \| space-evenly. |
| `hidden` | transform (smartInput: checkbox) | Hide screen contents. |
| `flexGrow` | checkbox | Expand to fill available space. |

**Screen Flows** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `pageLoadDataFlowIds` | combobox (multiselect, queryType: DataFlow) | Flows executed on page load. Active web flows only. |
| `flowsDelay` | slider (0–10, step 0.5) | Delay in seconds before page load flows execute. |
| `screenDataFlowIds` | combobox (multiselect, queryType: DataFlow) | Additional flows loaded for events/schedules/$executeFlow. |

**Page Load** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `pageLoadAction` | jsonata | Action run once on page load. |
| `intervals` | fieldGroup | Repeating timed actions. Sub-fields: `interval` (integer, seconds), `action` (jsonata). |

**Defaults:** `{ flexDirection: "column", flexWrap: true, alignItems: "stretch", justifyContent: "flex-start", width: "100%", height: "auto", flowsDelay: 0, removeOuterMargin: false }`

---

### Widget (`ScreenWidget`)

Embeds another screen as a reusable widget.

**Flags:** isScreenWidget
**Exposed Functions:** `fn.setParams`

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `screenId` | combobox (queryType: Screen) | Widget screen to render. Filtered: active, deployed, isWidget. |
| `screenParams` | transform (smartInput: json) | Parameters sent to the widget as URL parameters. |
| `width` | text | Element width. |
| `height` | text | Element height. |

**Defaults:** `{ screenParams: {}, width: "100%", height: "auto" }`

---

### Icon (`IconDisplay`)

Displays a FontAwesome icon.

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `title` | text | Icon tooltip title. |
| `icon` | icon | Icon selection. |
| `width` | text | Element width. |
| `height` | text | Element height. |

**Defaults:** `{ icon: { icon: "mouse-pointer", variant: "regular", color: "primary" }, title: "Icon", height: "48px", width: "48px" }`

---

### Tabs (`TabBar`)

Tab bar that switches between multiple groups of content.

**Exposed Functions:** `fn.setTab`, `fn.enableTab`, `fn.disableTab`, `fn.showTab`, `fn.hideTab`

**Display** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `width` | text | Element width. |
| `height` | text | Element height. |

**Configuration** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `orientation` | options | Options: horizontal \| vertical. |
| `justifyTabs` | options | Tab justify. Dynamic labels based on orientation. Options: start \| center \| end. |
| `defaultTabIndex` | options | Default tab. Dynamic options from tabs list. |
| `tabWidth` | integer (step 10, min 0) | Global tab width in px. |
| `tabActiveColor` | options | Active indicator color. Options: primary \| secondary. |
| `tabBarColor` | color | Tab bar background color. |
| `hidden` | checkbox | Hide tab bar, only render content. |

**Tabs** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `tabs` | fieldGroup (label via `tabTitle`, minItems: 1) | Tab definitions. Sub-fields: `tabTitle` (text), `tabIcon` (icon), `tabColor` (color), `tabWidth` (integer, visible when horizontal), `textWrap` (checkbox). |

**Defaults:** `{ width: "100%", height: "auto", orientation: "horizontal", justifyTabs: "start", tabWidth: 160, tabActiveColor: "secondary" }`

---

### Embedded Webpage (`EmbeddedWebpage`)

Embeds an external webpage via iframe.

**Flags:** canvas

**Basic** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `url` | transform (smartInput: text) | URL to embed (HTTPS required). |
| `width` | text | Element width. |
| `height` | text | Element height. |

**Permissions** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `allowSameOrigin` | switch | Allow navigation within the iframe. |
| `allowDownloads` | switch | Allow file downloads. |
| `allowPopups` | switch | Allow popups. |
| `allowScripts` | switch | Allow scripts. |
| `allowForms` | switch | Allow form submissions. |
| `windowModals` | switch | Allow window modals. |

**Advanced** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `style` | transform (smartInput: json) | Additional CSS styles. |

**Defaults:** `{ url: "", width: "100%", height: "auto", all permissions: false }`
