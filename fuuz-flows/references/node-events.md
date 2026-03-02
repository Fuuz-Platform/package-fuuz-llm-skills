# Events

Nodes for subscribing to and publishing messages through the event system.

---

### Data Changes (`dataChanges`)
**Type:** source

A node used to subscribe to data change events. This node will not process changes that are made by the flow where it's defined in an upcoming release.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `api` | string | | N | Which Fuuz API to subscribe to changes in. When not provided, the node will subscribe to data changes from all APIs. Default: `"Application"`. Options: `Application`, `System`. |
| `type` | string | | N | The GraphQL type to subscribe to changes in (e.g. ItemRevision). When not provided, the node will subscribe to data changes from all types. |
| `operations` | array | | N | The operations to filter data change events to. Options: `Create`, `Update`, `Delete`. |

Standard output port.

**Validation:** requireChangedName, requireOutputNode

---

### Topic (`topic`)
**Type:** source

A node used to subscribe to messages from a specific topic.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `topicName` | string | | Y | The topic to subscribe to. |

Standard output port.

**Validation:** requireChangedName, requireOutputNode

---

### Publish (`publish`)
**Type:** transition

A node used to publish a message to a topic.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `topicId` | string | | Y | The topic to publish to. |
| `bulkPublishing` | boolean | | N | When Bulk Publishing is enabled, an array input will be published as individual messages. Default: `false`. |

Standard output port.

**Validation:** requireChangedName, requireInputNode

---

### Request (`request`)
**Type:** source

A node used to initiate the flow in Fuuz from an external request. You must have a Response node to respond to the request with a payload.

_No configurable properties._

Standard output port.

**Validation:** requireOutputNode

---

### Response (`response`)
**Type:** transition

A node used to send a response to a flow execution initiated through a Request node. When the flow wasn't initiated through a Request node, the response node does nothing. The node always outputs the payload it received.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `responseTransform` | string | jsonata | N | A transform on the input for the response to be sent back to the requestor. Default: `"$"`. |

Standard output port.

**Validation:** requireInputNode

---

### Webhook (`webhook`)
**Type:** source

A node used to subscribe to messages from a specific topic that is webhook accessible.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `topicName` | string | | Y | The topic that is webhook accessible to listen on requests from. |

Standard output port.

**Validation:** requireChangedName, requireOutputNode
