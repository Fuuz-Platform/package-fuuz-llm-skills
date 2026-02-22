# UNS Publishing Patterns

## Topic Structure

UNS topics follow the ISA-95 hierarchy:

```
fuuz/{site}/{area}/{line}/{cell}/{equipment}/{datatype}
```

### Building Topic Paths with JSONata

```jsonata
$join([
  "fuuz",
  $site.code,
  $area.code,
  $line.code,
  $cell.code ? $cell.code : "nocell",
  $asset.code,
  $dp.code
], "/")
```

**Key:** Use conditional for optional hierarchy levels: `$cell.code ? $cell.code : "nocell"`

---

## Pattern: Publish Alarm State Changes to UNS

### GraphQL Query — DataPoint with Hierarchy

Query the DataPoint and traverse UP the hierarchy to build the topic path:

```graphql
query DataPointHierarchy($where: DataPointWhereInput) {
  dataPoint(where: $where, first: 1) {
    edges {
      node {
        id code displayName setPoint lowerLimit upperLimit deadband
        asset {
          id code
          cell {
            id code
            line {
              id code
              area {
                id code
                site { id code }
              }
            }
          }
        }
        unit { id code }
      }
    }
  }
}
```

### JSONata Transform — Alarm to UNS Message

```jsonata
(
  $dp := $.dataPoint.edges[0].node;
  $asset := $dp.asset;
  $cell := $asset.cell;
  $line := $cell.line;
  $area := $line.area;
  $site := $area.site;
  $alarmBefore := $state.context.message.value.before;
  $alarmAfter := $state.context.message.value.after;

  [
    {
      "topic": $join([
        "fuuz",
        $site.code,
        $area.code,
        $line.code,
        $cell.code,
        $asset.code,
        $dp.code,
        "alarm"
      ], "/"),
      "payload": $jsonStringify({
        "elementId": $alarmAfter.id,
        "displayName": $alarmAfter.code,
        "typeId": "alarm",
        "parentId": $alarmAfter.dataPointId,
        "hasChildren": false,
        "namespaceURI": "fuuz://alarm/" & $alarmAfter.id,
        "value": {
          "before": {
            "alarmStateId": $alarmBefore.alarmStateId,
            "limitType": $alarmBefore.limitType,
            "limitValue": $alarmBefore.limitValue,
            "triggerValue": $alarmBefore.triggerValue,
            "deviation": $alarmBefore.deviation,
            "triggeredAt": $alarmBefore.triggeredAt,
            "acknowledgedAt": $alarmBefore.acknowledgedAt,
            "clearedAt": $alarmBefore.clearedAt,
            "notes": $alarmBefore.notes,
            "updatedAt": $alarmBefore.updatedAt
          },
          "after": {
            "alarmStateId": $alarmAfter.alarmStateId,
            "limitType": $alarmAfter.limitType,
            "limitValue": $alarmAfter.limitValue,
            "triggerValue": $alarmAfter.triggerValue,
            "deviation": $alarmAfter.deviation,
            "triggeredAt": $alarmAfter.triggeredAt,
            "acknowledgedAt": $alarmAfter.acknowledgedAt,
            "clearedAt": $alarmAfter.clearedAt,
            "notes": $alarmAfter.notes,
            "updatedAt": $alarmAfter.updatedAt
          },
          "dataPoint": {
            "id": $dp.id,
            "code": $dp.code,
            "displayName": $dp.displayName,
            "setPoint": $dp.setPoint,
            "lowerLimit": $dp.lowerLimit,
            "upperLimit": $dp.upperLimit,
            "deadband": $dp.deadband
          },
          "asset": {
            "id": $asset.id,
            "code": $asset.code
          }
        },
        "timestamp": $moment(),
        "quality": "Good"
      }),
      "encoding": "UTF8"
    }
  ]
)
```

---

## Pattern: Publish Workcenter State Changes to UNS

### GraphQL Query — Workcenter Hierarchy

```graphql
query WorkcenterHierarchy($workcenterId: ID!) {
  workcenter(where: { id: { _eq: $workcenterId } }, first: 1) {
    edges {
      node {
        id code name description
        cell {
          id code name
          line {
            id code name
            area {
              id code name
              site { id code name }
            }
          }
        }
        line {
          id code name
          area {
            id code name
            site { id code name }
          }
        }
      }
    }
  }
}
```

**Note:** Workcenter can link to either `cell` or `line` directly. Query both and resolve:

```jsonata
$line := $wc.line ? $wc.line : $cell.line;
```

### JSONata Transform — Workcenter State to UNS

```jsonata
(
  $wc := $.workcenter.edges[0].node;
  $cell := $wc.cell;
  $line := $wc.line ? $wc.line : $cell.line;
  $area := $line.area;
  $site := $area.site;

  $historyBefore := $state.context.message.value.before;
  $historyAfter := $state.context.message.value.after;

  [
    {
      "topic": $join([
        "fuuz",
        $site.code,
        $area.code,
        $line.code,
        $cell.code ? $cell.code : "nocell",
        $wc.code,
        "state"
      ], "/"),
      "payload": $jsonStringify({
        "elementId": $wc.id,
        "displayName": $wc.code,
        "typeId": "workcenterState",
        "parentId": $cell.id ? $cell.id : $line.id,
        "hasChildren": false,
        "namespaceURI": "fuuz://workcenter/" & $wc.id,
        "value": {
          "stateBefore": {
            "modeId": $historyBefore.modeId,
            "stateId": $historyBefore.stateId,
            "eventId": $historyBefore.eventId,
            "occurAt": $historyBefore.occurAt,
            "notes": $historyBefore.notes
          },
          "stateAfter": {
            "modeId": $historyAfter.modeId,
            "stateId": $historyAfter.stateId,
            "eventId": $historyAfter.eventId,
            "occurAt": $historyAfter.occurAt,
            "endAt": $historyAfter.endAt,
            "duration": $historyAfter.duration,
            "runTime": $historyAfter.runTime,
            "plannedDowntime": $historyAfter.plannedDowntime,
            "unplannedDowntime": $historyAfter.unplannedDowntime,
            "notes": $historyAfter.notes
          },
          "workcenter": {
            "id": $wc.id,
            "code": $wc.code,
            "name": $wc.name
          }
        },
        "timestamp": $moment(),
        "quality": "Good"
      }),
      "encoding": "UTF8"
    }
  ]
)
```

---

## UNS Message Standard Fields

Every UNS message should include:

| Field | Type | Description |
|---|---|---|
| `elementId` | string | ID of the publishing entity |
| `displayName` | string | Human-readable name/code |
| `typeId` | string | Message type (alarm, workcenterState, telemetry) |
| `parentId` | string | Parent entity ID |
| `hasChildren` | boolean | Whether this entity has child entities |
| `namespaceURI` | string | Unique namespace URI |
| `value` | object | Payload data (before/after for state changes, value for telemetry) |
| `timestamp` | string | ISO 8601 timestamp |
| `quality` | string | Data quality indicator ("Good", "Bad", "Uncertain") |

## Key Implementation Notes

1. **Use `$jsonStringify()`** to serialize the payload when the publish node expects a string
2. **Use `$join()` with `"/"` separator** to build ISA-95 topic paths
3. **Always wrap in array `[{...}]`** — publish nodes expect an array of messages
4. **Set `"encoding": "UTF8"`** on all messages
5. **Traverse hierarchy upward** (child → parent) via relation fields, not downward via where clauses
