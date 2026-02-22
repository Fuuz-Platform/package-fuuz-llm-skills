# Fuuz Screen Runtime Context

> Complete documentation of the runtime context available to every Fuuz screen JSONata expression.
> Covers metadata, components, transform payload, trace, and data structures.

---

## Screen Runtime Context

Every Fuuz screen has a runtime context object available to all JSONata expressions. Understanding this structure is essential for writing transforms, conditional styling, and data binding. This context is accessible via `$metadata`, `$components`, `$data`, and related bindings.

### Context Structure Overview

```
{
  metadata: { ... },       // Platform, user, tenant, screen, settings, enterprise info
  components: { ... },     // Every component on the screen with fn, data, formState
  data: [ ... ],           // Current data array (table rows / query results)
  currentView: { ... },    // Active table view configuration
  flowEngine: { ... },     // Flow engine state
  initialData: [ ... ],    // Original data before any transforms
  urlParameters: { ... }   // URL parameters passed to the screen
}
```

### metadata Object

Available via `$metadata` in JSONata expressions:

```json
{
  "urlParameters": {},
  "querystring": {
    "Workcenters": { "WorkcenterTable": { "view": "Default" } }
  },
  "user": {
    "id": "ckzzmdqs8000y0jwx2o5qhwmi",
    "firstName": "Craig",
    "lastName": "Scott",
    "middleName": "",
    "email": "cscott@mfgx.io",
    "environments": [
      { "id": "build", "name": "Build", "url": "https://build.mfgx.fuuz.app" }
    ],
    "accessTypeId": "administrator"
  },
  "tenant": {
    "id": "fuuzOverflow",
    "name": "Fuuz Overflow",
    "tenantTypeId": "custom"
  },
  "userTenants": [
    {
      "id": "...",
      "accessTypeId": "administrator",
      "tenant": { "id": "...", "name": "...", "tenantTypeId": "custom" }
    }
  ],
  "screen": {
    "id": "workcenter",
    "forceFullscreen": false,
    "version": {
      "id": "cmlbkd55qeg36018tbrfwdllc",
      "number": "0.0.13",
      "createdAt": "2026-02-07T00:16:12.638Z"
    },
    "latestVersion": { "id": "...", "label": "0.0.13" }
  },
  "environment": "build",
  "platformVersion": "2026.2.0.766",
  "settings": {
    "AlwaysDisplayTimeZones": false,
    "DateFormat": "L",
    "DateTimeFormat": "L LT",
    "FloatFormat": "0,0.[00000000]",
    "IntegerFormat": "0,0",
    "Locale": null,
    "LogoImageSourceURL": "",
    "PrimaryColor": "#006FBA",
    "SecondaryColor": "#709CBA",
    "ThemeMode": "Light",
    "TimeFormat": "LT",
    "TimeZone": "America/Detroit"
  },
  "enterprise": {
    "id": "mfgx",
    "name": "MFGx",
    "maintenanceMode": false,
    "platformVersion": "2026.2.0.766",
    "environment": "build",
    "domain": "build.mfgx.fuuz.app"
  },
  "userRoles": []
}
```

**Common metadata access patterns in transforms:**
- `$metadata.user.id` — Current user ID
- `$metadata.user.firstName & " " & $metadata.user.lastName` — User display name
- `$metadata.user.accessTypeId` — Access level (`"administrator"`, `"standard"`, etc.)
- `$metadata.tenant.id` — Current tenant/app ID
- `$metadata.tenant.name` — Tenant display name
- `$metadata.screen.id` — Current screen ID
- `$metadata.screen.version.number` — Screen version
- `$metadata.settings.TimeZone` — Configured timezone
- `$metadata.settings.PrimaryColor` — Theme primary color
- `$metadata.settings.ThemeMode` — `"Light"` or `"Dark"`
- `$metadata.enterprise.environment` — `"build"`, `"production"`, etc.
- `$metadata.enterprise.maintenanceMode` — Boolean maintenance flag
- `$metadata.querystring` — URL query parameters parsed by component
- `$metadata.urlParameters` — URL route parameters

### components Object

Every component on the screen is available via `$components.<elementName>`:

```json
{
  "FormFilterPanel": {
    "data": {},
    "initialData": null,
    "defaultValues": {},
    "fn": {},
    "query": { "api": "Application", "model": "Workcenter" },
    "formState": {
      "dirty": false,
      "dirtyFields": {},
      "errors": {},
      "hasValidationErrors": false,
      "invalid": false,
      "modified": { "code": false, "cell": false },
      "pristine": true,
      "submitting": false,
      "submitFailed": false,
      "submitSucceeded": false,
      "touched": { "code": false, "cell": false },
      "valid": true,
      "values": {},
      "visited": { "code": false, "cell": false }
    }
  },
  "WorkcenterTable": {
    "data": null,
    "initialData": null,
    "defaultValues": { "cell": {}, "line": {}, "currentWorkOrder": {}, "scheduleGroup": {} },
    "fn": {},
    "currentView": {
      "name": "Default",
      "rowLimit": 500,
      "isDefault": true,
      "columns": [
        { "colId": "TableColumnActions", "hide": false, "rowGroup": false },
        { "colId": "TableColumnCode", "hide": false, "sort": "asc", "sortIndex": 0 }
      ],
      "origin": "system"
    }
  },
  "Screen": {
    "fn": {},
    "context": {}
  }
}
```

**Key component access patterns:**
- `$components.Screen.context` — Screen-level context set by `pageLoadAction`
- `$components.Screen.fn.setContext($obj)` — Set screen context
- `$components.Screen.fn.pageLoadFn()` — Re-execute pageLoadAction
- `$components.Form1.data` — Current form data
- `$components.Form1.fn.save()` — Save form
- `$components.Form1.fn.delete()` — Delete record
- `$components.Form1.fn.refresh()` — Reload form data
- `$components.Form1.fn.reset()` — Reset form to initial state
- `$components.Form1.formState.dirty` — Whether form has unsaved changes
- `$components.Form1.formState.valid` — Whether form passes validation
- `$components.Form1.formState.values` — Current form field values
- `$components.Table1.fn.search()` — Refresh table query
- `$components.Table1.data` — Current table data array
- `$components.Table1.currentView` — Active view configuration
- `$components.Cards1.fn.search()` — Refresh cards query
- `$components.Cards1.data` — Current cards data

### Transform Payload (Row-Level Context)

Every transform receives a `payload` containing the current row data. Inside table column transforms, `$data` refers to the current row:

```json
{
  "data": {
    "id": "wc-bmx-c2",
    "code": "WC-BMX-C2",
    "name": "Batch Mixing Workcenter 2",
    "description": "Primary production workcenter for Batch Mixing Cell 2",
    "cell": {
      "id": "cmmk01grpn00lfnbxmlc",
      "code": "BMX-C2",
      "name": "Batch Mixing Cell 2"
    },
    "line": {
      "id": "cmmk01grpn00lfmggxm4",
      "code": "BMIX",
      "name": "Batch Mixing"
    },
    "currentWorkOrder": {
      "id": "wo-mke-0368",
      "code": "WO-MKE-0368",
      "status": "Completed"
    },
    "isActive": true,
    "createdAt": "2026-01-05T16:00:30.920Z",
    "createdByUser": {
      "id": "ckzzmdqs8000y0jwx2o5qhwmi",
      "firstName": "Craig",
      "lastName": "Scott",
      "email": "cscott@mfgx.io"
    },
    "createdByDisplay": "Craig Scott",
    "activeColor": "#2e7d32",
    "activeDisplay": "Active",
    "modeColor": "#757575",
    "stateColor": "#757575"
  }
}
```

In transforms, access via:
- `$data.id` — Current row ID
- `$data.code` — Current row code
- `$data.cell.name` — Nested relationship field
- `$data.isActive` — Boolean field
- `$data.activeColor` — Computed display field (from dataTransform)

### Transform Trace (Debugging)

Every transform execution produces a trace object for debugging:

```json
{
  "runtime": 725,
  "property": "style",
  "id": "wc-bmx-c2",
  "column": "TableColumnActive",
  "dynamicPropPathComputeTime": 0,
  "dynamicPropPaths": [],
  "dynamicProps": {}
}
```

- `runtime` — Execution time in ms (watch for slow transforms)
- `property` — Which prop this transform applies to (`"style"`, `"data"`, `"hidden"`, etc.)
- `id` — The data record ID being processed
- `column` — Which column (for table column transforms)

### Transform Result

Each transform returns a result that gets applied to the target property:

```json
{ "color": "#2e7d32" }
```

For style transforms, the result is a CSS style object. For data transforms, the result is the transformed data value.

### Data Array

The `data` array contains the current query results (table rows, form data, etc.). Each row includes:
- All queried fields from the data model
- Nested relationship objects (cell, line, currentWorkOrder, createdByUser, etc.)
- Computed display fields added by `dataTransform` (createdByDisplay, activeColor, activeDisplay, etc.)

### initialData vs data

- `initialData` — The raw query results before any client-side transforms
- `data` — The current state after transforms and user edits

This distinction is important for forms: `formState.dirty` compares current `data` against `initialData` to detect changes.

### Preferred Data Distribution Pattern: pageLoadAction → setContext → bind

For screens that display read-only data from multiple sources, centralize data loading in `pageLoadAction` and distribute via `$components.Screen.fn.setContext()`:

```
pageLoadAction (JSONata):
  1. Execute GraphQL queries via $query()
  2. Transform/format results
  3. Call $components.Screen.fn.setContext({ key1: value1, key2: value2 })

DisplayText elements bind via:
  data.__transform: "$components.Screen.context.key1"
  data.__cacheKey: "uniqueCacheKey"
  data.__remote: false
```

**This pattern avoids:**
- Nested Forms with individual query subscriptions
- Redundant GraphQL calls for data that can be fetched once
- Complex per-element query configurations

**Use individual Form/Table queries only when:**
- The element needs a live, independent query subscription
- The data is truly independent of other screen data
- The element requires server-side `dataTransform` on query results

### Practical Examples

**Conditional styling based on row data:**
```jsonata
/* TableColumn style transform — color based on active status */
$data.isActive ? {"color": "#2e7d32"} : {"color": "#d32f2f"}
```

**Accessing metadata in pageLoadAction:**
```jsonata
(
  $tz := $metadata.settings.TimeZone;
  $userId := $metadata.user.id;
  $tenantId := $metadata.tenant.id;
  /* Use in queries or display */
)
```

**Checking form state before action:**
```jsonata
/* Disable save button when form is pristine or invalid */
$components.Form1.formState.pristine or $not($components.Form1.formState.valid)
```

**Dynamic visibility based on user access:**
```jsonata
/* Hide admin-only section for non-admins */
$metadata.user.accessTypeId != "administrator"
```

**Reading URL parameters:**
```jsonata
$metadata.querystring.Workcenters.WorkcenterTable.view
```

---
