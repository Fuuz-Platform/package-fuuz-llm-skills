# Transformations

Nodes for converting data between formats and filtering or reshaping arrays.

---

### JSON To CSV (`jsonToCsv`)
**Type:** transition

A node used to convert an array of JSON objects to a CSV.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `fieldDelimiterTransform` | string | jsonata | Y | The character that separates fields. Options: `Comma` (`,`), `Pipe` (`\|`), `Semicolon` (`;`). |
| `fieldWrapTransform` | string | jsonata | Y | The character that field values are wrapped in. Options: `Double Quote` (`"`), `Single Quote` (`'`). |
| `endOfLineTransform` | string | | Y | The end of line delimiter. Default: `"\\n"`. Options: `Line Feed` (`\n`), `Carriage Return` (`\r`), `Tab` (`\t`), `Carriage Return and New Line` (`\r\n`). |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### CSV To JSON (`csvToJson`)
**Type:** transition

A node used to transform a CSV string to a JSON object.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | Y | A transform to reformat the input payload. Default: `"$"`. |
| `fieldDelimiterTransform` | string | jsonata | Y | The character that separates fields. Options: `Comma` (`,`), `Pipe` (`\|`), `Semicolon` (`;`). |
| `wrapDelimiterTransform` | string | jsonata | Y | The character that field values are wrapped in. Options: `Double Quote` (`"`), `Single Quote` (`'`). |
| `eolDelimiterTransform` | string | jsonata | Y | The end of line delimiter. Default: `"\\n"`. Options: `Line Feed` (`\n`), `Carriage Return` (`\r`), `Tab` (`\t`), `Carriage Return and New Line` (`\r\n`). |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### JSON To XML (`jsonToXml`)
**Type:** transition

A node used to transform the input from JSON to XML.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | Y | A transform to reformat the input payload. Default: `"$"`. |
| `attributeKeyTransform` | string | jsonata | Y | The prefix used to access the attributes. Options: `$`, `@`. |
| `charKeyTransform` | string | jsonata | Y | The prefix used to access the character content. Options: `Underscore` (`_`), `Hash` (`#`). |
| `rootNameTransform` | string | jsonata | Y | The name of the root element. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### XML To JSON (`xmlToJson`)
**Type:** transition

A node used to transform the input from XML to a JSON object.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | Y | A transform to reformat the input payload. Default: `"$"`. |
| `attributeKeyTransform` | string | jsonata | Y | The prefix used to access the attributes. Options: `$`, `@`. |
| `charKeyTransform` | string | jsonata | Y | The prefix used to access the character content. Options: `Underscore` (`_`), `Hash` (`#`). |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |
| `advancedConfigurationTransform` | jsonata | jsonata | N | Advanced configuration supported by the binding. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Unique Array (`uniqueArray`)
**Type:** transition

A node used to transform an array with the duplicate items removed by using the uniqBy function.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `uniqueTransform` | string | jsonata | Y | The transform to evaluate items in the input array one at a time, from first to last. If this transform returns a value that has not been seen before, that item will be added to the output array. However, if the same transform result was already seen, the item will be dropped. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When checked, the Input and Output Transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Filter Array (`filterArray`)
**Type:** transition

A node used to filter items in an array passed into the node or generated through the input transform.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `filterTransform` | string | jsonata | Y | The input array will be checked against the transform provided here. The filter transform will operate in the context of a single record in the array, from first to last. If the transform returns true, the data will be passed to the next nodes. If the transform returns false, it will be removed from the array. From within the filter transform you can access the index of the record through the `$i` binding and the overall array through the `$a` binding. If you need to reference the input payload you can escape the scope using `$$`. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | N | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Group Array (`groupBy`)
**Type:** transition

A node used to group data from the input into an object based on the provided Group By transform.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `groupingTransform` | string | jsonata | Y | The input will be checked against the condition given here. If it returns true, it will be added to an object in the output. If it returns false, it will be ignored. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input and Output transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough

---

### Predicate Filter (`predicateFilter`)
**Type:** transition

A node used to filter a payload by given predicates.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `inputTransform` | string | jsonata | N | A transform to reformat the input payload. Default: `"$"`. |
| `payloadType` | string | jsonata | Y | Determines if the payload is an array or an object. When set to array, the payload will be automatically converted into an array. Options: `Array` (`array`), `Object` (`object`). |
| `predicateFilterTransform` | string | jsonata | Y | Predicate to filter the payload by. |
| `caseSensitiveComparison` | boolean | | Y | Determines whether the filter should be case sensitive. Default: `false`. |
| `outputTransform` | string | jsonata | N | A transform to format the output of the node. Default: `"$"`. |
| `enableAdvancedConfiguration` | boolean | | Y | When true, the Input, Output and Advanced Configuration transforms will become configurable. Default: `false`. |

Standard output port.

**Validation:** minimumNoteLength: 20, requireChangedName, requireInputNode, requireWalkthrough
