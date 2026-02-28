# Device Gateway

Nodes for interacting with devices through the MFGx Device Gateway.

---

### Execute Device Function (`executeDeviceFunction V2`)
**Type:** transition

A node used to execute a function on a device through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The device to execute the function on. |
| `functionIdTransform` | string | | Y | Which function to execute on the device. |
| `requestTransform` | object | | N | A series of transforms to produce the payload of the request to send to the device function. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

---

### Print File (`devicePrintFile`)
**Type:** transition

A node used to print an MFGx file through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The printer to send the document to. |
| `fileIdTransform` | string | | Y | The file to print. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

**Validation:** requireInputNode

---

### Print Data (`devicePrintDocument`)
**Type:** transition

A node used to print a document through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The printer to send the document to. |
| `dataTransform` | string | | Y | A transform producing the base-64 encoded document to send to the printer. |
| `filenameTransform` | string | | N | The name of the file to print. The extension on the filename determines how the gateway handles the document. If no filename is provided, a randomly generated ID is used, and the document is assumed to be a PDF. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

**Validation:** requireInputNode

---

### Print Raw (`devicePrintRaw`)
**Type:** transition

A node used to send raw commands (i.e. ZPL) to a printer through the MFGx Device Gateway.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceIdTransform` | string | | Y | The printer to send the raw commands to. |
| `dataTransform` | string | | Y | A transform producing the raw commands to send to the printer. |
| `filenameTransform` | string | | N | The name of the file to print. If no filename is provided, a randomly generated ID is used. |
| `functionTimeoutSecondsTransform` | string | | N | A transform producing the number of seconds to wait for the device to return a response before throwing an error. Defaults to the timeout defined on the function record when not supplied. |
| `requestIdTransform` | string | | N | A transform producing the ID to use for the request. When not provided, an ID is auto-generated, so this field is typically not required. |

Standard output port.

**Validation:** requireInputNode

---

### Device Subscription (`deviceSubscription`)
**Type:** source

A node used to subscribe to device subscriptions. Any dropdowns left blank will act as a wildcard, allowing you to subscribe to multiple subscriptions with one node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `deviceDriverType` | string | | N | The device driver type. |
| `driver` | string | | N | The device driver. |
| `device` | string | | N | The device. |
| `deviceGateway` | string | | N | The device gateway. |
| `deviceSubscriptionType` | string | | N | The device subscription type. |
| `deviceSubscription` | string | | N | The device subscription. |

Standard output port.

**Validation:** requireChangedName, requireOutputNode
