# Validation

Nodes for validating payloads against schemas.

---

### Validate (`validate`)
**Type:** transition

A node used to validate the payload with the provided schema. If the payload doesn't fit the schema, this node will throw an error.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `schema` | object | | Y | The JSON Schema to validate the payload against. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode
