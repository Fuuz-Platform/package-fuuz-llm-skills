# Fuuz Model Definition Templates

Complete, production-ready `modelDefinition` templates for common industrial data model patterns. Each template is a valid JSON block that can be placed directly into `package-data.json > dataModels[].version.modelDefinition`. Use these as starting points when designing new models.

**IMPORTANT:** All templates use `{moduleId}` and `{moduleGroupId}` as placeholders. Replace these with your actual Module and ModuleGroup IDs. See `references/prerequisites.md` for how to define these.

---

## Template 1 -- Setup Model: Mode

A simple setup/lookup model representing workcenter operating modes (e.g., "production", "maintenance", "idle"). Setup models use a create trigger to set the record `id` from the `code` field, giving each record a meaningful, human-readable ID instead of a system-generated CUID. This is the standard pattern for small, stable reference tables where the set of values is known at design time.

```json
{
  "name": "Mode",
  "metadata": {
    "mfgx": {
      "module": {
        "id": "{moduleId}",
        "group": {
          "id": "{moduleGroupId}"
        }
      },
      "exposed": true,
      "mutable": true,
      "mutations": {
        "create": true,
        "update": true,
        "del": true
      },
      "dataChangeCapture": {
        "exposed": false,
        "retentionDays": 120
      },
      "customFields": {
        "exposed": true
      },
      "labelField": "code",
      "editor": {
        "autosort": true
      },
      "triggers": {
        "create": "$ ~> | $ | { \"id\": code } |"
      }
    },
    "mongo": {
      "collection": "mode"
    },
    "graphql": {
      "type": "Object"
    }
  },
  "kind": "Reference",
  "fields": [
    {
      "name": "id",
      "type": "ID!",
      "description": "**`id`** (`ID!`)\nPrimary identifier. Set automatically from the `code` field via create trigger.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": false
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "code",
      "type": "String!",
      "description": "**`code`** (`String!`)\nShort unique code for the operating mode (e.g., `production`, `maintenance`, `idle`).",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "name",
      "type": "String!",
      "description": "**`name`** (`String!`)\nHuman-readable display name for the operating mode.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "description",
      "type": "String",
      "description": "**`description`** (`String`)\nOptional explanation of when this mode applies.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": false
          }
        },
        "unique": false
      }
    },
    {
      "name": "sortOrder",
      "type": "Int",
      "description": "**`sortOrder`** (`Int`)\nControls display ordering in dropdowns and lists.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "countAsDowntime",
      "type": "Boolean!",
      "description": "**`countAsDowntime`** (`Boolean!`)\nWhether time spent in this mode counts toward downtime metrics.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "defaultValue": false
        },
        "unique": false
      }
    },
    {
      "name": "countAsProductionTime",
      "type": "Boolean!",
      "description": "**`countAsProductionTime`** (`Boolean!`)\nWhether time spent in this mode counts toward planned production time.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "defaultValue": false
        },
        "unique": false
      }
    }
  ],
  "description": "Operating modes for workcenters (e.g., production, maintenance, idle). Used to classify workcenter state for OEE and downtime calculations."
}
```

**Key Patterns Demonstrated:**
- Create trigger sets `id` from `code`: `$ ~> | $ | { "id": code } |`
- Meaningful string IDs instead of CUIDs (e.g., `"production"`, `"idle"`)
- `code` field is `unique: true` since it serves as the natural key
- Boolean fields with `defaultValue: false` for metric classification flags
- `dataChangeCapture.exposed: false` -- setup models rarely need change tracking
- `sortOrder` field for UI ordering control
- No sequences needed -- codes are manually assigned

---

## Template 2 -- Master Model: Asset

A master model representing a physical asset (machine, device, sensor) in the plant hierarchy. Master models are core business entities with sequence-backed codes, multiple optional relationships to organizational hierarchy models, and one-to-many collection fields. This template demonstrates the full relationship pattern including foreign key fields, relation fields, and a reverse collection.

```json
{
  "name": "Asset",
  "metadata": {
    "mfgx": {
      "module": {
        "id": "{moduleId}",
        "group": {
          "id": "{moduleGroupId}"
        }
      },
      "indices": [
        {
          "name": "code",
          "unique": true,
          "fields": ["code"]
        }
      ],
      "exposed": true,
      "mutable": true,
      "mutations": {
        "create": true,
        "update": true,
        "del": true
      },
      "dataChangeCapture": {
        "exposed": true,
        "retentionDays": 120
      },
      "customFields": {
        "exposed": true
      },
      "labelField": "code",
      "editor": {
        "autosort": true
      }
    },
    "mongo": {
      "collection": "asset"
    },
    "graphql": {
      "type": "Object"
    }
  },
  "kind": "Reference",
  "fields": [
    {
      "name": "id",
      "type": "ID!",
      "description": "**`id`** (`ID!`)\nPrimary unique identifier for the asset record.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": false
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "code",
      "type": "String!",
      "description": "**`code`** (`String!`)\nUnique asset code, auto-generated from the `assetCode` sequence (e.g., ASSET-1, ASSET-2).",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          },
          "sequence": {
            "id": "assetCode"
          }
        },
        "unique": false
      }
    },
    {
      "name": "name",
      "type": "String!",
      "description": "**`name`** (`String!`)\nHuman-readable display name for the asset.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "description",
      "type": "String",
      "description": "**`description`** (`String`)\nOptional detailed description of the asset.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": false
          }
        },
        "unique": false
      }
    },
    {
      "name": "siteId",
      "type": "ID",
      "description": "**`siteId`** (`ID`)\nForeign key referencing the Site where this asset is located.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "site",
      "type": "Site",
      "description": "**`site`** (`Site`)\nRelated Site object where this asset is located.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "siteId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "areaId",
      "type": "ID",
      "description": "**`areaId`** (`ID`)\nForeign key referencing the Area within the site.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "area",
      "type": "Area",
      "description": "**`area`** (`Area`)\nRelated Area object within the site hierarchy.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "areaId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "lineId",
      "type": "ID",
      "description": "**`lineId`** (`ID`)\nForeign key referencing the production Line.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "line",
      "type": "Line",
      "description": "**`line`** (`Line`)\nRelated production Line object.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "lineId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "cellId",
      "type": "ID",
      "description": "**`cellId`** (`ID`)\nForeign key referencing the manufacturing Cell.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "cell",
      "type": "Cell",
      "description": "**`cell`** (`Cell`)\nRelated manufacturing Cell object.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "cellId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "dataPoints",
      "type": "[DataPoint!]!",
      "description": "**`dataPoints`** (`[DataPoint!]!`)\nCollection of DataPoint records associated with this asset.",
      "metadata": {
        "mfgx": {
          "relation": {
            "fields": {
              "from": "id",
              "to": "assetId"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    }
  ],
  "description": "Physical assets (machines, devices, sensors) in the plant hierarchy. Each asset belongs to an organizational location and owns a collection of data points for telemetry collection."
}
```

**Key Patterns Demonstrated:**
- Sequence-backed `code` field via `"sequence": { "id": "assetCode" }`
- `code` has `unique: false` because the sequence manages uniqueness; a composite index on `code` enforces it at the database level
- Composite unique index: `{ "name": "code", "unique": true, "fields": ["code"] }`
- `dataChangeCapture.exposed: true` for master model change tracking
- Optional hierarchy relationships (`siteId`/`site`, `areaId`/`area`, `lineId`/`line`, `cellId`/`cell`) -- all nullable (`ID` not `ID!`)
- One-to-many collection field (`dataPoints`) with reverse relation `from: "id", to: "assetId"`
- Collection fields have no `deletionReferenceBehavior` -- that is defined on the child model's relation field
- Every foreign key field is paired with its corresponding relation field

---

## Template 3 -- Transactional Model: Alarm

A transactional model representing alarm events with relationships to both custom models (AlarmState, DataPoint) and system models (User). Transactional models record business events and are created by flows or user actions. This template demonstrates sequence-backed codes, mixed required and optional relationships, temporal fields, and raw telemetry data storage.

```json
{
  "name": "Alarm",
  "metadata": {
    "mfgx": {
      "module": {
        "id": "{moduleId}",
        "group": {
          "id": "{moduleGroupId}"
        }
      },
      "exposed": true,
      "mutable": true,
      "mutations": {
        "create": true,
        "update": true,
        "del": true
      },
      "dataChangeCapture": {
        "exposed": false,
        "retentionDays": 120
      },
      "customFields": {
        "exposed": true
      },
      "labelField": "code",
      "editor": {
        "autosort": true
      }
    },
    "mongo": {
      "collection": "alarm"
    },
    "graphql": {
      "type": "Object"
    }
  },
  "kind": "Reference",
  "fields": [
    {
      "name": "id",
      "type": "ID!",
      "description": "**`id`** (`ID!`)\nPrimary unique identifier for the alarm record.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": false
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "code",
      "type": "String!",
      "description": "**`code`** (`String!`)\nUnique alarm code, auto-generated from the `alarmCode` sequence (e.g., ALM-001, ALM-002).",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "sequence": {
            "id": "alarmCode"
          }
        },
        "unique": false
      }
    },
    {
      "name": "deviation",
      "type": "Float",
      "description": "**`deviation`** (`Float`)\nCalculated deviation between the trigger value and the limit value.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "limitValue",
      "type": "Float!",
      "description": "**`limitValue`** (`Float!`)\nThe alarm threshold value that was exceeded.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "triggerValue",
      "type": "Float!",
      "description": "**`triggerValue`** (`Float!`)\nThe actual sensor value that triggered the alarm.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "limitType",
      "type": "String!",
      "description": "**`limitType`** (`String!`)\nType of limit that was breached (e.g., `high`, `low`, `highHigh`, `lowLow`).",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "notes",
      "type": "String",
      "description": "**`notes`** (`String`)\nOperator notes or comments about the alarm event.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": false
          }
        },
        "unique": false
      }
    },
    {
      "name": "acknowledgedAt",
      "type": "DateTime",
      "description": "**`acknowledgedAt`** (`DateTime`)\nTimestamp when the alarm was acknowledged by an operator.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "clearedAt",
      "type": "DateTime",
      "description": "**`clearedAt`** (`DateTime`)\nTimestamp when the alarm condition cleared.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "triggeredAt",
      "type": "DateTime!",
      "description": "**`triggeredAt`** (`DateTime!`)\nTimestamp when the alarm was first triggered.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "telemetryRawData",
      "type": "JSON",
      "description": "**`telemetryRawData`** (`JSON`)\nSnapshot of the raw telemetry reading that triggered the alarm.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": false
          }
        },
        "unique": false
      }
    },
    {
      "name": "duration",
      "type": "Duration",
      "description": "**`duration`** (`Duration`)\nTotal duration of the alarm from trigger to clear.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "alarmStateId",
      "type": "ID!",
      "description": "**`alarmStateId`** (`ID!`)\nForeign key referencing the current alarm state (e.g., active, acknowledged, cleared).",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "alarmState",
      "type": "AlarmState!",
      "description": "**`alarmState`** (`AlarmState!`)\nRelated AlarmState object for workflow state tracking.",
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
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "acknowledgedByUserId",
      "type": "ID",
      "description": "**`acknowledgedByUserId`** (`ID`)\nForeign key referencing the User who acknowledged the alarm.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "acknowledgedByUser",
      "type": "User",
      "description": "**`acknowledgedByUser`** (`User`)\nRelated User object for the operator who acknowledged the alarm.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "acknowledgedByUserId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "dataPointId",
      "type": "ID",
      "description": "**`dataPointId`** (`ID`)\nForeign key referencing the DataPoint that generated this alarm.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "dataPoint",
      "type": "DataPoint",
      "description": "**`dataPoint`** (`DataPoint`)\nRelated DataPoint object that generated this alarm.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "dataPointId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    }
  ],
  "description": "Alarm events triggered when telemetry values exceed configured thresholds. Tracks the full alarm lifecycle from trigger through acknowledgment to clear."
}
```

**Key Patterns Demonstrated:**
- Sequence-backed code: `"sequence": { "id": "alarmCode" }`
- Required relationship (`alarmStateId: ID!` / `alarmState: AlarmState!`) -- every alarm must have a state
- Optional relationships (`dataPointId: ID` / `dataPoint: DataPoint`) -- nullable FK and nullable relation type
- System model reference (`acknowledgedByUserId: ID` / `acknowledgedByUser: User`) -- `User` is a platform model, not defined in the package
- Named-role relationship: `acknowledgedByUserId` / `acknowledgedByUser` uses a role prefix because the model could reference User in multiple ways
- `dataChangeCapture.include: true` on key foreign keys (`alarmStateId`, `dataPointId`) for reactive flow triggers
- `JSON` field (`telemetryRawData`) with `where.include: false` -- JSON blobs should not be filterable
- `notes` and `description` fields have `where.include: false` -- large text fields are not useful as filters
- Temporal lifecycle fields: `triggeredAt` (required), `acknowledgedAt` and `clearedAt` (optional, set later)

---

## Template 4 -- Transactional Model with Trigger: OeeHourly

A transactional model that stores hourly OEE (Overall Equipment Effectiveness) calculations per workcenter. This template demonstrates an update trigger that auto-calculates a derived field whenever its source fields change. OEE is the product of availability, performance, and quality -- the trigger ensures this value is always consistent without requiring the calling flow to compute it.

```json
{
  "name": "OeeHourly",
  "metadata": {
    "mfgx": {
      "module": {
        "id": "{moduleId}",
        "group": {
          "id": "{moduleGroupId}"
        }
      },
      "exposed": true,
      "mutable": true,
      "mutations": {
        "create": true,
        "update": true,
        "del": true
      },
      "dataChangeCapture": {
        "exposed": false,
        "retentionDays": 120
      },
      "customFields": {
        "exposed": true
      },
      "labelField": "id",
      "editor": {
        "autosort": true
      },
      "triggers": {
        "update": "$after~>| $ | { \"oee\": $after.availability * $after.performance * $after.quality }|"
      }
    },
    "mongo": {
      "collection": "oeeHourly"
    },
    "graphql": {
      "type": "Object"
    }
  },
  "kind": "Reference",
  "fields": [
    {
      "name": "id",
      "type": "ID!",
      "description": "**`id`** (`ID!`)\nPrimary unique identifier for the OEE hourly record.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": false
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "date",
      "type": "Date!",
      "description": "**`date`** (`Date!`)\nThe calendar date for this OEE record.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "hour",
      "type": "Int!",
      "description": "**`hour`** (`Int!`)\nThe hour of the day (0-23) for this OEE record.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "availability",
      "type": "Float",
      "description": "**`availability`** (`Float`)\nAvailability component (0.0 to 1.0). Ratio of actual production time to planned production time.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "performance",
      "type": "Float",
      "description": "**`performance`** (`Float`)\nPerformance component (0.0 to 1.0). Ratio of actual throughput to ideal throughput.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "quality",
      "type": "Float",
      "description": "**`quality`** (`Float`)\nQuality component (0.0 to 1.0). Ratio of good units to total units produced.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "oee",
      "type": "Float",
      "description": "**`oee`** (`Float`)\nOverall Equipment Effectiveness (0.0 to 1.0). Auto-calculated by update trigger as availability * performance * quality.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "plannedProductionTime",
      "type": "Duration",
      "description": "**`plannedProductionTime`** (`Duration`)\nTotal scheduled production time for this hour.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "actualProductionTime",
      "type": "Duration",
      "description": "**`actualProductionTime`** (`Duration`)\nActual time the workcenter was producing during this hour.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "idealCycleTime",
      "type": "Duration",
      "description": "**`idealCycleTime`** (`Duration`)\nIdeal cycle time per unit for performance calculation.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "totalCount",
      "type": "Int",
      "description": "**`totalCount`** (`Int`)\nTotal units produced during this hour (good + reject).",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "goodCount",
      "type": "Int",
      "description": "**`goodCount`** (`Int`)\nNumber of good (non-defective) units produced during this hour.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "rejectCount",
      "type": "Int",
      "description": "**`rejectCount`** (`Int`)\nNumber of defective units produced during this hour.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "workcenterId",
      "type": "ID!",
      "description": "**`workcenterId`** (`ID!`)\nForeign key referencing the Workcenter this OEE record belongs to.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "workcenter",
      "type": "Workcenter!",
      "description": "**`workcenter`** (`Workcenter!`)\nRelated Workcenter object this OEE record belongs to.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "workcenterId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    }
  ],
  "description": "Hourly OEE (Overall Equipment Effectiveness) records per workcenter. Availability, performance, and quality are updated by flows; the OEE value is auto-calculated by an update trigger."
}
```

**Key Patterns Demonstrated:**
- Update trigger auto-calculates `oee`: `$after~>| $ | { "oee": $after.availability * $after.performance * $after.quality }|`
- The `$after` variable in update triggers references the document after the update is applied
- `labelField: "id"` -- no `code` field, so the record ID is used as the display label
- `Date` type for calendar date and `Int` for hour -- avoids timezone issues in time-bucketed aggregation
- `Duration` type fields for time measurements (planned, actual, ideal cycle)
- `dataChangeCapture.include: true` on OEE components and the workcenter FK for reactive dashboards
- No sequence -- OEE records are typically created by flows with generated IDs
- Trigger-computed `oee` field is still `create.include: true` and `update.include: true` so flows can optionally set it directly; the trigger overrides on subsequent updates

---

## Template 5 -- High-Volume Time-Series Model: TelemetryRaw

A minimal, high-volume transactional model for raw telemetry readings with a TTL index for automatic data expiry. Time-series models prioritize write throughput and storage efficiency over queryability. The TTL index automatically purges old records, preventing unbounded collection growth. This is the standard pattern for any data that streams in at high frequency and only needs short-term retention.

```json
{
  "name": "TelemetryRaw",
  "metadata": {
    "mfgx": {
      "module": {
        "id": "{moduleId}",
        "group": {
          "id": "{moduleGroupId}"
        }
      },
      "indices": [
        {
          "name": "TTLIndex",
          "unique": false,
          "ttl": 14400,
          "partialFilterPredicate": {},
          "fields": ["createdAt"]
        }
      ],
      "exposed": true,
      "mutable": true,
      "mutations": {
        "create": true,
        "update": true,
        "del": true
      },
      "dataChangeCapture": {
        "exposed": false,
        "retentionDays": 120
      },
      "customFields": {
        "exposed": false
      },
      "labelField": "id",
      "editor": {
        "autosort": true
      }
    },
    "mongo": {
      "collection": "telemetryRaw"
    },
    "graphql": {
      "type": "Object"
    }
  },
  "kind": "Reference",
  "fields": [
    {
      "name": "id",
      "type": "ID!",
      "description": "**`id`** (`ID!`)\nPrimary unique identifier for the telemetry reading.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": false
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "value",
      "type": "Float",
      "description": "**`value`** (`Float`)\nThe raw telemetry reading value from the sensor.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "dataPointId",
      "type": "ID!",
      "description": "**`dataPointId`** (`ID!`)\nForeign key referencing the DataPoint (sensor/tag) that produced this reading.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "dataPoint",
      "type": "DataPoint!",
      "description": "**`dataPoint`** (`DataPoint!`)\nRelated DataPoint object (sensor/tag configuration) that produced this reading.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "dataPointId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    }
  ],
  "description": "Raw telemetry readings from sensors and data points. High-volume, short-lived records automatically purged after 4 hours via TTL index. Aggregated into TelemetryHourly/TelemetryDaily by scheduled flows before expiry."
}
```

**Key Patterns Demonstrated:**
- TTL index for automatic expiry: `{ "name": "TTLIndex", "ttl": 14400, "fields": ["createdAt"] }`
- `ttl: 14400` = 4 hours in seconds (14400 / 3600 = 4 hours)
- TTL indexes target the `createdAt` field -- this is a system-managed DateTime field added automatically by the platform
- `partialFilterPredicate: {}` applies the TTL to all records; use a MongoDB filter object to selectively expire
- `dataChangeCapture.exposed: false` -- high-volume models should NOT enable change capture (performance impact)
- `customFields.exposed: false` -- time-series models should not allow runtime field extensions
- `labelField: "id"` -- no code or name field on minimal time-series models
- Minimal field set -- only the reading value and a relationship to its source data point
- No sequences or triggers -- records are created by ingestion flows at high throughput

---

## Template 6 -- Master Model: Workcenter

A master model representing a production workcenter -- the central entity in manufacturing execution. Workcenters aggregate state from multiple sources: current operating mode, production state, active product, work order, and schedule group. This template demonstrates a master model with many optional relationships, data change capture enabled for reactive state tracking, and a default boolean value.

```json
{
  "name": "Workcenter",
  "metadata": {
    "mfgx": {
      "module": {
        "id": "{moduleId}",
        "group": {
          "id": "{moduleGroupId}"
        }
      },
      "exposed": true,
      "mutable": true,
      "mutations": {
        "create": true,
        "update": true,
        "del": true
      },
      "dataChangeCapture": {
        "exposed": true,
        "retentionDays": 120
      },
      "customFields": {
        "exposed": true
      },
      "labelField": "code",
      "editor": {
        "autosort": true
      }
    },
    "mongo": {
      "collection": "workcenter"
    },
    "graphql": {
      "type": "Object"
    }
  },
  "kind": "Reference",
  "fields": [
    {
      "name": "id",
      "type": "ID!",
      "description": "**`id`** (`ID!`)\nPrimary unique identifier for the workcenter.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": false
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "code",
      "type": "String!",
      "description": "**`code`** (`String!`)\nUnique human-readable code identifying the workcenter.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": true
      }
    },
    {
      "name": "name",
      "type": "String!",
      "description": "**`name`** (`String!`)\nHuman-readable display name for the workcenter.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "description",
      "type": "String",
      "description": "**`description`** (`String`)\nOptional detailed description of the workcenter's purpose or configuration.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": false
          }
        },
        "unique": false
      }
    },
    {
      "name": "active",
      "type": "Boolean!",
      "description": "**`active`** (`Boolean!`)\nWhether the workcenter is currently active. Inactive workcenters are excluded from scheduling and dashboards.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          },
          "defaultValue": true
        },
        "unique": false
      }
    },
    {
      "name": "siteId",
      "type": "ID!",
      "description": "**`siteId`** (`ID!`)\nForeign key referencing the Site where this workcenter is located. Required.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "site",
      "type": "Site!",
      "description": "**`site`** (`Site!`)\nRelated Site object where this workcenter is located.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "siteId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "areaId",
      "type": "ID",
      "description": "**`areaId`** (`ID`)\nForeign key referencing the Area within the site.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "area",
      "type": "Area",
      "description": "**`area`** (`Area`)\nRelated Area object within the site hierarchy.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "areaId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "lineId",
      "type": "ID",
      "description": "**`lineId`** (`ID`)\nForeign key referencing the production Line.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "line",
      "type": "Line",
      "description": "**`line`** (`Line`)\nRelated production Line object.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "lineId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "cellId",
      "type": "ID",
      "description": "**`cellId`** (`ID`)\nForeign key referencing the manufacturing Cell.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "cell",
      "type": "Cell",
      "description": "**`cell`** (`Cell`)\nRelated manufacturing Cell object.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "cellId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "modeId",
      "type": "ID",
      "description": "**`modeId`** (`ID`)\nForeign key referencing the current operating Mode.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "mode",
      "type": "Mode",
      "description": "**`mode`** (`Mode`)\nRelated Mode object representing the current operating mode.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "modeId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "stateId",
      "type": "ID",
      "description": "**`stateId`** (`ID`)\nForeign key referencing the current production State.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "state",
      "type": "State",
      "description": "**`state`** (`State`)\nRelated State object representing the current production state.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "stateId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "productId",
      "type": "ID",
      "description": "**`productId`** (`ID`)\nForeign key referencing the Product currently being produced.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "product",
      "type": "Product",
      "description": "**`product`** (`Product`)\nRelated Product object currently being produced on this workcenter.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "setNull",
            "fields": {
              "from": "productId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "workOrderId",
      "type": "ID",
      "description": "**`workOrderId`** (`ID`)\nForeign key referencing the active WorkOrder.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "workOrder",
      "type": "WorkOrder",
      "description": "**`workOrder`** (`WorkOrder`)\nRelated WorkOrder object currently active on this workcenter.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "setNull",
            "fields": {
              "from": "workOrderId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "scheduleGroupId",
      "type": "ID",
      "description": "**`scheduleGroupId`** (`ID`)\nForeign key referencing the ScheduleGroup for shift scheduling.",
      "metadata": {
        "mfgx": {
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          },
          "dataChangeCapture": {
            "include": true
          }
        },
        "unique": false
      }
    },
    {
      "name": "scheduleGroup",
      "type": "ScheduleGroup",
      "description": "**`scheduleGroup`** (`ScheduleGroup`)\nRelated ScheduleGroup object that defines shift schedules for this workcenter.",
      "metadata": {
        "mfgx": {
          "relation": {
            "deletionReferenceBehavior": "prevent",
            "fields": {
              "from": "scheduleGroupId",
              "to": "id"
            }
          },
          "mutations": {
            "create": {
              "include": true
            },
            "update": {
              "include": true
            }
          },
          "queries": {
            "include": true
          },
          "where": {
            "include": true
          }
        },
        "unique": false
      }
    }
  ],
  "description": "Production workcenter -- the central entity for manufacturing execution. Tracks current operating mode, production state, active product, work order, and schedule group. Changes to state fields trigger reactive flows for OEE calculation and event logging."
}
```

**Key Patterns Demonstrated:**
- `dataChangeCapture.exposed: true` at the model level -- enables `dataChanges` flow triggers for reactive state tracking
- `dataChangeCapture.include: true` on all state-tracking foreign keys (`modeId`, `stateId`, `productId`, `workOrderId`, `scheduleGroupId`) so flows can react to state changes
- `defaultValue: true` on `active` field -- new workcenters default to active
- Required site relationship (`siteId: ID!` / `site: Site!`) vs. optional hierarchy (`areaId: ID` / `area: Area`)
- Mixed `deletionReferenceBehavior` strategies:
  - `"prevent"` on structural references (site, area, line, cell, mode, state, scheduleGroup) -- cannot delete these while workcenters reference them
  - `"setNull"` on transient references (product, workOrder) -- if the product or work order is deleted, the workcenter's reference clears to null
- `code` field is `unique: true` without a sequence -- workcenter codes are manually assigned meaningful identifiers
- No `indices` array needed -- the `unique: true` on `code` creates an implicit unique index
