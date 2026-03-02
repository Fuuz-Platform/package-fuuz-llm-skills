# ISA-95 Part 4: Objects and Attributes for Manufacturing Operations Management Integration

**Reference:** ANSI/ISA-95.00.04 / IEC 62264-4
**Scope:** Part 4 refines and extends the models from Parts 2 and 3 to define the detailed object models needed within Level 3 manufacturing operations management. While Parts 1-3 focus on the Level 3/4 boundary, Part 4 looks inside Level 3 and defines the "work view" -- how operations are planned, executed, and tracked from the shop floor perspective.

> **Copyright Note:** ISA-95 is a copyrighted standard published by the International Society of Automation. The descriptions below are written in our own words to explain the concepts and their application to the Fuuz platform. Clause references are provided so readers can consult the source standard for normative definitions.

---

## Business View vs Work View

*Reference: ISA-95 Part 4, Clause 4*

Part 4 introduces a critical distinction between two perspectives on manufacturing operations:

| Perspective | Scope | Audience | Time Horizon |
|-------------|-------|----------|--------------|
| **Business View** | Operations as seen from Level 4 (ERP). Uses operations definitions, schedules, and performance. Aggregated, product-centric. | Planners, supply chain managers, business analysts | Days to weeks |
| **Work View** | Operations as seen from the shop floor (Level 3). Uses work definitions, work schedules, and work performance. Detailed, resource-centric. | Supervisors, operators, maintenance technicians | Minutes to hours |

The business view models (from Parts 2-3) represent what the enterprise asked for and what was reported back. The work view models (from Part 4) represent the detailed instructions and actual execution as managed within Level 3. There is not necessarily a one-to-one mapping between the two views -- a single operations request may be broken into multiple work requests, or multiple operations requests may be combined into a single work execution.

**Fuuz Mapping:**
- **Business View** = `WorkOrder` (received from ERP), `ProductionLog` (reported to ERP)
- **Work View** = `JobOrder` (detailed shop floor work instructions), `JobResponse` (detailed execution results)

---

## Resource Relationship Networks

*Reference: ISA-95 Part 4, Clause 5*

Part 4 formalizes the concept of resource relationship networks, which describe how resources are connected and interact. Equipment can be related to other equipment, personnel can be related to equipment they are qualified to operate, and materials can be related to the processes that consume or produce them.

These relationships go beyond simple parent-child hierarchy to include:
- **Dependency relationships** (equipment A requires equipment B to be running)
- **Exclusion relationships** (equipment A and B cannot run simultaneously due to shared utilities)
- **Qualification relationships** (operator X is qualified for equipment classes Y and Z)

**Fuuz Implementation:**
- Equipment dependency and exclusion rules are modeled as `EquipmentRelationship` records used by scheduling logic
- Operator qualification relationships are captured in `OperatorQualification` linking operators to equipment classes
- Material compatibility rules (which materials can be processed on which equipment) are captured as process segment constraints

---

## Work Definition Hierarchy

*Reference: ISA-95 Part 4, Clause 6*

Part 4 defines a two-level hierarchy for work definitions that separates reusable patterns from specific instances:

### Work Master

A work master is a reusable template for performing a type of work. It defines the general pattern, including the sequence of steps, the types of resources needed, and the standard parameters. A work master is not tied to a specific product or job -- it represents "how we generally do this kind of work."

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **ID and description** | Unique identifier and human-readable name | `WorkMaster.code` and `WorkMaster.name` |
| **Work master parameters** | Standard parameters with default values | `WorkMasterParameter` records |
| **Workflow specification** | The sequence and logic of steps (can be sequential, parallel, branching) | `WorkMasterStep` records with sequence and dependency info |
| **Resource requirements** | Generic personnel, equipment, material, and physical asset requirements | Requirement records linked to work master steps |

### Work Directive

A work directive is a specific instance derived from a work master, customized for a particular product, situation, or customer requirement. It inherits the structure of the work master but may override parameters, add steps, or modify resource requirements.

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **ID and description** | Unique identifier, often includes product/customer reference | `WorkDirective.code` and `WorkDirective.name` |
| **Reference to work master** | Links back to the template it was derived from | `WorkDirective.workMasterId` FK |
| **Overridden parameters** | Product- or situation-specific parameter values | `WorkDirectiveParameter` records |
| **Modified steps** | Additional or modified workflow steps | `WorkDirectiveStep` records |
| **Specific resource assignments** | Named resources instead of generic types | Specific equipment or personnel assignments |

### Relationship Pattern

```
Work Master (reusable template)
    "How we generally do mixing"
        |
        v
Work Directive (product-specific instance)
    "How we mix Product ABC per Customer XYZ specs"
        |
        v
Job Order (actual execution instance)
    "Mix 500 kg of Product ABC, Work Order #12345, Line 3"
```

**Fuuz Implementation:**
- `WorkMaster` = Reusable operation template (e.g., "CNC Machining Operation")
- `WorkDirective` = Product-specific version (e.g., "CNC Machining for Part #ABC-123 per drawing Rev C")
- This maps closely to the `Operation` (generic) / `RoutingStep` (product-specific) pattern already in Fuuz
- The work master/directive pattern is especially valuable when the same base operation is customized for different products or customers

---

## Workflow Specification

*Reference: ISA-95 Part 4, Clause 6.3*

Work masters and work directives include a workflow specification that describes the execution logic for the steps. Part 4 notes that these workflows can be represented using standard workflow notations such as BPMN (Business Process Model and Notation).

### Workflow Elements

| Element | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Sequential steps** | Steps that must execute in order | Linear step sequence in `RoutingStep` order |
| **Parallel steps** | Steps that can execute simultaneously | Parallel branches in Fuuz Flows |
| **Conditional branching** | Steps that execute based on conditions (if/then) | Decision nodes in Fuuz Flows |
| **Looping** | Steps that repeat until a condition is met | Loop constructs in Fuuz Flows |
| **Synchronization** | Points where parallel branches must all complete before proceeding | Join nodes in Fuuz Flows |

**Fuuz Implementation:**
- Simple sequential workflows are modeled as ordered `RoutingStep` sequences
- Complex workflows with branching, parallelism, and loops are implemented as Fuuz Flows
- The Fuuz Flow engine supports all the workflow patterns described in Part 4
- For operator-facing workflows, Fuuz Screens present steps one at a time with data collection at each step

---

## Work Schedule and Work Request

*Reference: ISA-95 Part 4, Clause 7*

A work schedule is the Level 3 counterpart to the Level 4 operations schedule. While the operations schedule represents what the business wants done, the work schedule represents the detailed plan for how the shop floor will accomplish it.

### Work Schedule

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Time scope** | The period covered by this schedule (shift, day) | Schedule date range on the scheduling Screen |
| **Work requests** | Collection of individual work items to be performed | Collection of `JobOrder` records for the period |
| **Resource assignments** | Which specific resources are assigned to which work | Resource allocation records linking jobs to equipment/operators |

### Work Request

A work request is a single item within a work schedule, specifying a piece of work to be performed.

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Work directive reference** | Which work directive defines how to do this work | `JobOrder.workDirectiveId` FK |
| **Priority** | Relative priority for sequencing | `JobOrder.priority` field |
| **Timing** | Requested start/end times or window | `JobOrder.scheduledStart` and `JobOrder.scheduledEnd` |
| **Quantity** | Amount of work to perform | `JobOrder.targetQuantity` |
| **Resource requirements** | Specific or class-based resource needs | Resource allocation records |
| **Segment requirements** | Detailed per-step requirements | Step-level detail records on the job order |

**Fuuz Implementation:**
- Work schedules are presented to supervisors via scheduling Screens showing all jobs for a period
- Work requests map to `JobOrder` records that are the shop floor's view of work to be done
- A single `WorkOrder` (business view) may generate multiple `JobOrder` records (work view) -- for example, splitting a large order across shifts or lines

---

## Job Order Model

*Reference: ISA-95 Part 4, Clause 8*

The job order is the central execution object in Part 4. It represents a specific piece of work that has been authorized for execution on the shop floor. Job orders are the work view equivalent of operations requests.

### Job Order Attributes

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Job order ID** | Unique identifier | `JobOrder.code` |
| **Work directive reference** | How to perform this job | `JobOrder.workDirectiveId` |
| **Work request reference** | Which work request this fulfills | `JobOrder.workRequestId` |
| **Command** | Current instruction (Start, Stop, Pause, Resume, Abort, etc.) | `JobOrder.command` field |
| **Command rule** | How the command should be interpreted (1 = as-is, 2 = if-possible) | `JobOrder.commandRule` |
| **Dispatch status** | Current authorization state | `JobOrder.dispatchStatus` |
| **Job order parameters** | Execution parameters | `JobOrderParameter` records |
| **Personnel requirements** | Specific operator assignments | Personnel allocation records |
| **Equipment requirements** | Specific equipment assignments | Equipment allocation records |
| **Material requirements** | Specific material/lot requirements | Material allocation records |

### Job Order State Machine

*Reference: ISA-95 Part 4, Clause 8.3*

Part 4 defines a state machine for job orders that governs valid status transitions. The states represent the lifecycle of work on the shop floor:

```
                    +-----------+
                    |  Waiting  |  (created but not yet ready)
                    +-----+-----+
                          |
                          v
                    +-----+-----+
            +------>|   Ready   |  (all resources available, authorized to start)
            |       +-----+-----+
            |             |
            |             v
            |       +-----+-----+
            |       |  Running  |  (actively being executed)
            |       +-----+-----+
            |          |     |
            |          v     v
            |   +------++ +--+-------+
            |   |Paused | | Complete |  (temporarily halted | finished normally)
            |   +------++ +--+-------+
            |      |              |
            |      v              v
            +------+        +----+----+
                            |  Closed |  (archived, no further action)
                            +----+----+

    (From any active state)
            |
            v
      +-----+-----+
      |  Aborted  |  (terminated abnormally)
      +-----+-----+
            |
            v
      +-----+-----+
      |  Closed   |
      +-----------+
```

### Valid State Transitions

| From State | To State | Trigger | Fuuz Implementation |
|------------|----------|---------|---------------------|
| **Waiting** | Ready | Resources confirmed available | Flow checks resource availability |
| **Ready** | Running | Start command issued (dispatching) | Operator or supervisor starts job via Screen |
| **Running** | Paused | Pause command (material shortage, break, etc.) | Operator pauses via Screen, reason code required |
| **Paused** | Running | Resume command | Operator resumes via Screen |
| **Running** | Complete | Job finished normally (target quantity reached or end signal) | Flow auto-completes when target met, or operator signals completion |
| **Any active** | Aborted | Abort command (emergency, quality hold, etc.) | Supervisor aborts via Screen, reason code required |
| **Complete** | Closed | Archived after performance data collected | Flow auto-closes after reporting to ERP |
| **Aborted** | Closed | Archived after investigation | Supervisor closes after documenting reason |

**Fuuz Implementation:**
- `JobOrder.status` field tracks current state with valid transitions enforced by Fuuz Flows
- State transitions trigger events (MQTT messages, Flow executions) for downstream processing
- Paused and Aborted states require reason codes captured via operator Screens
- The Closed state triggers performance reporting back to the business view (`ProductionLog`)

---

## Work Performance and Job Response

*Reference: ISA-95 Part 4, Clause 9*

Work performance is the work view record of what actually happened during execution. Job responses are the detailed execution results for individual job orders.

### Job Response Attributes

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Job response ID** | Unique identifier | `JobResponse.code` |
| **Job order reference** | Which job order this responds to | `JobResponse.jobOrderId` FK |
| **Actual start/end** | When execution actually started and finished | `JobResponse.actualStart`, `JobResponse.actualEnd` |
| **Actual personnel** | Who actually performed the work | Operator records linked to `JobResponse` |
| **Actual equipment** | What equipment was actually used | Equipment records linked to `JobResponse` |
| **Actual material consumed** | What materials were actually consumed, with lot numbers | Material consumption records |
| **Actual material produced** | What was actually produced, with lot numbers and quantities | Production output records |
| **Job response parameters** | Actual parameter values recorded during execution | `JobResponseParameter` records |
| **Job response data** | Additional data collected (measurements, observations, events) | Linked data collection records |

### Work Performance Aggregation

Work performance aggregates job responses for a time period to create the data needed for the business view. Multiple job responses may feed into a single operations performance record reported to Level 4.

**Fuuz Implementation:**
- `JobResponse` records capture detailed execution data at the shop floor level
- Fuuz Flows aggregate `JobResponse` data into `ProductionLog` records for the business view
- The aggregation may involve summing quantities across multiple jobs, calculating actual cycle times, and computing resource utilization
- `ProductionLog` records are then available for ERP integration (reporting actual production back to Level 4)

---

## Work Capability

*Reference: ISA-95 Part 4, Clause 10*

Work capability describes what the shop floor can actually do at a given point in time. It combines resource availability with qualification and capacity information to answer the question: "What work can we take on right now?"

### Capability Components

| Component | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Personnel capability** | Available and qualified operators by skill/certification | Operator availability query combining schedule and qualifications |
| **Equipment capability** | Available and functional equipment by type/capacity | `Workcenter` status + `EquipmentClass` capability |
| **Material capability** | Available materials by type and quantity | Inventory levels by material definition and location |
| **Process capability** | Which process segments can currently be executed | Derived from the intersection of personnel, equipment, and material availability |

**Fuuz Implementation:**
- Work capability is calculated dynamically by Fuuz Flows that query current resource status
- Equipment availability comes from real-time `Workcenter` status (running, idle, down)
- Personnel availability comes from shift schedules and current assignment status
- Material availability comes from `Inventory` balance queries
- The combined capability informs the detailed scheduling activity

---

## Work Alert Model

*Reference: ISA-95 Part 4, Clause 11*

Work alerts are notifications generated during manufacturing operations to communicate conditions that require attention. They represent a standardized way for Level 3 systems to raise issues.

### Alert Attributes

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Alert ID** | Unique identifier | `Alert.code` |
| **Priority** | Urgency level (e.g., high, medium, low) | `Alert.priority` |
| **Category** | Type of alert (equipment fault, quality deviation, material shortage, safety) | `Alert.category` |
| **Description** | Human-readable description of the condition | `Alert.description` |
| **Timestamp** | When the condition was detected | `Alert.timestamp` |
| **Associated resources** | Equipment, personnel, or material involved | FK links to relevant resource records |
| **Recommended action** | Suggested response | `Alert.recommendedAction` |

**Fuuz Implementation:**
- Fuuz Flows generate alerts based on conditions (equipment fault detected via MQTT, quality out-of-spec, material below threshold)
- Alerts are displayed on operator and supervisor Screens with priority-based ordering
- Alert acknowledgment and resolution are tracked with timestamps and operator identification
- Alerts can trigger escalation Flows if not acknowledged within defined time windows
- Historical alert data feeds into performance analysis (frequency, response time, resolution effectiveness)

---

## Work Calendar Model

*Reference: ISA-95 Part 4, Clause 12*

The work calendar model defines the operational schedule of the facility -- when the plant is scheduled to operate, what shifts are defined, and what planned non-production periods exist.

### Calendar Components

| Component | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Work calendar definition** | The named calendar (e.g., "Plant A Production Calendar 2026") | `WorkCalendar` model |
| **Work calendar entry** | A specific time period within the calendar (shift, planned downtime, holiday) | `WorkCalendarEntry` with type, start, end |
| **Entry types** | Categories of calendar entries: production time, planned downtime, holiday, maintenance window | `WorkCalendarEntry.entryType` enum |

### Calendar and OEE

The work calendar is essential for OEE calculations because it defines the "planned production time" denominator:
- **Available time** = Calendar production entries (scheduled operating time)
- **Planned downtime** = Calendar entries for planned non-production (breaks, maintenance windows, holidays)
- **OEE Availability** = (Available time - Unplanned downtime) / Available time

**Fuuz Implementation:**
- `WorkCalendar` and `WorkCalendarEntry` models define the plant operating schedule
- Shift definitions are calendar entries with type "production"
- Planned maintenance windows are entries with type "planned downtime"
- The OEE calculation in Fuuz uses calendar data to determine planned production time
- Scheduling Screens reference the work calendar to show available capacity windows

---

## Work Record and KPI Models

*Reference: ISA-95 Part 4, Clauses 13-14*

### Work Record

Work records provide a historical archive of manufacturing operations data. They combine work schedules, job orders, job responses, and associated data into a comprehensive execution record.

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **Record ID** | Unique identifier | `WorkRecord.code` |
| **Time period** | Period covered by this record (shift, day, batch) | `WorkRecord.startTime`, `WorkRecord.endTime` |
| **Associated job orders** | Links to all job orders in the period | FK relationships to `JobOrder` records |
| **Associated job responses** | Links to all job responses in the period | FK relationships to `JobResponse` records |
| **Record entries** | Individual data points, observations, and events | `WorkRecordEntry` detail records |

### Key Performance Indicators (KPIs)

Part 4 (Clause 14) describes a model for defining and tracking KPIs within manufacturing operations. KPIs aggregate work performance data into meaningful metrics.

| Attribute | Description | Fuuz Mapping |
|-----------|-------------|--------------|
| **KPI definition** | What is being measured, how it is calculated | `KPIDefinition` with formula, target, and UOM |
| **KPI value** | Calculated result for a specific time period and scope | `KPIValue` record with timestamp, value, and scope (line, area, site) |
| **KPI scope** | What level of the hierarchy this KPI applies to | Scope FK to equipment hierarchy model |
| **KPI trend** | Historical values showing change over time | Time series of `KPIValue` records |

**Fuuz Implementation:**
- Work records are implemented as `ProductionLog` entries with comprehensive links to all related data
- KPI definitions are configured as dashboard metrics on Fuuz Screens
- KPI calculations can be performed by Fuuz Flows on a scheduled basis (shift-end, day-end)
- Standard manufacturing KPIs (OEE, yield, first-pass yield, MTBF, MTTR) are calculated from the underlying data models
- KPI dashboards provide drill-down from high-level metrics to the underlying job orders and events

---

## Summary: Part 4 Operations Management and Fuuz Mapping

| ISA-95 Part 4 Concept | Description | Key Fuuz Models |
|-----------------------|-------------|-----------------|
| Business vs Work View | Enterprise perspective vs shop floor perspective | `WorkOrder` (business) vs `JobOrder` (work) |
| Work Master | Reusable work template/pattern | `WorkMaster` or `Operation` |
| Work Directive | Product-specific work instance | `WorkDirective` or `RoutingStep` |
| Job Order | Authorized execution unit with state machine | `JobOrder` with status state machine |
| Job Order State Machine | Waiting/Ready/Running/Paused/Complete/Aborted/Closed | `JobOrder.status` with Flow-enforced transitions |
| Job Response | Actual execution results | `JobResponse` with actuals |
| Work Capability | Current ability to perform work | Dynamic capability calculation from resource status |
| Work Alert | Notification of conditions requiring attention | `Alert` model with priority and escalation |
| Work Calendar | Facility operating schedule | `WorkCalendar` + `WorkCalendarEntry` |
| Work Record | Historical archive of operations | `WorkRecord` linking all execution data |
| KPI | Aggregated performance metrics | `KPIDefinition` + `KPIValue` |
