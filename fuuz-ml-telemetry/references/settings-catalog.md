# ML Telemetry Settings Catalog

## Settings Query Pattern

All ML settings are stored as application Settings (System API) with `ml.` prefix:

```graphql
query GetMLSettings {
  setting(where: { code: { _in: [
    "ml.baseline.warmupThreshold",
    "ml.baseline.globalAlpha",
    "ml.anomaly.zScoreThreshold",
    "ml.anomaly.criticalZScore",
    "ml.trend.windowSize",
    "ml.trend.slopeThreshold",
    "ml.correlation.minCoefficient",
    "ml.correlation.strongThreshold",
    "ml.forecast.horizonSteps",
    "ml.forecast.confidenceZ",
    "ml.forecast.stabilityThreshold",
    "ml.forecast.maxBreachHorizonMinutes"
  ] } }) {
    edges { node { id code value } }
  }
}
```

## Complete Settings Reference

### Baseline Settings

| Setting Code | Default | Type | Description |
|---|---|---|---|
| `ml.baseline.warmupThreshold` | `30` | Int | Minimum samples before baseline is considered "warm" and ready for anomaly detection |
| `ml.baseline.globalAlpha` | `0.1` | Float | Default EWMA smoothing factor. Higher = more responsive to recent values, lower = more stable |

### Anomaly Detection Settings

| Setting Code | Default | Type | Description |
|---|---|---|---|
| `ml.anomaly.zScoreThreshold` | `3.0` | Float | Z-score threshold for warning-level anomaly detection |
| `ml.anomaly.criticalZScore` | `4.0` | Float | Z-score threshold for critical-level anomaly detection |

### Trend Analysis Settings

| Setting Code | Default | Type | Description |
|---|---|---|---|
| `ml.trend.windowSize` | `60` | Int | Number of recent samples used for linear regression trend window |
| `ml.trend.slopeThreshold` | `0.01` | Float | Minimum absolute slope value to classify as "trending" (vs "stable") |

### Correlation Settings

| Setting Code | Default | Type | Description |
|---|---|---|---|
| `ml.correlation.minCoefficient` | `0.7` | Float | Minimum absolute Pearson coefficient to store a correlation pair |
| `ml.correlation.strongThreshold` | `0.9` | Float | Coefficient above which correlation is classified as "strong" |

### Forecast Settings

| Setting Code | Default | Type | Description |
|---|---|---|---|
| `ml.forecast.horizonSteps` | `60` | Int | Number of projection steps per forecast (each step = 1 collection interval) |
| `ml.forecast.confidenceZ` | `2.0` | Float | Z-score for confidence band width (2.0 = ~95% confidence interval) |
| `ml.forecast.stabilityThreshold` | `0.001` | Float | Slope below this is classified as "stable" (no significant trend) |
| `ml.forecast.maxBreachHorizonMinutes` | `1440` | Int | Maximum lookahead for breach predictions (1440 = 24 hours) |

## Parse Pattern with Defaults

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
      "slopeThreshold": $lookup('ml.trend.slopeThreshold', 0.01),
      "minCoefficient": $lookup('ml.correlation.minCoefficient', 0.7),
      "strongThreshold": $lookup('ml.correlation.strongThreshold', 0.9),
      "horizonSteps": $lookup('ml.forecast.horizonSteps', 60),
      "confidenceZ": $lookup('ml.forecast.confidenceZ', 2.0),
      "stabilityThreshold": $lookup('ml.forecast.stabilityThreshold', 0.001),
      "maxBreachHorizonMinutes": $lookup('ml.forecast.maxBreachHorizonMinutes', 1440)
    }
  }
)
```

## Adding New Settings

To add new ML settings:

1. Create the Setting record in the System API:
   ```graphql
   mutation CreateSetting($payload: [SettingCreatePayloadInput!]!) {
     createSetting(payload: $payload) { id code value }
   }
   ```

2. Add the setting code to the query `_in` array in the flow's settings query node

3. Add the `$lookup()` call in the parse transform with appropriate default

4. Access via `$state.context.config.newSettingName` in downstream nodes

## Design Principles

1. **Always provide defaults** — Flows must work even if settings don't exist yet
2. **Use `$number()` for parsing** — Settings values are stored as strings
3. **Query once, use everywhere** — Query all settings at flow start, store in context
4. **Prefix consistently** — Use `ml.category.settingName` pattern
5. **Document in code** — Include setting descriptions in node descriptions
