# ML Telemetry Data Models

## TelemetryBaseline

Running EWMA statistics per datapoint. **Persistent** (no TTL) — these are the learning memory.

| Field | Type | Description |
|---|---|---|
| `id` | ID! | Unique identifier |
| `dataPointId` | ID! | FK to DataPoint |
| `assetId` | ID! | FK to Asset (denormalized for query efficiency) |
| `ewmaMean` | Float! | Current exponentially weighted mean |
| `ewmaVariance` | Float! | Current exponentially weighted variance |
| `sampleCount` | Int! | Total samples processed |
| `isWarm` | Boolean! | Whether baseline has enough samples (≥ warmupThreshold) |
| `frequency` | Float | Collection frequency in minutes |
| `alpha` | Float | Current EWMA smoothing factor |
| `lastValue` | Float | Most recent value processed |
| `trendSlope` | Float | Current linear regression slope |
| `trendDirection` | String | "rising", "falling", "stable" |
| `trendWindow` | JSON | Array of recent values for regression |

**Key metadata:**
- `dataChangeCapture: { "exposed": false }` — high-write model
- `customFields: { "exposed": false }`
- No TTL index — persistent learning data
- Unique composite index on `dataPointId`

---

## PatternInsight

Detected anomalies, trends, and predictions. **TTL: 90 days.**

| Field | Type | Description |
|---|---|---|
| `id` | ID! | Unique identifier |
| `dataPointId` | ID! | FK to DataPoint |
| `assetId` | ID! | FK to Asset |
| `type` | String! | "anomaly", "trend", "breach_prediction", "peak", "valley" |
| `severity` | String! | "critical", "warning", "info" |
| `value` | Float | Triggering value |
| `zScore` | Float | Z-score at detection (for anomalies) |
| `mean` | Float | Baseline mean at time of detection |
| `stdDev` | Float | Baseline std dev at time of detection |
| `slope` | Float | Trend slope at time of detection |
| `direction` | String | "above", "below", "rising", "falling" |
| `limitType` | String | "HighLimit", "LowLimit", "HighWarning", "LowWarning" (for breaches) |
| `minutesToBreach` | Float | Predicted minutes until limit breach |
| `estimatedBreachAt` | DateTime | Predicted breach timestamp |
| `confidenceScore` | Float | 0-1 confidence rating |
| `detectedAt` | DateTime! | When the insight was generated |
| `notes` | String | Human-readable description |

**Key metadata:**
- TTL index: 90 days on `createdAt`
- `dataChangeCapture: { "exposed": false }`
- Composite index on `[dataPointId, type, detectedAt]`

---

## CorrelationPair

Cross-asset Pearson correlation results. **TTL: 30 days.**

| Field | Type | Description |
|---|---|---|
| `id` | ID! | Unique identifier |
| `aDataPointId` | ID! | FK to first DataPoint |
| `bDataPointId` | ID! | FK to second DataPoint |
| `aAssetId` | ID! | FK to first Asset |
| `bAssetId` | ID! | FK to second Asset |
| `aDataPoint` | DataPoint! | Relation to first DataPoint |
| `bDataPoint` | DataPoint! | Relation to second DataPoint |
| `aAsset` | Asset! | Relation to first Asset |
| `bAsset` | Asset! | Relation to second Asset |
| `coefficient` | Float! | Pearson correlation coefficient (-1 to 1) |
| `strength` | String! | "strong", "moderate", "weak" |
| `direction` | String! | "positive", "negative" |
| `sampleCount` | Int! | Number of hourly data points used |
| `windowStartAt` | DateTime! | Start of correlation window |
| `windowEndAt` | DateTime! | End of correlation window |
| `recordedAt` | DateTime! | When correlation was calculated |

**Key metadata:**
- TTL index: 30 days on `createdAt`
- Paired relation naming: `aDataPointId`/`bDataPointId` (NOT `dataPointAId`)
- `deletionReferenceBehavior: "allow"` for self-referential-like relations
- Composite index on `[aDataPointId, bDataPointId, recordedAt]`

---

## TelemetryForecast

Projected trajectories with confidence bands. **TTL: 30 days.**

| Field | Type | Description |
|---|---|---|
| `id` | ID! | Unique identifier |
| `dataPointId` | ID! | FK to DataPoint |
| `assetId` | ID! | FK to Asset |
| `forecastAt` | DateTime! | When this forecast was generated |
| `horizonSteps` | Int! | Number of projection steps |
| `horizonMinutes` | Float! | Total projection horizon in minutes |
| `currentMean` | Float! | Baseline mean at forecast time |
| `currentVariance` | Float! | Baseline variance at forecast time |
| `trendSlope` | Float! | Slope used for projection |
| `trendDirection` | String! | "rising", "falling", "stable" |
| `confidenceZ` | Float! | Z-score used for confidence bands |
| `confidenceScore` | Float! | Overall forecast confidence (0-1) |
| `projectionData` | JSON | Array of {step, minutesAhead, forecast, upperBand, lowerBand} |
| `breachPredictions` | JSON | Array of breach predictions (limitType, minutesToBreach, etc.) |
| `previousForecastId` | ID | FK to previous forecast (self-referential) |
| `previousForecast` | TelemetryForecast | Self-referential relation |
| `forecastError` | Float | MAE vs actual (populated on next forecast) |

**Key metadata:**
- TTL index: 30 days on `createdAt`
- Self-referential relation uses `deletionReferenceBehavior: "allow"`
- `projectionData` and `breachPredictions` are JSON fields (not in table display)
- Composite index on `[dataPointId, forecastAt]`

---

## Naming Convention Reminders

- **Timestamp fields** use `At` suffix: `forecastAt`, `recordedAt`, `windowStartAt`
- **Paired relations** use `a`/`b` prefix: `aDataPointId`, `bDataPointId`
- **Boolean fields** use `is`/`has` prefix: `isWarm`, `hasChildren`
- **Model names** are PascalCase singular: `TelemetryBaseline`, `PatternInsight`
