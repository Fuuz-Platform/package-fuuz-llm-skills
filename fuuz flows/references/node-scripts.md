# Scripts

Nodes for running transformation scripts against the workflow state payload.

---

### JSONata (`transform`)
**Type:** transition

A node used to run a script using the JSONata language.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata script to run on the workflow state. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### JavaScript (`javascriptTransform`)
**Type:** transition

A node used to run a script using the JavaScript language.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | javascript | Y | A JavaScript script to run on the workflow state. |

Standard output port.

---

### Saved Script (`savedTransformV2`)
**Type:** transition

A node used to transform the state payload with the given saved script.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transformScriptLanguage` | string | | Y | The language used by transform. Default: `"JSONata"`. |
| `transformId` | string | | Y | The saved script to run on the workflow state. |
| `requestTransform` | string | jsonata | Y | A transform to populate the integrate request. Default: `"$"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the payload returned from the integrate request. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.

---

### Data Mapping (`dataMapping`)
**Type:** transition

A node used to transform the state payload with the given data mapping.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `dataMappingId` | string | | Y | A data mapping to run on the payload. |
| `transformScriptLanguage` | string | | Y | The language used by the transform. Default: `"JSONata"`. |
| `requestTransform` | string | jsonata | Y | A transform to populate the data mapping request. Default: `"$"`. |
| `responseTransform` | string | jsonata | Y | A transform to reformat the payload returned from the data mapping request. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | Enables configuration of the Script Language and the Request and Response transforms. Default: `false`. |

Standard output port.

---

### Object Diff (`objectDiff`)
**Type:** transition

A node that compares two objects and returns the differences.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `originalObj` | string | jsonata | Y | The first comparison object. |
| `updatedObj` | string | jsonata | Y | The second comparison object. |
| `ignore` | array | jsonata | N | An array of key paths to be excluded from comparison. |
| `detailed` | boolean | | Y | Shows a breakdown of which properties were added, removed, and changed. Default: `true`. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.
