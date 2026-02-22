# Telemetry Pipeline Patterns

## TelemetryHourly Optimization

For batch/correlation analysis, **always prefer `TelemetryHourly` over `TelemetryRaw`:**

| Metric | TelemetryRaw | TelemetryHourly |
|---|---|---|
| Records per day (193 datapoints) | ~417,000 | ~4,600 |
| Pre-aggregated | No | Yes (avg, min, max, std, count, sum, percentiles) |
| Time bucketing needed | Yes (in JavaScript) | No (keyed by `periodAt`) |
| Query performance | Slow (large volume) | Fast (small volume) |
| JS processing time | High | Low |

### TelemetryHourly Query

```graphql
query GetHourlyData($where: TelemetryHourlyWhereInput) {
  telemetryHourly(where: $where, first: 10000) {
    edges {
      node {
        id dataPointId assetId periodAt
        avg min max std count sum
        p05 p25 p50 p75 p95
      }
    }
  }
}
```

---

## Standard Flow Node Pattern (8-12 Nodes)

Most ML backend flows follow this pattern:

```
Trigger → Query Settings → Set Context → Query Data →
Guard (unless empty) → JavaScript Transform → Guard (unless empty) →
Mutate (create/upsert/delete) → [Optional: Parallel paths]
```

### Guard Nodes

Prevent empty mutations with `unless` guard:

```jsonata
$count($.modelName.edges.node) = 0
```

The `unless` node **passes** when the condition is **false** (i.e., when there IS data).

---

## Parallel Mutation Paths

When a JavaScript transform returns multiple result types, use parallel paths:

```
JS Transform → Transform A (extract type 1) → Mutate A → Response
            → Transform B (extract type 2) → Mutate B → Response
```

Each transform uses JSONata conditional:

```jsonata
$.hasCorrelations ? {
  "payload": $.correlations.({ "create": { ... } })
} : { "payload": [] }
```

---

## Real-Time Flow Pattern (Flow 1)

**Trigger:** `dataChanges` on TelemetryRaw Create
**Purpose:** Per-value EWMA update, anomaly detection, trend analysis

```
DataChanges (TelemetryRaw Create) →
Set Raw Data in Context →
Query Settings (System API) →
Set Config in Context →
Query DataPoint + Baseline →
JavaScript: EWMA + Anomaly + Trend →
Guard (skip if no changes) →
Fork:
  → Upsert TelemetryBaseline
  → Create PatternInsight (if anomaly/trend detected)
Response
```

### Key Design Points

- O(1) per incoming record — no multi-record queries
- Baseline is read AND updated each invocation
- Use `upsert` for baseline (create on first run, update after)
- Only create PatternInsight records for actual detections
- Settings queried once at flow start, stored in context

---

## Batch Correlation Flow (Flow 2)

**Trigger:** `schedule` (hourly)
**Purpose:** Cross-asset Pearson correlation

```
Schedule →
Query Settings →
Set Context →
Query TelemetryHourly (last 24h) →
JavaScript: Build time-aligned series + Correlations →
Guard (skip if no significant correlations) →
Fork:
  → Delete old CorrelationPairs →
  → Create new CorrelationPairs
Response
```

### Key Design Points

- Uses `TelemetryHourly` (not raw) for efficiency
- Groups data by `periodAt` for time alignment
- Only stores pairs above minimum coefficient threshold
- Deletes previous results before creating new ones (rolling window)

---

## Forecast Projection Flow (Flow 3)

**Trigger:** `schedule` (hourly)
**Purpose:** Forward-looking projections and breach prediction

```
Schedule →
Query Settings →
Set Context →
Query All Warm Baselines →
Guard (skip if none warm) →
JavaScript: Project + Breach check + Accuracy →
Guard (skip if no results) →
Fork:
  → Create TelemetryForecast records
  → Create PatternInsight (breach predictions)
Response
```

### Key Design Points

- Only processes baselines where `isWarm = true`
- Calculates forecast accuracy by comparing previous forecast to actual
- Uses confidence scoring to weight predictions
- Breach predictions check all 4 limit types per datapoint

---

## Configuration Query Pattern

```graphql
query GetSettings {
  setting(where: { code: { _in: [
    "ml.baseline.warmupThreshold",
    "ml.baseline.globalAlpha",
    "ml.anomaly.zScoreThreshold",
    "ml.anomaly.criticalZScore",
    "ml.trend.windowSize",
    "ml.trend.slopeThreshold"
  ] } }) {
    edges { node { id code value } }
  }
}
```

**Parse with defaults:**

```jsonata
(
  $settings := $.setting.edges.node;
  $lookup := function($code, $default) {
    $val := $settings[code = $code].value;
    $val ? $number($val) : $default
  };
  {
    "config": {
      "warmupThreshold": $lookup('ml.baseline.warmupThreshold', 30),
      "globalAlpha": $lookup('ml.baseline.globalAlpha', 0.1),
      "zScoreThreshold": $lookup('ml.anomaly.zScoreThreshold', 3.0),
      "criticalZScore": $lookup('ml.anomaly.criticalZScore', 4.0),
      "trendWindowSize": $lookup('ml.trend.windowSize', 60),
      "slopeThreshold": $lookup('ml.trend.slopeThreshold', 0.01)
    }
  }
)
```

---

## Common Pitfalls

| Symptom | Cause | Fix |
|---|---|---|
| "cannot read properties of toFixed" | `.toFixed()` used | Use `Math.round(val * 10000) / 10000` |
| "X is not defined" in loop | Variable scoping bug | Inline the expression |
| Empty mutation results | Guard node missing | Add `unless`: `$count($.model.edges.node) = 0` |
| Delete mutation fails | Wrong variablesTransform | Use `.{ "where": { "id": id } }` (no `[]`) |
| Query empty but data exists | Wrong API | Check `"api"` field |
| Context undefined in JS | setContext vs mergeContext | Use `mergeContext` when prior values needed |
| Forecast accuracy is null | No previous forecast | Expected on first run |
