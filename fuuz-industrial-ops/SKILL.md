---
name: fuuz-industrial-ops
description: Industrial operations patterns for the Fuuz platform (fuuz.com). Use when requests mention "UNS", "Unified Namespace", "ISA-95", "ISA 95", "alarm management", "alarm evaluation", "deadband", "OEE", "Overall Equipment Effectiveness", "ISO 22400", "time classification", "workcenter state", "workcenter history", "mode", "state", "event", "MES", "Manufacturing Execution System", "WMS", "Warehouse Management", "ERP integration", "Plex integration", "NetSuite integration", "production tracking", "downtime tracking", "asset hierarchy", "site area line cell", or when building manufacturing/industrial IoT applications on Fuuz. Covers UNS publishing, alarm evaluation with deadband, OEE time classification, workcenter state management, and ERP integration patterns.
---

# Fuuz Industrial Operations Patterns

**Version 1.2** | Last Updated: 2026-02-21

Domain-specific patterns for building industrial manufacturing and IoT applications on the Fuuz platform. This skill provides implementation patterns for common industrial operations requirements.

## When to Use This Skill

Use **fuuz-industrial-ops** when you need to:
- Publish data to a Unified Namespace (UNS) with ISA-95 topic structures
- Implement alarm evaluation with deadband logic
- Calculate OEE using ISO 22400 time classification
- Manage workcenter state transitions and history
- Design equipment-specific downtime tracking
- Integrate with ERP systems (Plex, NetSuite, SAP, Epicor, Dynamics 365)
- Build ISA-95 asset hierarchies (Site → Area → Line → Cell → Asset → DataPoint)

**This is a domain patterns skill.** For building the actual data models, flows, or screens, use the corresponding builder skills (fuuz-schema, fuuz-flows, fuuz-screens).

---

## ISA-95 Asset Hierarchy

Fuuz implements the ISA-95 equipment hierarchy as nested reference models:

```
Enterprise
└── Site
    └── Area
        └── Line
            └── Cell
                └── Asset (Equipment)
                    └── DataPoint (Tag/Sensor)
```

**Key relationships:**
- Each level has `code`, `name`, and a FK to its parent
- `Workcenter` links to either `Cell` or `Line` (the production unit boundary)
- `DataPoint` represents an individual sensor, tag, or calculated value on an asset
- DataPoints have limit fields: `upperLimit`, `lowerLimit`, `upperWarning`, `lowerWarning`, `setPoint`, `tolerance`, `deadband`

---

## Unified Namespace (UNS)

### Topic Structure

UNS topics follow the ISA-95 hierarchy with `/` separators:

```
fuuz/{site}/{area}/{line}/{cell}/{equipment}/{datatype}
```

**Examples:**
```
fuuz/PLT01/MFG/LN01/CL01/SUB-250/temperature
fuuz/PLT01/MFG/LN01/CL01/SUB-250/alarm
fuuz/PLT01/MFG/LN01/CL01/WC01/state
```

**Optional levels** — When a hierarchy level doesn't exist, use a placeholder:
```jsonata
$cell.code ? $cell.code : "nocell"
```

### UNS Publishing JSONata Patterns

**Topic construction** using `$join()` array pattern:
```jsonata
$topic := $join([
  "fuuz",
  $site.code,
  $area.code,
  $line.code,
  $cell.code ? $cell.code : "nocell",
  $asset.code,
  "telemetry"
], "/");
```

**Payload structure** using `$jsonStringify()`:
```jsonata
$payload := $jsonStringify({
  "elementId": $dataPoint.id,
  "displayName": $dataPoint.name,
  "typeId": $dataPoint.typeId,
  "parentId": $dataPoint.assetId,
  "hasChildren": false,
  "namespaceURI": $topic,
  "value": {
    "before": $before.value,
    "after": $after.value
  },
  "timestamp": $now(),
  "quality": "GOOD"
});
```

**Workcenter hierarchy with optional cell level:**
```jsonata
$line := $wc.line ? $wc.line : $cell.line;
$cell := $wc.cell ? $wc.cell : null;
```
When a workcenter is linked directly to a line (no cell), fall back to the line's parent references.

→ See `references/uns-patterns.md` for complete UNS publishing patterns including alarm and workcenter state examples.

---

## Alarm Management

### Alarm Lifecycle

```
Value exceeds limit → Check active alarms → Apply deadband →
Create alarm (ACTIVE) → Operator acknowledges (ACTIVE_ACK) →
Condition clears (CLEARED)
```

### Alarm States

| State | Code | Description |
|---|---|---|
| Active | `ACTIVE` | Alarm triggered, not yet acknowledged |
| Active Acknowledged | `ACTIVE_ACK` | Alarm acknowledged by operator, still active |
| Cleared | `CLEARED` | Alarm condition no longer present |

### Limit Types (Priority Order)

1. **HighLimit** / **LowLimit** — Critical limits (alarms)
2. **HighWarning** / **LowWarning** — Warning limits
3. **Deviation** — SetPoint ± tolerance deviation

### Deadband Logic

Deadband prevents alarm chatter by requiring a minimum change from the previous trigger value before creating a new alarm:

- If `|currentValue - previousTriggerValue| ≤ deadband` → skip (within deadband)
- If `|currentValue - previousTriggerValue| > deadband` → create new alarm

**Also applied to limit comparisons:**
- `value > (upperLimit + deadband)` → HighLimit alarm
- `value < (lowerLimit - deadband)` → LowLimit alarm
- `Math.abs(value - setPoint) > (tolerance + deadband)` → Deviation alarm

### Alarm Evaluation JavaScript Pattern

**Step 1: Check active alarms before creating new ones (prevent duplicates):**
```graphql
query CheckActiveAlarms($dataPointId: String) {
  alarm(where: {
    dataPointId: { _eq: $dataPointId }
    clearedAt: { _isNull: true }
  }) {
    edges {
      node { id limitType triggerValue }
    }
  }
}
```
- Use `$dataPointId: String` (not `ID!`) for alarm where clause
- Use `clearedAt: { _isNull: true }` to find active (uncleared) alarms

**Step 2: Evaluate limits with deadband (priority order):**
```javascript
// HighLimit / LowLimit (Critical — evaluate first)
if (value > (upperLimit + deadband)) {
  limitType = 'HighLimit';
  limitValue = upperLimit;
} else if (value < (lowerLimit - deadband)) {
  limitType = 'LowLimit';
  limitValue = lowerLimit;
}
// HighWarning / LowWarning (Warning — evaluate second)
else if (value > (upperWarning + deadband)) {
  limitType = 'HighWarning';
  limitValue = upperWarning;
} else if (value < (lowerWarning - deadband)) {
  limitType = 'LowWarning';
  limitValue = lowerWarning;
}
// Deviation (SetPoint tolerance — evaluate last)
else if (Math.abs(value - setPoint) > (tolerance + deadband)) {
  limitType = 'Deviation';
  limitValue = setPoint;
}
```

### Alarm Schema Reference

| Field | Type | Description |
|---|---|---|
| `id` | ID! | Unique identifier |
| `code` | String | Auto-generated `ALM-XXXXX` |
| `deviation` | Float | Deviation from setPoint or limit |
| `limitValue` | Float | The limit value that was exceeded |
| `triggerValue` | Float | The actual value that triggered the alarm |
| `limitType` | Enum | `HighLimit` \| `LowLimit` \| `HighWarning` \| `LowWarning` \| `Deviation` |
| `notes` | String | Operator or auto-generated notes |
| `triggeredAt` | DateTime! | When alarm was triggered |
| `acknowledgedAt` | DateTime | When operator acknowledged |
| `clearedAt` | DateTime | When alarm condition cleared |
| `alarmStateId` | String | `ACTIVE` \| `ACTIVE_ACK` \| `CLEARED` |
| `dataPointId` | String! | FK to DataPoint that triggered the alarm |

→ See `references/alarm-management.md` for complete alarm evaluation JavaScript, deadband implementation, and alarm schema reference.

---

## OEE & ISO 22400 Time Classification

### Time Buckets

| Mode | countAsProductionTime | countAsDowntime | countAsPlannedDowntime | Time Bucket |
|---|---|---|---|---|
| Production | true | false | false | `runTime` |
| Disabled/Down | false | true | false | `unplannedDowntime` |
| Planned Maintenance | false | true | true | `plannedDowntime` |
| No Schedule | false | false | false | excluded |
| Engineering | false | false | false | excluded |

### OEE Formula

```
Availability = runTime / (runTime + unplannedDowntime)
Performance = (idealCycleTime × totalParts) / runTime
Quality = goodParts / totalParts
OEE = Availability × Performance × Quality
```

### Time Window Best Practices

| Window | Use | Typical Update Interval |
|--------|-----|------------------------|
| Current Hour | Real-time monitoring | 1-5 minutes |
| Current Shift | Shift performance tracking | 5-15 minutes |
| Current Day | Daily production summary | 15-30 minutes |
| Rolling 24h | Continuous monitoring | 15-30 minutes |
| Rolling 90 days | Long-term trending | Daily |
| Custom Range | Analysis and reporting | On demand |

## OPC UA Quality Codes

Quality codes indicate data reliability. Apply during telemetry evaluation:

| Code | Status | Meaning | Action |
|------|--------|---------|--------|
| 192 | Good | Value is reliable | Process normally, omit quality field |
| 64 | Uncertain/Warning | Value may not be accurate | Store with quality flag, include in calculations with caveat |
| 16 | Bad/OutOfRange | Value is unreliable | Store for audit, EXCLUDE from OEE/aggregation calculations |

### Quality Evaluation Pattern

```javascript
'use strict';
var qualityCode = payload.quality || 192;
var isGood = qualityCode >= 192;
var isUncertain = qualityCode >= 64 && qualityCode < 192;
var isBad = qualityCode < 64;
```

## Ideal Cycle Time Fallback Pattern

When computing OEE Performance, the ideal cycle time may come from multiple sources:

```javascript
'use strict';
// Fallback chain: dataPoint idealCycleTime → standardCycleTime → 0
var idealCycleTimeMs = 0;
if (dataPoint.idealCycleTime && dataPoint.idealCycleTime > 0) {
  idealCycleTimeMs = dataPoint.idealCycleTime;
} else if (dataPoint.standardCycleTime && dataPoint.standardCycleTime > 0) {
  idealCycleTimeMs = dataPoint.standardCycleTime * 1000; // convert seconds to ms
}

// Calculate ideal units — do NOT round, keep exact decimal
var idealUnits = (operatingTimeMs > 0 && idealCycleTimeMs > 0)
  ? operatingTimeMs / idealCycleTimeMs
  : 0;

// Performance = actual / ideal (NOT capped at 100%)
var performance = idealUnits > 0 ? actualOutput / idealUnits : 0;
```

**Rules:**
- Do NOT round `idealUnits` -- keep exact decimal for accurate Performance calculation
- Performance is NOT capped at 100% -- values over 100% indicate the ideal cycle time may be too conservative
- Always convert units consistently (seconds to milliseconds)

## OEE Domain Formulas

### Core OEE Formula
```
OEE = Availability x Performance x Quality

Availability = Operating Time / Planned Production Time
Performance = (Ideal Cycle Time x Total Count) / Operating Time
Quality = Good Count / Total Count
```

### Industry Benchmarks
- **World-class OEE:** 85%
- **Typical OEE:** 60%
- **World-class Availability:** 90%
- **World-class Performance:** 95%
- **World-class Quality:** 99.9%

### TEEP (Total Effective Equipment Performance)
```
TEEP = OEE x Loading
Loading = Planned Production Time / Calendar Time
```

### Reliability Metrics
```
MTBF = Total Operating Time / Number of Failures
MTTR = Total Repair Time / Number of Failures
```
- Use 30-day rolling window for MTBF/MTTR trending
- Minimum 5 failure events for statistical significance
- Standards: SMRP Best Practice 5.1, EN 15341

→ See `references/oee-time-classification.md` for complete time classification JavaScript, mode flag reference, and OEE calculation patterns.

---

## Workcenter State Management

### State Transition Flow

```
Trigger: WorkcenterHistory Create/Update
→ Query Workcenter + Hierarchy
→ Calculate Time Classification (mode flags)
→ Update WorkcenterHistory with time buckets
→ Publish State Change to UNS
```

### WorkcenterHistory Fields

| Field | Type | Description |
|---|---|---|
| `workcenterId` | ID! | FK to Workcenter |
| `modeId` | ID! | FK to Mode (Production, Down, Planned, etc.) |
| `stateId` | ID! | FK to State (Running, Idle, Faulted, etc.) |
| `eventId` | ID | FK to Event (optional, specific reason) |
| `occurAt` | DateTime! | Start of state |
| `endAt` | DateTime | End of state (null if current) |
| `duration` | Int | Duration in milliseconds |
| `runTime` | Int | ms classified as production time |
| `plannedDowntime` | Int | ms classified as planned downtime |
| `unplannedDowntime` | Int | ms classified as unplanned downtime |
| `notes` | String | Operator notes |

### WorkcenterHistory Schema Reference

| Field | Type | Description |
|---|---|---|
| `id` | ID! | Unique identifier |
| `code` | String | Auto-generated `WCH-XXXXX` |
| `workcenterId` | ID! | FK to Workcenter |
| `modeId` | ID! | FK to Mode (Production, Down, Planned, etc.) |
| `stateId` | ID! | FK to State (Running, Idle, Faulted, etc.) |
| `eventId` | ID | FK to Event (optional, specific reason) |
| `scheduleId` | ID | FK to Schedule (optional) |
| `occurAt` | DateTime! | Start of state period |
| `endAt` | DateTime | End of state (null if current) |
| `duration` | Int | Duration in milliseconds |
| `runTime` | Int | ms classified as production time |
| `plannedDowntime` | Int | ms classified as planned downtime |
| `unplannedDowntime` | Int | ms classified as unplanned downtime |
| `notes` | String | Operator or auto-generated notes |

→ See `references/workcenter-patterns.md` for complete workcenter state management, equipment-specific note generation, and history schema.

---

## Equipment-Specific Downtime Notes

Generate realistic downtime notes by equipment type and state for better operational context:

| Equipment Type | State | Example Notes |
|---|---|---|
| Bioreactor (SUB) | Faulted | "Temperature excursion detected — cooling loop valve failure" |
| Bioreactor (SUB) | Blocked | "Downstream hold tank full — waiting for transfer" |
| Chromatography (CHR) | Faulted | "Pressure differential exceeded — column fouling suspected" |
| Chromatography (CHR) | Starved | "Waiting for harvest clarification — upstream delay" |
| TFF System (TFF) | E-Stopped | "Operator e-stop — transmembrane pressure spike" |
| TFF System (TFF) | Faulted | "Permeate flow rate below minimum threshold" |
| Machining Center | Faulted | "Spindle overload detected — tool breakage suspected" |
| Machining Center | Starved | "Raw material shortage — waiting for bar stock delivery" |

**Pattern:** Combine equipment type, state, and operational context to generate notes that help operators identify root causes. Notes should reference specific subsystems or process parameters relevant to the equipment type.

---

## GraphQL Syntax Reference

Common GraphQL syntax patterns used across industrial operations queries:

| Pattern | Syntax | Notes |
|---|---|---|
| **OrderBy** | `orderBy: [{ field: fieldName, direction: asc }]` | Array of order objects, `asc` or `desc` |
| **Null Check** | `{ _isNull: true }` | NOT `_is: null` — Fuuz uses `_isNull` |
| **Logical OR** | `_or: [{ condition1 }, { condition2 }]` | Array of condition objects |
| **Logical AND** | `_and: [{ condition1 }, { condition2 }]` | Array of condition objects |
| **Duration format** | `duration(format: milliseconds)` | Returns duration in ms |
| **Not equal** | `{ _ne: value }` | Not equal comparison |
| **In list** | `{ _in: [value1, value2] }` | Value in array |
| **Greater than** | `{ _gt: value }` | Greater than comparison |
| **Less than** | `{ _lt: value }` | Less than comparison |

---

## ERP Integration Patterns

### General Principles

1. **Use Fuuz connectors** — Pre-built authentication for Plex, NetSuite, SAP, Epicor, Dynamics 365
2. **Map external IDs** — Store ERP record IDs alongside Fuuz IDs (`externalId`, `erpOrderNumber`)
3. **Idempotent operations** — Use upsert with external IDs as the where clause
4. **Batch operations** — ERP APIs have rate limits. Batch records and respect throttling.
5. **Audit logging** — Create integration log records for every API call
6. **Sandbox first** — Never develop against production ERP systems

### Common Integration Flows

| Pattern | Trigger | Direction | Example |
|---|---|---|---|
| **Order sync** | Schedule (every 15 min) | ERP → Fuuz | Pull work orders from Plex/NetSuite |
| **Production reporting** | DataChanges on ProductionLog | Fuuz → ERP | Push production counts to Plex |
| **Inventory update** | DataChanges on InventoryTransaction | Fuuz → ERP | Update inventory levels in NetSuite |
| **Master data sync** | Schedule (daily) | ERP → Fuuz | Sync products, BOMs, routings |
| **Quality reporting** | DataChanges on InspectionLog | Fuuz → ERP | Push quality results to ERP |

→ See `references/erp-integration.md` for connector-specific patterns and authentication configuration.

---

## Resources

### Reference Files in This Skill

| File | Content |
|------|---------|
| `references/uns-patterns.md` | Complete UNS topic structure, publishing patterns for alarms, workcenter states, and telemetry with ISA-95 hierarchy |
| `references/alarm-management.md` | Alarm evaluation JavaScript, deadband logic, alarm schema, limit type priority |
| `references/oee-time-classification.md` | ISO 22400 mode flags, time classification JavaScript, OEE calculation formulas |
| `references/workcenter-patterns.md` | Workcenter state management, equipment-specific notes, WorkcenterHistory schema |
| `references/erp-integration.md` | ERP connector patterns, Plex/NetSuite/SAP best practices, sync flow architecture |

### Related Skills

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| **fuuz-schema** | Design data models for industrial apps | Creating Asset, Alarm, Workcenter, OEE models |
| **fuuz-flows** | Build processing flows | Implementing alarm evaluation, OEE calculation, UNS publish flows |
| **fuuz-screens** | Create operator interfaces | Building HMI screens, alarm dashboards, OEE displays |
| **fuuz-platform** | Platform reference | Looking up connectors, device drivers, system settings |
| **fuuz-ml-telemetry** | ML and telemetry patterns | Adding predictive analytics to industrial data |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.2 | 2026-02-21 | Added OPC UA quality codes, ideal cycle time fallback pattern, OEE domain formulas (core OEE, TEEP, MTBF/MTTR), time window best practices, industry benchmarks |
| 1.1 | February 2026 | Enhanced alarm evaluation with duplicate-check pattern and schema reference, UNS publishing JSONata patterns, workcenter history schema, equipment-specific downtime notes, GraphQL syntax reference |
| 1.0 | February 2026 | Initial release — UNS patterns, alarm management, OEE time classification, workcenter state management, ERP integration patterns |
