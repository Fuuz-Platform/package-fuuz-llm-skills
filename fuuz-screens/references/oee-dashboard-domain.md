# OEE Dashboard Development Skill

## Overview

This skill covers the development of real-time OEE (Overall Equipment Effectiveness) dashboards for manufacturing workcenters. It includes domain knowledge for manufacturing metrics, calculation patterns, data modeling, and visualization best practices based on ISO 22400 standards.

---

## Domain Knowledge

### OEE (Overall Equipment Effectiveness)

OEE is the gold standard for measuring manufacturing productivity. It combines three factors:

```
OEE = Availability x Performance x Quality
```

| Component | Formula | Measures |
|-----------|---------|----------|
| **Availability** | Run Time / Scheduled Time | Equipment uptime during planned production |
| **Performance** | Actual Output / Ideal Output | Speed efficiency vs theoretical maximum |
| **Quality** | Good Units / Total Units | First-pass yield, defect rate |

**Industry Benchmarks:**
- World-class OEE: 85%
- Typical OEE: 60%
- Availability target: 90%
- Performance target: 95%
- Quality target: 99%

### TEEP (Total Effective Equipment Performance)

TEEP extends OEE to measure against total calendar time, not just scheduled time:

```
TEEP = OEE x Loading
Loading = Scheduled Time / Calendar Time
```

TEEP reveals hidden capacity by showing how much of total available time is productively used.

### MTBF (Mean Time Between Failures)

```
MTBF = Operating Time / Number of Failures
```

**Critical considerations:**
- Operating Time should EXCLUDE time spent in fault states
- Only count time when equipment is running normally (non-faulted production)
- Higher MTBF = better reliability

### MTTR (Mean Time To Repair)

```
MTTR = Total Repair Time / Number of Failures
```

**Critical considerations:**
- Repair Time = time spent IN fault states (actual repair activity)
- Should NOT include planned downtime (scheduled maintenance)
- Should NOT include degraded running states (faulted but still producing)
- Lower MTTR = faster recovery from failures

---

## Time Window Best Practices

### For OEE Metrics

| Window | Use Case |
|--------|----------|
| **Current Hour** | Real-time operator awareness, live calculations |
| **Current Shift** | Shift performance, handover context |
| **Current Day** | Daily production tracking |
| **Rolling 24h** | Trend analysis, removes shift bias |
| **Rolling 90d** | Long-term efficiency metrics |

### For Reliability Metrics (MTBF/MTTR)

**Single aggregates are problematic** - they hide whether reliability is improving or degrading.

**Recommended: 30-day trending periods**

| Period | Purpose |
|--------|---------|
| **0-30 days** | Current reliability - what's happening now |
| **30-60 days** | Prior period - comparison baseline |
| **60-90 days** | Historical baseline - where were we before |

This enables trend calculations:
```javascript
mtbfChange = (current.mtbf - prior.mtbf) / prior.mtbf
trend = mtbfChange > 0.1 ? "improving" : mtbfChange < -0.1 ? "degrading" : "stable"
```

**Industry standards for reliability windows:**
- 7-day minimum: SMRP explicit floor for any reliability reporting
- 30-day standard: Operational tracking per SMRP Best Practice 3.2
- 90-day recommended: Maintenance planning per EN 15341:2007

**Minimum sample sizes for statistical validity:**
- 3 failures: Absolute floor (low confidence)
- 5-10 failures: Rough operational estimate
- 20+ failures: Actionable maintenance decisions

---

## Manufacturing State Classifications

### Mode vs State Distinction

**Mode** = What the machine is supposed to be doing (Production, Setup, Cleaning, Idle)
**State** = Condition of the machine (Running, Faulted, Stopped)

### Mode Flags (ISO 22400 aligned)

| Flag | Meaning | Examples |
|------|---------|----------|
| `countAsProductionTime` | Counts toward run time / operating time | Production, some degraded states |
| `countAsDowntime` | Machine is stopped/down | Breakdowns, unplanned stops |
| `countAsPlannedDowntime` | Scheduled non-production | Maintenance, breaks, changeover |

### State Flags

| Flag | Meaning |
|------|---------|
| `isFault` | Equipment has an active fault condition |

### Scenario Matrix

| Mode | State | runTime | downtime | MTBF Operating Time | MTTR Repair Time |
|------|-------|---------|----------|---------------------|------------------|
| Production | Normal | Yes | No | Count | No |
| Production | Faulted | Yes | No | Exclude | Count |
| Downtime | Faulted | No | Yes | Exclude | Count |
| Planned DT | Normal | No | Yes | Exclude | Exclude |

**Key insight:** A machine can be running (countAsProductionTime=true) while faulted (degraded operation). This time should count toward MTTR (it's time spent dealing with a fault) but NOT toward MTBF operating time.

---

## Calculation Patterns

### OEE Calculation

```javascript
const calculateOEE = (totals) => {
  const scheduledMs = totals.scheduledTimeMs;
  const runMs = totals.runMinutesMs;
  const goodUnits = totals.goodUnits;
  const totalUnits = totals.totalUnits;
  const idealUnits = totals.idealUnits;

  // No scheduled time = no OEE (avoid division by zero)
  if (scheduledMs === 0) {
    return { availability: null, performance: null, quality: null, oee: null };
  }

  // Clamp values between 0 and 1 (can exceed 1 due to data anomalies)
  const clamp = (v) => Math.max(0, Math.min(1, v));

  const availability = clamp(runMs / scheduledMs);
  const performance = idealUnits > 0 ? clamp(totalUnits / idealUnits) : 1;
  const quality = totalUnits > 0 ? clamp(goodUnits / totalUnits) : 1;
  const oee = availability * performance * quality;

  return { availability, performance, quality, oee };
};
```

### Reliability Calculation

```javascript
// Operating time: production time when NOT faulted
const sumOperatingTime = (nodes) => {
  return nodes.reduce((sum, node) => {
    return sum + (node._aggregate?.runTime?.sum || 0);
  }, 0);
};

// Faults: count and repair time (runTime while IN fault state)
const sumFaults = (nodes) => {
  let faultCount = 0;
  let repairTimeMs = 0;

  nodes.forEach(node => {
    faultCount += node._aggregate?.id?.count || 0;
    repairTimeMs += node._aggregate?.runTime?.sum || 0;
  });

  return { faultCount, repairTimeMs };
};

// MTBF and MTTR
const calculateReliability = (operatingTimeMs, faultCount, repairTimeMs) => {
  const mtbf = faultCount > 0 ? operatingTimeMs / faultCount : null;
  const mttr = faultCount > 0 ? repairTimeMs / faultCount : null;

  return { mtbf, mttr, faultCount };
};
```

### Trend Calculation

```javascript
const calculateTrend = (current, prior) => {
  if (prior == null || prior === 0) return null;
  return (current - prior) / prior;
};

// For MTBF: positive change = improving (longer time between failures)
const mtbfTrend = change > 0.1 ? "improving" : change < -0.1 ? "degrading" : "stable";

// For MTTR: negative change = improving (faster repairs)
const mttrTrend = change < -0.1 ? "improving" : change > 0.1 ? "degrading" : "stable";
```

### Duration Formatting

```javascript
const formatDuration = (ms) => {
  if (ms == null || ms === 0) return "0m";
  const hours = Math.floor(ms / 3600000);
  const mins = Math.floor((ms % 3600000) / 60000);
  return hours > 0 ? `${hours}h ${mins}m` : `${mins}m`;
};
```

---

## Data Query Patterns

### Operating Time Query (for MTBF numerator)

```graphql
operatingTime: workcenterHistory(
  where: {
    _and: [
      { workcenterId: { _eq: $wcId } }
      { occurAt: { _gte: $windowStart } }
      { occurAt: { _lt: $windowEnd } }
      { mode: { countAsProductionTime: { _eq: true } } }
      { state: { isFault: { _eq: false } } }  # Exclude fault time!
    ]
  }
) {
  edges {
    node {
      _aggregate {
        runTime { sum }
      }
    }
  }
}
```

### Faults Query (for MTBF denominator and MTTR)

```graphql
faults: workcenterHistory(
  where: {
    _and: [
      { workcenterId: { _eq: $wcId } }
      { occurAt: { _gte: $windowStart } }
      { occurAt: { _lt: $windowEnd } }
      { state: { isFault: { _eq: true } } }
    ]
  }
) {
  edges {
    node {
      eventId
      event { id code }
      _aggregate {
        runTime { sum }  # This is the repair time
        id { count }     # This is the fault count
      }
    }
  }
}
```

### OEE Hourly Aggregates

```graphql
oeeHourly: oeeHourly(
  where: {
    _and: [
      { workcenterId: { _eq: $wcId } }
      { periodStartAt: { _gte: $windowStart } }
      { periodStartAt: { _lt: $windowEnd } }
    ]
  }
  orderBy: [{ field: periodStartAt, direction: asc }]
) {
  edges {
    node {
      periodStartAt(round: { unit: hour })
      _aggregate {
        goodUnits { sum }
        scrapUnits { sum }
        totalUnits { sum }
        idealUnits { sum }
        runMinutes { values }
        downtimeMinutes { values }
        plannedDowntimeMinutes { values }
        scheduledTime { values }
      }
    }
  }
}
```

### Downtime by Event (for Pareto analysis)

```graphql
downtimeByEvent: workcenterHistory(
  where: {
    _and: [
      { workcenterId: { _eq: $wcId } }
      { occurAt: { _gte: $windowStart } }
      { occurAt: { _lt: $windowEnd } }
      { mode: { countAsDowntime: { _eq: true } } }
      { eventId: { _isNull: false } }
    ]
  }
) {
  edges {
    node {
      eventId
      event { id code }
      _aggregate {
        unplannedDowntime { sum }
        plannedDowntime { sum }
        id { count }
      }
    }
  }
}
```

---

## Visualization Recommendations

### Primary KPIs

| Metric | Chart Type | Notes |
|--------|------------|-------|
| OEE, Availability, Performance, Quality | **Gauge / Radial** | Color zones: Red <60%, Yellow 60-85%, Green >85% |
| MTBF, MTTR | **Big Number + Trend Arrow** | Show value + % change vs prior period |
| Current Status | **Traffic Light / Icon** | Running (green), Faulted (red), Down (yellow) |

### Trends

| Metric | Chart Type | Notes |
|--------|------------|-------|
| OEE over 24h | **Line Chart** | Hourly data points, optional A/P/Q overlay |
| MTBF/MTTR by period | **Bar Chart** | 3 bars: 60-90d, 30-60d, 0-30d |
| Time allocation | **Stacked Area** | Run time vs planned vs unplanned downtime |

### Analysis

| Metric | Chart Type | Notes |
|--------|------------|-------|
| Top downtimes | **Pareto Chart** | Bars sorted by duration + cumulative line |
| OEE losses | **Waterfall Chart** | 100% -> -Avail Loss -> -Perf Loss -> -Qual Loss -> OEE |
| Hourly performance | **Heatmap** | 24 cols (hours) x 7 rows (days), color = OEE |

### Real-Time

| Metric | Chart Type | Notes |
|--------|------------|-------|
| Current hour activity | **Timeline / Gantt** | State transitions color-coded |
| Time in current state | **Live Counter** | "Running for 47m 23s" with JS timer |

---

## Dashboard Layout Principles

1. **Status first** - Current state and duration at top
2. **KPIs prominent** - Gauges or big numbers for OEE components
3. **Trends visible** - 24h line chart for pattern recognition
4. **Actionable insights** - Pareto downtimes, MTBF/MTTR trends
5. **Drill-down available** - Multiple time windows (hour, shift, day, 24h)

### Recommended Layout

```
+-------------------------------------------------------------+
|  [Status Bar]  Machine: CNC-01  |  Running  |  47m 23s      |
+-------------------------------------------------------------+
|  [Gauges Row]                                               |
|  +---------+ +---------+ +---------+ +---------+           |
|  |   OEE   | |  Avail  | |  Perf   | |  Qual   |           |
|  |   77%   | |   88%   | |   92%   | |   95%   |           |
|  +---------+ +---------+ +---------+ +---------+           |
+-------------------------------------------------------------+
|  [Main Content]                                             |
|  +-------------------------+ +----------------------------+ |
|  |  OEE Trend (24h)        | |  Reliability              | |
|  |  [Line Chart]           | |  MTBF: 46h  +15%          | |
|  |                         | |  MTTR: 23m  -8%           | |
|  |                         | |  [Period Bar Chart]       | |
|  +-------------------------+ +----------------------------+ |
+-------------------------------------------------------------+
|  [Analysis Row]                                             |
|  +-------------------------+ +----------------------------+ |
|  |  Top Downtimes (Pareto) | |  Loss Waterfall           | |
|  +-------------------------+ +----------------------------+ |
+-------------------------------------------------------------+
```

---

## Output Data Structure

A well-structured OEE calculation output should include:

```javascript
{
  // Identity
  workcenterId: "wc-001",
  workcenter: { name: "CNC Mill 01" },
  timezone: "America/Chicago",
  lastRefresh: "2026-02-05T14:30:00Z",

  // Current machine state
  currentStatus: {
    mode: { code: "PROD" },
    state: { code: "RUN" },
    duration: "47m 23s",
    durationMs: 2843000
  },

  // OEE metrics by time window
  metrics: {
    currentHour: { oee, availability, performance, quality, oeePct, ... },
    shift: { ... },
    day: { ... },
    rolling24h: { ... },
    rolling90d: { ... }
  },

  // TEEP
  teep: {
    teep, teepPct,
    loading, loadingPct
  },

  // Reliability with trending
  reliability: {
    current: { mtbf, mtbfFormatted, mttr, mttrFormatted, faultCount, periodLabel: "Last 30 days" },
    prior: { ..., periodLabel: "30-60 days ago" },
    baseline: { ..., periodLabel: "60-90 days ago" },
    overall: { ..., periodLabel: "Last 90 days" },
    trend: {
      mtbfChangeVsPrior: 0.15,
      mtbfTrend: "improving",  // "improving" | "degrading" | "stable"
      mttrChangeVsPrior: -0.08,
      mttrTrend: "improving"
    },
    summary: {
      mtbfFormatted: "46h 12m",
      mtbfTrendIcon: "▲",
      mtbfTrendLabel: "+15.0% vs prior 30d",
      mttrFormatted: "23m",
      mttrTrendIcon: "▼",
      mttrTrendLabel: "-8.0% vs prior 30d"
    }
  },

  // Efficiency metrics
  efficiency: {
    utilization, utilizationPct,
    throughput: "12.5 units/hr",
    oeeGap, oeeGapPct
  },

  // Loss analysis
  lossAnalysis: {
    plannedDowntime, unplannedDowntime,
    performanceLoss, qualityLoss
  },

  // Top downtimes for Pareto
  topDowntimes: {
    shift: [{ eventCode, totalMs, totalFormatted, occurrences }, ...],
    rolling24h: [...]
  }
}
```

---

## Common Pitfalls

1. **Using single 90-day aggregates for MTBF/MTTR** - Hides trends, use 30-day periods instead

2. **Including fault time in MTBF operating time** - Inflates MTBF, exclude `state.isFault = true` records

3. **Using `unplannedDowntime` field for MTTR** - May be unpopulated, use `runTime` from fault state records

4. **Including planned downtime in MTTR** - Skews repair metrics, only count unplanned fault states

5. **Not handling zero scheduled time** - Causes division by zero, return null for OEE

6. **Counting degraded running as repair time** - Machine running faulted is still running, track separately if needed

7. **Insufficient sample size** - MTBF/MTTR need 5+ failures minimum for meaningful values

---

## References

- **ISO 22400-2**: Key Performance Indicators for Manufacturing Operations Management
- **SMRP Best Practice 3.2**: Maintenance and Reliability Key Performance Indicators
- **EN 15341:2007**: Maintenance Key Performance Indicators
- **OEE Foundation**: Overall Equipment Effectiveness standards