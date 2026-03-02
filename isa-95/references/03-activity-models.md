# ISA-95 Part 3: Activity Models of Manufacturing Operations Management

**Reference:** ANSI/ISA-95.00.03 / IEC 62264-3
**Scope:** Defines the activity models for manufacturing operations management (MOM). While Part 1 identified the four MOM categories and Part 2 defined the data objects, Part 3 describes the activities -- the things that people and systems actually do to manage manufacturing operations at Level 3.

> **Copyright Note:** ISA-95 is a copyrighted standard published by the International Society of Automation. The descriptions below are written in our own words to explain the concepts and their application to the Fuuz platform. Clause references are provided so readers can consult the source standard for normative definitions.

---

## The Generic 8-Activity Model

*Reference: ISA-95 Part 3, Clause 5*

Part 3's most powerful contribution is the identification of a generic pattern of eight activities that applies to all four MOM categories (production, maintenance, quality, and inventory). Each category performs the same fundamental types of activities, just with different subjects and details.

### The Eight Activities

| # | Activity | Purpose | Typical Questions Answered |
|---|----------|---------|---------------------------|
| 1 | **Resource Management** | Manage the availability, capability, and status of resources (people, equipment, material) needed for operations | What resources do we have? Are they available? Are they qualified? |
| 2 | **Definition Management** | Manage the rules, instructions, recipes, and procedures that define how operations should be performed | How do we make this product? What are the steps? What are the specifications? |
| 3 | **Detailed Scheduling** | Create and manage detailed schedules that assign specific resources to specific work at specific times | When exactly should each job run? On which machine? With which operator? |
| 4 | **Dispatching** | Release scheduled work for execution, providing resources with the information they need to begin | Start this job now. Here are the instructions, materials, and assignments. |
| 5 | **Execution Management** | Direct and supervise the actual performance of work, handling in-process decisions and adjustments | Is the job running correctly? Do we need to adjust? What exceptions have occurred? |
| 6 | **Data Collection** | Capture data from the execution of operations, both automated (from sensors) and manual (from operators) | What actually happened? What quantities were produced? What values were measured? |
| 7 | **Tracking** | Maintain a record of where things are, what state they are in, and what has happened to them | Where is this lot? What is the status of this work order? What happened on this shift? |
| 8 | **Performance Analysis** | Analyze collected data against definitions and schedules to measure efficiency, quality, and conformance | How well did we do? Where are the gaps? What are the trends? |

### Activity Data Flow Pattern

The eight activities form a logical sequence, though in practice they operate concurrently with feedback loops (Part 3, Clause 5.2):

```
Level 4 (ERP)
    |
    v
[1. Resource Management] <--> Resource status and availability
[2. Definition Management] <--> Recipes, procedures, specifications
    |
    v
[3. Detailed Scheduling] --> Detailed schedule (who, what, when, where)
    |
    v
[4. Dispatching] --> Work instructions and resource assignments
    |
    v
[5. Execution Management] --> Commands, adjustments, exception handling
    |
    v
[6. Data Collection] <-- Actual values from sensors and operators
    |
    v
[7. Tracking] --> Current status and location information
    |
    v
[8. Performance Analysis] --> KPIs, reports, deviations
    |
    v
Level 4 (ERP) -- performance results returned
```

**Fuuz Mapping (Generic):**

| Activity | Fuuz Implementation |
|----------|---------------------|
| Resource Management | Fuuz data models for equipment status, operator availability, and material inventory; Flows for status change automation |
| Definition Management | `Routing`, `BOM`, `Operation`, `QualityTestSpec` models; Screens for recipe/procedure authoring |
| Detailed Scheduling | Scheduling Screens and Flows; integration with ERP schedule imports; drag-and-drop Gantt or list-based scheduling |
| Dispatching | Fuuz Flows that push work instructions to operator screens; MQTT messages to control systems; status transitions on `WorkOrder` |
| Execution Management | Operator-facing Screens for job execution; Flows for exception handling and escalation; real-time dashboards |
| Data Collection | `DataPoint` ingestion via MQTT; manual entry Screens; barcode/RFID scan capture; `ProductionLog` and `InspectionLog` creation |
| Tracking | Status fields on `WorkOrder`, `Lot`, `Asset`; `InventoryTransaction` history; `WorkcenterHistory` time tracking |
| Performance Analysis | Fuuz dashboards with OEE, yield, quality metrics; `ProductionLog` aggregation; trend analysis Screens |

---

## Production Operations Management

*Reference: ISA-95 Part 3, Clause 6*

Production operations management applies the generic 8-activity model to the domain of making products. This is typically the most developed MOM category in manufacturing implementations.

### Production-Specific Activity Details

| Activity | Production-Specific Focus | Fuuz Details |
|----------|--------------------------|--------------|
| **Resource Management** | Track production equipment availability (running, idle, down, changeover), operator assignments by shift, and material stock at workstations | `Workcenter` state tracking, `WorkcenterHistory` for time classification, shift `Schedule` models |
| **Definition Management** | Maintain product routings (sequence of operations), bills of material, process parameters, and work instructions | `Routing` + `RoutingStep` models, `BOM` with component quantities, `Operation` parameters, SOP document links |
| **Detailed Scheduling** | Sequence work orders on specific lines/cells with start and end times, considering changeover times, material availability, and operator skills | Work order scheduling Screens with capacity visualization, constraint-based scheduling Flows |
| **Dispatching** | Release work orders to the shop floor, push instructions to operator displays, allocate materials and tooling | `WorkOrder` status transition to "Released"/"In Progress", Flows triggering operator screen updates via MQTT |
| **Execution Management** | Monitor production progress, handle deviations (machine fault, quality hold, material shortage), manage in-process decisions | Real-time production dashboards, exception handling Flows, operator alert Screens, escalation chains |
| **Data Collection** | Capture production counts (good, scrap, rework), cycle times, process parameters, operator entries, and equipment events | `DataPoint` MQTT ingestion, production count Screens, `ProductionLog` creation Flows, barcode scan events |
| **Tracking** | Maintain work order status, lot genealogy, material consumption records, and equipment utilization history | `WorkOrder` status tracking, `Lot` genealogy via `InventoryTransaction`, `WorkcenterHistory` time records |
| **Performance Analysis** | Calculate OEE (availability, performance, quality), yield, throughput, cycle time analysis, and production variance reports | OEE dashboard Screens, `ProductionLog` aggregation queries, shift comparison reports, trend analysis |

### Scheduling Concepts for Production

Part 3 (Clause 6.3) introduces important scheduling concepts:

**Finite Capacity Scheduling:** Schedules work based on the actual available capacity of resources, respecting constraints. A work center can only run one (or a defined maximum number of) jobs at a time.

**Resource Types for Scheduling:**

| Resource Type | Behavior | Example |
|---------------|----------|---------|
| **Consumable** | Used up during production, must be replenished | Raw materials, packaging, lubricants |
| **Non-consumable** | Available after use, capacity-limited but reusable | Equipment, tools, operators |

**Fuuz Implementation:** Production scheduling in Fuuz combines:
- `WorkOrder` priority, due date, and quantity requirements
- `Workcenter` capacity and current status (from real-time equipment state)
- Operator availability from shift schedules
- Material availability from inventory records
- Changeover time estimates between product types

---

## Maintenance Operations Management

*Reference: ISA-95 Part 3, Clause 7*

Maintenance operations management applies the same 8-activity model to maintaining equipment. The outputs of maintenance directly affect production capability -- equipment taken offline for maintenance reduces production capacity.

### Maintenance-Specific Activity Details

| Activity | Maintenance-Specific Focus | Fuuz Details |
|----------|---------------------------|--------------|
| **Resource Management** | Track maintenance personnel certifications, spare parts inventory, maintenance tools, and contractor availability | `MaintenanceTechnician` with certifications, spare parts `Inventory`, tool tracking models |
| **Definition Management** | Maintain preventive maintenance procedures, corrective maintenance instructions, inspection checklists, and maintenance BOMs (parts lists) | `MaintenanceProcedure` models, PM checklists, spare parts lists per equipment class |
| **Detailed Scheduling** | Schedule preventive maintenance based on time or usage triggers, plan corrective maintenance around production schedules | PM scheduling Flows triggered by calendar or meter readings, coordination with production schedule |
| **Dispatching** | Assign maintenance work orders to technicians, provide procedures and parts information, coordinate equipment lockout/tagout | `MaintenanceWorkOrder` assignment, push notifications to technician devices |
| **Execution Management** | Monitor maintenance task progress, handle parts unavailability, manage scope changes, track actual repair time | Maintenance execution Screens, parts request Flows, time tracking |
| **Data Collection** | Capture labor hours, parts consumed, failure codes, repair descriptions, meter readings | Technician entry Screens, failure code selection, parts consumption records |
| **Tracking** | Maintain equipment maintenance history, track mean time between failures (MTBF), monitor PM compliance rates | `MaintenanceLog` history, MTBF/MTTR calculations, PM compliance dashboards |
| **Performance Analysis** | Analyze maintenance effectiveness, parts usage trends, failure patterns, and maintenance cost per asset | Maintenance KPI dashboards, failure analysis reports, cost tracking |

### Cross-Category Interaction: Maintenance and Production

Part 3 (Clause 7.1) emphasizes that maintenance and production operations must coordinate closely:
- Production must yield equipment for scheduled maintenance windows
- Maintenance must communicate expected repair durations so production can reschedule
- Emergency maintenance creates unplanned downtime that production must respond to

**Fuuz Implementation:** Coordination between maintenance and production uses:
- Shared `Workcenter` status model (production sees "Down for Maintenance")
- Flows that automatically notify production scheduling when maintenance takes equipment offline
- Maintenance completion Flows that update equipment availability for production dispatching

---

## Quality Operations Management

*Reference: ISA-95 Part 3, Clause 8*

Quality operations management applies the 8-activity model to testing, inspection, and quality assurance. Quality operations can be triggered by production events, schedules, or external requirements.

### Quality-Specific Activity Details

| Activity | Quality-Specific Focus | Fuuz Details |
|----------|----------------------|--------------|
| **Resource Management** | Track lab equipment calibration, inspector qualifications, test material availability (reagents, standards) | `LabEquipment` calibration tracking, inspector `CertificationRecord`, reagent inventory |
| **Definition Management** | Maintain test specifications, sampling plans, acceptance criteria, and SPC control limits | `QualityTestSpec`, `SamplingPlan`, `ControlLimit` models |
| **Detailed Scheduling** | Schedule inspections tied to production events (first article, in-process, final), plan calibration activities, schedule lab tests | Quality event triggers in production Flows, calibration schedules |
| **Dispatching** | Assign inspection tasks to inspectors, route samples to labs, initiate automated test sequences | `InspectionRequest` creation, sample routing Flows |
| **Execution Management** | Monitor test execution, handle out-of-spec results, manage disposition decisions (accept, reject, rework, waiver) | Inspection execution Screens, disposition workflow Flows, nonconformance management |
| **Data Collection** | Capture test measurements, inspection results, pass/fail determinations, and defect classifications | `InspectionLog` entry Screens, `QualityResult` records, defect code capture |
| **Tracking** | Maintain quality history by lot, equipment, operator, and product; track nonconformance through resolution | `QualityResult` history, lot quality status, NCR tracking models |
| **Performance Analysis** | Analyze yield, defect rates, SPC trends, Cpk/Ppk capability indices, supplier quality metrics | Quality dashboard Screens, SPC charts, Pareto analysis, trend reports |

### Quality Triggers

Quality operations are typically initiated by events from other MOM categories (Part 3, Clause 8.2):
- **Production trigger:** First article inspection when a new work order starts, in-process checks at defined intervals, final inspection before release
- **Maintenance trigger:** Equipment verification after repair, calibration checks
- **Inventory trigger:** Incoming material inspection, periodic inventory verification
- **Schedule trigger:** Routine lab tests on a calendar basis

**Fuuz Implementation:** Quality trigger patterns in Fuuz:
- Fuuz Flows listen for production events (new work order started, quantity threshold reached) and automatically create inspection requests
- Flows triggered by maintenance completion create equipment verification inspections
- Receiving Flows create incoming inspection requests when new material lots arrive
- Scheduled Flows create routine test requests on calendar intervals

---

## Inventory Operations Management

*Reference: ISA-95 Part 3, Clause 9*

Inventory operations management applies the 8-activity model to the storage, movement, and tracking of materials. This category manages the physical flow of material through the facility.

### Inventory-Specific Activity Details

| Activity | Inventory-Specific Focus | Fuuz Details |
|----------|-------------------------|--------------|
| **Resource Management** | Track storage locations, material handling equipment, warehouse personnel, and storage capacity | `StorageLocation` models, forklift/AGV tracking, warehouse operator assignments |
| **Definition Management** | Maintain storage rules, FIFO/FEFO policies, hazardous material handling procedures, and location mapping | `StorageRule` models, FIFO enforcement logic, hazmat classification |
| **Detailed Scheduling** | Plan material movements to support production schedules, schedule receipts, and plan shipments | Material staging schedules, delivery coordination, shipping schedules |
| **Dispatching** | Issue pick lists, stage materials for production, direct material handlers to perform moves | Pick list generation Flows, material request Screens, move instructions |
| **Execution Management** | Monitor material movement, confirm receipts, manage discrepancies, handle material holds | Material movement confirmation Screens, discrepancy handling Flows |
| **Data Collection** | Capture material movements via barcode/RFID scanning, record quantities, locations, and timestamps | Barcode scan Screens, `InventoryTransaction` creation, RFID event processing |
| **Tracking** | Maintain current inventory levels by location, track lot locations, manage inventory accuracy | `Inventory` balance models, lot location tracking, cycle count reconciliation |
| **Performance Analysis** | Analyze inventory accuracy, fill rates, storage utilization, material handling efficiency | Inventory accuracy dashboards, aging reports, stock level trend analysis |

**Fuuz Implementation:** Inventory operations in Fuuz center on:
- `InventoryTransaction` records (receipt, issue, transfer, adjust, consume, produce) that form a complete audit trail
- `Lot` status management (available, allocated, quarantined, consumed) with location tracking
- Barcode scanning Screens for material identification and movement confirmation
- Flows that automatically update inventory when production consumes or produces material
- Integration with production scheduling to verify material availability before dispatching work

---

## Cross-Category Interactions

*Reference: ISA-95 Part 3, Clause 10*

Part 3 emphasizes that the four MOM categories do not operate in isolation. They share resources, exchange triggers, and must coordinate to avoid conflicts.

### Key Interaction Patterns

| Interaction | Description | Fuuz Implementation |
|-------------|-------------|---------------------|
| **Production <-> Maintenance** | Maintenance requires equipment downtime; production needs equipment uptime. Coordination determines maintenance windows. | Shared `Workcenter` status, maintenance window scheduling, automated notifications |
| **Production <-> Quality** | Production events trigger quality inspections; quality holds can stop production. | Quality trigger Flows, disposition-based production hold/release |
| **Production <-> Inventory** | Production consumes materials and produces products; inventory must stage materials and receive output. | Material staging Flows, automatic consumption recording, production receipt processing |
| **Maintenance <-> Quality** | Equipment calibration and verification after maintenance; quality of maintenance work. | Post-maintenance verification Flows, calibration tracking |
| **Quality <-> Inventory** | Incoming inspection gates material availability; quality holds affect inventory disposition. | Incoming inspection Flows, lot status management based on quality results |
| **Maintenance <-> Inventory** | Spare parts management links maintenance needs to inventory operations. | Spare parts inventory, maintenance BOM consumption tracking |

### Shared Resource Conflicts

When multiple categories compete for the same resource (e.g., a technician needed for both production support and maintenance), the activities must be prioritized. Part 3 notes that this prioritization is typically managed at the Area level, where a supervisor or scheduling function arbitrates across categories.

**Fuuz Implementation:** Cross-category coordination in Fuuz is achieved through:
- Shared data models that all categories can read and write (equipment status, material availability, personnel assignment)
- Fuuz Flows that react to status changes and propagate effects across categories
- Dashboard Screens that provide visibility across all four categories for supervisors
- Priority rules configured in scheduling Flows that resolve resource conflicts

---

## Summary: Part 3 Activity Models and Fuuz Mapping

| Concept | Fuuz Implementation Pattern |
|---------|----------------------------|
| 8-Activity Model | Maps to the standard pattern of Fuuz implementation: reference data models (1-2), scheduling Screens/Flows (3-4), execution Screens/Flows (5), data collection via MQTT + Screens (6), tracking dashboards (7), analytics dashboards (8) |
| Production Operations | Work order lifecycle management with real-time data collection and OEE analysis |
| Maintenance Operations | Maintenance work order management with PM scheduling and equipment health tracking |
| Quality Operations | Inspection and test management triggered by production, maintenance, and schedule events |
| Inventory Operations | Material movement tracking via barcode/RFID with automated production integration |
| Cross-Category Coordination | Shared data models, event-driven Flows, and unified supervisor dashboards |
| Finite Capacity Scheduling | Workcenter capacity tracking with constraint-based scheduling logic |
