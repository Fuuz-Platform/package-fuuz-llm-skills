# Debugging

Nodes for logging and inspecting data during flow execution.

---

### Log (`log`)
**Type:** transition

A node used to create persistent logs at runtime.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `messageTransform` | string | jsonata | Y | A JSONata transform to run on the workflow state to transform it into a loggable string. Default: `""` |
| `level` | string | | Y | The logging level to send this message at. If you aren't seeing logs from this node, verify the Data Flow log level is set to the same Log Level or lower. Options: `Debug`, `Info`, `Warn`, `Error` |
| `includeContext` | boolean | | Y | When enabled, the logs will contain the current state context. Default: `false` |
| `includePayload` | boolean | | Y | When enabled, the logs will contain the payload passed into this node. Default: `false` |

Standard output port.

---

### Echo (`echo`)
**Type:** transition

A node which routes the unmodified payload to the next nodes.

Standard output port.
