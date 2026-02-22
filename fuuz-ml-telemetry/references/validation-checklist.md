# ML Telemetry Validation Checklist

Use this checklist before delivering any ML telemetry artifact.

## Data Model Checklist

- [ ] `id` field is first, `ID!`, `unique: true`, `update.include: false`
- [ ] Every relation has both FK field (`assetId`) AND relation field (`asset`)
- [ ] FK → relation name mapping is correct (`from: "assetId"`, `to: "id"`)
- [ ] Module and ModuleGroup are in data array with `insertEarly: true`
- [ ] TTL index on `createdAt` for transactional models (PatternInsight: 90d, CorrelationPair: 30d, TelemetryForecast: 30d)
- [ ] `dataChangeCapture: { "exposed": false }` for high-volume models
- [ ] `customFields: { "exposed": false }` for high-volume models
- [ ] TelemetryBaseline has NO TTL (persistent learning data)
- [ ] Self-referential relations use `deletionReferenceBehavior: "allow"`
- [ ] Paired relations use `a`/`b` prefix: `aDataPointId`/`bDataPointId`
- [ ] Timestamp fields use `At` suffix: `forecastAt`, `recordedAt`, `windowStartAt`
- [ ] Boolean fields use `is`/`has` prefix: `isWarm`, `hasChildren`

## Flow Checklist

### JavaScript Runtime

- [ ] No `.toFixed()` anywhere (use `Math.round(val * N) / N`)
- [ ] No `let` / `const` (use `var`)
- [ ] No arrow functions `=>` (use `function() {}`)
- [ ] No template literals `` `${var}` `` (use string concatenation `+`)
- [ ] No destructuring `{ a, b } = obj`
- [ ] No spread operator `...`
- [ ] All JS starts with `'use strict';`
- [ ] No variable declarations inside loop bodies that are used only once (inline them)

### Flow Architecture

- [ ] Real-time flows are O(1) per incoming record
- [ ] Batch calculations use `TelemetryHourly`, not `TelemetryRaw`
- [ ] Guard nodes (`unless`) before every mutation to prevent empty payloads
- [ ] Settings queried via System API at flow start, not hardcoded
- [ ] Default values provided for all settings (graceful degradation)
- [ ] `variablesTransform: "{}"` on queries with no dynamic parameters
- [ ] Correct API specified: `"api": "application"` vs `"api": "system"`
- [ ] Context captured early with `setContext`/`mergeContext`
- [ ] `setContext` vs `mergeContext` used correctly (replace vs add)
- [ ] Descriptive names on all nodes (not "Transform" or "Query")

### Mutation Patterns

- [ ] All mutation payloads wrapped in arrays: `[{ "create": ... }]`
- [ ] Delete mutations use `.{ "where": { "id": id } }` per-record wrapping
- [ ] Upsert mutations have both `create` and `update` blocks
- [ ] JSONata mapping uses `.{}` notation (not `[]` brackets)
- [ ] Mutation type names are PascalCase: `TelemetryBaselineUpsertPayloadInput`

## Screen Checklist

- [ ] ROOT has `module`/`moduleGroup`/`dataModel`/`template` in custom
- [ ] Brand gradient separator: `linear-gradient(90deg, #39005a, #5b30df, #03caaf)`
- [ ] Filter panel: ResizablePanel → Form → Selects → DateRange → GraphQL (last)
- [ ] Table has `filterFormName` pointing to the Form element
- [ ] All table query `fields` include relation paths (e.g., `asset.code`, `dataPoint.code`)
- [ ] Datetime columns have `timeZone: "setting"`
- [ ] Float columns have `sortAsNumber: true` and `formatString`
- [ ] Menu column `dataPath` is empty string `""` (not a model field name)
- [ ] CellTransform color coding for severity/trend direction

## Cross-Model Field Coverage

- [ ] Every non-JSON scalar field appears in the table query `fields` array
- [ ] JSON fields (`projectionData`, `breachPredictions`) excluded from table, included in detail views
- [ ] Relation fields include nested path: `"asset.code"` not just `"assetId"`

## Deliverable Sequence

When building a complete ML feature, deliver in this order:

1. **Data Model Package** (manifest.json, definition.json, package-data.json)
2. **Flow(s)** as paste-ready markdown with node-by-node JSON
3. **Application Settings** required by the flows
4. **Screen(s)** as paste-ready JSON arrays
5. **Architecture document** with algorithm reference and model diagrams
