# Fuuz Screen Common Errors & Solutions

> Quick-reference troubleshooting guide for Fuuz screen render errors and data binding issues.

---

## Common Errors and Solutions

| Error | Cause | Solution |
|---|---|---|
| Blank white screen / React error boundary | Missing required property | Check elementId, name, parent, query.api, query.model, dataPath |
| Component not found / render error | Wrong `resolvedName` | Use exact names: `ActionButton`, `DisplayText`, `Container` |
| Layout not rendering | Gap as number | Use `"gap": "16px"` (string with units) |
| Width not applied | Missing units | Use `"width": "280px"` not `"280"` |
| DisplayText not updating | Missing `__dynamicFields` | List ALL `$state.context.*` / `$components.Screen.context.*` fields in context array |
| Screen fails to load | Missing ROOT props | Include `flowsDelay: 0`, `removeOuterMargin: true` |
| Child not rendering | Node ID mismatch | Exact match between parent `nodes` array and child key |
| Table not filling height | Wrong container sizing | Use `height: "0px"` + `flexGrow: true` chain |
| Filter not connected | Missing `filterFormName` | Set `filterFormName` on Table to match form's `elementName` |
| Form input not working | Missing `formElement` | Set `formElement` on every input to its parent Form's `elementName` |
| "Cannot read property of null" | Null/empty transform | Never use `null` for `data`, `style`, `bordersInput`, `filterPredicate` — use `{}` |
| Form crashes on mount | Missing `query` object | Even `autoLoad: false` forms need a full `query` object with `api`, `model` |
| Data binding shows nothing | Missing `dataPath` on input/display | Every bound element needs `dataPath` matching the model field |
| Save does nothing | Missing `field` on input | Input elements need `field` property for write-back |
| DisplayText label renders "[object Object]" | Dynamic label without `__fallbackValue` | Add `__fallbackValue` to dynamic label transforms |
| Cards element invisible | Empty data with no hidden handler | Use dynamic `hidden` with `$isNilOrEmpty()` check |
| Parameters error | Empty `parameters` set to null | Use `parameters: "{}"` (empty JSONata object string), never null |
| DisplayText not rendering metadata/context values | `data` binding missing `__cacheKey` and/or `__remote` | Always include full triplet: `__transform`, `__cacheKey`, `__remote: false` |
| Array transform returns single object instead of array | Mapping expression (`.{}`) with one result returns object, not array | Wrap mapping in `[]` array literal: `[$metadata.items.{ "id": id }]` |
| Cards element errors on auto-generated query | Empty `fields` and `parameters` on Cards with client-side `dataTransform` | Include `fields: ["id"]` and dummy `parameters` with a filter (e.g., `id _eq $metadata.user.id`) |
| Passive Form errors on load | `autoLoad: false` with empty parameters | Keep `autoLoad: true` with dummy filter (`id _eq "system"`) and `dataTransform: { "remote": true }` |
| Description on Container/Form/Cards causes issues | `description` added to structural/layout elements | Only add `description` to DisplayText, input elements, and ActionButton — never to Container, Form, Cards, Table, RichText |

---

## Action Chain & Mutation Errors

| Error | Cause | Solution |
|---|---|---|
| "Syntax Error: Unexpected Name" on mutation | Wrong mutation format or mutation string malformed | Use the visual GraphQL builder — don't manually type mutation strings. Verify the model name and mutation operation match. |
| "Variable not provided" on mutation | Payload structure mismatch — `create` not wrapped in array | Mutation payloads must be arrays: `{ "payload": [{ "create": data }] }` not `{ "payload": { "create": data } }` |
| "Field doesn't exist on type ModelName" | Table query requesting a non-existent field (common: `"dataPath": "actions"` on menu column) | Menu columns must use `"dataPath": ""` (empty string). Remove any field name that doesn't exist in the data model from the query fields array. |
| Table shows spinner forever / no data loads | Query failing silently — check browser console Network tab | Open browser DevTools (F12) → Network tab → look for GraphQL requests with error responses. Common causes: wrong API (`system` vs `application`), missing model, invalid field name. |
| Create button spins but nothing happens | Mutation succeeds but table doesn't refresh | Ensure the action chain has a third step: `{ "type": "transformation", "transformation": "$components.TableName.fn.search()", "remote": false }` |
| Edit form opens empty | Missing `transforms.pre` on form step | For edit forms, add `"transforms": { "pre": "data" }` to pre-populate the form with the selected row's data |
| Form dialog shows but fields are blank/broken | Missing required form field properties | Every form field needs: `element`, `field`, `dataPath`, and `query.fields` — see Form Action Field Configuration in component-patterns.md |
| Delete confirmation appears but delete fails | Wrong delete payload structure | Delete mutations use `{ "payload": [{ "where": { "id": data.id } }] }` — the `where` wrapper is required |

---

## Setup vs Master Table Decision

| Factor | Setup Table | Master Table |
|---|---|---|
| **Data type** | Reference/lookup data (types, categories, codes) | Transactional/operational data (orders, logs, events) |
| **CRUD complexity** | Simple — 2-3 fields, basic create/edit/delete | Complex — many fields, relationships, custom actions |
| **Relationships** | Minimal — standalone or simple FK | Rich — multiple FKs, nested displays |
| **Row count** | Low (10s to 100s) | High (100s to millions) |
| **Template** | `"template": "setup"` | `"template": "master"` |

> **Note:** Some reference data screens may use the master template if they have rich CRUD needs despite being "setup" data.

---

## Debugging Tips

1. **Browser Console (F12 → Console):** Look for React error messages, GraphQL errors, and JSONata evaluation errors
2. **Network Tab (F12 → Network):** Filter by "graphql" to see all API requests and their responses
3. **Common GraphQL error locations:** Check `query.fields` array for typos, verify `query.model` matches the data model name exactly
4. **Action chain debugging:** If a button does nothing, verify the `action` (singular for ActionButton) vs `actions` (plural for menu) property naming
5. **Form field debugging:** If form fields don't render, check that `element`, `field`, `dataPath`, and `query.fields` are all present on each field

---

## Full JSON Output Required

**ALWAYS output the complete screen JSON.** Fuuz screens cannot be partially updated via import — the platform replaces the entire screen definition on import. There is no merge or patch mechanism. This means:

- **Never output "just the changed section"** — The user needs the full `[{ "name": "...", "type": "canvas", "props": { "design": { ... } } }]` every time.
- **Never say "update the X section with..."** — Output the entire JSON file with the change incorporated.
- **For edits to existing screens** — Take the user's existing JSON, apply the requested changes, and output the complete modified JSON.
- **Save as `.json` file** — Always save output as a downloadable `.json` file the user can import directly.

### How Users Import Screen JSON

There are two methods to get the JSON into Fuuz:

**Method 1: Application Designer Import (recommended for new screens)**
1. Go to **Application Designer** (Configuration → Application Designer)
2. Open or create a screen
3. Use **File → Import JSON** to upload the `.json` file
4. The screen renders immediately in the designer canvas

**Method 2: Screen Version Paste (for updating existing screens)**
1. Go to **Screens** list (Configuration → Screens)
2. Select the target screen (or create a new one)
3. Go to the **Versions** tab
4. Either create a new version or edit an existing version
5. Paste the full JSON into the version text editor
6. Save the version

Both methods require the complete JSON structure — partial JSON will fail or overwrite the screen with only the provided elements.

### Exception: Single Element Definition Editing

If the user only needs to update a **single element** (not nested children), you can provide just that element's definition JSON. The user can apply it in the Screen Designer:

1. Click the element in the designer canvas
2. Click the **edit icon** at the top of the property panel
3. Select **"Definition"**
4. Paste the updated element JSON

**Limitations:** This only works for a single, flat element — it does not support nesting. If the element contains children (e.g., a Container with child elements), you must output the full screen JSON instead.

When providing a single element definition, output the node's complete definition object:
```json
{
  "type": { "resolvedName": "DisplayText" },
  "isCanvas": false,
  "props": { ... },
  "displayName": "DisplayText",
  "custom": { "elementName": "MyDisplayText" },
  "parent": "ParentElementName",
  "hidden": false,
  "nodes": [],
  "linkedNodes": {}
}
```

---

