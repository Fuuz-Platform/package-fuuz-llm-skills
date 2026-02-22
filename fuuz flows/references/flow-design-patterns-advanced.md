# Fuuz Flow Design & Data Processing Patterns

## Overview
This skill provides guidance on designing and implementing Fuuz flows for industrial IoT, manufacturing operations, and OEE (Overall Equipment Effectiveness) calculations. Fuuz flows are event-driven data processing pipelines that handle telemetry ingestion, state management, production tracking, and real-time aggregations following ISO 22400 standards.

## When to Use This Skill
- Building Fuuz flows for manufacturing data processing
- Implementing OEE calculations and aggregations
- Creating telemetry generators and simulators
- Managing workcenter state transitions
- Processing production data and work orders
- Handling time-series data aggregations
- Implementing batch processing patterns
- Working with timezone-aware calculations

## Core Flow Components

### 1. Node Types

#### JavaScript Nodes
Execute custom JavaScript code for complex logic, calculations, and data transformations.

**Characteristics:**
- Access to `$` (payload) and `$state` (flow context)
- Can use `$moment()` for date/time operations
- Returns data to next node
- Use `'use strict';` at the top
- No access to Node.js modules (browser-like environment)

**Access Patterns:**
```javascript
// Current payload
var data = $;

// Flow state context
var contextData = $state.context.myData;

// Previous node's output
var previousResult = $.fieldName;
```

#### JSONata Nodes
Transform data using JSONata query language - ideal for simple mappings and filters.

**When to use:**
- Simple field mappings
- Filtering arrays
- Extracting nested data
- Format conversions
- NOT for complex calculations (use JavaScript instead)

**Common patterns:**
```jsonata
/* Extract nested fields */
$.workcenter.edges.node

/* Map and filter */
$.items[quantity > 0].{ "id": id, "qty": quantity }

/* Conditional logic */
status = "Active" ? "Running" : "Stopped"
```

#### GraphQL Query Nodes
Fetch data from Fuuz data models.

**Key points:**
- Returns data in `edges` -> `node` structure
- Always unwrap in subsequent nodes
- Use variables for dynamic filtering
- Specify `first: N` to limit results

#### Mutate Nodes
Create, update, or delete records in Fuuz data models.

**Payload Format:**
```javascript
{
  "payload": [
    { "create": { ...fields } }  // For creates
  ]
}

// Or for updates
{
  "payload": [
    {
      "where": { "id": "..." },
      "update": { ...fields }
    }
  ]
}
```

#### Array Nodes
Iterate over arrays, processing each item through downstream nodes.

**Configuration:**
- **Array Path:** `$` or `$.arrayField`
- **Item Variable:** Optional name for current item
- Downstream nodes execute once per array item

### 2. Data Flow Patterns

#### Pattern: Query -> Transform -> Mutate
```
GraphQL Query (get data)
  |
JavaScript/JSONata (transform)
  |
Mutate (save results)
```

#### Pattern: Batch Processing with Array Node
```
JavaScript (generate batches)
  |
Array Node (iterate batches)
  |
  +- Transform (prepare mutation)
  +- Mutate (process batch)
```

#### Pattern: Aggregation Pipeline
```
GraphQL Query (raw data with aggregates)
  |
JavaScript (batch by grouping key)
  |
Array Node (process each group)
  |
  +- JavaScript (calculate metrics)
  +- Mutate (save aggregated record)
```

## Critical Fuuz Flow Rules

### 1. Memory Wrapping for Array Processing

**CRITICAL ISSUE:** When JavaScript returns arrays that will be processed by Array nodes, you MUST wrap single items or risk undefined mutations.

**The Problem:**
```javascript
// If this returns empty or single item
return [item];  // Array node might pass undefined to mutation
```

**The Solution:**
```javascript
// ALWAYS wrap as nested array for Array node iteration
return [$.payload];  // Returns [] if undefined, avoids mutation errors
```

**Pattern Application:**
```javascript
// Bad - can cause "undefined" in mutations
return chunks;  // If chunks is [item], mutation gets undefined

// Good - safe for Array node
return chunks.length > 0 ? chunks : [];  // Returns [] for empty

// Best - wrap for safety
return [$.payload];  // Even undefined becomes [], not passed to mutation
```

### 2. JSONata Array Transformation Syntax

**CRITICAL:** Use correct syntax for iterating arrays in JSONata.
```jsonata
/* WRONG - collects values into arrays per field */
$.{
  "create": {
    "field1": field1,
    "field2": field2
  }
}
/* Result: { "field1": [val1, val2, val3], "field2": [val4, val5, val6] } */

/* RIGHT - iterates each element, creates separate objects */
$[].{
  "create": {
    "field1": field1,
    "field2": field2
  }
}
/* Result: [{ "create": {...} }, { "create": {...} }] */
```

**Rule:** `$[]` explicitly iterates over root array elements.

### 3. Mutation Payload Format

**Fuuz Standard:**
```javascript
{
  "payload": [
    { "create": { ...fields } },
    { "create": { ...fields } }
  ]
}
```

**NOT:**
```javascript
// WRONG - missing "create" wrapper
{
  "payload": [
    { ...fields },
    { ...fields }
  ]
}
```

**For Empty Payloads:**
```javascript
// Return empty array, not null
return { "payload": [] };
```

### 4. Timezone Handling

**CRITICAL:** All Fuuz timestamps are stored in UTC but represent local time.
```javascript
// Get tenant timezone
var timezone = $.setting.edges[0].node.defaultValue || 'UTC';

// Calculate in local time
var localMoment = $moment().tz(timezone);

// Convert to UTC for storage
var utcTimestamp = localMoment.utc().toISOString();
```

**Pattern: Calculate Previous Hour in Local Time**
```javascript
// Previous hour start (local)
var prevHourStart = $moment().tz(timezone).startOf('hour').subtract(1, 'hour');

// Previous hour end (local)
var prevHourEnd = $moment(prevHourStart).add(1, 'hour');

// Convert to UTC for database query
var startUTC = prevHourStart.utc().toISOString();
var endUTC = prevHourEnd.utc().toISOString();
```

**Pattern: Calculate Yesterday in Local Time**
```javascript
var yesterdayStart = $moment().tz(timezone).subtract(1, 'day').startOf('day');
var yesterdayEnd = $moment().tz(timezone).subtract(1, 'day').endOf('day');

var startUTC = yesterdayStart.utc().toISOString();
var endUTC = yesterdayEnd.utc().toISOString();
```

### 5. Duration Calculations

**Store durations as milliseconds (integer), not ISO strings:**
```javascript
function calculateDuration(startTimestamp, endTimestamp) {
  var startMs = $moment(startTimestamp).valueOf();
  var endMs = $moment(endTimestamp).valueOf();

  var durationMs = endMs - startMs;
  if (durationMs < 0) durationMs = 0;

  return durationMs;  // Return pure milliseconds
}
```

**NOT:**
```javascript
// WRONG - don't use ISO duration strings
var duration = $momentDuration(diffMs).toISOString();  // "PT1H30M"
```

### 6. Batch Processing Strategy

**When processing thousands of records:**

1. **Generate all records in JavaScript**
2. **Chunk into batches** (typically 1000 per batch)
3. **Return first batch only** (or use Array node)
4. **Process sequentially**
```javascript
// Generate all records
var allRecords = [];
for (var i = 0; i < dataPoints.length; i++) {
  // ... generate records
  allRecords.push(record);
}

// Chunk into batches
var CHUNK_SIZE = 1000;
var chunks = [];
for (var i = 0; i < allRecords.length; i += CHUNK_SIZE) {
  chunks.push(allRecords.slice(i, i + CHUNK_SIZE));
}

// Return first chunk (or all chunks for Array node)
return chunks.length > 0 ? chunks[0] : [];
```

### 7. State Management in Flows

**Flow Context Pattern:**
```javascript
// Store in flow context (persists across nodes)
$state.context.activeWorkOrders = workOrders;

// Access in later nodes
var workOrders = $state.context.activeWorkOrders || [];
```

**Use Cases:**
- Store query results for later nodes
- Pass data between non-sequential nodes
- Maintain state across iterations

## ISO 22400 Standards Implementation

### 1. OEE Time Classification

**Three time buckets (mutually exclusive):**

1. **Run Time (Production Time)**
   - Mode flag: `countAsProductionTime = true`
   - Equipment actively producing
   - Used in: Availability, Performance, OEE

2. **Planned Downtime**
   - Mode flags: `countAsDowntime = true` AND `countAsPlannedDowntime = true`
   - Scheduled stops (maintenance, breaks, changeovers)
   - Excluded from OEE calculations

3. **Unplanned Downtime**
   - Mode flags: `countAsDowntime = true` AND `countAsPlannedDowntime = false`
   - Unscheduled stops (breakdowns, faults, starvation)
   - Reduces Availability

**Implementation:**
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

  return { runTime, plannedDowntime, unplannedDowntime };
}
```

### 2. OEE Performance Calculation

**ISO 22400 Formula:**
```
Performance = (Actual Output / Ideal Output) x 100%

Where:
- Actual Output = Total units produced (good + scrap)
- Ideal Output = Available Time / Ideal Cycle Time
```

**Implementation:**
```javascript
// Calculate ideal units based on time available
var idealCycleTimeMs = product.idealCycleTime || (product.standardCycleTime * 1000);
var cycleTimeMs = actualCycleTime * 1000;
var idealUnits = cycleTimeMs / idealCycleTimeMs;  // DO NOT ROUND

// Calculate performance
var actualOutput = quantityGood + quantityScrap;
var performance = idealUnits > 0 ? (actualOutput / idealUnits) * 100 : 0;
```

### 3. Ideal Cycle Time Fallback

**Always provide fallback for missing idealCycleTime:**
```javascript
// idealCycleTime is in milliseconds, standardCycleTime is in seconds
var idealCycleTimeMs;
if (product.idealCycleTime && product.idealCycleTime > 0) {
  idealCycleTimeMs = product.idealCycleTime;
} else {
  // Fallback: convert standard cycle time to milliseconds
  idealCycleTimeMs = product.standardCycleTime * 1000;
  console.log('Using standardCycleTime fallback: ' + idealCycleTimeMs + 'ms');
}
```

## Common Patterns

### Pattern 1: Telemetry Aggregation (Raw -> Hourly)
```javascript
// 1. Query raw telemetry with aggregates grouped by dataPoint
// 2. Transform aggregates into hourly records

// JSONata Transform:
$count($.telemetryRaw.edges) > 0 ? {
  "payload": $.telemetryRaw.edges.{
    "create": {
      "dataPointId": node.dataPointId,
      "periodAt": $$.periodAt,
      "count": node._aggregate.value.count,
      "min": node._aggregate.value.minimum[0],      /* Unwrap array */
      "max": node._aggregate.value.maximum[0],      /* Unwrap array */
      "avg": node._aggregate.value.average,
      "sum": node._aggregate.value.sum,
      "std": node._aggregate.value.standardDeviation,
      "first": node._aggregate.value.first[0],      /* Unwrap array */
      "last": node._aggregate.value.last[0],        /* Unwrap array */
      "p05": node._aggregate.value.percentile[0],
      "p25": node._aggregate.value.percentile[1],
      "p50": node._aggregate.value.percentile[2],
      "p75": node._aggregate.value.percentile[3],
      "p95": node._aggregate.value.percentile[4],
      "distinctCount": node._aggregate.value.uniqueCount
    }
  }
} : {
  "payload": []
}
```

### Pattern 2: Daily Aggregation (Hourly -> Daily)
```javascript
// 1. Query hourly records for yesterday
// 2. Batch by dataPointId
// 3. Aggregate each batch into single daily record

// Batch by DataPoint:
var groupedByDataPoint = {};
hourlyRecords.forEach(function(record) {
  var dpId = record.dataPointId;
  if (!groupedByDataPoint[dpId]) {
    groupedByDataPoint[dpId] = [];
  }
  groupedByDataPoint[dpId].push(record);
});

// Create batch per datapoint
var batches = [];
for (var dpId in groupedByDataPoint) {
  batches.push({
    dataPointId: dpId,
    periodAt: periodAt,
    telemetryHourly: {
      edges: groupedByDataPoint[dpId].map(function(r) {
        return { node: r };
      })
    }
  });
}
return batches;

// Then in Array node, aggregate:
var totalCount = hourlyRecords.reduce(function(sum, r) { return sum + r.count; }, 0);
var dailyMin = Math.min.apply(null, hourlyRecords.map(function(r) { return r.min; }));
var dailyMax = Math.max.apply(null, hourlyRecords.map(function(r) { return r.max; }));
var dailyAvg = totalCount > 0 ? totalSum / totalCount : 0;

// Pooled standard deviation
var varianceSum = 0;
hourlyRecords.forEach(function(r) {
  varianceSum += (r.std * r.std) * r.count;
});
var dailyStd = totalCount > 0 ? Math.sqrt(varianceSum / totalCount) : 0;
```

### Pattern 3: Production Simulation
```javascript
// 1. Get active work orders
// 2. Group by workcenterId
// 3. Simulate production for time window
// 4. Generate production records + work order updates

// Key calculations:
var totalUnitsProduced = Math.floor(timeAvailableSeconds / actualCycleTime);
var actualGood = Math.floor(totalUnitsProduced * yieldDecimal);
var actualScrap = totalUnitsProduced - actualGood;

// Ideal units calculation:
var cycleTimeMs = cycleTime * 1000;
var idealUnits = cycleTimeMs / idealCycleTimeMs;  // Don't round

// ISO 22400 performance:
var performance = idealUnits > 0 ? (totalUnitsProduced / idealUnits) * 100 : 0;
```

### Pattern 4: Workcenter State Changes
```javascript
// 1. Get open workcenter history records
// 2. Determine which workcenters should change state
// 3. Close old records with time classification
// 4. Create new records
// 5. Update workcenter current state

// Time classification when closing:
var durationMs = calculateDuration(record.occurAt, endAt);
var mode = modeById[record.modeId];
var timeClass = calculateTimeClassification(durationMs, mode);

// Update includes:
{
  "endAt": endAt,
  "duration": durationMs,
  "runTime": timeClass.runTime,
  "plannedDowntime": timeClass.plannedDowntime,
  "unplannedDowntime": timeClass.unplannedDowntime
}

// New records have null durations (unknown until closed):
{
  "occurAt": nowUTC,
  "modeId": newMode.id,
  "stateId": newState.id,
  "runTime": null,
  "plannedDowntime": null,
  "unplannedDowntime": null
}
```

### Pattern 5: Telemetry Generation with Quality Codes
```javascript
// OPC UA quality codes based on limits:
var quality = null;
var qualityCode = null;

if (dp.upperLimit != null && value > dp.upperLimit) {
  quality = "Bad";
  qualityCode = 16;  // Out of Range - High
} else if (dp.lowerLimit != null && value < dp.lowerLimit) {
  quality = "Bad";
  qualityCode = 16;  // Out of Range - Low
} else if (dp.upperWarning != null && value > dp.upperWarning) {
  quality = "Uncertain";
  qualityCode = 64;  // Warning - High
} else if (dp.lowerWarning != null && value < dp.lowerWarning) {
  quality = "Uncertain";
  qualityCode = 64;  // Warning - Low
}
// Good quality (192) - omit quality fields

// Only include quality when NOT good:
var record = {
  dataPointId: dp.id,
  recordedAt: timestamp,
  value: value
};

if (quality !== null) {
  record.quality = quality;
  record.qualityCode = qualityCode;
}
```

## Error Handling Patterns

### 1. Validation Before Processing
```javascript
function validateProductionData(workOrder, workcenterId) {
  var errors = [];

  if (!workcenterId) {
    errors.push('Missing workcenterId');
  }
  if (!workOrder.id) {
    errors.push('Missing workOrder.id');
  }
  if (!workOrder.product?.standardCycleTime) {
    errors.push('Missing product.standardCycleTime');
  }

  return errors;
}

// Use before processing:
var validationErrors = validateProductionData(workOrder, workcenterId);
if (validationErrors.length > 0) {
  console.error('Validation failed: ' + validationErrors.join(', '));
  return null;  // Skip this record
}
```

### 2. Null Safety in Data Access
```javascript
// Safe access with fallbacks
var edges = ($ && $.workcenter && $.workcenter.edges) || [];
var timezone = $.setting?.edges?.[0]?.node?.defaultValue || 'UTC';

// Safe iteration
edges.forEach(function(edge) {
  var node = edge?.node;
  if (!node) return;  // Skip if no node

  // Process node...
});
```

### 3. Early Exit Patterns
```javascript
// Check for empty data
if (!Array.isArray(records) || records.length === 0) {
  console.log('No records to process');
  return { "payload": [] };
}

// Check for missing required data
if (!mode) {
  console.error('Mode not found');
  return null;
}
```

### 4. Try-Catch for Lock Cleanup

**CRITICAL:** Use try-catch nodes BEFORE locks to ensure cleanup on downstream failure.
```
Try-Catch Node
  |
Lock Node (acquire)
  |
Process Data
  |
Unlock Node (release)
  |
(Catch path also releases lock)
```

**Wrap payload to avoid undefined:**
```javascript
// In catch/error handler
return [$.payload];  // Returns [] instead of undefined
```

## Performance Optimization

### 1. Broadcast Pattern for Scale

**Problem:** Loading 10,000 workcenter records = 10,000 schedule lookups

**Solution:** Broadcast current schedules by groupId
```javascript
// Pre-compute schedule map (1 query)
var currentScheduleByGroup = {};
schedules.forEach(function(s) {
  if (s.scheduleGroupId) {
    currentScheduleByGroup[s.scheduleGroupId] = s.id;
  }
});

// Use in workcenter processing (O(1) lookup)
var scheduleId = wc.scheduleGroupId
  ? currentScheduleByGroup[wc.scheduleGroupId]
  : null;
```

**Result:** 10,000 queries -> 1 query + 10,000 O(1) lookups

### 2. Pre-Population Strategy

Create skeleton records before data arrives:
```javascript
// Create empty OEE records for upcoming hour
for (var hour = 0; hour < 24; hour++) {
  oeeRecords.push({
    workcenterId: wc.id,
    periodAt: hourTimestamp,
    availability: 0,
    performance: 0,
    quality: 0,
    oee: 0
  });
}
```

### 3. Chunking Large Datasets
```javascript
var CHUNK_SIZE = 1000;

for (var i = 0; i < allRecords.length; i += CHUNK_SIZE) {
  var chunk = allRecords.slice(i, i + CHUNK_SIZE);
  chunks.push(chunk);
}

// Process chunks sequentially to avoid memory issues
```

### 4. Efficient Aggregations
```javascript
// Use GraphQL aggregations instead of JavaScript
query GetStats {
  telemetryRaw(groupBy: [dataPointId]) {
    edges {
      node {
        dataPointId
        _aggregate {
          value {
            count
            average
            minimum
            maximum
          }
        }
      }
    }
  }
}

// NOT: Load all raw data and calculate in JavaScript
```

## Debugging and Logging

### 1. Structured Console Logging
```javascript
console.log('=============================================================');
console.log('Production Simulation Complete');
console.log('=============================================================');
console.log('Input: ' + activeWorkOrders.length + ' work orders');
console.log('Output: ' + productionRecords.length + ' production records');
console.log('-------------------------------------------------------------');
console.log('Performance Stats:');
console.log('  Min: ' + minPerformance + '%');
console.log('  Max: ' + maxPerformance + '%');
console.log('  Avg: ' + avgPerformance + '%');
console.log('=============================================================');
```

### 2. Validation Issue Tracking
```javascript
var validationIssues = [];

if (errors.length > 0) {
  validationIssues.push({
    workcenterId: workcenterId,
    workOrderId: workOrder.id,
    issue: 'VALIDATION_FAILED',
    errors: errors
  });
}

// Include in output summary
return {
  productionRecords: productionRecords,
  validationIssues: validationIssues
};
```

### 3. Breakpoint Debugging

Enable breakpoints in node configuration:
```json
{
  "debug": {
    "breakpointEnabled": true
  }
}
```

Flow will pause at this node for inspection.

## Common Pitfalls and Solutions

### Pitfall 1: Incorrect Array Iteration
**Problem:** Using `$.{}` instead of `$[].{}`
**Result:** Fields become arrays instead of separate objects
**Solution:** Use `$[]` for explicit iteration

### Pitfall 2: Not Unwrapping Aggregate Arrays
**Problem:** Using `minimum` directly instead of `minimum[0]`
**Result:** Mutation error "Float cannot represent array"
**Solution:** Always unwrap: `minimum[0]`, `maximum[0]`, `first[0]`, `last[0]`

### Pitfall 3: Forgetting Empty Payload Handling
**Problem:** Returning `null` when no data
**Result:** Mutation fails
**Solution:** Return `{ "payload": [] }`

### Pitfall 4: Timezone Confusion
**Problem:** Using UTC for business logic
**Result:** Incorrect day/hour boundaries
**Solution:** Calculate in local timezone, convert to UTC for storage

### Pitfall 5: Rounding Ideal Units
**Problem:** Using `Math.floor(idealUnits)`
**Result:** Inaccurate performance calculations
**Solution:** Keep exact decimal value

### Pitfall 6: Missing Mutation Wrapper
**Problem:** Passing `{ ...fields }` instead of `{ "create": { ...fields } }`
**Result:** Mutation fails silently
**Solution:** Always wrap with `create` or `update`

### Pitfall 7: Sequence Collisions
**Problem:** "Mongo bulk write error" on create
**Result:** ID collision with existing sequence
**Solution:** Update sequence start value above existing max

### Pitfall 8: Missing Lock Cleanup
**Problem:** Downstream node fails after acquiring lock
**Result:** Lock never released, flow deadlocks
**Solution:** Use try-catch node before lock

## Best Practices Checklist

Flow Design:
- Use GraphQL for data fetching
- Use JavaScript for complex logic
- Use JSONata for simple transforms
- Batch process large datasets
- Return `[]` for empty payloads, never `null`
- Wrap arrays properly for Array nodes: `[$.payload]`

Data Processing:
- Validate data before processing
- Handle null/undefined gracefully
- Use early exit patterns
- Log validation issues
- Track processing statistics

Time Handling:
- Calculate in local timezone
- Store as UTC ISO strings
- Use milliseconds for durations
- Handle timezone conversions explicitly

OEE Calculations:
- Follow ISO 22400 standards
- Classify time correctly (run/planned/unplanned)
- Use idealCycleTime with fallback
- Don't round idealUnits
- Calculate performance as actual/ideal

Mutations:
- Wrap payloads: `{ "payload": [{ "create": {...} }] }`
- Unwrap aggregate arrays: `minimum[0]`
- Set null for unknown values (duration on open records)
- Use batch mutations for multiple records

Performance:
- Use broadcast patterns for large lookups
- Chunk large datasets (1000 per batch)
- Pre-populate skeleton records
- Aggregate in database, not JavaScript

Error Handling:
- Validate before processing
- Use try-catch before locks
- Return safe defaults
- Log all validation failures
- Include error context in output

## Summary

Fuuz flows are powerful for processing industrial IoT and manufacturing data when following these key principles:

1. **Proper data structures** - Wrap arrays, unwrap aggregates, format mutations correctly
2. **Timezone awareness** - Calculate in local, store in UTC
3. **ISO 22400 compliance** - Classify time correctly, calculate performance accurately
4. **Batch processing** - Handle large datasets efficiently
5. **Error handling** - Validate, log, and fail gracefully
6. **Performance** - Broadcast lookups, chunk data, aggregate in database

Following these patterns ensures reliable, scalable, and standards-compliant manufacturing data processing.