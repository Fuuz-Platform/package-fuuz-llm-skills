# Flow Control

Nodes for controlling the execution path and timing of a flow — broadcasting, forking, combining, collecting, delaying, scheduling, error handling, and mutex locking.

---

### Broadcast (`broadcast`)
**Type:** transition

A node which outputs a stream of individual messages based on an input array or object. The Broadcast node can be used to implement "for-each" or "map" style logic, with subsequent nodes operating on each individual element of an array or object rather than on the array or object as a whole. If a non-object value is passed to the node, the value is wrapped into an array with one element.

The individual streamed messages can be recombined into a single value using a Combine node.

No configurable properties beyond the standard output port.

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Fork (`fork`)
**Type:** transition

A node which creates batches of messages based on configurable branch outputs. Each branch is executed in parallel with the full payload passed to the Fork node. This allows flows to run different flow paths on the same payload in parallel.

The forked branches can be recombined into a single object using a Combine node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `branches` | array | | Y | A list of branches to fork to. Each branch will be executed in parallel. See sub-table below. |

**Branch items:**

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `name` | string | | N | The name of this branch. When forked branches are recombined, the value of this branch will be keyed by this name. |
| `nextNodes` | array | port | Y | The branch of nodes to route a copy of the input payload to. |

**Validation:** requireInputNode, requireOutputNode

---

### Combine (`combine`)
**Type:** transition

A node which combines batches of messages created through Fork or Broadcast nodes back into a single message.

If the number of batches does not match the number of batches created by a Broadcast or Fork node, this node will throw an error.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `executionTimeout` | integer | | Y | The number of seconds to wait for the next message in a batch created by a Fork or Broadcast node. If the timeout expires, this node will throw an error. Default: `10`. Min: 1, Max: 900. |
| `payloadCombinationStrategy` | string | | N | Determines how payloads should be combined. Options: `Index`, `Last`, `Merge`. Default: `"index"`. See overview for details. |
| `contextCombinationStrategy` | string | | N | Determines how contexts should be combined. Options: `Index`, `Last`, `Merge`. Default: `"first"`. See overview for details. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Collect (`collect`)
**Type:** transition

A node which collects messages by time or count, emitting an array of collected messages when the time or count is hit. You must provide both a batch count and a timeout to ensure the messages will be emitted. This node generally shouldn't follow a broadcast or fork node, to combine the results of those nodes you generally want to use a Combine node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `batchCount` | integer | | N | The number of messages to collect before emitting a batch. If no value is provided, it will default to 1000 messages. Min: 2, Max: 1000. |
| `batchTimeMs` | integer | | N | The number of milliseconds to collect messages for before emitting a batch. If no value is provided, it will default to 5 minutes. Min: 1000, Max: 300000. |
| `payloadCombinationStrategy` | string | | N | Determines how payloads should be combined. Options: `Index`, `Last`, `Merge`. Default: `"index"`. See overview for details. |
| `contextCombinationStrategy` | string | | N | Determines how contexts should be combined. Options: `Index`, `Last`, `Merge`. Default: `"first"`. See overview for details. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Delay (`delay`)
**Type:** transition

A node used to delay execution of the next nodes by a specified amount of time.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `delay` | integer | | Y | The number of milliseconds to delay execution of the next nodes in the flow. Minimum: 0. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireOutputNode

---

### Mutex Lock (`mutexLock`)
**Type:** transition

Prevents downstream nodes from executing until this lock is released, ensuring only one message can process a shared resource at a time. The lock is released when a Mutex Unlock node executes or when the TTL expires. Locks are scoped to the specific flow version. They won't block execution in other flows or other versions of this flow even if using the same Resource ID. If the lock can't be acquired after all retry attempts, the node can be configured to either discard the message silently or throw an error.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `resourceIdTransform` | string | jsonata | Y | A unique identifier for the resource being locked. Messages with the same Resource ID will wait for each other. Use different IDs for independent operations. |
| `lockTTLms` | integer | | Y | The time in milliseconds the lock is held before automatically releasing. This is a safety limit. Ideally, your flow should release the lock explicitly with a Mutex Unlock node before this expires. Default: `30000`. Min: 30000, Max: 300000. |
| `lockRetries` | integer | | Y | The maximum number of attempts to acquire a lock. Set to -1 for infinite retries (use with caution). Default: `3`. Min: -1. |
| `throwErrorWhenLockNotAcquired` | boolean | | Y | Throws an error instead of silently discarding the message when the lock can't be acquired. Enable this if you need to handle lock failures explicitly (e.g., with a Try/Catch node) or want failed attempts logged. Default: `false`. |

Standard output port.

**Validation:** requireInputNode, requireOutputNode

---

### Mutex Unlock (`mutexUnlock`)
**Type:** transition

A node which unlocks the last lock placed by a Mutex Lock node.

No configurable properties beyond the standard output port.

Standard output port.

**Validation:** requireInputNode

---

### Schedule (`schedule`)
**Type:** source

A node used to receive the payload from defined frequencies of schedules attached to the dataflow.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `configure` | display | | N | Follow the link to configure a schedule for this flow. |

Standard output port.

**Validation:** requireChangedName, requireOutputNode

---

### Try Catch (`tryCatch`)
**Type:** transition

A node used to catch errors that may happen downstream.

When an error occurs on the route linked to the Try port, that branch is stopped and nodes linked to the Catch Port are then executed. When no nodes are linked to the Catch Port, the current Try Catch handling is cleared for downstream nodes.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `tryNextNodes` | array | port | Y | The nodes to attempt to run during normal operation. |
| `catchNextNodes` | array | port | Y | The nodes to route to if an error occurs on the Try route. |

**Validation:** requireInputNode, requireOutputNode

---

### Throw Error (`throwError`)
**Type:** transition

A node used to throw a ThrowBindingError which halts execution. The error will use the provided Message and the data will include an object with an 'info' property holding the value of the Error Info parameter.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `messageTransform` | string | jsonata | Y | The error message. |
| `errorInfoTransform` | string | jsonata | N | A Transform on the input to create an object with properties that will be included with the error. Default: `"{}"`. |

No standard output port (execution halts on throw).

**Validation:** requireInputNode
