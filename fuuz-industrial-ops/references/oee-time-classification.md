# OEE & ISO 22400 Time Classification

## Mode Flags and Time Buckets

| Mode | countAsProductionTime | countAsDowntime | countAsPlannedDowntime | Time Bucket |
|---|---|---|---|---|
| Production | true | false | false | `runTime` |
| Disabled/Down | false | true | false | `unplannedDowntime` |
| Planned Maintenance | false | true | true | `plannedDowntime` |
| No Schedule | false | false | false | excluded |
| Engineering | false | false | false | excluded |

## JavaScript — Time Classification Function

```javascript
function calculateTimeClassification(durationMs, mode) {
  var runTime = 0;
  var plannedDowntime = 0;
  var unplannedDowntime = 0;

  if (!mode) {
    unplannedDowntime = durationMs;
  } else if (mode.countAsProductionTime) {
    runTime = durationMs;
  } else if (mode.countAsDowntime) {
    if (mode.countAsPlannedDowntime) {
      plannedDowntime = durationMs;
    } else {
      unplannedDowntime = durationMs;
    }
  }
  // else: excluded from OEE calculation (No Schedule, Engineering)

  return {
    runTime: runTime,
    plannedDowntime: plannedDowntime,
    unplannedDowntime: unplannedDowntime
  };
}
```

## OEE Formulas

### Availability

```
Available Time = runTime + unplannedDowntime
Availability = runTime / Available Time
```

**Note:** Planned downtime and excluded time are NOT included in available time per ISO 22400.

### Performance

```
Ideal Output = runTime / idealCycleTime
Performance = actualOutput / Ideal Output
```

Or equivalently:
```
Performance = (idealCycleTime × totalParts) / runTimeMs
```

### Quality

```
Quality = goodParts / totalParts
```

Where `totalParts = goodParts + scrapParts + reworkParts`

### OEE

```
OEE = Availability × Performance × Quality
```

## JavaScript — OEE Calculation

```javascript
'use strict';

function calculateOee(runTimeMs, unplannedDowntimeMs, idealCycleTimeMs, goodQty, scrapQty) {
  var totalParts = goodQty + scrapQty;
  var availableTime = runTimeMs + unplannedDowntimeMs;

  // Availability
  var availability = availableTime > 0
    ? runTimeMs / availableTime
    : 0;

  // Performance
  var performance = runTimeMs > 0 && idealCycleTimeMs > 0
    ? (idealCycleTimeMs * totalParts) / runTimeMs
    : 0;

  // Quality
  var quality = totalParts > 0
    ? goodQty / totalParts
    : 1; // No parts = 100% quality (no defects)

  // Cap performance at 1.0 (>100% means cycle time is faster than ideal)
  if (performance > 1.0) {
    performance = 1.0;
  }

  var oee = availability * performance * quality;

  return {
    availability: Math.round(availability * 10000) / 10000,
    performance: Math.round(performance * 10000) / 10000,
    quality: Math.round(quality * 10000) / 10000,
    oee: Math.round(oee * 10000) / 10000,
    runTimeMs: runTimeMs,
    unplannedDowntimeMs: unplannedDowntimeMs,
    availableTimeMs: availableTime,
    totalParts: totalParts,
    goodParts: goodQty,
    scrapParts: scrapQty
  };
}
```

## OEE Hourly Aggregation Flow Pattern

```
Schedule (hourly) →
Calculate Time Window (previous hour) →
Set Context →
Query Active Workcenters →
Broadcast →
  Query WorkcenterHistory for hour window →
  Query ProductionLogs for hour window →
  JavaScript: Calculate OEE →
  Upsert OeeHourly record →
Response
```

### Time Window Calculation (JSONata)

```jsonata
(
  $tz := $state.context.timezone;
  $hourEnd := $momentTz($moment(), $tz).startOf('hour');
  $hourStart := $moment($hourEnd).subtract(1, 'hour');
  {
    "periodAt": $hourStart.toISOString(),
    "periodStart": $hourStart.toISOString(),
    "periodEnd": $hourEnd.toISOString()
  }
)
```

## Duration Field Handling

Duration fields may return mixed formats. Always use a robust parser:

```javascript
function getDurationMs(values) {
  if (!values || values.length === 0) return 0;

  var totalMs = 0;
  for (var i = 0; i < values.length; i++) {
    var v = values[i];
    if (typeof v === 'object' && v !== null && v.milliseconds !== undefined) {
      totalMs += v.milliseconds || 0;
    }
    // Plain strings like "PT0M" represent zero — skip
  }
  return totalMs;
}
```

## Common OEE Data Model Fields

### OeeHourly

| Field | Type | Description |
|---|---|---|
| `workcenterId` | ID! | FK to Workcenter |
| `periodAt` | DateTime! | Hour start timestamp |
| `availability` | Float | Availability ratio (0-1) |
| `performance` | Float | Performance ratio (0-1) |
| `quality` | Float | Quality ratio (0-1) |
| `oee` | Float | OEE = A × P × Q |
| `runMinutes` | Float | Production run time in minutes |
| `downMinutes` | Float | Unplanned downtime in minutes |
| `plannedDownMinutes` | Float | Planned downtime in minutes |
| `unitsGood` | Int | Good parts produced |
| `unitsScrap` | Int | Scrap/rejected parts |
| `idealCycleTime` | Duration | Standard cycle time per unit |

### Trigger — Compute OEE on Update

```json
"triggers": {
  "update": "$after ~> | $ | { \"oee\": $after.availability * $after.performance * $after.quality } |"
}
```
