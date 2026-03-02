---
name: scada-alarming
description: "SCADA alarm management reference based on ISA-18.2 / IEC 62682 for the Fuuz Industrial Operations Platform. Use when requests mention alarm management, alarm rationalization, alarm philosophy, alarm priority, alarm shelving, alarm suppression, alarm flooding, alarm deadband, alarm lifecycle, alarm states, alarm KPIs, alarm metrics, alarm audit, standing alarms, stale alarms, chattering alarms, nuisance alarms, first-out alarms, alarm rate, ISA-18.2, IEC 62682, EEMUA 191, SCADA alarms, HMI alarms, operator alarm load, or when designing alarm systems, alarm dashboards, alarm evaluation flows, or alarm rationalization workflows on Fuuz."
---

# SCADA Alarm Management Skill

**Version 1.0** | Last Updated: 2026-03-02

Reference for designing alarm management systems on the Fuuz platform, grounded in the ISA-18.2 / IEC 62682 alarm management lifecycle. Covers alarm philosophy, state models, priority classification, rationalization, design parameters, performance metrics, advanced techniques, and Fuuz implementation mapping.

## When to Use This Skill

Use **scada-alarming** when you need to:
- Define an alarm philosophy or design basis for a Fuuz application
- Classify alarm priorities and map them to Fuuz `alarmPriority` / `isCritical` fields
- Understand alarm states and transitions (ISA-18.2 and Fuuz)
- Rationalize alarms (purpose, consequence, response, setpoints)
- Configure deadband, delay timers, and setpoint parameters on DataPoints
- Evaluate alarm system health using ISA-18.2 performance benchmarks
- Implement suppression, shelving, flooding management, or alarm grouping
- Build alarm dashboards, alarm summary screens, or alarm KPI reports

**This is a standards-and-patterns skill.** For building data models, flows, or screens, use fuuz-schema, fuuz-flows, or fuuz-screens.

---

## 1. Alarm Philosophy

An alarm philosophy documents the principles governing every alarm in a facility. It is the foundation of ISA-18.2 compliance and should be established before any alarm is configured.

| Section | Purpose |
|---------|---------|
| Objectives | Why alarms exist -- safety, environment, equipment, product quality |
| Scope | Which systems, units, and equipment are covered |
| Definitions | Standard terminology (alarm, alert, notification, event) |
| Priority scheme | Priority levels, definitions, and response-time targets |
| Design criteria | Setpoint selection, deadband policy, delay-timer policy |
| Roles | Who configures, rationalizes, reviews, and acknowledges alarms |
| Performance targets | Alarm rate, standing alarm, and distribution targets |
| Management of change | How alarms are added, modified, or removed |
| Audit cycle | Frequency and scope of periodic alarm system reviews |

**Fuuz tip:** Store the alarm philosophy as a Document record with category `AlarmPhilosophy`.

---

## 2. Alarm Lifecycle (ISA-18.2 States)

### ISA-18.2 State Diagram

```
+------+  alarm  +-------+  acknowledge  +-------+  clear   +------+
| NORM |-------->| UNACK |-------------->| ACKED |--------->| NORM |
+------+         +-------+              +-------+          +------+
                    |                                         ^
                    +--- clear (before ack) ------------------+
                    |                                         ^
                    +-- clear --> RTN-UNACK -- acknowledge ---+

Side states (enter/exit from any active state):
  SUPPRESSED    logic-based or state-based hide from operator
  SHELVED       time-limited disable (auto-returns on expiry)
  OUT-OF-SERVICE  disabled for maintenance (manual re-enable)
```

### ISA-18.2 to Fuuz State Mapping

| ISA-18.2 State | Fuuz `alarmStateId` | Implementation Notes |
|----------------|---------------------|----------------------|
| UNACK | `ACTIVE` | Alarm triggered, not yet acknowledged |
| ACKED | `ACTIVE_ACK` | Operator acknowledged, condition still active |
| NORM (cleared+acked) | `CLEARED` | `clearedAt` is set; alarm is historical |
| RTN-UNACK | `CLEARED` | Fuuz clears on condition return; ack is optional |
| SUPPRESSED | -- | Flow guard skips alarm creation when suppression conditions met |
| SHELVED | -- | Set `alarmEnabled = false` + scheduled flow to re-enable |
| OUT-OF-SERVICE | -- | `alarmEnabled = false` on DataPoint |

### Fuuz State Transitions

```
           trigger              acknowledge             clear
  NORM --> ACTIVE (alarmStateId) --> ACTIVE_ACK --> CLEARED
             |                                        ^
             +------ clear (before acknowledge) ------+
```

- Active alarms identified by `clearedAt: { _isNull: true }`

---

## 3. Priority Classification

ISA-18.2 recommends 3-4 priority levels in a pyramid -- few critical, more low-priority.

| Priority | Response Time | Consequence of Inaction | Fuuz Fields |
|----------|--------------|-------------------------|-------------|
| **Critical** | < 5 min | Safety incident, environmental release | `alarmPriority: 1`, `isCritical: true` |
| **High** | < 10 min | Significant production loss, equipment damage | `alarmPriority: 2`, `isCritical: true` |
| **Medium** | < 30 min | Moderate process deviation, quality impact | `alarmPriority: 3`, `isCritical: false` |
| **Low** | < 60 min | Minor deviation, maintenance advisory | `alarmPriority: 4`, `isCritical: false` |

**Target distribution (pyramid):** ~5% Critical, ~15% High, ~30% Medium, ~50% Low. An inverted pyramid indicates poor rationalization.

---

## 4. Alarm Rationalization

Rationalization is the structured review documenting and justifying every alarm.

| Item | Question to Answer |
|------|--------------------|
| Purpose | What condition does this alarm detect? |
| Consequence | What happens if the operator does not respond? |
| Operator action | What specific action should the operator take? |
| Response time | How quickly must the operator act? |
| Priority | What level, based on consequence and response time? |
| Setpoint | What value triggers the alarm? How was it determined? |
| Deadband | What deadband prevents chatter without masking real changes? |
| Delay timer | Should a time delay filter transient conditions? |
| Related alarms | Are there correlated alarms to group or suppress? |
| Classification | Safety, environmental, equipment protection, quality, process, or diagnostic? |

### Alarm Classification Categories

| Category | Typical Priority | Examples |
|----------|-----------------|----------|
| Safety | Critical / High | Gas leak, fire, personnel hazard |
| Environmental | Critical / High | Chemical release, emissions exceedance |
| Equipment protection | High / Medium | Overtemperature, vibration, overload |
| Quality | Medium | Out-of-spec product, SPC violation |
| Process | Medium / Low | Abnormal process deviation |
| Diagnostic / Advisory | Low | Maintenance advisory, calibration due |

---

## 5. Alarm Design Parameters

### Setpoint, Deadband, and Limit Diagram

```
Value
  ^
  |--- upperLimit + deadband ------- alarm triggers (HighLimit)
  |--- upperLimit ------------------- alarm clears
  |--- upperWarning + deadband ------ alarm triggers (HighWarning)
  |--- upperWarning ----------------- alarm clears
  |=== setPoint +/- tolerance ======= normal operating range
  |--- lowerWarning ----------------- alarm clears
  |--- lowerWarning - deadband ------ alarm triggers (LowWarning)
  |--- lowerLimit ------------------- alarm clears
  |--- lowerLimit - deadband -------- alarm triggers (LowLimit)
  v
```

### Fuuz DataPoint Limit Fields

| Field | Type | Purpose |
|-------|------|---------|
| `upperLimit` / `lowerLimit` | Float | Critical thresholds (HighLimit / LowLimit alarms) |
| `upperWarning` / `lowerWarning` | Float | Warning thresholds (HighWarning / LowWarning alarms) |
| `setPoint` | Float | Target operating value |
| `tolerance` | Float | Acceptable deviation from setPoint (Deviation alarm if exceeded) |
| `deadband` | Float | Hysteresis band to prevent alarm chatter |
| `alarmEnabled` | Boolean | Master enable/disable for alarm evaluation |
| `isCritical` | Boolean | Quick filter for safety-critical alarms (priority 1-2) |
| `alarmPriority` | Int | Numeric priority (1=Critical, 2=High, 3=Medium, 4=Low) |

### Limit Evaluation Priority (first match wins)

1. **HighLimit / LowLimit** -- `value > upperLimit + deadband` or `value < lowerLimit - deadband`
2. **HighWarning / LowWarning** -- `value > upperWarning + deadband` or `value < lowerWarning - deadband`
3. **Deviation** -- `|value - setPoint| > tolerance + deadband`

### Deadband Behavior

Two applications of deadband:
1. **Limit deadband** -- Offset added to limit thresholds before triggering
2. **Active alarm deadband** -- New alarm only when `|currentValue - lastTriggerValue| > deadband`

```
Example: upperLimit=100, deadband=5
  Value 103 --> below 105 (limit+deadband) --> NO alarm
  Value 106 --> above 105 --> HighLimit ALARM (triggerValue=106)
  Value 104 --> |104-106|=2 < 5 --> SKIP (within active alarm deadband)
```

### Delay Timers

Implementable in flow logic (timer node before alarm creation):

| Scenario | Recommended Delay |
|----------|-------------------|
| Electrical noise | 2-5 seconds |
| Startup transient | 10-30 seconds |
| Batch stage transition | 30-120 seconds |

---

## 6. Performance Metrics (ISA-18.2 Benchmarks)

| Metric | Target | Description |
|--------|--------|-------------|
| Average alarm rate | ≤ 6/hr per operator | Manageable workload |
| Peak alarm rate | < 10 per 10 min | Avoids alarm flooding |
| Standing alarms | < 5 per operator | Active > 24 hours |
| Stale alarms | 0 | No longer relevant |
| Chattering alarms | 0 | > 3 state changes/min |
| Nuisance alarms | < 5% of total | No action required |
| Priority distribution | ~80% Low/Med, ~15% High, ~5% Critical | Pyramid shape |
| Alarm floods | < 1% of operating time | > 10 alarms/10 min |

**Key definitions:**
- **Standing alarm** -- active (uncleared) > 24 hours; chronic condition needing re-rationalization
- **Chattering alarm** -- > 3 state changes/min; needs deadband or setpoint adjustment
- **Nuisance alarm** -- triggers but requires no operator action; candidate for removal
- **Alarm flood** -- rate exceeds 10/10 min per operator; requires suppression or grouping

---

## 7. Advanced Alarm Management

### Suppression

| Type | Trigger | Example |
|------|---------|---------|
| State-based | Equipment in specific mode | Suppress low-flow alarm when pump in maintenance |
| Logic-based | Process condition combination | Suppress high-level alarm when transfer valve open |
| Consequence-based | Upstream alarm active | Suppress downstream alarms when upstream trip occurs |

**Fuuz:** Guard node in alarm flow checks suppression condition before creating alarm. Log suppressed alarms for audit.

### Shelving

Operator-initiated, time-limited disable. Max 8-12 hours (one shift). Require supervisor approval for safety alarms. Record who, when, why, duration.

**Fuuz:** Set `alarmEnabled = false` on DataPoint + scheduled flow to re-enable after duration.

### Flooding Management

1. **Suppression logic** -- auto-suppress consequential alarms during known upsets
2. **First-out annunciation** -- highlight first alarm in cascade for root-cause identification
3. **Alarm grouping** -- group by equipment, cause, area, or time window
4. **Prioritized display** -- during floods, show only Critical/High; queue Medium/Low
5. **Post-flood review** -- analyze for rationalization improvements

### Grouping Strategies

| Strategy | Criteria | Benefit |
|----------|----------|---------|
| Equipment-based | Same asset or cell | Equipment context for operator |
| Cause-based | Common root cause | Reduces alarm count during upsets |
| Area-based | Same ISA-95 area | Geographic operator responsibility |
| Time-based | Short time window | Identifies cascading failures |

---

## 8. Fuuz Implementation Mapping

### Alarm Evaluation Flow

```
TelemetryRaw Create (DataChanges trigger)
  --> Query DataPoint + Active Alarms (clearedAt _isNull: true)
  --> Guard: alarmEnabled == true
  --> JavaScript: Evaluate limits with deadband (priority order)
  --> Guard: alarm condition detected
  --> Mutate: Create Alarm (alarmStateId: ACTIVE)
  --> Optional: Publish to UNS
```

### DataPoint-to-Alarm Relationship

```
DataPoint (1) --------> (*) Alarm
  upperLimit, lowerLimit       limitType, limitValue
  upperWarning, lowerWarning   triggerValue, deviation
  setPoint, tolerance          alarmStateId
  deadband, alarmEnabled       triggeredAt, acknowledgedAt, clearedAt
  isCritical, alarmPriority    dataPointId (FK), notes
```

### Alarm Schema

| Field | Type | Description |
|-------|------|-------------|
| `id` | ID! | CUID |
| `code` | String | Auto-generated `ALM-XXXXX` |
| `limitType` | String | `HighLimit`, `LowLimit`, `HighWarning`, `LowWarning`, `Deviation` |
| `limitValue` | Float | Limit threshold exceeded |
| `triggerValue` | Float | Actual value that triggered |
| `deviation` | Float | Deviation from setPoint or limit |
| `triggeredAt` | DateTime! | When triggered |
| `acknowledgedAt` | DateTime | When acknowledged (null if unacknowledged) |
| `clearedAt` | DateTime | When cleared (null if active) |
| `alarmStateId` | String | `ACTIVE`, `ACTIVE_ACK`, `CLEARED` |
| `dataPointId` | String! | FK to DataPoint |
| `notes` | String | Operator or auto-generated description |

### UNS Alarm Topic

```
fuuz/{site}/{area}/{line}/{cell}/{equipment}/alarm
```

### Example Queries

**Active alarms:**
```graphql
query ActiveAlarms {
  alarm(where: { clearedAt: { _isNull: true } }, orderBy: [{ field: triggeredAt, direction: desc }]) {
    edges { node { id code limitType alarmStateId triggeredAt dataPoint { code asset { code } } } }
  }
}
```

**Standing alarms (active > 24h):**
```graphql
query StandingAlarms($threshold: DateTime!) {
  alarm(where: { clearedAt: { _isNull: true }, triggeredAt: { _lt: $threshold } }) {
    totalCount
    edges { node { id code dataPointId triggeredAt limitType } }
  }
}
```

---

## 9. Cross-References

| Skill | Relevance |
|-------|-----------|
| **isa-95** | Equipment hierarchy structuring alarm ownership and UNS topics |
| **isa-88** | PackML state machine; state-based alarm suppression during mode transitions |
| **fuuz-industrial-ops** | Complete alarm evaluation JavaScript, deadband implementation, UNS publishing patterns |
| **fuuz-flows** | Building alarm evaluation flows with DataChanges, guards, JavaScript transforms, mutations |
| **fuuz-screens** | Alarm summary screens, alarm banners, acknowledge buttons, alarm history tables |
| **fuuz-schema** | Designing Alarm and DataPoint models with proper fields and relationships |
| **fuuz-graphql** | Query patterns for active alarms, alarm history, and alarm metrics |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-03-02 | Initial release -- alarm philosophy, ISA-18.2 state model with Fuuz mapping, priority classification, rationalization, design parameters, performance metrics, advanced management techniques, Fuuz implementation patterns |
