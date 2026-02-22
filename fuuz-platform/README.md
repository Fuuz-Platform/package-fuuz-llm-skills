# Fuuz Platform Reference Skill (fuuz-platform)

Shared platform knowledge for the Fuuz Industrial Operations Platform (fuuz.com).

---

## Overview

This skill provides cross-cutting reference material used by all other Fuuz skills. It covers platform architecture, terminology, system seeded values, connector configurations, device drivers, visualization library, and cross-skill routing.

**This is a reference skill — it does not produce artifacts.** For building data models, flows, or screens, use the corresponding builder skills.

**Covers:** Platform glossary (40+ concepts), dual API architecture, multi-tenancy model, 44 integration connectors, 13 device driver types, 19 device drivers, 80+ FusionCharts visualization types, 12 platform settings, package anatomy, and cross-skill task routing.

---

## When to Use

Activate this skill when requests mention:
- "fuuz platform", "fuuz architecture", "platform concepts"
- "system seeded values", "connectors", "device drivers", "device gateway"
- "platform settings", "tenant types", "fuuz glossary"
- "fuuz package anatomy", "application structure", "module hierarchy"
- "visualization library", "FusionCharts", "chart types"
- Cross-cutting questions that span data models, flows, and screens
- "which skill should I use", "where do I start"

---

## File Structure

```
fuuz-platform/
├── SKILL.md                                  (~300 lines)  Core reference: architecture, seeded values summary, cross-skill index
├── README.md                                 (~160 lines)  This file
└── references/
    ├── platform-glossary.md                  (~400 lines)  Definitions for all platform concepts
    ├── platform-architecture.md              (~400 lines)  Dual API, multi-tenancy, deployment, gateway architecture
    ├── cross-skill-index.md                  (~350 lines)  Task→skill routing, dependency graph, multi-skill workflows
    ├── package-anatomy.md                    (~350 lines)  High-level guide to Fuuz packages
    ├── connectors-reference.md               (~600 lines)  All 44 connectors categorized with auth types
    ├── system-seeded-values.md             (~2,150 lines)  Complete seeded values reference
    └── visualization-library.md            (~2,250 lines)  80+ FusionCharts types with data formats
```

---

## Quick Start

### Step 1: Read SKILL.md
Start with the core skill file for a platform overview, architecture summary, and quick references for all major topics.

### Step 2: Read Relevant Reference Files

| Task | Read These References |
|------|---------------------|
| New to Fuuz platform | `platform-glossary.md`, `platform-architecture.md` |
| Need to pick a skill for a task | `cross-skill-index.md` |
| Setting up a cloud integration | `connectors-reference.md` |
| Setting up device integration | `connectors-reference.md` (device drivers section) |
| Full connector credentials/options | `system-seeded-values.md` (Connectors + Device Gateway sections) |
| Building a dashboard with charts | `visualization-library.md` |
| Understanding Fuuz packages | `package-anatomy.md` |
| Looking up a specific seeded value | `system-seeded-values.md` |

---

## Companion Skills

| Scenario | Skill Needed | Why |
|----------|-------------|-----|
| Designing data models and schemas | **fuuz-schema** | Builder skill for the data layer |
| Building backend or web flows | **fuuz-flows** | Builder skill for server-side logic |
| Creating screens and UIs | **fuuz-screens** | Builder skill for user interfaces |
| Building a complete application | **All four skills** | Schema → Flows → Screens, with Platform for reference |

### Skill Relationship

```
                    ┌──────────────────┐
                    │  fuuz-platform   │
                    │  (reference)     │
                    └────────┬─────────┘
                             │ provides shared knowledge to
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
    │ fuuz-schema │  │ fuuz-flows  │  │ fuuz-screens│
    │ (models)    │  │ (logic)     │  │ (UI)        │
    └─────────────┘  └─────────────┘  └─────────────┘
```

---

## Key Concepts Summary

| Concept | Definition |
|---------|-----------|
| **Tenant** | Isolated environment with its own database (5 types) |
| **ModuleGroup** | Top-level application grouping |
| **Module** | Functional area within an app (groups models, flows, screens) |
| **Data Model** | Schema definition with auto-generated GraphQL API |
| **Data Flow** | Server-side logic (System, Screen, Edge, Integration, Document) |
| **Screen** | User interface definition (table, form, dashboard, custom) |
| **Package** | `.fuuz` tar archive for deploying applications |
| **Connector** | Cloud integration adapter (44 built-in) |
| **Device Driver** | On-premise device communication adapter (19 built-in) |
| **Sequence** | Auto-increment code generator |
| **Setting** | Platform configuration (locale, theme, formats) |

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | February 2026 | Initial release — glossary, architecture, connectors, cross-skill index, package anatomy |

---

**Platform:** [fuuz.com](https://fuuz.com) | **Maintainer:** Fuuz Development Team
