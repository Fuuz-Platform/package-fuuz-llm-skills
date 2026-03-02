---
name: isa-88
description: "ISA-88 (S88) batch control standard reference for the Fuuz Industrial Operations Platform. Covers physical models, process models, procedural control, recipe management, PackML state machine, equipment hierarchy, and ISA-95 integration. Trigger when users mention ISA-88, S88, batch control, batch processing, PackML, state machine, state model, recipe management, process cell, unit procedure, equipment phase, procedural control, batch recipe, general recipe, master recipe, control recipe, equipment module, control module, workcenter state transitions, Hold/Unhold, Suspend/Unsuspend, or machine modes."
---

# ISA-88 Batch Control Skill

> **Purpose:** Provide Claude with a working knowledge of the ISA-88 (ANSI/ISA-88.00.01-2010) batch control standard and the PackML state machine (ISA-TR88.00.02-2022) so it can make informed decisions when designing Fuuz data models, flows, screens, and workcenter state management for batch and discrete manufacturing.

ISA-88 defines the standard models and terminology for batch manufacturing control. It separates *what* to make (recipes) from *how* equipment makes it (equipment control), enabling flexible, reusable automation. The PackML technical report extends these concepts to discrete and packaging machinery with a standardized state machine used across all types of automated equipment.

---

## 1. Physical Model (Equipment Hierarchy)

The ISA-88 physical model organizes manufacturing equipment into a hierarchy. The top three levels are defined in more detail by ISA-95; the bottom four are the core of ISA-88.

```
Enterprise                  (ISA-95 scope)
  Site                      (ISA-95 scope)
    Area                    (ISA-95 scope)
      Process Cell          --- ISA-88 boundary ---
        Unit
          Equipment Module
            Control Module
```

| ISA-88 Level | Definition | Fuuz Mapping |
|---|---|---|
| **Enterprise** | Organization operating one or more sites | Tenant |
| **Site** | Physical/geographical subdivision of enterprise | Site model |
| **Area** | Subdivision of a site containing process cells | Area model |
| **Process Cell** | Logical grouping of equipment to produce batches (= ISA-95 Work Center) | Line or Cell (Workcenter parent) |
| **Unit** | Equipment performing one or more major processing activities (= ISA-95 Work Unit) | Workcenter / Asset |
| **Equipment Module** | Functional group performing minor processing activities (e.g., dosing system, agitator) | Asset (child) or DataPoint group |
| **Control Module** | Lowest-level grouping: sensors, actuators, control loops | DataPoint |

**Key rules:**
- A Process Cell must contain at least one Unit.
- A Unit operates on only one batch at a time (in batch processing).
- Equipment Modules may be recipe-aware (directly initiated by recipes) or generic.
- Control Modules are the interface to physical equipment (sensors, valves, motors).

---

## 2. Process Model

The process model describes *what* needs to happen, independent of equipment. It is the basis for general and site recipe procedures.

```
Process
  Process Stage         (major independent part of a process)
    Process Operation   (major processing activity, e.g., react, charge)
      Process Action    (minor processing activity, e.g., add catalyst, heat)
```

**Fuuz mapping:** The process model maps conceptually to how work orders and production flows define the sequence of manufacturing steps. A Fuuz work order's operations list aligns with process operations; individual steps within those operations align with process actions.

---

## 3. Procedural Control Model

The procedural control model describes *how* equipment executes the process. It is the basis for master and control recipe procedures.

```
Procedure                 (entire batch, runs across process cell)
  Unit Procedure          (contiguous production in a single unit)
    Operation             (major processing sequence in one unit)
      Phase               (lowest procedural element, e.g., "Add Material")
```

| Procedural Level | Scope | Fuuz Mapping |
|---|---|---|
| **Procedure** | Entire batch across process cell | Work Order execution plan |
| **Unit Procedure** | One unit, contiguous production | Workcenter-level operation sequence |
| **Operation** | Major processing task in one unit | Operation step on a work order |
| **Phase** | Smallest process-oriented task | Equipment command / flow trigger |

**Relationship between models:**

```
Process Model          Procedural Model         Physical Model
-----------            ----------------         --------------
Process           -->  Procedure           -->  Process Cell
Process Stage     -->  Unit Procedure      -->  Unit
Process Operation -->  Operation           -->  (within Unit)
Process Action    -->  Phase               -->  Equipment Module
```

---

## 4. Recipe Types

ISA-88 defines four recipe types, progressing from abstract to executable:

| Recipe Type | Scope | Equipment Dependency | Fuuz Analog |
|---|---|---|---|
| **General Recipe** | Enterprise-wide | Independent | Product specification / BOM |
| **Site Recipe** | Site-specific | Independent | Site-specific product config |
| **Master Recipe** | Process-cell-specific | Dependent | Routing / operation template |
| **Control Recipe** | Single batch execution | Dependent | Active work order with parameters |

**Recipe contents** (all types):
- **Header** -- identification, version, approval status
- **Formula** -- process inputs (materials), process parameters (temp, time), process outputs (expected yield)
- **Equipment Requirements** -- constraints on allowable equipment
- **Procedure** -- ordered steps (structure depends on recipe type)
- **Other Information** -- regulatory, safety, packaging info

**Key principles:**
- General/Site recipes use the Process Model structure (equipment-independent).
- Master/Control recipes use the Procedural Control Model structure (equipment-dependent).
- A control recipe is created from a master recipe for each batch, then may be modified during execution.
- Master and control recipes are mandatory; general and site may be omitted.

---

## 5. PackML State Machine

The PackML state machine (ISA-TR88.00.02-2022) defines a standard set of states and transitions for automated machines and units. This is the primary reference for Fuuz workcenter state management.

### 5.1 The 17 States

| # | State | Type | Description |
|---|---|---|---|
| 1 | **Stopped** | Wait | Machine powered, stationary. Requires Reset to proceed. |
| 2 | **Resetting** | Acting | Clears faults, prepares machine for Idle. |
| 3 | **Idle** | Wait | Ready for production. Waiting for Start command. |
| 4 | **Starting** | Acting | Performing ramp-up steps to begin execution. |
| 5 | **Execute** | Acting | Actively producing / processing material. |
| 6 | **Completing** | Acting | Wrapping up normal production (end of run). |
| 7 | **Complete** | Wait | Production run finished. Waiting for Reset. |
| 8 | **Holding** | Acting | Bringing to controlled stop for **internal** condition (e.g., refill glue). |
| 9 | **Held** | Wait | Stopped due to internal condition. Waiting for Unhold. |
| 10 | **Unholding** | Acting | Restarting from Held state back to Execute. |
| 11 | **Suspending** | Acting | Bringing to controlled stop for **external** condition (blocked/starved). |
| 12 | **Suspended** | Wait | Stopped due to external condition (upstream/downstream). |
| 13 | **Unsuspending** | Acting | Returning from Suspended back to Execute. |
| 14 | **Stopping** | Acting | Controlled stop in response to Stop command. |
| 15 | **Aborting** | Acting | Emergency/rapid stop from any state. |
| 16 | **Aborted** | Wait | Faulted. Requires Clear command, then manual intervention. |
| 17 | **Clearing** | Acting | Clearing faults from Aborted before going to Stopped. |

**Critical distinction:**
- **Holding/Held** = internal machine issue (operator refills, internal fault). Triggered by Hold command.
- **Suspending/Suspended** = external line issue (upstream starved, downstream blocked). Triggered by Suspend command.

### 5.2 State Transition Diagram (Simplified)

```
                            [Abort from any state] --> ABORTING --> ABORTED
                                                                      |
                                                                   [Clear]
                                                                      v
    STOPPED <--[SC]-- STOPPING <--[Stop from most states]         CLEARING
       |                                                             |
    [Reset]                                                       [SC]
       v                                                             v
    RESETTING --[SC]--> IDLE                                     STOPPED
                          |
                       [Start]
                          v
                       STARTING --[SC]--> EXECUTE --[Complete]--> COMPLETING --[SC]--> COMPLETE
                                            |   ^                                        |
                                         [Hold] |                                     [Reset]
                                            v   |                                        v
                                         HOLDING [Unhold]                             RESETTING
                                            |     ^
                                         [SC]     |
                                            v     |
                                           HELD --+

                                          EXECUTE
                                            |   ^
                                      [Suspend] |
                                            v   |
                                       SUSPENDING [Unsuspend]
                                            |       ^
                                         [SC]       |
                                            v       |
                                        SUSPENDED --+
                                            |
                                       [Unsuspend]
                                            v
                                      UNSUSPENDING --[SC]--> EXECUTE
```

SC = State Complete (acting state finishes its procedure)

### 5.3 State Commands

| Command | From States | To State |
|---|---|---|
| **Start** | Idle | Starting |
| **Hold** | Execute, Suspended | Holding |
| **Unhold** | Held | Unholding |
| **Suspend** | Execute | Suspending |
| **Unsuspend** | Suspended | Unsuspending |
| **Complete** | Execute, Held, Suspended | Completing |
| **Stop** | Most acting/wait states (except Aborting, Aborted) | Stopping |
| **Abort** | Any state (except Aborting, Aborted) | Aborting |
| **Clear** | Aborted | Clearing |
| **Reset** | Stopped, Complete | Resetting |

### 5.4 Unit/Machine Modes

Modes define which subset of states, commands, and transitions are active:

| Mode | Value | Purpose |
|---|---|---|
| **Production** | 1 | Normal production operation. Full state model. |
| **Maintenance** | 2 | Individual machine testing/trials independent of line. |
| **Manual** | 3 | Direct control of individual machine modules (jog, index). |
| **User-defined** | 4-31 | Custom modes (Clean-In-Place, Dry Cycle, Run-Out, etc.) |

**Mode behavior:**
- Each mode uses a subset of the base state model (states can be disabled per mode).
- The same state name may have different logic in different modes (e.g., Execute in Production = making product; Execute in Manual = jogging).
- Mode transitions may be restricted (e.g., cannot go from Production to Manual while in Execute).

### 5.5 Mapping PackML to Fuuz Workcenter States

When implementing workcenter state management in Fuuz:

| Fuuz Concept | PackML Concept |
|---|---|
| Workcenter `currentState` | Status.StateCurrent |
| Workcenter `currentMode` | Status.UnitModeCurrent |
| State change event | State command triggering transition |
| Workcenter state history | Time-stamped log of state transitions |
| Downtime reason | Admin.StopReason + state context |
| OEE availability | Calculated from time in Execute vs. other states |

**OEE time classification by state:**
- **Producing:** Execute
- **Planned Downtime:** Stopped (scheduled), Idle (waiting for schedule)
- **Unplanned Downtime:** Held, Suspended, Aborted, Stopping, Aborting
- **Changeover:** Resetting, Starting, Completing (depends on context)

---

## 6. ISA-88 / ISA-95 Integration

ISA-88 and ISA-95 are complementary. ISA-95 defines the enterprise-to-control-system information exchange; ISA-88 defines the control-system-level execution of batch processes.

### Equipment Model Alignment

| ISA-88 | ISA-95 | Fuuz |
|---|---|---|
| Enterprise | Enterprise | Tenant |
| Site | Site | Site |
| Area | Area | Area |
| Process Cell | Work Center | Line/Cell |
| Unit | Work Unit | Workcenter/Asset |
| Equipment Module | *(below ISA-95 scope)* | Asset (child) |
| Control Module | *(below ISA-95 scope)* | DataPoint |

### Activity Model Alignment

| ISA-88 Control Activity | ISA-95 Production Activity | Fuuz Implementation |
|---|---|---|
| Recipe Management | Product Definition Management | Product/BOM/routing configuration |
| Production Planning & Scheduling | Detailed Scheduling, Dispatching | Work order scheduling, dispatch flows |
| Production Information Management | Data Collection, Tracking, Analysis | Production history, OEE dashboards |
| Process Management | Production Execution Management | Workcenter execution flows |
| Unit Supervision | *(Level 2 -- below ISA-95 scope)* | Equipment-level flow logic |
| Process Control | *(Level 1-2 -- below ISA-95 scope)* | PLC/device gateway integration |

### Key Integration Points

- **Recipes to Work Orders:** ISA-95 production schedules become ISA-88 batch schedules. In Fuuz, work orders (ISA-95 segment) carry recipe parameters (ISA-88 formula) to the workcenter.
- **State Reporting Upward:** ISA-88 state transitions at the unit/process cell level feed ISA-95 production performance data. In Fuuz, workcenter state history feeds OEE and production dashboards.
- **Resource Management:** ISA-95 manages resources at the work center level; ISA-88 manages allocation/arbitration within the process cell. In Fuuz, equipment availability and material tracking span both concerns.

---

## 7. Cross-References

- **isa-95** -- Enterprise-control integration framework. Equipment hierarchy levels 0-4, functional model, information exchange categories. Use ISA-95 for enterprise-level context and ISA-88 for control-level detail.
- **fuuz-industrial-ops** -- Workcenter state management implementation, alarm evaluation, OEE time classification, UNS topic structure. The practical Fuuz patterns that implement ISA-88/PackML concepts.
- **fuuz-schema** -- Batch-oriented data model design. Use ISA-88 physical model to guide equipment hierarchy design; use recipe model to guide product/BOM/routing schemas.
- **fuuz-flows** -- Batch process automation flows. Use ISA-88 procedural control model and PackML state machine to design state transition flows, recipe execution flows, and production tracking flows.
