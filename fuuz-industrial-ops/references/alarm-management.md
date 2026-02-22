# Alarm Management Patterns

## Alarm Evaluation Flow

```
DataChanges (TelemetryRaw Create) →
Query DataPoint + Active Alarms →
JavaScript: Evaluate Limits with Deadband →
Guard: Skip if no alarm condition →
Mutate: Create Alarm Record →
Optional: Publish to UNS
```

## GraphQL Query — DataPoint with Active Alarms

```graphql
query DataPoint($where: DataPointWhereInput, $dataPointId: String) {
  dataPoint(where: $where, first: 1) {
    edges {
      node {
        id code frequency precision deadband
        lowerLimit lowerWarning scaleFactor setPoint tolerance
        upperLimit upperWarning alarmEnabled enabled historize
        isCritical isOnChange alarmPriority category dataType
        description displayName name
        asset { id code }
        unit { id code }
      }
    }
  }

  activeAlarms: alarm(
    where: {
      dataPointId: { _eq: $dataPointId }
      clearedAt: { _isNull: true }
    }
    orderBy: [{ field: triggeredAt, direction: desc }]
    first: 10
  ) {
    edges {
      node {
        id code alarmStateId
        alarmState { id code }
        limitType limitValue triggerValue
        triggeredAt acknowledgedAt
      }
    }
  }
}
```

**Key Points:**
- Use `$dataPointId: String` (not `ID!`) for alarm where clause
- `clearedAt: { _isNull: true }` returns active/acknowledged but uncleared alarms
- Order by `triggeredAt desc` to get most recent alarm first

## JavaScript Transform — Alarm Evaluation with Deadband

```javascript
'use strict';

// Inputs
var telemetry = $state.context.rawData.value.after;
var dpEdges = $.dataPoint.edges;
var activeAlarmEdges = $.activeAlarms ? $.activeAlarms.edges : [];

// 1. Guard: DataPoint exists
if (!dpEdges || dpEdges.length === 0) {
  return {
    skip: true,
    reason: 'DataPoint ' + telemetry.dataPointId + ' not found'
  };
}
var dpData = dpEdges[0].node;

// 2. Guard: Alarms enabled
if (!dpData.alarmEnabled) {
  return {
    skip: true,
    reason: 'Alarms disabled for ' + dpData.code
  };
}

// 3. Extract values
var value = telemetry.value;
var deadband = dpData.deadband || 0;
var activeAlarms = [];
for (var i = 0; i < activeAlarmEdges.length; i++) {
  activeAlarms.push(activeAlarmEdges[i].node);
}

// 4. Deadband check against active alarms
if (activeAlarms.length > 0) {
  var latestAlarm = activeAlarms[0];
  var previousValue = latestAlarm.triggerValue;
  var valueDelta = Math.abs(value - previousValue);

  if (valueDelta <= deadband) {
    return {
      skip: true,
      reason: 'Within deadband of active alarm ' + latestAlarm.code,
      activeAlarmId: latestAlarm.id,
      delta: valueDelta
    };
  }
}

// 5. Evaluate limit conditions (priority order)
var limitType = null;
var limitValue = null;
var deviation = null;

// Priority 1: Critical limits
if (dpData.upperLimit != null && value > (dpData.upperLimit + deadband)) {
  limitType = 'HighLimit';
  limitValue = dpData.upperLimit;
  deviation = value - dpData.upperLimit;
}
else if (dpData.lowerLimit != null && value < (dpData.lowerLimit - deadband)) {
  limitType = 'LowLimit';
  limitValue = dpData.lowerLimit;
  deviation = dpData.lowerLimit - value;
}
// Priority 2: Warning limits
else if (dpData.upperWarning != null && value > (dpData.upperWarning + deadband)) {
  limitType = 'HighWarning';
  limitValue = dpData.upperWarning;
  deviation = value - dpData.upperWarning;
}
else if (dpData.lowerWarning != null && value < (dpData.lowerWarning - deadband)) {
  limitType = 'LowWarning';
  limitValue = dpData.lowerWarning;
  deviation = dpData.lowerWarning - value;
}
// Priority 3: SetPoint tolerance
else if (dpData.setPoint != null && dpData.tolerance != null) {
  var spDeviation = Math.abs(value - dpData.setPoint);
  if (spDeviation > (dpData.tolerance + deadband)) {
    limitType = 'Deviation';
    limitValue = dpData.setPoint;
    deviation = spDeviation;
  }
}

// 6. No violation
if (!limitType) {
  return {
    skip: true,
    reason: 'Value ' + value + ' within all limits for ' + dpData.code
  };
}

// 7. Build alarm payload
return {
  dataPointId: dpData.id,
  limitType: limitType,
  limitValue: limitValue,
  triggerValue: value,
  deviation: deviation,
  triggeredAt: telemetry.recordedAt || new Date().toISOString(),
  alarmStateId: 'ACTIVE',
  notes: limitType + ' alarm triggered for ' + dpData.displayName + ' (' + dpData.code + ')',
  customFields: {
    assetCode: dpData.asset ? dpData.asset.code : null,
    assetId: dpData.asset ? dpData.asset.id : null,
    unitCode: dpData.unit ? dpData.unit.code : null,
    dataPointCode: dpData.code,
    dataPointName: dpData.displayName,
    category: dpData.category,
    isCritical: dpData.isCritical,
    alarmPriority: dpData.alarmPriority
  }
};
```

## Deadband Logic Explained

```
previousValue = 100, deadband = 5

Value 103 → delta 3 → within deadband → SKIP
Value 97  → delta 3 → within deadband → SKIP
Value 106 → delta 6 → exceeds deadband → CREATE ALARM
Value 94  → delta 6 → exceeds deadband → CREATE ALARM
```

**Key:** `Math.abs(value - previousValue)` handles both directions.

Deadband is ALSO applied to limit comparisons:
- `value > (upperLimit + deadband)` — not just `value > upperLimit`
- `value < (lowerLimit - deadband)` — not just `value < lowerLimit`

---

## Alarm Schema Reference

```json
{
  "id": "String (CUID)",
  "code": "String (auto-generated: ALM-XXXXX)",
  "deviation": "Float",
  "limitValue": "Float",
  "triggerValue": "Float",
  "limitType": "String (HighLimit|LowLimit|HighWarning|LowWarning|Deviation)",
  "notes": "String",
  "triggeredAt": "DateTime",
  "acknowledgedAt": "DateTime (null if not acknowledged)",
  "clearedAt": "DateTime (null if active)",
  "alarmStateId": "String (ACTIVE|ACTIVE_ACK|CLEARED)",
  "dataPointId": "String (FK to DataPoint)"
}
```

## Alarm State Transitions

```
ACTIVE → (operator acknowledges) → ACTIVE_ACK → (condition clears) → CLEARED
ACTIVE → (condition clears before ack) → CLEARED
```

## Mutation — Create Alarm

```graphql
mutation CreateAlarm($payload: [AlarmCreatePayloadInput!]!) {
  createAlarm(payload: $payload) {
    id code alarmStateId limitType triggerValue triggeredAt
  }
}
```

**variablesTransform:**
```jsonata
$.skip ? {} : {
  "payload": [{
    "create": {
      "dataPointId": $.dataPointId,
      "limitType": $.limitType,
      "limitValue": $.limitValue,
      "triggerValue": $.triggerValue,
      "deviation": $.deviation,
      "triggeredAt": $.triggeredAt,
      "alarmStateId": $.alarmStateId,
      "notes": $.notes
    }
  }]
}
```
