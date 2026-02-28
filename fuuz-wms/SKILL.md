---
name: fuuz-wms
description: Warehouse Management System (WMS) reference schema and patterns for the Fuuz Industrial Operations platform (fuuz.com). Use when requests mention "WMS", "warehouse management", "inventory tracking", "order processing", "cycle counting", "logistics", "site management", "order receiving", "warehouse layout", "storage locations", "pick list", "put-away", "receiving", "shipping", "lot tracking", "bin location", or building warehouse management applications on the Fuuz platform. Provides a complete WMS reference data schema (37 models, 556 fields, 124 relationships) organized into 7 functional modules, plus implementation patterns for warehouse operations.
---

# Fuuz WMS Reference Schema

**Version 1.0.0** | Last Updated: 2026-02-27

Complete reference data schema for building Warehouse Management System (WMS) applications on the Fuuz Industrial Operations Platform.

## Overview

This skill provides a production-ready WMS reference schema that can be used as a starting point for warehouse management applications built on Fuuz. The schema covers the core functional areas of warehouse operations and is designed to integrate with the Fuuz platform's auto-generated GraphQL API, data flow engine, and screen designer.

## Schema Summary

| Metric | Value |
|--------|-------|
| **Total Models** | 37 |
| **Total Relationships** | 124 |
| **Total Fields** | 556 |
| **System Models** | 1 |
| **Custom Models** | 36 |

## Functional Modules

The WMS schema is organized into 7 functional modules:

| Module | Description | Key Models |
|--------|-------------|------------|
| **Inventory Tracking** | Real-time stock levels and item locations | Adjustment, Area, Inventory, InventoryHistory, Location, Product |
| **Order Processing** | Customer/supplier orders and fulfillment | Order, OrderLine, OrderStatus, OrderType |
| **Cycle Counting** | Inventory verification and adjustments | CountHeader, CountLine, CountStatus |
| **Logistics** | Transportation and delivery management | Carrier, Shipment, ShipmentLine, ShipmentStatus |
| **Site Management** | Physical warehouse layout and storage | Site, Zone, Aisle, Rack, Level, Bin |
| **Order Receiving** | Inbound receipt and inspection workflows | ReceiptHeader, ReceiptLine, ReceiptStatus |
| **Testing** | Sensor measurement and equipment monitoring | Sensor, Measurement |

## When to Use This Skill

Use **fuuz-wms** when you need to:
- Design a warehouse management application on Fuuz from scratch
- Understand standard WMS data model patterns and relationships
- Reference a complete WMS schema as a starting point for customization
- Build inventory tracking, order processing, or logistics features
- Understand the data model behind common warehouse operations

**This is a domain reference skill.** For building the actual data models, use **fuuz-schema** and **fuuz-packages**. For building flows and screens, use **fuuz-flows** and **fuuz-screens**.

## References

### `references/wms-data-schema.md`
Complete WMS data schema documentation with:
- All 37 models organized by functional module
- Full field definitions with types and constraints
- Relationship mappings between models
- Module descriptions and organization

## Implementation Notes

- The reference schema is designed as an accelerator — customize model names, fields, and relationships for your specific warehouse requirements
- All models follow Fuuz naming conventions (PascalCase singular model names, camelCase fields)
- Enum/lookup models use `dataModelTypeId: "setup"`, transactional models use `"transactional"`
- The schema integrates with Fuuz standard system models (User, Unit, etc.)
