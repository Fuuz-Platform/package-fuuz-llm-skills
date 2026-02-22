# ML Algorithm Reference

## Algorithm Overview

| Algorithm | Type | Complexity | Flow | Purpose |
|---|---|---|---|---|
| EWMA Baseline | Self-learning | O(1)/value | Flow 1 (real-time) | Adapting mean/variance |
| Z-Score Anomaly | Detection | O(1) | Flow 1 | Statistical outlier detection |
| Linear Regression | Trend | O(n), n≤60 | Flow 1 | Drift direction & rate |
| Extrapolation | Predictive | O(1) | Flow 1 | Time-to-limit-breach |
| Peak/Valley | Cyclical | O(n), n≤60 | Flow 1 | Oscillation pattern detection |
| Pearson Correlation | Cross-asset | O(n×m²) | Flow 2 (hourly) | Inter-asset dependencies |
| Forecast Projection | Predictive | O(k)/dp | Flow 3 (hourly) | Future trajectory with confidence |
| Breach Prediction | Predictive | O(4)/dp | Flow 3 | Time-to-breach for all limits |
| Forecast Accuracy | Self-calibrating | O(1)/dp | Flow 3 | Retrospective error tracking |

---

## 1. EWMA Baseline (Flow 1 — Real-Time)

### Purpose
Maintain a running statistical baseline for each datapoint that adapts to gradual process changes while detecting sudden anomalies.

### Implementation

```javascript
'use strict';

// Inputs
var newValue = $state.context.rawData.value.after.value;
var baseline = $.telemetryBaseline.edges[0].node; // or null if first run
var config = $state.context.config;

// Determine alpha from collection frequency
var frequency = baseline ? baseline.frequency : 5; // minutes
var alpha = 2 / (Math.round(60 / frequency) + 1);

// Cold start — initialize baseline
if (!baseline || !baseline.isWarm) {
  var count = baseline ? baseline.sampleCount + 1 : 1;
  var warmupThreshold = config.warmupThreshold || 30;

  // Running mean/variance during warmup
  var runMean = baseline ? baseline.ewmaMean : newValue;
  var runVar = baseline ? baseline.ewmaVariance : 0;

  if (count > 1) {
    runMean = alpha * newValue + (1 - alpha) * runMean;
    var diff = newValue - baseline.ewmaMean;
    runVar = (1 - alpha) * (runVar + alpha * diff * diff);
  }

  return {
    ewmaMean: runMean,
    ewmaVariance: runVar,
    sampleCount: count,
    isWarm: count >= warmupThreshold,
    lastValue: newValue
  };
}

// Warm baseline — full EWMA update
var oldMean = baseline.ewmaMean;
var oldVariance = baseline.ewmaVariance;

var newMean = alpha * newValue + (1 - alpha) * oldMean;
var diff = newValue - oldMean;
var newVariance = (1 - alpha) * (oldVariance + alpha * diff * diff);

return {
  ewmaMean: Math.round(newMean * 10000) / 10000,
  ewmaVariance: Math.round(newVariance * 10000) / 10000,
  sampleCount: baseline.sampleCount + 1,
  isWarm: true,
  lastValue: newValue,
  previousMean: oldMean,
  previousVariance: oldVariance
};
```

---

## 2. Z-Score Anomaly Detection (Flow 1 — Real-Time)

### Purpose
Detect statistical outliers by measuring how many standard deviations a value is from the baseline mean.

### Implementation

```javascript
// Requires warm baseline
if (!baseline.isWarm) {
  return { skip: true, reason: 'Baseline not warm' };
}

var mean = baseline.ewmaMean;
var variance = baseline.ewmaVariance;
var stdDev = Math.sqrt(variance);

// Avoid division by zero
if (stdDev < 0.0001) {
  return { skip: true, reason: 'Variance too low for detection' };
}

var zScore = (newValue - mean) / stdDev;
var absZ = Math.abs(zScore);

var zThreshold = config.zScoreThreshold || 3.0;
var criticalZ = config.criticalZScore || 4.0;

var isAnomaly = absZ > zThreshold;
var severity = absZ > criticalZ ? 'critical' : absZ > zThreshold ? 'warning' : 'normal';

if (isAnomaly) {
  return {
    type: 'anomaly',
    severity: severity,
    zScore: Math.round(zScore * 1000) / 1000,
    value: newValue,
    mean: mean,
    stdDev: Math.round(stdDev * 10000) / 10000,
    direction: zScore > 0 ? 'above' : 'below'
  };
}
```

---

## 3. Linear Regression Trend (Flow 1 — Real-Time)

### Purpose
Detect drift direction and rate using a sliding window of recent values.

### Implementation

```javascript
// Use recent values from baseline's trend window (last N values)
var window = baseline.trendWindow || [];
window.push(newValue);
if (window.length > (config.trendWindowSize || 60)) {
  window = window.slice(window.length - (config.trendWindowSize || 60));
}

var n = window.length;
if (n < 10) {
  return { trendWindow: window, slope: 0, direction: 'insufficient_data' };
}

// Simple linear regression: y = mx + b
var sumX = 0, sumY = 0, sumXY = 0, sumXX = 0;
for (var i = 0; i < n; i++) {
  sumX += i;
  sumY += window[i];
  sumXY += i * window[i];
  sumXX += i * i;
}

var slope = (n * sumXY - sumX * sumY) / (n * sumXX - sumX * sumX);
var slopeThreshold = config.slopeThreshold || 0.01;

var direction = Math.abs(slope) < slopeThreshold ? 'stable' :
                slope > 0 ? 'rising' : 'falling';

return {
  trendWindow: window,
  slope: Math.round(slope * 100000) / 100000,
  direction: direction,
  windowSize: n
};
```

---

## 4. Breach Prediction (Flow 1/3)

### Purpose
Predict when a trending value will exceed its configured limits.

### Implementation

```javascript
function predictBreach(mean, slope, limit, frequency) {
  if (Math.abs(slope) < 0.0001) return null; // stable, no breach

  var stepsToLimit = (limit - mean) / slope;
  if (stepsToLimit <= 0) return null; // already past or moving away

  var minutesToBreach = stepsToLimit * frequency;
  var maxHorizon = config.maxBreachHorizonMinutes || 1440; // 24h

  if (minutesToBreach > maxHorizon) return null; // too far out

  return {
    minutesToBreach: Math.round(minutesToBreach),
    stepsToLimit: Math.round(stepsToLimit),
    limit: limit,
    estimatedBreachAt: $moment().add(Math.round(minutesToBreach), 'minutes').toISOString()
  };
}

// Check all 4 limits
var breaches = [];
var dp = $state.context.dataPoint;

if (dp.upperLimit != null) {
  var b = predictBreach(mean, slope, dp.upperLimit, dp.frequency);
  if (b) { b.limitType = 'HighLimit'; breaches.push(b); }
}
if (dp.lowerLimit != null) {
  var b = predictBreach(mean, slope, dp.lowerLimit, dp.frequency);
  if (b) { b.limitType = 'LowLimit'; breaches.push(b); }
}
if (dp.upperWarning != null) {
  var b = predictBreach(mean, slope, dp.upperWarning, dp.frequency);
  if (b) { b.limitType = 'HighWarning'; breaches.push(b); }
}
if (dp.lowerWarning != null) {
  var b = predictBreach(mean, slope, dp.lowerWarning, dp.frequency);
  if (b) { b.limitType = 'LowWarning'; breaches.push(b); }
}
```

---

## 5. Forecast Projection (Flow 3 — Hourly)

### Mathematics

```
Projection:        forecast(t) = ewmaMean + trendSlope × t
Confidence bands:  ±Z × √(ewmaVariance × (1 + t / sampleCount))
Breach detection:  t_breach = (limit - ewmaMean) / trendSlope
Breach minutes:    t_breach × collectionFrequency
Confidence score:  min(sampleCount/500, 1.0) × cv_penalty
  where cv_penalty = √variance/|mean| > 1.0 → 0.5, > 0.5 → 0.75, else 1.0
```

### Implementation

```javascript
var horizonSteps = config.horizonSteps || 60;
var confidenceZ = config.confidenceZ || 2.0;

var projections = [];
for (var t = 1; t <= horizonSteps; t++) {
  var forecast = mean + slope * t;
  var uncertainty = confidenceZ * Math.sqrt(variance * (1 + t / sampleCount));

  projections.push({
    step: t,
    minutesAhead: t * frequency,
    forecast: Math.round(forecast * 10000) / 10000,
    upperBand: Math.round((forecast + uncertainty) * 10000) / 10000,
    lowerBand: Math.round((forecast - uncertainty) * 10000) / 10000
  });
}

// Confidence score
var cvPenalty = 1.0;
var cv = Math.abs(mean) > 0.001 ? Math.sqrt(variance) / Math.abs(mean) : 0;
if (cv > 1.0) cvPenalty = 0.5;
else if (cv > 0.5) cvPenalty = 0.75;

var confidenceScore = Math.min(sampleCount / 500, 1.0) * cvPenalty;
```

---

## 6. Pearson Correlation (Flow 2 — Hourly Batch)

### Purpose
Detect cross-asset dependencies using hourly aggregated data.

### Implementation Notes
- Query `TelemetryHourly` for the correlation window (e.g., last 24 hours)
- Group by `periodAt` to align time series
- Calculate Pearson coefficient for each unique pair of datapoints
- Only store pairs with `|r| > minCoefficient` (default 0.7)
- O(n×m²) where n=hours and m=datapoints — appropriate for hourly batch, not real-time

```javascript
function pearsonCorrelation(x, y) {
  var n = Math.min(x.length, y.length);
  if (n < 5) return null;

  var sumX = 0, sumY = 0, sumXY = 0, sumXX = 0, sumYY = 0;
  for (var i = 0; i < n; i++) {
    sumX += x[i]; sumY += y[i];
    sumXY += x[i] * y[i];
    sumXX += x[i] * x[i];
    sumYY += y[i] * y[i];
  }

  var denom = Math.sqrt((n * sumXX - sumX * sumX) * (n * sumYY - sumY * sumY));
  if (denom < 0.0001) return null;

  return (n * sumXY - sumX * sumY) / denom;
}
```

---

## JavaScript Runtime Reminders

- **No `.toFixed()`** — Use `Math.round(val * 10000) / 10000`
- **No `let`/`const`** — Use `var`
- **No arrow functions** — Use `function(x) { return x; }`
- **Inline loop variables** — Don't declare variables inside loops that are used once
- **Always `'use strict';`** at top of scripts
