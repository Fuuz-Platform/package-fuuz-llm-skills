---
name: isa-95
description: "ISA-95 (ANSI/ISA-95.00.01) enterprise-control system integration standard reference for the Fuuz platform. Use when requests mention \"ISA-95\", \"ISA 95\", \"S95\", \"IEC 62264\", \"enterprise-control integration\", \"equipment hierarchy\", \"role-based equipment\", \"physical asset model\", \"functional hierarchy\", \"Level 0-4\", \"manufacturing operations management\", \"MOM\", \"MO&C\", \"work center\", \"work unit\", \"process segment\", \"product segment\", \"production capability\", or when designing asset hierarchies, UNS topic structures, work order models, or OEE systems aligned with ISA-95 on Fuuz."
---

# ISA-95 Reference for Fuuz Development

**Version 1.0** | Last Updated: 2026-03-02

ISA-95 (ANSI/ISA-95.00.01-2025 / IEC 62264-1) defines standard models and terminology for integrating enterprise business systems with manufacturing operations and control systems. It establishes the boundary between Level 4 (business planning and logistics) and Level 3 (manufacturing operations management), providing hierarchy models, functional models, and information models that form the foundation for MES/MOM implementations.

## When to Use This Skill

Use **isa-95** when you need to:
- Design or validate equipment/asset hierarchies following ISA-95 patterns
- Understand the distinction between role-based equipment and physical assets
- Map ISA-95 functional levels (0-4) to Fuuz platform layers
- Structure UNS topics according to ISA-95 hierarchy
- Model manufacturing operations data (production, maintenance, quality, inventory)
- Define information flows between enterprise (ERP) and control (MES/Fuuz) systems

**This is a standards reference skill.** For building the actual implementations, use the corresponding builder skills (fuuz-schema, fuuz-flows, fuuz-screens, fuuz-industrial-ops).

---

## Equipment Hierarchy — The Core of ISA-95

### Role-Based Equipment Hierarchy

The role-based hierarchy defines equipment by the Level 3/4 functions it performs, not its physical composition:

```
Enterprise
└── Site
    └── Area
        └── Work Center (Production Line | Production Unit | Process Cell | Storage Zone)
            └── Work Unit (Work Cell | Unit | Storage Unit)
```

| Level | ISA-95 Term | Scope | Fuuz Mapping |
|-------|-------------|-------|--------------|
| Top | **Enterprise** | Collection of sites; determines what products are made and where | `Enterprise` model |
| 2 | **Site** | Physical/geographical grouping; local management and optimization | `Site` model |
| 3 | **Area** | Grouping within a site; most Level 3 functions occur here | `Area` model |
| 4 | **Work Center** | Scheduled by Level 3/4; has defined capabilities and capacities | `Line` or `Workcenter` model |
| 5 | **Work Unit** | Lowest level scheduled by Level 3; within a work center | `Cell` or `Asset` model |

**Work Center types by manufacturing strategy:**

| Type | Strategy | Contains | Fuuz Example |
|------|----------|----------|--------------|
| Production Line | Discrete/repetitive | Work Cells | Bottling Line, Assembly Line |
| Production Unit | Continuous | Units | Cracker Unit, Reactor Train |
| Process Cell | Batch | Units | Mixing Cell, Fermentation Cell |
| Storage Zone | Material movement | Storage Units | Warehouse, Tank Farm |

### Role-Based vs Physical Asset Hierarchy

ISA-95 defines two distinct but related hierarchies:

| Aspect | Role-Based Equipment | Physical Asset |
|--------|---------------------|----------------|
| **Organized by** | Function performed (Level 3/4 roles) | Physical composition or cost center |
| **Purpose** | Scheduling, capacity planning, production tracking | Maintenance, financial tracking, asset lifecycle |
| **Relationship** | May overlap at any level; elements may correspond | Often has additional or differently-named levels |
| **Fuuz usage** | Asset hierarchy (Enterprise > Site > Area > Line > Cell > Asset) | Physical asset records with maintenance and financial data |

A single physical asset (e.g., "Test Machine 12") may serve in a role-based position (e.g., Work Unit on Line 52) but is tracked independently for maintenance and depreciation.

---

## Functional Hierarchy (Levels 0-4)

ISA-95 defines five logical levels that operate on different time frames:

| Level | Name | Time Frame | Scope | Fuuz Layer |
|-------|------|------------|-------|------------|
| **4** | Business Planning & Logistics | Months, weeks, days | Plant schedule, inventory levels, material delivery | ERP systems (Plex, NetSuite, SAP) |
| **3** | Manufacturing Operations Management | Days, shifts, hours, minutes | Workflow control, production records, dispatching | **Fuuz platform** (flows, screens, data models) |
| **2** | Monitoring & Supervisory Control | Hours, minutes, seconds | Automated/manual control of production process | SCADA, DCS, HMI systems |
| **1** | Sensing & Manipulating | Seconds and faster | Sensors, actuators, direct process interaction | PLCs, RTUs, device gateways |
| **0** | Physical Process | Continuous | The actual production process | Physical equipment and materials |

**The ISA-95 boundary is between Level 3 and Level 4.** Fuuz operates primarily at Level 3, receiving schedules from Level 4 (ERP) and collecting data from Levels 0-2 (control systems via device gateways and MQTT).

### Level 3 Inclusion Criteria

An activity belongs at Level 3 if it involves personnel, equipment, or material and is critical to:
- Plant safety or reliability
- Plant efficiency
- Product quality
- Regulatory compliance

---

## Manufacturing Operations Management (MOM)

ISA-95 defines four categories of operations management, all following the same information pattern:

| Category | ISA-95 Term | Fuuz Implementation |
|----------|-------------|---------------------|
| **Production** | Production Operations Management | Work orders, production logs, OEE tracking |
| **Maintenance** | Maintenance Operations Management | Maintenance requests, work orders, equipment health |
| **Quality** | Quality Operations Management | Quality tests, inspections, COAs, waivers |
| **Inventory** | Inventory Operations Management | Material tracking, storage, movement, WMS |

### Information Pattern (applies to all four categories)

Each category exchanges four types of information between Level 3 and Level 4:

| Information Type | Direction | Content | Fuuz Example |
|------------------|-----------|---------|--------------|
| **Schedule/Request** | L4 -> L3 | What work to perform | Work order from ERP |
| **Performance/Response** | L3 -> L4 | What work was done | Production counts back to ERP |
| **Capability** | L3 -> L4 | What resources are available | Equipment status, capacity |
| **Definition** | L4 -> L3 | How to make a product | BOMs, routings, SOPs |

---

## How ISA-95 Maps to Fuuz

### Asset Hierarchy Implementation

Fuuz implements ISA-95 as nested reference models with code-based foreign keys:

```
Enterprise                    (ISA-95: Enterprise)
└── Site                      (ISA-95: Site)
    └── Area                  (ISA-95: Area)
        └── Line              (ISA-95: Work Center — Production Line)
            └── Cell          (ISA-95: Work Unit — Work Cell)
                └── Asset     (ISA-95: Equipment below Work Unit scope)
                    └── DataPoint  (Tags, sensors, calculated values)
```

**Key design decisions:**
- `Workcenter` links to either `Cell` or `Line` (the ISA-95 scheduling boundary)
- `DataPoint` captures Level 0-2 data flowing into Level 3
- Each level has `code`, `name`, and a FK to its parent

### UNS Topic Structure

UNS topics follow the ISA-95 hierarchy with `/` separators:

```
fuuz/{site}/{area}/{line}/{cell}/{equipment}/{datatype}
```

Example: `fuuz/PLT01/MFG/LN01/CL01/SUB-250/telemetry`

### ISA-95 Information Models in Fuuz

| ISA-95 Concept | Fuuz Data Model | Purpose |
|----------------|-----------------|---------|
| Production Schedule | `WorkOrder` | Schedule received from ERP (L4 -> L3) |
| Production Performance | `ProductionLog` | Actual production results (L3 -> L4) |
| Production Capability | `Workcenter` + `WorkcenterHistory` | Equipment status and capacity |
| Product Definition | `Product`, `BOM`, `Routing` | How to make a product |
| Process Segment | `Operation` / `ProcessStep` | Generic capability definition |
| Product Segment | `RoutingStep` | Product-specific process step |
| Equipment Status | `Mode`, `State`, `Event` | OEE time classification |
| Material Tracking | `InventoryTransaction`, `Lot` | Inventory operations |
| Maintenance Request | `MaintenanceWorkOrder` | Maintenance operations |
| Quality Test | `InspectionLog`, `QualityResult` | Quality operations |

### OEE Alignment

OEE maps directly to ISA-95 capacity concepts:

| ISA-95 Capacity | OEE Metric | Fuuz Field |
|-----------------|------------|------------|
| Committed capacity (used) | Run Time | `WorkcenterHistory.runTime` |
| Unattainable capacity (unplanned) | Unplanned Downtime | `WorkcenterHistory.unplannedDowntime` |
| Unattainable capacity (planned) | Planned Downtime | `WorkcenterHistory.plannedDowntime` |
| Available capacity | Available Time | Calculated from schedule |

---

## Key Data Flows Between Enterprise and Control

The primary information exchanges that Fuuz mediates between Level 4 (ERP) and Levels 0-2 (control):

```
ERP (Level 4)                    Fuuz (Level 3)                   Control (Level 0-2)
─────────────                    ──────────────                   ───────────────────
Production Schedule ──────────>  Work Order Management
                                 Dispatching & Scheduling
Production Performance <────────  Production Tracking  <────────  Sensor Data (MQTT)
Production Capability <─────────  Equipment Status     <────────  PLC/Device Status
Product Definition ────────────>  Recipe/BOM Management
Material Requirements <─────────  Inventory Tracking   <────────  Barcode/RFID Events
Maintenance Schedule ──────────>  Maintenance Execution
Quality Standards ─────────────>  Quality Testing      <────────  Inline QC Sensors
```

---

## Cross-References to Other Fuuz Skills

| Skill | Relationship to ISA-95 | When to Use Together |
|-------|----------------------|----------------------|
| **fuuz-industrial-ops** | ISA-95 asset hierarchy implementation, UNS topic patterns, OEE time classification, workcenter state management | Building industrial operations aligned with ISA-95 hierarchy and MOM categories |
| **fuuz-schema** | Data model design following ISA-95 entity patterns (equipment, materials, personnel, segments) | Designing models for work orders, production logs, BOMs, or asset hierarchies |
| **fuuz-flows** | Automation workflows for Level 3 manufacturing operations (production tracking, alarm evaluation, state transitions) | Implementing ISA-95 information exchanges as Fuuz data flows |
| **fuuz-graphql** | Querying ISA-95 aligned data models (equipment hierarchy traversal, production performance aggregation) | Building queries against ISA-95 structured data |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2026-03-02 | Initial release -- equipment hierarchy, functional levels, MOM categories, Fuuz mapping, information models, cross-references |
