# Context

Nodes for reading, writing, and removing data from the flow execution context.

---

### Merge Context (`mergeContext`)
**Type:** transition

A node used to merge data into the flow execution context. The input is deep-merged into the existing state, concatenating arrays if possible.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the data flow payload. The output is stored in the state context, which can be accessed with $state.context. |

Standard output port.

---

### Remove From Context (`removeFromContext`)
**Type:** transition

A node that takes in an array of key paths which will be removed from the state context.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the data flow state context. This should return an array of key paths that will be removed from the state context if a match is found. If the result is not an array, no changes to the context will be made. The state context can be accessed with $state.context. Default: `"[]"` |

Standard output port.

---

### Set Context (`setContext`)
**Type:** transition

A node used to set the flow execution context.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the data flow payload. The output is stored in the state context, which can be accessed with $state.context. |

Standard output port.
