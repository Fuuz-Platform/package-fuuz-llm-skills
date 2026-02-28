# JSONata Platform Evaluation Context

JSONata expressions are evaluated in two distinct contexts: **Screen Elements** (frontend) and **Data Flows** (backend). Each provides different context variables.

---

## Screen Element Context

Used in transform props, onChange handlers, validation expressions, table column data/style.

### Root Input (`$`)

The root `$` is the component-specific data, assembled by each component's `mapPropsToPayload`. Varies by component type:
- **Form inputs**: current form field values
- **Table columns**: current row data
- **Action buttons**: data context of the triggering element

### `metadata` — Platform Metadata

Available in all frontend JSONata expressions. Assembled from Redux state.

| Path | Description |
|------|-------------|
| `metadata.user` | Current user object — `.id`, `.firstName`, `.lastName`, `.email`, `.environments`, `.accessTypeId` |
| `metadata.tenant` | Current tenant — `.id`, `.name`, `.tenantTypeId` |
| `metadata.userTenants` | All tenants the user belongs to — `[].id`, `[].tenant.id`, `[].tenant.name` |
| `metadata.userRoles` | Roles — `[].role.id`, `[].role.name`, `[].role.description`, `[].role.homeScreenId` |
| `metadata.screen` | Current screen metadata |
| `metadata.environment` | Environment name (from enterprise) |
| `metadata.platformVersion` | Platform version string |
| `metadata.settings` | Localization: `.DateFormat`, `.TimeFormat`, `.DateTimeFormat`, `.IntegerFormat`, `.FloatFormat`, `.Locale`, `.TimeZone`, `.AlwaysDisplayTimeZones` |
| `metadata.enterprise` | Enterprise — `.id`, `.name`, `.domain`, `.maintenanceMode`, `.platformVersion`, `.environment` |
| `metadata.urlParameters` | URL path parameters (e.g., `:id` from `/screens/:id`) |
| `metadata.querystring` | URL query string parameters |

### `$components` — Screen Component Shared State

Hierarchical store of all named screen components. Each component registers:

```
$components.<Name>.data     — Component's current data
$components.<Name>.fn       — Component's callable functions
$components.<Name>.context  — Component-specific context (e.g., row data in tables)
```

#### Table Component (`$components.MyTable`)

| Path | Description |
|------|-------------|
| `.data` | Current table row data |
| `.fn.loadData({filter, rowLimit})` | Reload data from backend |
| `.fn.search({rowsPerPage})` | Search with filter form |
| `.fn.setRows(array)` | Overwrite all rows |
| `.fn.addRows(array)` | Append rows |
| `.fn.updateRows(array)` | Update existing rows by id |
| `.fn.upsertRows(array)` | Add or update rows by id |
| `.fn.deleteRows(array)` | Delete rows by id |
| `.fn.selectRow(array)` | Select rows |
| `.fn.deselectRow(array)` | Deselect rows |
| `.fn.toggleRowSelection(array)` | Toggle selection |
| `.fn.selectAllRows()` | Select all rows |
| `.fn.deselectAllRows()` | Deselect all rows |
| `.fn.selectAllFiltered()` | Select all filtered rows |
| `.fn.deselectAllFiltered()` | Deselect all filtered rows |
| `.fn.setSelectedRows(array)` | Set exact selection |
| `.fn.setData(data)` | Set table data directly |

#### Form Component (`$components.MyForm`)

| Path | Description |
|------|-------------|
| `.data` | Current form field values |
| `.fn.setValue(field, value)` | Set a field value |
| `.fn.focus(field)` | Focus a field |
| `.fn.blur(field)` | Blur a field |
| `.fn.loadData()` | Reload form data |
| `.fn.validate()` | Validate and return errors |
| `.fn.save(saveAll?)` | Save the form |
| `.fn.delete()` | Delete form data |
| `.fn.disableField(field)` | Disable a field |
| `.fn.enableField(field)` | Enable a field |
| `.fn.getUpdateMutation()` | Get GraphQL mutation |
| `.fn.getUpdatePayload()` | Get mutation variables |
| `.fn.getVariables()` | Get all form variables |

#### Container Component (`$components.MyContainer`)

| Path | Description |
|------|-------------|
| `.fn.hide()` | Hide the container |
| `.fn.show()` | Show the container |
| `.fn.toggle()` | Toggle visibility |
| `.fn.resetHidden()` | Reset hidden fields |
| `.fn.collapse()` | Collapse the container |
| `.fn.expand()` | Expand the container |

#### Screen Element (`$components.MyScreen`)

| Path | Description |
|------|-------------|
| `.context` | Screen context data |
| `.fn.hide()` | Hide |
| `.fn.show()` | Show |
| `.fn.toggle()` | Toggle visibility |
| `.fn.showLoading()` | Show loading indicator |
| `.fn.hideLoading()` | Hide loading indicator |
| `.fn.executePageLoadAction()` | Re-execute page load action |

#### TabBar Component (`$components.MyTabBar`)

| Path | Description |
|------|-------------|
| `.tabIndex` | Current tab index (number) |
| `.tabs` | Array of tab context objects |
| `.currentTab` | Current tab context object |
| `.fn.setTab(index)` | Switch to tab by index |
| `.fn.enableTab(index)` | Enable a tab |
| `.fn.disableTab(index)` | Disable a tab |
| `.fn.showTab(index)` | Show a tab |
| `.fn.hideTab(index)` | Hide a tab |

#### Widget Element (`$components.MyWidget`)

| Path | Description |
|------|-------------|
| `.fn.setParams(params)` | Set parameters for the embedded screen |

#### Action / Flow / Split Button (`$components.MyButton`)

| Path | Description |
|------|-------------|
| `.fn.execute(payload?)` | Execute the button's action with optional payload |

#### Cards Component (`$components.MyCards`)

| Path | Description |
|------|-------------|
| `.data` | Array of card data objects |
| `.fn.loadData()` | Load/reload card data |
| `.fn.search()` | Search cards using filter form |

### `$appConfig` — Application Configuration

The tenant's application configuration object. Available in all local (non-remote) frontend evaluations.

### `showScreenDialog` — Dialog Function

Opens a screen in a dialog. Available in screen element context.

### Frontend-Only Functions

These functions are only available in browser context:

| Function | Description |
|----------|-------------|
| `$navigateTo(pathname, options?)` | Navigate to URL. Options: `{newWindow, replace}` |
| `$navigateBack()` | Navigate back in history |
| `$navigateReload()` | Reload current screen |
| `$showAlertDialog(message, options?)` | Show alert dialog |
| `$showConfirmDialog(message, options?)` | Show confirmation dialog (returns boolean) |
| `$showFormDialog(formProps)` | Show form dialog |
| `$showScreenDialog(props)` | Show screen dialog |
| `$retrieveFileContent(fileId, encoding?)` | Download and decode a file by ID |
| `$addNotificationMessage(title, options?)` | Show UI notification |
| `$writeToClipboard(data)` | Write to clipboard |
| `$readFromClipboard()` | Read from clipboard |

---

## Data Flow Context

Used in Transform nodes, Set Context, Merge Context, Conditional nodes, and other data flow nodes.

### Root Input (`$`)

The current node's payload — `state.payload`. This is the data flowing from the previous node.

### `$state` — Flow Execution State

The full accumulated execution state. Available as a binding in all data flow node evaluations.

| Path | Description |
|------|-------------|
| `$state.payload` | Current payload (same as `$`) |
| `$state.context` | User-defined context set by Set Context / Merge Context nodes |
| `$state.claims` | Auth claims of the triggering user |
| `$state.batches` | Batch tracking array (Fork/Broadcast/Combine) |
| `$state.messageId` | Unique message ID for this flow execution |
| `$state.metadata` | Flow metadata (tenantId, etc.) |
| `$state.depth` | Current execution depth counter |
| `$state.nextNodes` | Array of next node IDs to execute |
| `$state.trace` | Trace entries (when tracing enabled) |
| `$state.lastError` | Error from last caught error (with catchErrorNode) |
| `$state.catchErrorNode` | ID of the error handler node |

#### `$state.context`

Free-form object that accumulates data across the flow:
- **Set Context** nodes replace the entire context
- **Merge Context** nodes deep-merge into the existing context
- Access as `$state.context.anyPath`
- Primary mechanism for passing data between nodes outside the payload

#### `$state.claims`

Authentication claims of the user or system that triggered the flow. Available for security/authorization logic.

#### `$state.lastError`

When using Try-Catch error handling: `{name, message, stack, info, node}` from the last failed node.

#### `$state.metadata`

Flow metadata: `{tenantId, enterpriseId, flowId}`.

### Node Types and JSONata Results

| Node Type | What happens with JSONata result |
|-----------|--------------------------------|
| JSONata (transform) | Result becomes new `payload` |
| If Else | Result (boolean) determines output port |
| Route | Result determines which branches to route to |
| Accept / Reject | Result (boolean) determines whether to continue |
| Set Context | Result replaces the entire `$state.context` |
| Merge Context | Result is deep-merged into `$state.context` |
| Remove From Context | Result (array of paths) removes keys from context |
| Response | Result becomes the response payload |
| Log | Result is logged as the message |

---

## Transform Props Pattern

Screen element props that accept JSONata use this object structure:

```json
{
  "__transform": "expression or { id: savedTransformId }",
  "__remote": false,
  "__language": "JSONata",
  "__dynamicFields": {
    "payload": ["fieldA", "fieldB"],
    "context": ["metadata.user"]
  },
  "__cacheKey": "unique-key",
  "__refreshInterval": 5000,
  "__fallbackValue": "default"
}
```

| Key | Description |
|-----|-------------|
| `__transform` | JSONata expression string, or `{id}` / `{name}` for saved transform |
| `__remote` | `true` to evaluate server-side (default `false`) |
| `__language` | `"JSONata"` (default) or `"JavaScript"` |
| `__dynamicFields` | Paths in payload/context that the transform depends on. When values at these paths change, the transform re-executes and the component re-renders. Shape: `{ payload: ["data.createdByUser.id"], context: [] }`. Set to `false` to evaluate once only. |
| `__cacheKey` | Unique key for storing the transform result, scoped to the element. |
| `__refreshInterval` | Milliseconds between automatic re-evaluations |
| `__fallbackValue` | Default value while transform is pending |

---

## Evaluation Pipeline Summary

### Frontend (Screen Element)

1. Component has prop with `{ __transform: "...", __remote: false }`
2. `withTransform` HOC detects dynamic props, calls `getExpressionValue()`
3. Assembles: payload (from `mapPropsToPayload`), context (`{metadata, components}`), binding functions
4. Routes to `evaluateLocalExpression` or `evaluateRemoteTransform`
5. Calls `expression.evaluate(payload, { ...context, appConfig, ...bindingFunctions })`
6. `$` = payload, all binding keys available as `$variableName`

### Backend (Data Flow)

1. Data flow node receives `{ context, node, state }`
2. Node calls `dependencies.jsonata.evaluate({ transform, payload: state.payload, bindings: { state } })`
3. Calls `expression.evaluate(payload, { state, ...bindingFunctions })`
4. `$` = current payload, `$state` = full execution state

---

## Context Differences

| Feature | Screen Elements | Data Flows |
|---------|----------------|------------|
| Root `$` | Component data (mapPropsToPayload) | Node's current payload |
| `metadata` | User, tenant, settings, URL params | Not directly available |
| `$components` | Screen component shared state | Not available |
| `$state` | Not available | Full flow state (payload, context, claims, batches) |
| `$appConfig` | Application configuration | Not available |
| UI functions | `$navigateTo`, `$showAlertDialog`, etc. | Not available |
| Evaluation | Local (browser) or remote (`__remote: true`) | Always server-side |
