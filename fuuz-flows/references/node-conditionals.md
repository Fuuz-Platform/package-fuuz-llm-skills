# Conditionals

Nodes for branching and filtering flow execution based on conditions.

---

### If Else (`ifElse`)
**Type:** transition

A node used to conditionally select one of two output ports based on the result of a single transform.

When the provided transform returns true, nodes linked to the True Port are executed; when the transform returns false, nodes linked to the False Port are executed.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the workflow state. This transform should return a boolean value. |
| `onTrueNextNodes` | array | port | Y | The nodes to route to if the transform evaluates to true. |
| `onFalseNextNodes` | array | port | Y | The nodes to route to if the transform evaluates to false. |

**Validation:** requireInputNode

---

### Route (`switch`)
**Type:** transition

A node used to conditionally route to one of many ports based on a list of transformations.

The branch transforms are executed in order, from top to bottom. For the first transform that returns true, the engine will route the payload to the nodes attached to that branch's port, and branch evaluation will stop.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `branches` | array | | Y | A list of conditional branches to test. At least one branch must be matched. Each branch has: `name` (string, required) — The name of this branch; `transform` (string jsonata, required) — A JSONata transform that should return a boolean value; `nextNodes` (array port, required) — The nodes to route to if the Transform evaluates to true. |

**Validation:** requireInputNode

---

### Accept (`when`)
**Type:** transition

Note: This node was previously called "Filter". A node used to conditionally accept messages from the flow. If the Transform returns true, the message will be passed to the next nodes; if the Transform returns false, the message will be dropped.

This node is the logical opposite of the Reject node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the workflow state. This transform should return a boolean value. True indicates this branch should proceed; false indicates the branch should terminate. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireOutputNode

---

### Reject (`unless`)
**Type:** transition

A node used to conditionally reject messages from the flow.

If the Transform returns true, the message will be dropped; if the Transform returns false, the message will be passed to the next nodes.

This node is the logical opposite of the Accept node.

| Property | Type | Format | Req | Description |
|----------|------|--------|-----|-------------|
| `transform` | string | jsonata | Y | A JSONata transform to run on the workflow state. This transform should return a boolean value. True indicates this branch should terminate; false indicates the branch should proceed. |

Standard output port.

**Validation:** requireChangedName, requireInputNode, requireOutputNode
