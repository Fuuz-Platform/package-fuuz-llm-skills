# Fuuz Relationship Patterns Reference

Fuuz supports four relationship types between data models: one-to-one (1:1), many-to-one (many:1), one-to-many (1:many), and many-to-many (many:many via junction model). Fuuz does **NOT** support embedded/nested models — every related entity must be its own separate model with a foreign key reference.

---

## Core Rule: Always Define Both Fields

**CRITICAL:** Every relationship requires **two** fields on the referencing model:

1. **Foreign key field** — Type `ID` or `ID!`, stores the referenced record's ID
2. **Relation field** — Type is the related model name (e.g., `AlarmState!`), declares the relationship with `relation` metadata

Omitting either field will cause the relationship to not function correctly.

---

## Pattern 1: Many-to-One (Most Common)

Multiple records in this model reference one record in the related model.

**Example: Alarm → AlarmState** (many alarms can have the same state)

### Foreign Key Field

```json
{
  "name": "alarmStateId",
  "type": "ID!",
  "description": "**`alarmStateId`** (`ID!`)\nForeign key referencing the current alarm state.",
  "metadata": {
    "mfgx": {
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
}
```

### Relation Field

```json
{
  "name": "alarmState",
  "type": "AlarmState!",
  "description": "**`alarmState`** (`AlarmState!`)\nRelated AlarmState object for workflow state.",
  "metadata": {
    "mfgx": {
      "relation": {
        "deletionReferenceBehavior": "prevent",
        "fields": {
          "from": "alarmStateId",
          "to": "id"
        }
      },
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
}
```

**Key details:**
- `type` is the related model's PascalCase name (e.g., `"AlarmState!"`)
- `relation.fields.from` is the foreign key field on THIS model (`"alarmStateId"`)
- `relation.fields.to` is the target field on the RELATED model (almost always `"id"`)
- `deletionReferenceBehavior: "prevent"` blocks deleting the AlarmState while any Alarm references it

---

## Pattern 2: One-to-Many (Collection / Reverse Relationship)

One record in this model has many related records in another model.

**Example: Line → Cells** (one line contains many cells)

### Collection Field (on the Line model)

```json
{
  "name": "cells",
  "type": "[Cell!]!",
  "description": "**`cells`** (`[Cell!]!`)\nCollection of Cells associated with this Line.",
  "metadata": {
    "mfgx": {
      "relation": {
        "fields": {
          "from": "id",
          "to": "lineId"
        }
      },
      "mutations": {
        "create": { "include": true },
        "update": { "include": true }
      },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
}
```

**Key details:**
- `type` uses array syntax: `"[Cell!]!"` (array of required Cell objects, array itself required)
- `relation.fields.from` is `"id"` (this model's primary key)
- `relation.fields.to` is `"lineId"` (the foreign key on the CHILD model)
- No `deletionReferenceBehavior` on collection fields — that's on the child's relation field

**The Cell model must have the corresponding many-to-one fields:**

```json
{
  "name": "lineId",
  "type": "ID!",
  "description": "Foreign key to the parent Line.",
  "metadata": { "mfgx": { "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
},
{
  "name": "line",
  "type": "Line!",
  "description": "Parent Line for this Cell.",
  "metadata": { "mfgx": { "relation": { "deletionReferenceBehavior": "prevent", "fields": { "from": "lineId", "to": "id" } }, "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
}
```

---

## Pattern 3: One-to-One

One record in this model references exactly one record in another model, and the reference is unique.

Implemented the same as many-to-one, but with `unique: true` on the foreign key field. **IMPORTANT:** As with all relationships, define BOTH the FK field AND the relation field:

```json
{
  "name": "configurationId",
  "type": "ID!",
  "description": "Foreign key to the unique configuration record.",
  "metadata": {
    "mfgx": {
      "mutations": { "create": { "include": true }, "update": { "include": true } },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": true
  }
},
{
  "name": "configuration",
  "type": "WorkcenterConfiguration!",
  "description": "The unique configuration for this workcenter.",
  "metadata": {
    "mfgx": {
      "relation": { "deletionReferenceBehavior": "prevent", "fields": { "from": "configurationId", "to": "id" } },
      "mutations": { "create": { "include": true }, "update": { "include": true } },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
}
```

The `unique: true` is on the FK field (`configurationId`), not the relation field. This enforces that only one record can reference a given configuration.

---

## Pattern 4: Many-to-Many (Junction Model)

Fuuz does not have native many-to-many support. Implement using a **junction model** (also called a join table or association model).

**Example: WorkOrder ↔ Product (many work orders can produce many products)**

Create a junction model `WorkOrderProduct`:

```json
{
  "name": "WorkOrderProduct",
  "metadata": {
    "mfgx": {
      "module": { "id": "myModule", "group": { "id": "myModuleGroup" } },
      "exposed": true,
      "mutable": true,
      "mutations": { "create": true, "update": true, "del": true },
      "labelField": "id",
      "mongo": { "collection": "workOrderProduct" },
      "graphql": { "type": "Object" }
    }
  },
  "kind": "Reference",
  "fields": [
    {
      "name": "id",
      "type": "ID!",
      "metadata": { "mfgx": { "mutations": { "create": { "include": true }, "update": { "include": false } }, "queries": { "include": true }, "where": { "include": true } }, "unique": true }
    },
    {
      "name": "workOrderId",
      "type": "ID!",
      "metadata": { "mfgx": { "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
    },
    {
      "name": "workOrder",
      "type": "WorkOrder!",
      "metadata": { "mfgx": { "relation": { "deletionReferenceBehavior": "cascade", "fields": { "from": "workOrderId", "to": "id" } }, "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
    },
    {
      "name": "productId",
      "type": "ID!",
      "metadata": { "mfgx": { "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
    },
    {
      "name": "product",
      "type": "Product!",
      "metadata": { "mfgx": { "relation": { "deletionReferenceBehavior": "cascade", "fields": { "from": "productId", "to": "id" } }, "mutations": { "create": { "include": true }, "update": { "include": true } }, "queries": { "include": true }, "where": { "include": true } }, "unique": false }
    }
  ]
}
```

Junction models typically use `deletionReferenceBehavior: "cascade"` so that deleting either side removes the association.

---

## Pattern 5: Reference to System Models

Custom models can reference system models like `User`. The pattern is the same, but the type uses the system model name.

**Example: Alarm → User (acknowledgedBy)**

```json
{
  "name": "acknowledgedByUserId",
  "type": "ID",
  "description": "**`acknowledgedByUserId`** (`ID`)\nForeign key referencing the user who acknowledged the alarm.",
  "metadata": {
    "mfgx": {
      "mutations": { "create": { "include": true }, "update": { "include": true } },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
},
{
  "name": "acknowledgedByUser",
  "type": "User",
  "description": "**`acknowledgedByUser`** (`User`)\nRelated User object for acknowledgment tracking.",
  "metadata": {
    "mfgx": {
      "relation": {
        "deletionReferenceBehavior": "prevent",
        "fields": {
          "from": "acknowledgedByUserId",
          "to": "id"
        }
      },
      "mutations": { "create": { "include": true }, "update": { "include": true } },
      "queries": { "include": true },
      "where": { "include": true }
    },
    "unique": false
  }
}
```

**Note:** The `User` type references the system model — it is not defined in the custom package. The foreign key field is optional (`ID` not `ID!`) because the user reference may not always be set.

---

## deletionReferenceBehavior Decision Guide

| Behavior | When to Use | Example |
|----------|-------------|---------|
| `"prevent"` | The referenced record should not be deletable while references exist. **Default choice.** | Alarm → AlarmState (can't delete a state that alarms are using) |
| `"cascade"` | Deleting the parent should also delete this record. | Junction model records, child records that are meaningless without parent |
| `"setNull"` | Deleting the referenced record should clear the reference but keep this record. | ProductionLog → User (operator). If user is removed, log stays but operator becomes null. |

---

## Naming Conventions for Relationship Fields

| Pattern | Foreign Key Field | Relation Field |
|---------|-------------------|----------------|
| Standard | `{relatedModel}Id` | `{relatedModel}` |
| Named role | `{role}{RelatedModel}Id` | `{role}{RelatedModel}` |
| Collection | — | `{relatedModels}` (plural) |

**Examples:**
| Relationship | FK Field | Relation Field |
|-------------|----------|----------------|
| Alarm → AlarmState | `alarmStateId` | `alarmState` |
| Alarm → User (acknowledged by) | `acknowledgedByUserId` | `acknowledgedByUser` |
| Alarm → DataPoint | `dataPointId` | `dataPoint` |
| ProductionLog → User (operator) | `operatorId` | `operator` |
| Line → Cells (collection) | — | `cells` |

**Rules:**
- Foreign key fields use camelCase with `Id` suffix
- Relation fields use camelCase matching the related model name
- When a model has multiple references to the same type, prefix with the role (e.g., `acknowledgedByUserId`, `clearedByUserId`)
- Collection fields use plural form of the related model name
