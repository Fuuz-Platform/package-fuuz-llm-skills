# Fuuz Field Types Reference

Every field in a Fuuz data model has a `type` that determines its GraphQL type, storage format, and available query operators. Append `!` to make a field required (non-nullable).

---

## Required Modifier

| Syntax | Meaning | Example |
|--------|---------|---------|
| `"String"` | Optional (nullable) | Field can be `null` |
| `"String!"` | Required (non-nullable) | Field must have a value on create |

**Rule:** Use `!` for fields that are essential to the record's identity or business logic. The `id` field must always be `ID!`.

---

## Field Type Reference

### Core Types

| Type | GraphQL Type | Description | When to Use |
|------|-------------|-------------|-------------|
| `ID` / `ID!` | `ID` | Unique identifier. Stored as string. | Primary key (`id`), foreign keys (`entityId`). Every model MUST have `id: ID!`. |
| `String` / `String!` | `String` | Text value. No length limit enforced at schema level. | Names, codes, descriptions, notes, labels, categories. |
| `Int` / `Int!` | `Int` | 32-bit integer. | Counts, quantities, sort orders, sequence numbers. |
| `Float` / `Float!` | `Float` | Double-precision floating point. | Measurements, calculations, percentages, rates. |
| `Boolean` / `Boolean!` | `Boolean` | True/false. | Flags, toggles, enabled/disabled states. |

### Date/Time Types

| Type | GraphQL Type | Description | When to Use |
|------|-------------|-------------|-------------|
| `DateTime` / `DateTime!` | `DateTime` | ISO 8601 timestamp with timezone. Stored as UTC. | Event timestamps, created/updated dates, triggered/cleared times. |
| `Date` / `Date!` | `Date` | Calendar date only (no time component). | Scheduled dates, due dates, birth dates. |
| `Time` / `Time!` | `Time` | Time of day only (no date component). | Shift start/end times, daily recurring times. |
| `Duration` / `Duration!` | `Duration` | ISO 8601 duration (e.g., `PT8H`, `P1D`). Supports format arguments in GraphQL queries. | Run times, cycle times, downtime durations, shift lengths. |
| `TimeZone` / `TimeZone!` | `TimeZone` | IANA timezone string (e.g., `America/Detroit`). | Site/workcenter timezone configuration. |
| `RRule` / `RRule!` | `RRule` | iCalendar recurrence rule string. | Schedule recurrence patterns (shifts, maintenance schedules). |

### Structured Types

| Type | GraphQL Type | Description | When to Use |
|------|-------------|-------------|-------------|
| `JSON` / `JSON!` | `JSON` | Arbitrary JSON value (any type). | Flexible/dynamic data, telemetry payloads, configuration blobs. |
| `JSONObject` / `JSONObject!` | `JSONObject` | JSON object specifically (not array or primitive). Use with `schema` metadata for validated structure. | Structured configuration with known shape, form schemas. |
| `RichText` / `RichText!` | `RichText` | Rich text content (Draft.js format). | Long-form descriptions, documentation fields, formatted notes. |
| `Address` / `Address!` | `Address` | Structured address object (street, city, state, postal, country). | Site addresses, shipping/billing addresses, contact locations. |

### Network Types

| Type | GraphQL Type | Description | When to Use |
|------|-------------|-------------|-------------|
| `IPCidr` / `IPCidr!` | `IPCidr` | IP address with CIDR notation (IPv4 or IPv6). | Network configuration, device addressing, access control. |
| `IPv4Cidr` / `IPv4Cidr!` | `IPv4Cidr` | IPv4 address with CIDR notation specifically. | IPv4-only network configuration. |
| `IPv6Cidr` / `IPv6Cidr!` | `IPv6Cidr` | IPv6 address with CIDR notation specifically. | IPv6-only network configuration. |

### Measurement Types

| Type | GraphQL Type | Description | When to Use |
|------|-------------|-------------|-------------|
| `Measure` / `Measure!` | `Measure` | Numeric value with unit of measure (e.g., `{ value: 100, unitId: "kg" }`). | Weights, dimensions, temperatures, pressures — any value that requires a unit. |
| `RatioMeasure` / `RatioMeasure!` | `RatioMeasure` | Ratio of two measures (e.g., `{ numerator: { value: 10, unitId: "ea" }, denominator: { value: 1, unitId: "h" } }`). | Rates, throughput (parts/hour), speeds (m/s), concentrations. |

### Relationship Types

| Type | GraphQL Type | Description | When to Use |
|------|-------------|-------------|-------------|
| `ModelName` / `ModelName!` | Custom Object | Reference to another model. Requires `relation` metadata. | To-one relationships (e.g., `"type": "AlarmState!"`). |
| `[ModelName!]!` | Custom Array | Collection of related records. Requires reverse `relation` metadata. | One-to-many relationships (e.g., `"type": "[Cell!]!"`). |

See `references/relationship-patterns.md` for complete relationship field examples.

---

## Type Selection Decision Tree

```
Is this a primary key or foreign key?
  → Yes: ID / ID!

Is this a reference to another model?
  → Yes, single record: ModelName / ModelName!
  → Yes, collection: [ModelName!]!

Is this a date, time, or duration?
  → Full timestamp: DateTime
  → Date only: Date
  → Time only: Time
  → Duration/interval: Duration
  → Timezone identifier: TimeZone
  → Recurrence pattern: RRule

Is this a number?
  → Whole number (count, quantity): Int
  → Decimal (measurement, calculation): Float
  → Number with unit: Measure
  → Rate (number per unit): RatioMeasure

Is this text?
  → Short text (name, code, label): String
  → Rich formatted text: RichText
  → IP address: IPCidr / IPv4Cidr / IPv6Cidr
  → Physical address: Address

Is this true/false?
  → Boolean

Is this flexible/dynamic data?
  → Any JSON value: JSON
  → JSON object with known schema: JSONObject
```

---

## Duration Field Special Behavior

Duration fields support format arguments in GraphQL queries:

```graphql
query {
  alarm {
    edges {
      node {
        duration                    # Returns ISO 8601 string: "PT2H30M"
        duration(format: "hours")   # Returns numeric: 2.5
        duration(format: "minutes") # Returns numeric: 150
      }
    }
  }
}
```

**IMPORTANT:** Duration fields may return mixed formats depending on context. In flow transforms, always handle both ISO 8601 strings and numeric values. See `fuuz flows/references/common-pitfalls.md` for duration handling patterns.

---

## Common Field Patterns by Model Type

### Master Models (Asset, Product, Workcenter)
```
id: ID!          — Primary key
code: String!    — Human-readable unique code (often with sequence)
name: String!    — Display name
description: String — Optional description
active: Boolean  — Soft delete / enable-disable
```

### Setup Models (Mode, State, EventCategory)
```
id: ID!          — Primary key (often meaningful, e.g., "production", "idle")
code: String!    — Short code for UI display
name: String!    — Full display name
description: String — Explanation of the value
sortOrder: Int   — UI ordering
```

### Transactional Models (ProductionLog, Alarm, TelemetryRaw)
```
id: ID!              — Primary key (often CUID-generated)
code: String!        — Sequence-generated code
{entityId}: ID!      — Foreign key to parent/related record
{entity}: ModelName! — Relation field
{timestamps}: DateTime — triggeredAt, clearedAt, startedAt, endedAt
{measures}: Float/Int/Duration — quantityGood, duration, value
notes: String        — Operator notes
```
