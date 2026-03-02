# Interaction

Button elements for triggering flows, actions, and common operations.

---

### Flow (`FlowButton`)

Customizable button that executes a web flow when clicked.

**Flags:** isButton
**Exposed Functions:** `fn.execute`

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `title` | text | Tooltip title. |
| `useIconButton` | checkbox | Use icon button style. |
| `text` | transform (smartInput: text) | Button text. |
| `buttonColor` | combobox | Button color. Options: primary \| secondary \| error \| red \| green \| amber. |
| `icon` | transform (smartInput: icon) | Button icon. |

**Display** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `margin` | slider (0–96, step 4) | Element margin. |
| `width` | text | Element width. |
| `height` | text | Element height. |

**Flow** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `dataFlowId` | combobox (queryType: DataFlow) | Flow to execute. Filters: active web flows. |
| `payload` | transform (smartInput: json) | Payload for flow execution. |

**Advanced** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `customButtonColor` | transform (smartInput: color) | Custom button color override. |
| `customIconColor` | transform (smartInput: color) | Custom icon color override. |
| `disabled` | transform (smartInput: checkbox) | Disabled state. |
| `hidden` | transform (smartInput: checkbox) | Hidden state. |
| `customIconSize` | slider (1–10, step 1) | Icon size. |
| `customTextSize` | slider (12–96, step 4) | Text size. |

**Defaults:** `{ margin: 6, buttonColor: "primary", icon: { icon: "project-diagram", variant: "duotone", size: "icon" }, width: "76px", height: "48px", disabled: false, hidden: false, useIconButton: false, payload: {} }`

---

### Action (`ActionButton`)

Customizable button that supports a wide array of actions.

**Flags:** isButton
**Exposed Functions:** `fn.execute`

**Basic** (expanded):

Same as FlowButton Basic, plus:

| Field | Type | Description |
|-------|------|-------------|
| `disableHover` | checkbox | Disable hover effect. |
| `disableRipple` | checkbox | Disable ripple effect. |

**Display** (collapsed): Same as FlowButton.

**Action** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `action` | action | Array of action steps to execute on click. |

**Advanced** (collapsed): Same as FlowButton.

**Defaults:** `{ margin: 6, buttonColor: "primary", icon: { icon: "mouse-pointer", variant: "duotone", size: "icon" }, width: "76px", height: "48px", disabled: false, hidden: false, useIconButton: false, disableHover: false, disableRipple: false, action: [] }`

---

### Split (`SplitButton`)

Compound button with dropdown supporting multiple actions or flows.

**Exposed Functions:** `fn.execute`

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `title` | text | Tooltip title. |
| `buttonColor` | combobox | Button color. Options: primary \| secondary \| error \| red \| green \| amber. |

**Display** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `margin` | slider (0–96, step 4) | Element margin. |
| `width` | text | Element width. |
| `height` | text | Element height. |

**Action** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `format` | combobox | Output format. Options: action \| flow. |
| `actions` | fieldGroup | Action steps. Visible when format = action. Sub-fields: `text` (text), `actionsAction` (action). |
| `flows` | fieldGroup | Flow steps. Visible when format = flow. Sub-fields: `text` (text), `dataFlowId` (combobox, DataFlow), `payload` (transform, smartInput: json). |

**Advanced** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `customButtonColor` | transform (smartInput: color) | Custom button color override. |
| `disabled` | transform (smartInput: checkbox) | Disabled state. |
| `hidden` | transform (smartInput: checkbox) | Hidden state. |
| `customTextSize` | slider (12–96, step 4) | Text size. |

**Defaults:** `{ margin: 6, buttonColor: "primary", width: "150px", height: "48px", disabled: false, hidden: false, actions: [], flows: [] }`

---

### Menu (`MenuButton`)

Menu button with dropdown items that execute actions or flows.

**Flags:** isButton

**Basic** (expanded):

| Field | Type | Description |
|-------|------|-------------|
| `title` | text | Tooltip title. |
| `isFlow` | switch | Toggle between Action mode and Flow mode. |
| `flows` | fieldGroup | Flow items. Visible when isFlow = true. Sub-fields: `text` (text), `icon` (icon), `dataFlowId` (select, DataFlow), `payload` (transform, smartInput: json). |
| `actions` | fieldGroup | Action items. Visible when isFlow = false. Sub-fields: `text` (text), `icon` (icon), `actionsAction` (action). |

**Display** (collapsed):

| Field | Type | Description |
|-------|------|-------------|
| `width` | text | Element width. |
| `height` | text | Element height. |
| `menuIcon` | options | Menu icon style. Options: ellipsis-v (Kebab) \| ellipsis-h (Meatballs) \| bars (Hamburger) \| grip-lines (Hotdog). |
| `iconSize` | options | Icon size. Options: small \| medium \| large. |

**Defaults:** `{ margin: 0, isFlow: false, height: "48px", width: "48px", menuIcon: "ellipsis-v", iconSize: "small" }`

---

## Custom Action Buttons

These six buttons auto-generate action chains based on their target element context (form or table). They have no configurable editor panel fields — their behavior is determined by the element they are bound to.

All share: **Flags:** isActionButton

### Create (`AddButton`)

**Requires:** Table. Generates: open form → mutation (create) → refresh table.

**Defaults:** `{ icon: { icon: "plus", variant: "regular" }, buttonColor: "secondary", title: "Create" }`

---

### Edit (`EditButton`)

**Requires:** Table. Generates: open form (pre-filled from selected row) → mutation (update) → refresh table.

**Defaults:** `{ icon: { icon: "pencil", variant: "duotone" }, buttonColor: "secondary", title: "Update" }`

---

### Save (`SaveButton`)

**Requires:** Form. Generates: call form's `fn.save()`.

**Defaults:** `{ icon: { icon: "save" }, buttonColor: "primary" }`

---

### Print (`PrintButton`)

**Requires:** Form. Generates: render PDF document → open document (auto-print).

**Defaults:** `{ icon: { icon: "print" } }`

---

### Delete (`DeleteButton`)

**Requires:** Table or Form. In table context: confirmation → mutation (delete selected rows) → refresh table. In form context: confirmation → call `fn.delete()` → navigate back.

**Defaults:** `{ buttonColor: "error", icon: { icon: "trash", variant: "solid" } }`

---

### Search (`SearchButton`)

**Requires:** Table. Generates: call table's `fn.search()`.

**Defaults:** `{ icon: { icon: "search", variant: "duotone" }, buttonColor: "primary", title: "Search" }`
