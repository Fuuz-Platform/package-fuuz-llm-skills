# ISA-95 Part 2: Object Models

**Reference:** ANSI/ISA-95.00.02 / IEC 62264-2
**Scope:** Defines the object models used to exchange information between enterprise and manufacturing operations systems. These models describe the "nouns" -- the data structures representing people, equipment, materials, and processes that flow across the Level 3/4 boundary.

> **Copyright Note:** ISA-95 is a copyrighted standard published by the International Society of Automation. The descriptions below are written in our own words to explain the concepts and their application to the Fuuz platform. Clause references are provided so readers can consult the source standard for normative definitions.

---

## Core Design Pattern: Class, Instance, Property

Before examining individual models, it is essential to understand the extensibility pattern that Part 2 applies consistently across all resource models (as introduced in ISA-95 Part 2, Clause 5). Every resource model follows a three-tier structure:

| Tier | Purpose | Example |
|------|---------|---------|
| **Class** | Template or category defining common characteristics | "CNC Machine", "Machinist", "Steel Alloy" |
| **Instance** | A specific individual belonging to a class | "CNC-42 on Line 3", "Jane Smith", "Lot 2025-0847" |
| **Property** | Named attribute with value, unit of measure, and optional description | "Spindle Speed = 12000 RPM", "Certification Expiry = 2026-06-15" |

Properties can be defined at the class level (as templates or defaults) and overridden or extended at the instance level. This pattern allows systems to define standard expectations at the class level and capture actual values at the instance level.

**Fuuz Mapping:** This class/instance/property pattern maps directly to Fuuz schema design:
- **Class** = Fuuz reference model (e.g., `EquipmentClass`, `MaterialDefinition`) with standard field set
- **Instance** = Fuuz operational record (e.g., `Asset`, `Lot`) linked to its class via foreign key
- **Property** = Fuuz dynamic property model or JSON property bag on the instance, keyed by property name with value and UOM fields

---

## Personnel Model

*Reference: ISA-95 Part 2, Clause 6*

The personnel model describes the people and qualifications relevant to manufacturing operations. It distinguishes between the general definition of a role and the specific person filling that role.

### Key Concepts

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Personnel Class** | A category of personnel sharing common properties and qualifications (e.g., "Certified Welder", "Quality Inspector") | `PersonnelClass` reference model |
| **Person** | A specific individual instance, potentially belonging to one or more personnel classes | `User` or `Operator` model with role assignments |
| **Personnel Class Property** | Attribute defined at the class level describing expected characteristics or qualifications | Properties on `PersonnelClass` (e.g., required certifications) |
| **Person Property** | Actual attribute values for a specific person, including test results and qualification status | Properties on the `User`/`Operator` record |
| **Qualification Test Specification** | Defines the criteria a person must meet to be considered qualified for a personnel class | `CertificationRequirement` model linked to `PersonnelClass` |
| **Qualification Test Result** | The outcome of evaluating a person against a qualification test | `CertificationRecord` on the `User`/`Operator` |

### Capability Testing Pattern

The personnel model supports a "capability test" pattern (Part 2, Clause 6.4): a qualification test specification defines what must be demonstrated, and a qualification test result records whether a specific person passed. This drives personnel capability determinations -- a person is capable of a task only if their qualification test results satisfy the test specifications required by the relevant personnel class.

**Fuuz Implementation:**
- `PersonnelClass` defines required certifications and training
- `Operator` records link to `CertificationRecord` entries tracking expiry dates
- Fuuz Flows can check operator qualification before allowing job order assignment
- Screens can display operator qualification status with visual indicators for expired or expiring certifications

---

## Equipment Model

*Reference: ISA-95 Part 2, Clause 7*

The equipment model describes role-based equipment -- meaning equipment defined by the function it performs within the manufacturing process, not by its physical composition. This is the model used for scheduling, capacity planning, and production tracking.

### Key Concepts

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Equipment Class** | A category of equipment sharing common properties and capabilities (e.g., "Filling Machine", "CNC Lathe") | `EquipmentClass` reference model |
| **Equipment** | A specific equipment instance performing a defined role in the hierarchy (maps to the role-based hierarchy from Part 1) | `Asset`, `Cell`, `Line`, or `Workcenter` depending on hierarchy level |
| **Equipment Class Property** | Standard attributes expected for all equipment of a class | Properties defined on `EquipmentClass` |
| **Equipment Property** | Actual attribute values for a specific equipment instance, including current status | Properties on the specific hierarchy model |
| **Capability Test Specification** | Criteria for determining if equipment is capable of performing its role | `EquipmentCapabilityTest` definition |
| **Capability Test Result** | Recorded outcome of testing equipment capability | `EquipmentTestResult` linked to the equipment record |
| **Maintenance Information** | References to maintenance data relevant to this equipment in its role | Link to `MaintenanceWorkOrder` and `MaintenanceLog` records |

### Equipment Hierarchy Levels in Part 2

Part 2 (Clause 7.2) reinforces the hierarchy from Part 1 and adds the object model detail. Equipment instances exist at each level (Enterprise, Site, Area, Work Center, Work Unit) and can reference their parent in the hierarchy. Each equipment instance may belong to one or more equipment classes.

**Fuuz Implementation:**
- The Fuuz asset hierarchy (`Enterprise` > `Site` > `Area` > `Line` > `Cell` > `Asset`) directly implements the role-based equipment hierarchy
- `EquipmentClass` provides a way to group equipment across hierarchy levels (e.g., all "Conveyor" equipment regardless of which line they serve)
- Equipment properties support both static configuration values and references to real-time data via `DataPoint` linkages
- Capability testing integrates with the maintenance module to track equipment readiness

---

## Physical Asset Model

*Reference: ISA-95 Part 2, Clause 8*

The physical asset model represents equipment organized by physical composition, ownership, or financial tracking rather than by functional role. This model exists separately from the equipment model because the same physical thing may serve different roles over time, and organizations need to track physical assets independently for maintenance, depreciation, and regulatory purposes.

### Key Distinction from Equipment Model

| Aspect | Equipment (Role-Based) | Physical Asset |
|--------|----------------------|----------------|
| **Organized by** | Function in the manufacturing process | Physical structure or financial ownership |
| **Identity** | Defined by what it does (its role) | Defined by what it is (serial number, model) |
| **Lifetime** | Exists as long as the role exists | Exists from acquisition to disposal |
| **Hierarchy** | Follows ISA-95 levels (Enterprise through Work Unit) | Follows physical containment or organizational structure |
| **Primary use** | Scheduling, dispatching, production tracking | Maintenance planning, capital tracking, spare parts |

### Key Concepts

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Physical Asset Class** | Category of physical assets with shared properties (e.g., "ABB IRB 6700 Robot") | `PhysicalAssetClass` reference model |
| **Physical Asset** | A specific trackable physical item with serial number and acquisition data | `PhysicalAsset` model with maintenance and financial fields |
| **Physical Asset Property** | Attributes of the physical asset (serial number, purchase date, depreciation schedule) | Fields and properties on `PhysicalAsset` |
| **Equipment-to-Physical-Asset mapping** | The relationship between a role-based equipment entry and the physical asset filling that role | FK from `Asset` (equipment role) to `PhysicalAsset` (physical thing) |

**Fuuz Implementation:**
- `PhysicalAsset` records track serial numbers, vendor information, purchase dates, warranty status, and depreciation
- A mapping table (or FK on the equipment record) connects the role-based equipment hierarchy to the physical asset that currently fills each role
- When a physical asset is swapped out (e.g., replacing a failed pump), the physical asset record moves but the equipment role remains the same
- Maintenance work orders can be written against either the equipment role or the physical asset, depending on whether the maintenance is role-specific or asset-specific

---

## Material Model

*Reference: ISA-95 Part 2, Clause 9*

The material model describes the materials consumed, produced, and tracked by manufacturing operations. It follows the same class/instance/property extensibility pattern and adds the concept of material lots and sublots for traceability.

### Key Concepts

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Material Class** | A category of materials sharing common properties (e.g., "Carbon Steel Sheet", "Lubricant") | `MaterialClass` reference model |
| **Material Definition** | A specific type of material with defined properties, often corresponding to a part number or SKU | `Product` or `MaterialDefinition` model |
| **Material Lot** | A specific batch or quantity of a material definition that was produced, received, or managed as a unit | `Lot` model with quantity, status, and traceability data |
| **Material Sublot** | A portion of a lot that is tracked separately, often due to being stored in a different location or having different test results | `Sublot` model linked to parent `Lot` |
| **Material Class Property** | Standard properties expected for materials in a class | Properties on `MaterialClass` |
| **Material Definition Property** | Properties specific to a material definition, including specifications and tolerances | Properties/specs on `Product` or `MaterialDefinition` |
| **Material Lot Property** | Actual measured or assigned property values for a specific lot | Properties on `Lot` records |
| **Material Test Specification** | Criteria that material must meet (incoming inspection requirements, in-process tests) | `QualityTestSpec` linked to `MaterialDefinition` |
| **Material Test Result** | Outcome of testing a lot or sublot against a test specification | `QualityResult` or `InspectionLog` record |

### Lot Genealogy and Traceability

Part 2 (Clause 9.5) describes the assembly relationship between material lots -- how lots of raw materials are consumed to produce lots of finished goods. This genealogy forms the traceability chain from finished product back to incoming materials.

**Fuuz Implementation:**
- `Lot` records track material through the production process with status (available, in-use, quarantined, consumed)
- `LotGenealogy` or consumption records link parent lots (inputs) to child lots (outputs) through production operations
- `InventoryTransaction` records every movement, consumption, and production of material lots
- Barcode/RFID scanning at workstations captures lot identity, enabling real-time traceability
- Quality test results attached to lots drive disposition decisions (release, hold, reject)

---

## Process Segment Model

*Reference: ISA-95 Part 2, Clause 10*

The process segment model describes the generic capabilities of a manufacturing operation -- what a facility can do, independent of any specific product. A process segment defines a logical grouping of personnel, equipment, material, and physical assets needed to perform a step of manufacturing.

### Key Concepts

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Process Segment** | A generic description of a manufacturing step or capability, independent of product | `Operation` or `ProcessStep` model |
| **Process Segment Parameter** | Configurable values that modify how the segment executes (e.g., temperature setpoint, speed) | `OperationParameter` with name, value, UOM, and type |
| **Personnel Segment Specification** | The personnel requirements for a process segment (roles, quantities, qualifications needed) | Personnel requirements linked to `Operation` |
| **Equipment Segment Specification** | The equipment requirements for a process segment (equipment class, quantity, properties needed) | Equipment requirements linked to `Operation` |
| **Material Segment Specification** | The material requirements for a process segment, categorized as consumed, produced, or consumed/produced | Material specs linked to `Operation` (BOM-like relationships) |
| **Physical Asset Segment Specification** | Physical asset requirements for a process segment | Asset requirements linked to `Operation` |

### Parameter Types

Process segment parameters support different usage types (Part 2, Clause 10.4):

| Type | Meaning | Fuuz Example |
|------|---------|--------------|
| **Descriptive** | Information about the segment, not used for control | "This step performs final assembly" |
| **Prescriptive** | Target values that should be achieved | "Target temperature = 180C" |
| **Reportable** | Values that must be recorded during execution | "Record actual torque value" |

### Material Use Categories

Materials referenced by a process segment are categorized by how they are used (Part 2, Clause 10.6):

| Category | Meaning | Example |
|----------|---------|---------|
| **Consumed** | Material used up during the segment | Raw material inputs, consumables |
| **Produced** | Material created by the segment | Finished goods, intermediates |
| **Consumed and Produced** | Material transformed (tracked in and out) | Material undergoing processing (same lot ID, different state) |

**Fuuz Implementation:**
- `Operation` models define generic process capabilities with parameters
- Each operation specifies its resource requirements (personnel, equipment, material) as linked specification records
- Operations are product-independent -- they describe what the facility can do
- When combined with a specific product definition, operations become `RoutingStep` entries (product segments in ISA-95 terminology)

---

## Operations Definition Model

*Reference: ISA-95 Part 2, Clause 11*

The operations definition model describes how to manufacture a specific product by composing process segments with product-specific parameters. This bridges the gap between generic capabilities (process segments) and specific manufacturing instructions.

### Key Concepts

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Operations Definition** | A complete set of instructions for manufacturing a product, combining one or more product segments | `Routing` model |
| **Product Segment** | A product-specific version of a process segment, with actual parameter values for a particular product | `RoutingStep` with product-specific parameters |
| **Bill of Material** | The material requirements derived from combining product segment material specifications | `BOM` model linked to `Product` |
| **Bill of Resources** | The combined personnel and equipment requirements across all product segments | Resource requirements on `Routing` |

The relationship chain is: Operations Definition contains Product Segments, which reference Process Segments and add product-specific parameter values.

**Fuuz Implementation:**
- `Routing` = Operations Definition (how to make Product X)
- `RoutingStep` = Product Segment (step 1: mix at 180C for 30 min using Process Segment "Mixing")
- `BOM` = Material requirements aggregated across all routing steps
- `Product` ties together the routing, BOM, and quality specifications

---

## Operations Schedule, Performance, and Capability Models

*Reference: ISA-95 Part 2, Clauses 12-14*

These models define the information exchanged between Level 4 and Level 3 for planning and executing manufacturing operations.

### Operations Schedule (Clause 12)

Represents requests from Level 4 to Level 3 to perform work. A schedule contains one or more requests, each specifying what to produce, how much, when, and with what resources.

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Operations Schedule** | Collection of operations requests for a time period | Collection of `WorkOrder` records for a shift/day |
| **Operations Request** | A single request to perform work (produce quantity X of product Y) | `WorkOrder` model |
| **Segment Requirement** | Specific resource and parameter requirements for one segment of the request | `WorkOrderStep` or operation-level details on `WorkOrder` |

### Operations Performance (Clause 13)

Represents responses from Level 3 to Level 4 reporting what work was actually performed.

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Operations Performance** | Collection of performance responses for a time period | Collection of `ProductionLog` records |
| **Operations Response** | Actual results for a single operations request | `ProductionLog` or `WorkOrderResult` |
| **Segment Response** | Actual resource usage and results for one segment | Step-level actuals on the production log |

### Operations Capability (Clause 14)

Represents information about what Level 3 is currently able to do, reported to Level 4 for planning purposes.

| Concept | Description | Fuuz Mapping |
|---------|-------------|--------------|
| **Operations Capability** | Statement of current manufacturing capability | `WorkcenterCapability` or capacity model |
| **Segment Capability** | Capability at the process segment level (what equipment/personnel/material is available) | Resource availability records |

Capability is typically expressed in terms of committed, available, and unattainable capacity (as discussed in the OEE section of the main SKILL.md).

**Fuuz Implementation:**
- `WorkOrder` records flow from ERP (Level 4) into Fuuz (Level 3) via GraphQL mutations or ERP integration flows
- `ProductionLog` records flow from Fuuz back to ERP with actual quantities, times, and resource usage
- `WorkcenterCapability` is derived from current equipment status, operator availability, and material stock levels
- Fuuz Flows automate the bidirectional exchange: receiving schedules, collecting actuals, and reporting performance

---

## Summary: Part 2 Object Models and Fuuz Mapping

| ISA-95 Part 2 Model | Primary Purpose | Key Fuuz Models |
|---------------------|-----------------|-----------------|
| Personnel Model | Define and track people and qualifications | `User`, `Operator`, `PersonnelClass`, `CertificationRecord` |
| Equipment Model | Define role-based equipment for scheduling/tracking | `Asset`, `Cell`, `Line`, `Workcenter`, `EquipmentClass` |
| Physical Asset Model | Track physical items for maintenance/finance | `PhysicalAsset`, `PhysicalAssetClass` |
| Material Model | Define and track materials, lots, and sublots | `Product`, `MaterialDefinition`, `Lot`, `Sublot`, `MaterialClass` |
| Process Segment Model | Define generic manufacturing capabilities | `Operation`, `ProcessStep`, `OperationParameter` |
| Operations Definition | Define product-specific manufacturing instructions | `Routing`, `RoutingStep`, `BOM` |
| Operations Schedule | Communicate work requests from L4 to L3 | `WorkOrder`, `WorkOrderStep` |
| Operations Performance | Communicate actual results from L3 to L4 | `ProductionLog`, `WorkOrderResult` |
| Operations Capability | Communicate available capacity from L3 to L4 | `WorkcenterCapability`, resource availability |
