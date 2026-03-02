---
name: iso-22400
description: "ISO 22400 MES KPI standard reference for the Fuuz Industrial Operations Platform. Covers Part 1 (concepts, terminology, time model) and Part 2 (34 KPI definitions). Use when requests mention ISO 22400, MES KPIs, manufacturing KPIs, OEE formula, OEE standard, availability, effectiveness, performance ratio, quality ratio, throughput rate, MTBF, MTTR, MTTF, worker efficiency, utilization efficiency, setup ratio, scrap ratio, rework ratio, first pass yield, process capability, machine capability, Cp, Cpk, Cm, Cmk, net equipment effectiveness, NEE, TEEP, inventory turns, production loss, equipment load, energy consumption KPI, time model, planned busy time, actual production time, or KPI calculation methodology for manufacturing operations."
---

# ISO 22400 MES KPI Reference for Fuuz Development

**Version 1.0** | Last Updated: 2026-03-02

ISO 22400 (Parts 1 and 2, 2014) defines standardized Key Performance Indicators for Manufacturing Operations Management (MOM). Part 1 establishes the framework, terminology, and time model. Part 2 specifies 34 KPIs with formulas, units, and scope. These KPIs reside at Level 3 (MOM domain) per IEC 62264 / ISA-95 and apply across production, maintenance, quality, and inventory operations.

## When to Use This Skill

Use **iso-22400** when you need to:
- Calculate OEE or its sub-components (Availability, Performance/Effectiveness, Quality) with standard-compliant formulas
- Understand how time elements (planned, actual, maintenance) relate to each other
- Implement any of the 34 standardized MES KPIs on the Fuuz platform
- Map ISO 22400 time elements to Fuuz data models (`oeeHourlies`, `WorkcenterHistory`)
- Design KPI dashboards or reports aligned with international standards
- Validate OEE calculations against the formal ISO definitions

**This is a standards reference skill.** For building the actual implementations, use the corresponding builder skills (fuuz-schema, fuuz-flows, fuuz-screens, fuuz-industrial-ops).

---

## Time Model

ISO 22400 defines a hierarchy of time elements that underpin all KPI calculations. Understanding how these times nest is essential for correct OEE and efficiency calculations.

### Time Element Hierarchy

```
Calendar Time (total available hours)
|
+-- Planned Operation Time (POT) ............... scheduled production window
|   |
|   +-- Planned Busy Time (PBT) ............... POT minus planned downtime
|   |   |
|   |   +-- Actual Unit Busy Time (AUBT) ...... time unit is occupied
|   |   |   |
|   |   |   +-- Actual Production Time (APT) .. value-adding run time
|   |   |   |
|   |   |   +-- Actual Unit Delay Time (ADET).. malfunction interruptions
|   |   |   |
|   |   |   +-- Actual Unit Setup Time (AUST).. setup and changeover
|   |   |
|   |   +-- Actual Unit Down Time (ADOT) ...... unplanned downtime
|   |
|   +-- Planned Downtime ...................... scheduled maintenance, breaks
|
+-- Unscheduled Time .......................... no production planned
```

### Planned Time Elements

| Abbreviation | Name | Definition |
|---|---|---|
| **POET** | Planned Order Execution Time | Total planned time for an order across all work units |
| **POT** | Planned Operation Time | Scheduled production window for a work unit |
| **PUST** | Planned Unit Setup Time | Planned time for setup and changeover |
| **PBT** | Planned Busy Time | POT minus planned downtime; the time available for production |
| **PRI** | Planned Run Time Per Item | Ideal cycle time for one unit of output |

### Actual Time Elements

| Abbreviation | Name | Definition |
|---|---|---|
| **AUBT** | Actual Unit Busy Time | Total time the work unit is occupied (setup + production + delays) |
| **AUPT** | Actual Unit Processing Time | Setup time plus actual production time |
| **APT** | Actual Production Time | Value-adding production time only (excludes setup, delays) |
| **AUST** | Actual Unit Setup Time | Actual time spent on setup and changeover |
| **ADET** | Actual Unit Delay Time | Time lost to malfunction interruptions during busy time |
| **ADOT** | Actual Unit Down Time | Unplanned downtime (equipment failures, breakdowns) |
| **AOET** | Actual Order Execution Time | Total elapsed time from order start to order completion |
| **AQT** | Actual Queuing Time | Time an order waits between operations |
| **ATT** | Actual Transport Time | Time spent moving material between work units |
| **APWT** | Actual Personnel Work Time | Time a worker spends on productive tasks |
| **APAT** | Actual Personnel Attendance Time | Total time a worker is present (working + idle) |

### Maintenance Time Elements

| Abbreviation | Name | Definition |
|---|---|---|
| **TBF** | Time Between Failures | Operating time between consecutive failure events |
| **TTR** | Time to Repair | Duration from failure occurrence to restoration |
| **TTF** | Time to Failure | Operating time from start (or last repair) to next failure |
| **FE** | Failure Event Count | Number of failure events in the measurement period |
| **CMT** | Corrective Maintenance Time | Total time spent on unplanned (reactive) maintenance |
| **PMT** | Preventive Maintenance Time | Total time spent on planned (proactive) maintenance |

### Logistical Elements

| Abbreviation | Name | Definition |
|---|---|---|
| **PQ** | Produced Quantity | Total units produced (good + scrap + rework) |
| **GQ** | Good Quantity | Units meeting quality requirements on first pass |
| **SQ** | Scrap Quantity | Units rejected as non-recoverable waste |
| **RQ** | Rework Quantity | Units requiring additional processing to meet spec |
| **PSQ** | Planned Scrap Quantity | Expected scrap based on process capability |
| **POQ** | Planned Order Quantity | Target quantity for the production order |

---

## Core KPI Formulas

ISO 22400 Part 2 defines 34 KPIs across production, quality, maintenance, and inventory operations. The tables below group the most important KPIs by category.

### OEE Components

These four KPIs are the most critical for Fuuz implementations. OEE is the product of its three sub-components.

| # | KPI | Formula | Unit | Trend |
|---|-----|---------|------|-------|
| 6 | **OEE** | Availability x Effectiveness x Quality Ratio | % | Higher = better |
| 8 | **Availability** | APT / PBT | % | Higher = better |
| 9 | **Effectiveness** (Performance) | PRI x PQ / APT | % | Higher = better |
| 10 | **Quality Ratio** | GQ / PQ | % | Higher = better |
| 7 | NEE (Net Equipment Effectiveness) | (AUPT / PBT) x Effectiveness x Quality | % | Higher = better |

**Scope:** Work unit (Fuuz workcenter).

### Production Efficiency

| # | KPI | Formula | Unit | Trend |
|---|-----|---------|------|-------|
| 1 | Worker Efficiency | APWT / APAT | % | Higher = better |
| 2 | Allocation Ratio | SUM(AUBT) / AOET | % | Higher = better |
| 3 | Throughput Rate | PQ / AOET | units/time | Higher = better |
| 4 | Allocation Efficiency | AUBT / PBT | % | Higher = better |
| 5 | Utilization Efficiency | APT / AUBT | % | Higher = better |
| 11 | Setup Ratio | AUST / AUPT | % | Lower = better |
| 12 | Technical Efficiency | APT / (APT + ADET) | % | Higher = better |
| 13 | Production Process Ratio | SUM(APT) / AOET | % | Higher = better |
| 27 | Equipment Load Ratio | AUPT / POT | % | Higher = better |
| 33 | Equipment Production Capacity | SUM(PQ x PRI) / POT | % | 0-100% |

### Quality

| # | KPI | Formula | Unit | Trend |
|---|-----|---------|------|-------|
| 10 | Quality Ratio | GQ / PQ | % | Higher = better |
| 14 | Actual-to-Planned Scrap | SQ / PSQ | ratio | Lower = better |
| 15 | First Pass Yield | Good Parts In / Total Parts In | % | Higher = better |
| 16 | Scrap Ratio | SQ / PQ | % | Lower = better |
| 17 | Rework Ratio | RQ / PQ | % | Lower = better |
| 18 | Fall Off Ratio | (PQ - GQ) / PQ | % | Lower = better |
| 19 | Machine Capability (Cm) | (USL - LSL) / 6 sigma | index | Higher = better |
| 20 | Critical Machine Capability (Cmk) | min((USL - x_bar), (x_bar - LSL)) / 3 sigma | index | Higher = better |
| 21 | Process Capability (Cp) | (USL - LSL) / 6 sigma_hat | index | Higher = better |
| 22 | Critical Process Capability (Cpk) | min((USL - x_bar_bar), (x_bar_bar - LSL)) / 3 sigma_hat | index | Higher = better |

**Capability index notes:**
- Cm/Cmk use short-term data (within-subgroup variation, sigma)
- Cp/Cpk use long-term data (total variation, sigma_hat)
- Target: Cm/Cp >= 1.67, Cmk/Cpk >= 1.33 (industry standard minimums)

### Maintenance

| # | KPI | Formula | Unit | Trend |
|---|-----|---------|------|-------|
| 23 | Mean Operating Time Between Failures (MTBF) | SUM(TBF) / FE | time | Higher = better |
| 24 | Mean Time to Repair (MTTR) | SUM(TTR) / FE | time | Lower = better |
| 25 | Mean Time to Failure (MTTF) | SUM(TTF) / FE | time | Higher = better |
| 26 | Corrective Maintenance Ratio | CMT / (CMT + PMT) | % | Lower = better |

**Minimum data:** At least 5 failure events (FE >= 5) for statistically meaningful results. Use a 30-day rolling window for trending.

### Inventory and Losses

| # | KPI | Formula | Unit | Trend |
|---|-----|---------|------|-------|
| 28 | Finished Goods Ratio | FGI / SUM(PQ) | ratio | Lower = better |
| 29 | Inventory Turns | Cost of Materials / (RMI + WIP + FGI) | turns | Higher = better |
| 30 | Production Loss Ratio | PL / PQ | % | Lower = better |
| 31 | Storage and Transport Loss | STL / PQ | % | Lower = better |
| 32 | Other Loss Ratio | OL / PQ | % | Lower = better |
| 34 | Comprehensive Energy Consumption | Energy / PQ | energy/unit | Lower = better |

---

## OEE Deep Dive

OEE is the single most important KPI for Fuuz workcenter performance. It quantifies the percentage of planned production time that is truly productive.

### The Three Components

```
OEE = Availability x Effectiveness x Quality

         APT              PRI x PQ            GQ
OEE = --------- x -------------------- x ----------
         PBT                APT               PQ
```

**Availability** measures how much of the planned busy time the work unit was actually producing (excluding unplanned downtime and delays):

```
                 APT                  Actual Production Time
Availability = ------- = ------------------------------------------
                 PBT       Planned Operation Time - Planned Downtime
```

- Lost by: equipment breakdowns (ADOT), malfunction delays (ADET)
- Fuuz source: `runTime / (runTime + unplannedDowntime)`

**Effectiveness** (Performance) measures whether the work unit ran at its ideal speed during actual production time:

```
                  PRI x PQ       Ideal Cycle Time x Produced Quantity
Effectiveness = ------------ = ----------------------------------------
                    APT                  Actual Production Time
```

- Lost by: speed losses, minor stops, reduced throughput
- Fuuz source: `(idealCycleTime x totalParts) / runTime`

**Quality Ratio** measures the proportion of output that meets specification on the first pass:

```
                  GQ         Good Quantity
Quality Ratio = ------ = --------------------
                  PQ       Produced Quantity
```

- Lost by: scrap, rework, startup rejects
- Fuuz source: `goodCount / (goodCount + scrapCount)`

### OEE Loss Waterfall

The six big losses that OEE captures, mapped to their component:

| Loss Category | OEE Component | ISO 22400 Time Element | Fuuz Field |
|---|---|---|---|
| Equipment breakdown | Availability | ADOT (down time) | `unplannedDowntime` |
| Setup and changeover | Availability | AUST (setup time) | `unplannedDowntime` or `plannedDowntime` |
| Minor stops | Effectiveness | Small speed reductions | Reduced part count |
| Reduced speed | Effectiveness | Longer actual cycle time | Reduced part count |
| Startup rejects | Quality | SQ during ramp-up | `scrapCount` |
| Production rejects | Quality | SQ during steady-state | `scrapCount` |

### World-Class Benchmarks

| Metric | World-Class | Typical |
|---|---|---|
| **OEE** | 85% | 60% |
| Availability | 90% | 80% |
| Effectiveness / Performance | 95% | 85% |
| Quality Ratio | 99.9% | 95% |

### TEEP (Total Effective Equipment Performance)

TEEP extends OEE to account for total calendar time, not just planned production time:

```
TEEP = OEE x Loading
Loading = Planned Production Time / Calendar Time
```

TEEP reveals hidden capacity. A plant running OEE of 85% on a single 8-hour shift has TEEP of only 28% (85% x 33% loading), meaning significant capacity remains in second/third shifts.

---

## Fuuz Implementation Mapping

### Time Elements to Fuuz Data

| ISO 22400 Element | Fuuz Source | Notes |
|---|---|---|
| **PBT** (Planned Busy Time) | `runTime + unplannedDowntime` from `WorkcenterHistory` | Excludes planned downtime |
| **APT** (Actual Production Time) | `runTime` from `WorkcenterHistory` | Time in Production mode with `countAsProductionTime = true` |
| **ADOT** (Down Time) | `unplannedDowntime` from `WorkcenterHistory` | Mode with `countAsDowntime = true` and `countAsPlannedDowntime = false` |
| **Planned Downtime** | `plannedDowntime` from `WorkcenterHistory` | Mode with `countAsPlannedDowntime = true` |
| **PRI** (Ideal Cycle Time) | `idealCycleTime` on DataPoint or Product | Fallback: `standardCycleTime` |
| **PQ** (Produced Quantity) | `goodCount + scrapCount` from `oeeHourlies` | Or production transaction totals |
| **GQ** (Good Quantity) | `goodCount` from `oeeHourlies` | Parts meeting quality spec |
| **SQ** (Scrap Quantity) | `scrapCount` from `oeeHourlies` | Rejected parts |

### Equipment Hierarchy Mapping

| ISO 22400 Level | ISA-95 Equivalent | Fuuz Model |
|---|---|---|
| Enterprise | Enterprise | `Enterprise` |
| Site | Site | `Site` |
| Area | Area | `Area` |
| Work Centre | Work Center | `Line` or `Cell` |
| **Work Unit** | **Work Unit** | **`Workcenter`** (primary OEE scope) |

The **work unit** is the primary scope for most ISO 22400 KPIs. In Fuuz, this maps to the `Workcenter` model, which is the scheduling and OEE tracking boundary.

### oeeHourlies Data Model

The `oeeHourlies` model stores pre-calculated hourly OEE aligned with ISO 22400:

| Field | ISO 22400 Mapping | Description |
|---|---|---|
| `oee` | KPI #6 (OEE) | Overall Equipment Effectiveness (0-1) |
| `availability` | KPI #8 (Availability) | APT / PBT (0-1) |
| `performance` | KPI #9 (Effectiveness) | PRI x PQ / APT (0-1) |
| `quality` | KPI #10 (Quality Ratio) | GQ / PQ (0-1) |
| `goodCount` | GQ | Good quantity for the hour |
| `scrapCount` | SQ | Scrap quantity for the hour |
| `plannedDowntimeMinutes` | Planned Downtime | Minutes of planned downtime |
| `unplannedDowntimeMinutes` | ADOT | Minutes of unplanned downtime |

### MOM Category Mapping

ISO 22400 KPIs span all four ISA-95 Manufacturing Operations Management categories:

| MOM Category | ISO 22400 KPIs | Fuuz Implementation |
|---|---|---|
| **Production Operations** | OEE (#6), Availability (#8), Effectiveness (#9), Throughput (#3), Worker Efficiency (#1), Utilization (#5) | `oeeHourlies`, `WorkcenterHistory`, `ProductionLog` |
| **Maintenance Operations** | MTBF (#23), MTTR (#24), MTTF (#25), Corrective Maintenance Ratio (#26) | `MaintenanceWorkOrder`, `WorkcenterHistory` (failure events) |
| **Quality Operations** | Quality Ratio (#10), First Pass Yield (#15), Scrap Ratio (#16), Rework Ratio (#17), Cm/Cmk/Cp/Cpk (#19-22) | `InspectionLog`, `QualityResult`, `oeeHourlies` |
| **Inventory Operations** | Finished Goods Ratio (#28), Inventory Turns (#29), Storage Loss (#31) | `InventoryTransaction`, `Lot`, WMS models |

### Production Methodology Applicability

ISO 22400 KPIs apply across all three production methodologies defined in ISA-95:

| Methodology | Primary KPIs | Fuuz Context |
|---|---|---|
| **Discrete** | OEE, Throughput Rate, Setup Ratio, First Pass Yield | Assembly lines, machining centers |
| **Batch** | OEE, Allocation Ratio, Production Process Ratio, Quality Ratio | Bioreactors, mixing vessels, reactors |
| **Continuous** | OEE, Utilization Efficiency, Technical Efficiency, Energy Consumption | Continuous process lines, extrusion |

---

## Cross-References to Other Fuuz Skills

| Skill | Relationship to ISO 22400 | When to Use Together |
|-------|---------------------------|----------------------|
| **isa-95** | ISO 22400 references ISA-95 for equipment hierarchy (work centre, work unit) and MOM categories (production, maintenance, quality, inventory). ISA-95 defines the Level 3 boundary where these KPIs operate. | Designing equipment hierarchies that support ISO 22400 KPI scope, mapping MOM categories |
| **fuuz-industrial-ops** | Contains the practical OEE time classification patterns, workcenter state management, and mode flag definitions that implement ISO 22400 time elements in Fuuz. | Building OEE calculation flows, workcenter state transitions, time bucket classification |
| **fuuz-flows** | Flow automation for calculating and storing ISO 22400 KPIs. Scheduled flows compute hourly OEE; event-driven flows track state changes and production counts. | Implementing KPI calculation logic, scheduled OEE aggregation, production event processing |
| **fuuz-schema** | Data model design for storing ISO 22400 KPI results (`oeeHourlies`, `WorkcenterHistory`, production/quality/maintenance models). | Creating or modifying models to capture KPI inputs and outputs |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-03-02 | Initial release -- time model hierarchy, 34 KPI formulas grouped by category, OEE deep dive with loss waterfall, Fuuz implementation mapping, cross-references |
