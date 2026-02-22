# Workcenter State Management

## WorkcenterHistory Schema Reference

```json
{
  "id": "String (CUID)",
  "code": "String (auto-generated: WCH-XXXXX)",
  "workcenterId": "ID! (FK to Workcenter)",
  "modeId": "ID! (FK to Mode)",
  "stateId": "ID! (FK to State)",
  "eventId": "ID (FK to Event, nullable)",
  "scheduleId": "ID (FK to Schedule, nullable)",
  "occurAt": "DateTime! (start of state)",
  "endAt": "DateTime (end of state, null if current)",
  "duration": "Int (milliseconds)",
  "runTime": "Int (ms classified as production time)",
  "plannedDowntime": "Int (ms classified as planned downtime)",
  "unplannedDowntime": "Int (ms classified as unplanned downtime)",
  "notes": "String (operator notes)"
}
```

## State Transition Processing Flow

```
DataChanges (WorkcenterHistory Create/Update) →
Query Workcenter + Hierarchy →
Query Mode Flags →
JavaScript: Calculate Time Classification →
Update WorkcenterHistory with time buckets →
Publish State Change to UNS
```

## Mode/State/Event Lookup

Modes, States, and Events are **setup data** that define the vocabulary for workcenter tracking:

### Modes (HOW the equipment is being used)

| Mode | countAsProductionTime | countAsDowntime | countAsPlannedDowntime |
|---|---|---|---|
| Production | true | false | false |
| Disabled | false | true | false |
| Planned Maintenance | false | true | true |
| No Schedule | false | false | false |
| Engineering | false | false | false |

### States (WHAT is happening)

| State | Description |
|---|---|
| Running | Equipment actively producing |
| Idle | Equipment powered but not producing |
| Faulted | Equipment in error state |
| E-Stopped | Emergency stop activated |
| Blocked | Cannot produce — downstream issue |
| Starved | Cannot produce — upstream issue |
| Setup | Changeover or setup in progress |
| Cleaning | CIP/cleaning cycle |

### Events (WHY — specific reason)

Events are optional and provide granular reason codes for downtime:
- "Motor overload fault"
- "Waiting for material"
- "Scheduled maintenance — monthly PM"
- "Product changeover A→B"

## Equipment-Specific Operator Notes

Generate realistic downtime notes based on equipment type:

```javascript
var equipmentNotes = {
  'bioreactor': {
    'faulted': [
      'DO probe calibration drift detected - recalibrating',
      'pH control loop deviation - checking CO2 sparge valve',
      'Temperature excursion alarm - jacket valve stuck',
      'Agitation motor overcurrent fault',
      'Foam probe triggered - antifoam addition issue'
    ],
    'e-stopped': [
      'Emergency stop - contamination suspected',
      'E-stop activated during inoculation'
    ],
    'blocked': [
      'Waiting for QC release of media',
      'Hold for in-process sampling'
    ],
    'starved': [
      'Media tank empty - waiting for prep',
      'Glucose feed depleted'
    ]
  },
  'chromatography': {
    'faulted': [
      'Column pressure drop exceeded limit',
      'UV detector signal saturation',
      'Conductivity probe fault'
    ]
  },
  'machining': {
    'faulted': [
      'Spindle overtemperature alarm',
      'Tool breakage detected',
      'Coolant level low',
      'Axis servo fault'
    ],
    'blocked': [
      'Waiting for part inspection',
      'Downstream conveyor full'
    ],
    'starved': [
      'Material handling robot cycle timeout',
      'Bar feeder empty'
    ]
  }
};

function getEquipmentType(wcId) {
  var id = (wcId || '').toLowerCase();
  if (id.indexOf('sub') >= 0 || id.indexOf('bioreactor') >= 0) return 'bioreactor';
  if (id.indexOf('chr') >= 0) return 'chromatography';
  if (id.indexOf('tff') >= 0) return 'tff';
  if (id.indexOf('vmc') >= 0 || id.indexOf('hmc') >= 0) return 'machining';
  return 'machining'; // Default fallback
}

function generateDowntimeNote(wc, mode, state) {
  // Only for unplanned downtime
  if (!mode || !mode.countAsDowntime || mode.countAsPlannedDowntime) {
    return null;
  }

  var equipType = getEquipmentType(wc.id);
  var stateId = state ? state.id : 'faulted';

  var notesForEquip = equipmentNotes[equipType] || equipmentNotes['machining'];
  var notesForState = notesForEquip[stateId] || notesForEquip['faulted'];

  if (!notesForState || notesForState.length === 0) return null;
  return notesForState[Math.floor(Math.random() * notesForState.length)];
}
```

## Query Pattern — Active Workcenter State

```graphql
query ActiveState($workcenterId: ID!) {
  workcenterHistory(
    where: {
      workcenterId: { _eq: $workcenterId }
      endAt: { _isNull: true }
    }
    first: 1
    orderBy: [{ occurAt: DESC }]
  ) {
    edges {
      node {
        id modeId stateId eventId occurAt
        mode { id code countAsProductionTime countAsDowntime countAsPlannedDowntime }
        state { id code }
        event { id code description }
      }
    }
  }
}
```

## Closing a State Period

When a new state begins, close the previous one:

```graphql
mutation CloseState($payload: [WorkcenterHistoryUpdatePayloadInput!]!) {
  updateWorkcenterHistory(payload: $payload) {
    id endAt duration runTime plannedDowntime unplannedDowntime
  }
}
```

**variablesTransform:**
```jsonata
(
  $now := $moment();
  $prev := $.workcenterHistory.edges[0].node;
  $dur := $moment($now).diff($moment($prev.occurAt));
  $mode := $prev.mode;
  $tc := $mode.countAsProductionTime ? {"runTime": $dur, "plannedDowntime": 0, "unplannedDowntime": 0} :
         $mode.countAsDowntime and $mode.countAsPlannedDowntime ? {"runTime": 0, "plannedDowntime": $dur, "unplannedDowntime": 0} :
         $mode.countAsDowntime ? {"runTime": 0, "plannedDowntime": 0, "unplannedDowntime": $dur} :
         {"runTime": 0, "plannedDowntime": 0, "unplannedDowntime": 0};
  {
    "payload": [{
      "where": { "id": $prev.id },
      "update": {
        "endAt": $now,
        "duration": $dur,
        "runTime": $tc.runTime,
        "plannedDowntime": $tc.plannedDowntime,
        "unplannedDowntime": $tc.unplannedDowntime
      }
    }]
  }
)
```
