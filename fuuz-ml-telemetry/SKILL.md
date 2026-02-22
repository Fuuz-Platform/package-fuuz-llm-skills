---
name: fuuz-ml-telemetry
description: Machine learning and telemetry analysis patterns for the Fuuz platform (fuuz.com). Use when requests mention "machine learning", "ML", "anomaly detection", "Z-score", "EWMA", "baseline", "telemetry analysis", "telemetry forecast", "predictive maintenance", "trend analysis", "correlation", "Pearson correlation", "breach prediction", "confidence bands", "TelemetryHourly", "TelemetryBaseline", "PatternInsight", "CorrelationPair", "TelemetryForecast", "self-learning", "adaptive baseline", "statistical analysis", or when building predictive analytics, telemetry pipelines, or ML features on the Fuuz platform. Covers EWMA baselines, Z-score anomaly detection, linear regression, breach prediction, forecast projection, Pearson correlation, hybrid flow architecture, and ML delivery methodology.
---

# Fuuz ML & Telemetry Analysis Patterns

**Version 1.1** | Last Updated: February 2026

Implementation patterns for machine learning pipelines, telemetry analysis, and predictive maintenance on the Fuuz Industrial Operations platform.

## When to Use This Skill

Use **fuuz-ml-telemetry** when you need to:
- Build self-learning baselines for sensor data (EWMA)
- Implement anomaly detection (Z-score)
- Create trend analysis and breach prediction
- Build cross-asset correlation analysis (Pearson)
- Design forecast projection with confidence bands
- Optimize telemetry queries using TelemetryHourly
- Build complete ML telemetry pipelines

**This is a domain patterns skill.** For building the actual data models, flows, or screens, use the corresponding builder skills (fuuz-schema, fuuz-flows, fuuz-screens).

---

## JavaScript Runtime Limitations (CRITICAL)

The Fuuz flow JS transform engine is a **restricted ES5-compatible runtime**. Violating these constraints causes silent failures or cryptic errors.

### NOT Supported (Will Fail)

| Feature | Status | Workaround |
|---|---|---|
| `.toFixed()` | **NOT supported** | `Math.round(val * 10000) / 10000` |
| `let` / `const` | **NOT supported** | Use `var` for all declarations |
| Arrow functions `=>` | **NOT supported** | Use `function() {}` declarations |
| Template literals `` ` `` | **NOT supported** | Use string concatenation `+` |
| Destructuring `{ a, b }` | **NOT supported** | Use `var a = obj.a; var b = obj.b;` |
| Spread operator `...` | **NOT supported** | Use explicit assignment or loops |
| `async` / `await` | **NOT supported** | Use synchronous patterns only |
| Variable scoping in loops | **Unreliable** | Inline expressions preferred |

### Supported (Safe to Use)

- `'use strict';` — **Always start every JS transform with this**
- `var` declarations
- `Math.*` (Math.round, Math.abs, Math.sqrt, Math.min, Math.max, Math.pow, Math.log, Math.floor, Math.ceil)
- `JSON.stringify()` / `JSON.parse()`
- `Object.keys()`
- `Array.isArray()`
- `parseFloat()` / `parseInt()`
- `for` / `while` / `if` / `switch` control flow
- `function` declarations (named functions)

### Rounding Pattern (Use Everywhere)

```javascript
// WRONG — will crash:
var result = value.toFixed(4);

// CORRECT — always use this:
var result = Math.round(value * 10000) / 10000;
```

---

## ML Pipeline Architecture

### Hybrid Multi-Flow Design

Complex ML systems should separate concerns into distinct flows:

| Flow Type | Trigger | Complexity | Purpose |
|---|---|---|---|
| **Real-time** | `dataChanges` on TelemetryRaw Create | O(1) per record | EWMA baseline update, Z-score anomaly, trend |
| **Scheduled batch** | `schedule` trigger (hourly/daily) | O(n) cross-record analysis | Cross-asset Pearson correlation, aggregation |
| **Scheduled projection** | `schedule` trigger (hourly) | O(k) forward-looking | Forecast projection, breach prediction, accuracy |

**Key principle:** Real-time flows must be O(1) per incoming record. Any calculation requiring multiple records belongs in a scheduled batch flow.

### Standard Flow Node Pattern

Every ML flow follows this standard node sequence:

```
Trigger → Query Settings → Set Context → Query Data → Guard → JS Transform → Guard → Mutate
```

| Node | Purpose |
|---|---|
| **Trigger** | `dataChanges` or `schedule` — initiates the flow |
| **Query Settings** | Fetch ML parameters from Settings (system API) |
| **Set Context** | Store settings in `$state.context` via `setContext` |
| **Query Data** | Fetch telemetry, baselines, or related records |
| **Guard (pre-JS)** | Prevent processing empty datasets: `$count($.modelName.edges.node) = 0` |
| **JS Transform** | Core algorithm logic (EWMA, Z-score, correlation, etc.) |
| **Guard (post-JS)** | Prevent empty mutation payloads |
| **Mutate** | Upsert/create/delete results |

**Guard node pattern:**
```jsonata
$count($.modelName.edges.node) = 0
```
When the guard evaluates to `true`, the flow stops (no data to process).

### `$state.context` Survival Note

Context values set via `setContext` **DO survive** through `broadcast` fan-out nodes. You can safely set context in an early node and read it in parallel branches after a broadcast.

### Parallel Mutation Paths

When a JS transform returns multiple result types (e.g., baseline updates AND anomaly records), use a broadcast node after the JS transform to fan out into parallel mutation paths:

```
JS Transform → Broadcast → [Path A: Guard → Mutate Baselines]
                         → [Path B: Guard → Mutate Anomalies]
                         → [Path C: Guard → Mutate Forecasts]
```

Each path has its own guard node to prevent empty payloads for that specific result type.

---

## Data Models

| Model | Type | TTL | Purpose |
|---|---|---|---|
| `TelemetryBaseline` | Transactional | None (persistent) | Running EWMA stats per datapoint |
| `PatternInsight` | Transactional | 90 days | Detected anomalies, trends, predictions |
| `CorrelationPair` | Transactional | 30 days | Cross-asset Pearson correlation results |
| `TelemetryForecast` | Transactional | 30 days | Projected trajectories with confidence bands |

→ See `references/data-models.md` for complete model field definitions.

---

## Algorithms

### EWMA Baseline (Self-Learning, O(1))

Exponentially Weighted Moving Average enables O(1) per-value statistical learning:

```javascript
var newMean = alpha * newValue + (1 - alpha) * oldMean;
var diff = newValue - oldMean;
var newVariance = (1 - alpha) * (oldVariance + alpha * diff * diff);
var newSampleCount = oldSampleCount + 1;
```

**Frequency-adaptive alpha calculation:**
```javascript
// Alpha adapts to data frequency — faster sensors get smaller alpha (more smoothing)
var samplesPerHour = Math.round(60 / frequencyMinutes);
var alpha = 2 / (samplesPerHour + 1);
// Example: 1-min frequency → 60 samples/hr → alpha = 0.0328
// Example: 5-min frequency → 12 samples/hr → alpha = 0.1538
```

**Cold start handling:**
```javascript
var isWarm = (sampleCount >= warmupThreshold); // default warmupThreshold = 30
if (!isWarm) {
  // During cold start: update baseline stats but do NOT generate alerts
  // Use simple running average until warm
  var newMean = ((oldMean * oldSampleCount) + newValue) / (oldSampleCount + 1);
  // Skip anomaly detection, trend analysis, and breach prediction
}
```
- Set `isWarm` to `true` when `sampleCount >= warmupThreshold` (default 30)
- Never generate alerts, anomalies, or predictions before the baseline is warm
- During cold start, still update mean/variance to build the baseline

### Z-Score Anomaly Detection (O(1))

```javascript
var zScore = (newValue - mean) / Math.sqrt(variance);
var isAnomaly = Math.abs(zScore) > threshold; // default 3.0
var isCritical = Math.abs(zScore) > criticalThreshold; // default 4.0
```

### Linear Regression Trend (O(n), n ≤ 60)

```javascript
var slope = (n * sumXY - sumX * sumY) / (n * sumXX - sumX * sumX);
```

### Breach Prediction (O(1))

```javascript
var stepsToLimit = (limit - mean) / slope;
var minutesToBreach = stepsToLimit * frequencyMinutes;
```

### Pearson Correlation (O(n×m²))

Cross-asset correlation using hourly aggregated data.

### Forecast Projection

```
Projection:      forecast(t) = ewmaMean + trendSlope × t
Confidence:      ±Z × √(ewmaVariance × (1 + t / sampleCount))
Breach time:     t_breach = (limit - ewmaMean) / trendSlope
Confidence:      min(sampleCount/500, 1.0) × cv_penalty
```

→ See `references/algorithms.md` for complete algorithm implementations with JavaScript code.

---

## GraphQL Mutation Patterns

### Delete Mutations

Use `payload` array with `.{ "where": { "id": id } }` mapping pattern -- NOT `_in` arrays:

```jsonata
/* WRONG — _in is not supported for delete: */
{ "where": { "id": { "_in": $deleteIds } } }

/* CORRECT — use .{} mapping to create individual delete operations: */
$deletedIds.{ "where": { "id": id } }
```

### Create Mutations

Each record must be wrapped in a `"create"` object:

```jsonata
$records.{
  "create": {
    "field1": field1,
    "field2": field2,
    "createdAt": $now()
  }
}
```

### Upsert Mutations

Upsert requires three fields: `"where"`, `"create"`, and `"update"`:

```jsonata
$records.{
  "where": { "compositeKey": compositeKey },
  "create": {
    "compositeKey": compositeKey,
    "field1": field1
  },
  "update": {
    "field1": field1
  }
}
```

### Negation Pattern

There is no `_nin` operator. Use `_not` with `_in`:

```graphql
where: {
  status: { _not: { _in: ["DELETED", "ARCHIVED"] } }
}
```

---

## TelemetryHourly Optimization

For batch/correlation analysis, **always query `TelemetryHourly` instead of `TelemetryRaw`:**

- Pre-aggregated: `avg`, `min`, `max`, `std`, `count`, `sum`, percentiles
- 193 datapoints × 24 hours ≈ 4,600 records vs ~417,000 raw records
- No time bucketing needed — keyed by `periodAt` (already hourly aligned)
- Dramatically reduces both query volume and JS processing time

```graphql
query GetHourlyData($where: TelemetryHourlyWhereInput) {
  telemetryHourly(where: $where) {
    edges {
      node {
        id dataPointId assetId periodAt avg min max std count
      }
    }
  }
}
```

---

## Configuration via Settings

Store ALL ML parameters in application Settings, not hardcoded in flows:

| Setting Code | Default | Description |
|---|---|---|
| `ml.baseline.warmupThreshold` | `30` | Samples before baseline is "warm" |
| `ml.baseline.globalAlpha` | `0.1` | Default EWMA smoothing factor |
| `ml.anomaly.zScoreThreshold` | `3.0` | Z-score for anomaly detection |
| `ml.anomaly.criticalZScore` | `4.0` | Z-score for critical severity |
| `ml.trend.windowSize` | `60` | Samples for trend regression window |
| `ml.trend.slopeThreshold` | `0.01` | Min slope to qualify as "trending" |
| `ml.correlation.minCoefficient` | `0.7` | Min correlation for storage |
| `ml.correlation.strongThreshold` | `0.9` | Strong correlation threshold |
| `ml.forecast.horizonSteps` | `60` | Projection steps per forecast |
| `ml.forecast.confidenceZ` | `2.0` | Z-score for confidence bands (95%) |
| `ml.forecast.stabilityThreshold` | `0.001` | Slope below this = "stable" |
| `ml.forecast.maxBreachHorizonMinutes` | `1440` | Max lookahead for breach (24h) |

→ See `references/settings-catalog.md` for complete settings reference.

---

## Validation Checklist

### Data Model Validation

- [ ] `id` field is first, `ID!`, `unique: true`, `update.include: false`
- [ ] Every relation has both FK field and relation field
- [ ] Module and ModuleGroup in data array with `insertEarly: true`
- [ ] TTL index on `createdAt` for transactional models
- [ ] `dataChangeCapture: false` for high-volume models

### Flow Validation

- [ ] No `.toFixed()` anywhere in JS transforms
- [ ] No variable scoping issues in loops (inline expressions)
- [ ] All JS starts with `'use strict';`
- [ ] Guard nodes before mutations to prevent empty payloads
- [ ] Settings queried via System API, not hardcoded
- [ ] Default values for all settings (graceful degradation)
- [ ] `variablesTransform: "{}"` on queries with no dynamic parameters
- [ ] Correct API: `"api": "application"` vs `"api": "system"`

### Screen Validation

- [ ] ROOT has module/moduleGroup/dataModel/template in custom
- [ ] Table has `filterFormName` pointing to filter form
- [ ] All query fields include relation paths (e.g., `asset.code`)
- [ ] Datetime columns have `timeZone: "setting"`
- [ ] Float columns have `sortAsNumber: true`

→ See `references/validation-checklist.md` for the expanded checklist.

---

## Common Pitfalls & Debugging

| Error / Symptom | Cause | Fix |
|---|---|---|
| `"cannot read properties of toFixed"` | `.toFixed()` not supported in Fuuz JS runtime | Use `Math.round(val * 10000) / 10000` |
| `"X is not defined"` inside a loop | Variable scoping unreliable in loops | Inline the expression instead of assigning to a variable |
| Empty mutation results (nothing written) | No guard node before mutation — empty payload sent | Add guard node: `$count($.modelName.edges.node) = 0` |
| Delete mutation `"invalid payload"` | Using `_in` array pattern for delete | Use `.{ "where": { "id": id } }` mapping pattern |
| Query returns empty but data exists | Wrong `api` field on query node | Check `"api": "application"` vs `"api": "system"` |
| Context values undefined in JS transform | Used `setContext` but values not available | Use `mergeContext` or verify context path; `$state.context` survives broadcast |
| `"unexpected token"` in JS | Used ES6+ syntax (let, const, =>, template literals) | Rewrite using `var`, `function(){}`, string concatenation |
| Forecast shows NaN | Division by zero in slope calculation | Add guard: `if (denominator === 0) { slope = 0; }` |
| Correlation always returns 0 | Using TelemetryRaw instead of TelemetryHourly | Switch to TelemetryHourly for batch analysis |

---

## Life Sciences / Bioprocess Asset Context

> **Note:** Domain-specific asset context (e.g., bioreactor SUB-250, chromatography CHR-01, TFF-300, summation vessel SUM-500) should be configured per-deployment in the **fuuz-industrial-ops** skill, not hardcoded in the ML telemetry skill. The ML pipeline is equipment-agnostic -- it processes telemetry data from any asset type using the same statistical algorithms. Refer to **fuuz-industrial-ops** for equipment-specific configurations, downtime note patterns, and asset hierarchy definitions.

---

## Resources

### Reference Files in This Skill

| File | Content |
|------|---------|
| `references/algorithms.md` | Complete ML algorithm implementations: EWMA, Z-score, linear regression, Pearson correlation, forecast projection, breach prediction |
| `references/telemetry-patterns.md` | TelemetryHourly optimization, real-time vs batch design, parallel mutation paths, flow node patterns |
| `references/data-models.md` | TelemetryBaseline, PatternInsight, CorrelationPair, TelemetryForecast field definitions |
| `references/settings-catalog.md` | Complete ML settings reference with defaults and descriptions |
| `references/validation-checklist.md` | Pre-delivery validation checklist for data models, flows, and screens |

### Related Skills

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| **fuuz-schema** | Design ML data models | Creating TelemetryBaseline, PatternInsight, Forecast models |
| **fuuz-flows** | Build ML processing flows | Implementing EWMA, anomaly detection, correlation flows |
| **fuuz-screens** | Create analytics interfaces | Building forecast dashboards, anomaly tables, correlation displays |
| **fuuz-platform** | Platform reference | Looking up system settings, connectors |
| **fuuz-industrial-ops** | Industrial operations | Combining ML with alarm management, OEE tracking |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.1 | February 2026 | Added JS runtime limitations, standard flow node pattern, parallel mutation paths, GraphQL mutation patterns, EWMA cold start handling, common pitfalls table, domain-specific asset context note |
| 1.0 | February 2026 | Initial release — ML algorithms, telemetry patterns, data models, settings, validation checklists |
