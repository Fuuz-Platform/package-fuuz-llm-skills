# Fuuz Data Model Designer Skill (fuuz-schema)

Design data models and generate importable Fuuz package files for the Fuuz Industrial Operations Platform (fuuz.com).

---

## Overview

This skill generates complete, importable Fuuz package JSON for data model design. Every Fuuz data model automatically gets a fully-featured GraphQL API. Models are deployed via `.fuuz` package files — there is no standalone model import feature.

**Covers:** Data model types (master, setup, transactional), 20+ field types, relationship patterns (1:1, 1:many, many:1, many:many), model metadata (indices, triggers, mutations), prerequisite artifacts (modules, sequences), and Fuuz package JSON output format.

---

## When to Use

Activate this skill when requests mention:
- "fuuz data model", "fuuz schema", "data model", "schema design"
- "fuuz package", "package file"
- "field types", "model relationships"
- "master data", "setup data", "transactional data"
- "module", "moduleGroup", "sequence"
- Designing the data layer for Fuuz applications

---

## File Structure

```
fuuz schema/
├── SKILL.md                              (~500 lines)  Core rules, field types, relationships, workflow, checklist
├── README.md                             (~180 lines)  This file
└── references/
    ├── package-format.md                 (~400 lines)  Fuuz package JSON: manifest, definition, package-data
    ├── field-types.md                    (~350 lines)  All 20+ field types with decision tree
    ├── field-metadata.md                 (~400 lines)  Field-level metadata: mutations, queries, sequence, relation
    ├── model-metadata.md                 (~350 lines)  Model-level metadata: indices, triggers, dataChangeCapture
    ├── relationship-patterns.md          (~300 lines)  All relationship types with complete JSON examples
    ├── prerequisites.md                  (~250 lines)  ModuleGroup → Module → Sequence dependency chain
    ├── model-templates.md                (~600 lines)  6 complete model templates (setup, master, transactional)
    └── seed-data-patterns.md             (~300 lines)  data[] array structure, ordering rules, record formats
```

---

## Quick Start

### Step 1: Read SKILL.md
Start with the core skill file for rules, field types overview, critical rules, and the design workflow.

### Step 2: Read Relevant Reference Files

| Task | Read These References |
|------|---------------------|
| First time designing a schema | `prerequisites.md`, `field-types.md`, `package-format.md` |
| Defining model fields | `field-types.md`, `field-metadata.md` |
| Setting up relationships | `relationship-patterns.md` |
| Configuring indices, triggers | `model-metadata.md` |
| Need a starting template | `model-templates.md` |
| Structuring seed/reference data | `seed-data-patterns.md` |
| Generating the package output | `package-format.md` |

### Step 3: Follow the Design Workflow
1. Define ModuleGroup → Module hierarchy
2. Classify entities (master/setup/transactional)
3. Plan sequences for auto-increment codes
4. Design fields and relationships
5. Configure model metadata
6. Structure seed data
7. Generate package JSON
8. Validate with the output checklist

---

## Companion Skills

| Scenario | Skill Needed | Why |
|----------|-------------|-----|
| Building data flows that use your models | **fuuz-flows** | Flows query/mutate your models via GraphQL |
| Creating screens that display your data | **fuuz-screens** | Screens bind to models via `query.model` |
| Looking up platform concepts, seeded values | **fuuz-platform** | Shared platform reference material |
| Building a complete application | **All four skills** | Schema → Flows → Screens, with Platform for reference |

---

## Critical Rules Summary

1. **No embedded models** — Every relationship requires a separate model with foreign key references
2. **Prerequisites first** — ModuleGroup → Module → Sequence must be defined before DataModels
3. **Package-based deployment** — All model deployment goes through `.fuuz` package files
4. **Both relationship fields required** — Always define the FK field (`entityId`) AND the relation field (`entity`)
5. **System fields are automatic** — Never manually define `createdAt`, `updatedAt`, `createdByUser`, etc.
6. **Every model needs an `id` field** — Type `ID!`, unique, immutable after creation
7. **TTL indices for time-series** — Prevent unbounded storage growth on high-volume models

---

## Output Format

The skill produces three JSON files that form a `.fuuz` package:

| File | Content |
|------|---------|
| `manifest.json` | Package identity, version, platform compatibility |
| `definition.json` | Export selections with dependency ordering metadata |
| `package-data.json` | Data model definitions + seed data records |

Users import packages via **Configuration → Packages** in the Fuuz platform.

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | February 2026 | Initial release — complete data model design and package generation |

---

**Platform:** [fuuz.com](https://fuuz.com) | **Maintainer:** Fuuz Development Team
