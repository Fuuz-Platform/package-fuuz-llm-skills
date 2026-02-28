---
name: fuuz-documents
description: Design and build document templates, reports, and dashboards for the Fuuz Industrial Operations platform (fuuz.com). Use when requests mention "fuuz document", "document design", "report template", "Stimulsoft", ".mrt file", "document render", "report builder", "fuuz reports", "print template", "label template", "document designer", "report expressions", "barcode label", or building printable documents, reports, or dashboards within Fuuz applications. Covers Stimulsoft Reports integration, band types, expression syntax, data binding, report rendering, and document design patterns for manufacturing use cases (work order travelers, pick lists, inspection reports, shipping labels, production summaries).
---

# Fuuz Document Designer Skill

**Version 1.0.0** | Last Updated: 2026-02-27

Design and build document templates, reports, and printable documents for the Fuuz Industrial Operations Platform.

## Overview

Fuuz uses **Stimulsoft Reports & Dashboards** as its document engine. Documents are designed as `.mrt` template files using the built-in Document Designer interface, then rendered on-demand by the platform. Documents can be triggered from data flows, screen actions, or scheduled jobs.

## When to Use This Skill

Use **fuuz-documents** when you need to:
- Design report templates (.mrt files) for manufacturing documents
- Build work order travelers, pick lists, or inspection reports
- Create barcode/QR code labels for assets, inventory, or shipping
- Configure data bindings between Fuuz models and report templates
- Write Stimulsoft expressions for calculated fields, conditionals, and formatting
- Build dashboard visualizations using Stimulsoft Dashboards
- Troubleshoot document rendering issues

**This is a document patterns skill.** For building the data models that feed documents, use **fuuz-schema**. For building flows that trigger document rendering, use **fuuz-flows**. For screen buttons that generate documents, use **fuuz-screens**.

## Document Workflow in Fuuz

1. **Design** — Create a `.mrt` template in the Fuuz Document Designer
2. **Data Source** — Connect the template to Fuuz data models via GraphQL queries
3. **Trigger** — Render the document from a flow (DocumentRender node), screen action, or schedule
4. **Output** — PDF, Excel, Word, HTML, or image output

## System Models for Documents

| Model | Purpose |
|-------|---------|
| `DocumentDesign` | The template definition (stores the .mrt content) |
| `DocumentDesignVersion` | Versioned snapshots of the template |
| `DocumentRender` | Records of rendered documents (output files) |
| `DocumentRenderFormat` | Available output formats (PDF, Excel, etc.) |

## Key Concepts

### Band-Based Layout
Stimulsoft uses a band-based layout model. Bands are horizontal sections that control when and how data renders. See `references/stimulsoft-reports.md` for the complete band type reference.

### Expression Syntax
Expressions use curly braces: `{DataSource.FieldName}`. Supports:
- Direct field references: `{WorkOrder.workOrderNumber}`
- Calculations: `{WorkOrder.quantityCompleted / WorkOrder.quantityPlanned * 100}`
- Conditional formatting: `{IIF(WorkOrder.status == "Complete", "green", "red")}`
- Aggregation: `{Sum(OrderLines, OrderLines.quantity)}`

### Data Binding
Documents bind to Fuuz data via:
- **Direct data source** — GraphQL query results passed to the template
- **Variables** — Parameters passed at render time (e.g., record ID, date range)
- **Relations** — Parent-child joins for master-detail reports

## References

### `references/stimulsoft-reports.md`
Complete Stimulsoft Reports & Dashboards reference covering:
- Band types and rendering order
- Expression syntax and functions
- Component types (text, images, charts, barcodes, tables, cross-tabs)
- Data dictionary configuration
- Dashboard element types
- Rendering and export options
- Best practices for report design

## Common Manufacturing Document Patterns

### Work Order Traveler
- **Bands:** ReportTitle (header), DataBand (work order details), ChildBand (BOM lines), GroupFooter (sign-off)
- **Data:** WorkOrder → WorkOrderProcess → BOM items
- **Output:** PDF (one per work order)

### Inventory Pick List
- **Bands:** PageHeader (warehouse info), GroupHeader (zone/aisle), DataBand (item lines with barcodes)
- **Data:** PickList → PickListLine → Product → Location
- **Output:** PDF with barcode labels

### Inspection Report
- **Bands:** ReportTitle, DataBand (inspection points), GroupFooter (pass/fail summary)
- **Data:** InspectionRecord → InspectionPoint → Measurement
- **Output:** PDF with charts for measurement trends

### Shipping Label
- **Bands:** DataBand (one label per item)
- **Components:** Barcode (Code128/QR), text fields (address, PO, SKU)
- **Output:** Label printer format or PDF
