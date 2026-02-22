# FUUZ Skills Version Manifest

> **Purpose:** Central tracking file for all FUUZ skill files — their versions, deployment status, and change history.
>
> **How to use:** When a skill file is updated, increment its version here and update the status. When deployed to a Claude project or environment, update the `deployed` column with the deployment target and date.

---

## Version Status Legend

| Status | Meaning |
|--------|---------|
| `draft` | Work in progress — not ready for deployment |
| `review` | Ready for developer review before deployment |
| `ready` | Reviewed and approved — ready to deploy |
| `deployed` | Actively deployed to one or more targets |
| `deprecated` | Superseded by a newer skill — do not deploy |
| `baked-in` | Content incorporated into a structured skill file — original no longer needed |

---

## Active Skills

### Package & Schema Skills

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz-packages/SKILL.md` | 2.1.1 | ready | 2026-02-22 | fuuz-skills project | **UPDATED** — Added references section with package-deployment-lifecycle.md |
| `fuuz skills/fuuz schema/SKILL.md` | 2.0.0 | ready | 2026-02-21 | fuuz-skills project | **UPDATED** — Synced with fuuz-packages v2.1: relationship TRIPLETS, usable/active, data retention tiers, 24 critical rules |
| `fuuz skills/fuuz-packages/fuuz-package-creator-skill.md` | 2.1.0 | ready | 2026-02-21 | synced to fuuz-packages | **Source file** — 71 golden rules, synced to deployed fuuz-packages/SKILL.md |
| `fuuz skills/fuuz-packages/references/package-deployment-lifecycle.md` | 1.0.0 | ready | 2026-02-22 | fuuz-skills project | **NEW** — Green/Blue deployment, enterprise architecture, validation/linting, industry accelerators, data migration, deployment checklist |

### Flow Skills

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz flows/SKILL.md` | 1.3.0 | ready | 2026-02-22 | fuuz-skills project | **UPDATED** — Added flow-essentials.md: webhook URL format, 5 manufacturing patterns, flow documentation standard |
| `fuuz skills/fuuz flows/references/node-catalog.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Complete node type reference (50+ nodes) |
| `fuuz skills/fuuz flows/references/flow-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Common flow design patterns |
| `fuuz skills/fuuz flows/references/jsonata-bindings.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | JSONata custom function reference |
| `fuuz skills/fuuz flows/references/graphql-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | GraphQL query/mutation patterns for flows |
| `fuuz skills/fuuz flows/references/common-pitfalls.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Flow development common mistakes |
| `fuuz skills/fuuz flows/references/flow-design-patterns-advanced.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — Array iteration, duration storage, memory wrapping, OEE patterns, telemetry aggregation |
| `fuuz skills/fuuz flows/references/flow-context-reference.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — Flow JSON structure, 21 node types with production examples, dataChanges payload |
| `fuuz skills/fuuz flows/references/flow-essentials.md` | 1.0.0 | ready | 2026-02-22 | fuuz-skills project | **NEW** — Node categories, webhook config, 5 manufacturing flow patterns, timezone rules, flow documentation standard |

### Screen Skills

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz screens/SKILL.md` | 1.3.0 | ready | 2026-02-22 | fuuz-skills project | **UPDATED** — Added screen-essentials.md: MainFormContainer pattern, action pipelines, ISA-101, design checklist |
| `fuuz skills/fuuz screens/fuuz-screens-v1.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Screen builder reference |
| `fuuz skills/fuuz screens/references/element-types.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | UI element catalog |
| `fuuz skills/fuuz screens/references/component-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Component design patterns |
| `fuuz skills/fuuz screens/references/layout-templates.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Screen layout templates |
| `fuuz skills/fuuz screens/references/dashboard-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Dashboard design patterns |
| `fuuz skills/fuuz screens/references/common-errors.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Screen development mistakes |
| `fuuz skills/fuuz screens/references/graphql-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | GraphQL patterns for screens |
| `fuuz skills/fuuz screens/references/screen-runtime-context.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Screen runtime context reference |
| `fuuz skills/fuuz screens/references/frontend-jsonata-bindings.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Frontend JSONata bindings |
| `fuuz skills/fuuz screens/references/oee-dashboard-domain.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — OEE/TEEP/MTBF/MTTR formulas, state classifications, time windows, calculation patterns |
| `fuuz skills/fuuz screens/references/stimulsoft-reports.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — Stimulsoft report architecture, band types, expressions, 7+ report types, Fuuz integration |
| `fuuz skills/fuuz screens/references/screen-essentials.md` | 1.0.0 | ready | 2026-02-22 | fuuz-skills project | **NEW** — MainFormContainer standard pattern, action pipelines (10 types), screen context functions, ISA-101, design checklist |

### Platform Skills

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz-platform/SKILL.md` | 1.5.0 | ready | 2026-02-22 | fuuz-skills project | **UPDATED** — Added graphql-essentials.md and jsonata-javascript-essentials.md |
| `fuuz skills/fuuz-platform/references/platform-architecture.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Architecture reference |
| `fuuz skills/fuuz-platform/references/platform-glossary.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Platform terminology |
| `fuuz skills/fuuz-platform/references/platform-training-guide.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Training guide |
| `fuuz skills/fuuz-platform/references/package-anatomy.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Package structure anatomy |
| `fuuz skills/fuuz-platform/references/cross-skill-index.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Cross-reference between skills |
| `fuuz skills/fuuz-platform/references/connectors-reference.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | External connector reference |
| `fuuz skills/fuuz-platform/references/integration-landscape.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — 500+ system integration catalog, protocol support matrix |
| `fuuz skills/fuuz-platform/references/jsonata-bindings-complete.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — Complete 294 JSONata binding reference |
| `fuuz skills/fuuz-platform/references/graphql-syntax-patterns.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — GraphQL API reference: operators, pagination, aggregations, mutations |
| `fuuz skills/fuuz-platform/references/fuuz-platform-development.md` | 1.0.0 | ready | 2026-02-21 | fuuz-skills project | **NEW** — Platform development patterns, duration handling, screen component hierarchy |
| `fuuz skills/fuuz-platform/references/graphql-essentials.md` | 1.0.0 | ready | 2026-02-22 | fuuz-skills project | **NEW** — Application API filter operators, relationship mutations, subscriptions, manufacturing queries |
| `fuuz skills/fuuz-platform/references/jsonata-javascript-essentials.md` | 1.0.0 | ready | 2026-02-22 | fuuz-skills project | **NEW** — Script Editor $ access, diagnostic script, manufacturing JSONata patterns, Transformation Explorer |

### Schema Reference Skills

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz schema/references/package-format.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Package JSON format |
| `fuuz skills/fuuz schema/references/field-metadata.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Field metadata shapes |
| `fuuz skills/fuuz schema/references/field-types.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Supported field types |
| `fuuz skills/fuuz schema/references/model-metadata.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Model-level metadata |
| `fuuz skills/fuuz schema/references/model-templates.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Model templates |
| `fuuz skills/fuuz schema/references/prerequisites.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Module/sequence prerequisites |
| `fuuz skills/fuuz schema/references/relationship-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Relationship patterns |
| `fuuz skills/fuuz schema/references/seed-data-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Seed data patterns |

### Industrial Operations Skills

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz-industrial-ops/SKILL.md` | 1.2.0 | ready | 2026-02-21 | fuuz-skills project | **UPDATED** — OPC UA quality codes, ideal cycle time fallback, OEE domain formulas, time window best practices |
| `fuuz skills/fuuz-industrial-ops/references/uns-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Unified Namespace patterns |
| `fuuz skills/fuuz-industrial-ops/references/alarm-management.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Alarm management |
| `fuuz skills/fuuz-industrial-ops/references/oee-time-classification.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | OEE time classification |
| `fuuz skills/fuuz-industrial-ops/references/workcenter-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Workcenter patterns |
| `fuuz skills/fuuz-industrial-ops/references/erp-integration.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | ERP integration patterns |

### ML & Telemetry Skills

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz-ml-telemetry/SKILL.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | ML and telemetry patterns |
| `fuuz skills/fuuz-ml-telemetry/references/algorithms.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | ML algorithms |
| `fuuz skills/fuuz-ml-telemetry/references/telemetry-patterns.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Telemetry collection patterns |
| `fuuz skills/fuuz-ml-telemetry/references/data-models.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | ML data models |
| `fuuz skills/fuuz-ml-telemetry/references/settings-catalog.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Settings catalog |
| `fuuz skills/fuuz-ml-telemetry/references/validation-checklist.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | ML validation checklist |

### Shared References

| Skill File | Version | Status | Last Updated | Deployed To | Notes |
|-----------|---------|--------|-------------|-------------|-------|
| `fuuz skills/fuuz flows/references/system-schema.json` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | System schema JSON |
| `fuuz skills/fuuz flows/references/system-seeded-values.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | System seeded values |
| `fuuz skills/fuuz flows/references/time-schedule-bindings.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Time/schedule bindings |
| `fuuz skills/fuuz flows/references/visualization-library.md` | 1.0.0 | deployed | 2026-02 | fuuz-skills project | Visualization library |

---

## Legacy / Standalone Skills

These files exist outside the structured `fuuz skills/` directory. They may contain useful content but are not part of the structured skill set.

| Skill File | Version | Status | Last Updated | Notes |
|-----------|---------|--------|-------------|-------|
| `Documentation/fuuz-skill.md` | — | baked-in | 2026-02-21 | → `fuuz-platform/references/fuuz-platform-development.md` |
| `Documentation/fuuz_jsonata_bindings_skill.md` | — | baked-in | 2026-02-21 | → `fuuz-platform/references/jsonata-bindings-complete.md` |
| `Documentation/Screens/oee-dashboard-skill.md` | — | baked-in | 2026-02-21 | → `fuuz-screens/references/oee-dashboard-domain.md` |
| `Documentation/FuuzDataFlowSkill_Context.md` | — | baked-in | 2026-02-21 | → `fuuz-flows/references/flow-context-reference.md` |
| `Documentation/Fuuz Flow Design & Data Processin.md` | — | baked-in | 2026-02-21 | → `fuuz-flows/references/flow-design-patterns-advanced.md` |
| `Documentation/compass_artifact doc.md` | — | baked-in | 2026-02-21 | → `fuuz-platform/references/integration-landscape.md` |
| `Documentation/Fuuz GraphQL Syntax & Patterns.md` | — | baked-in | 2026-02-21 | → `fuuz-platform/references/graphql-syntax-patterns.md` |
| `Documentation/STIMULSOFT_SKILL.md` | — | baked-in | 2026-02-21 | → `fuuz-screens/references/stimulsoft-reports.md` |

---

## Change Log

### 2026-02-22 (Update 2) — New Training Data Integration

**5 new training files from `NewTrainingData/` analyzed, organized into skill reference directories:**

- `flow skill.md` → `fuuz-flows/references/flow-essentials.md` — Webhook URL format, 5 manufacturing patterns, flow documentation standard
- `graphql skill.md` → `fuuz-platform/references/graphql-essentials.md` — Application API filter operators (`equals`/`contains`/`startsWith`), relationship mutations, subscriptions, manufacturing queries
- `jsonata skill.md` → `fuuz-platform/references/jsonata-javascript-essentials.md` — Script Editor `$` access pattern, diagnostic script, manufacturing JSONata patterns, Transformation Explorer
- `package skill.md` → `fuuz-packages/references/package-deployment-lifecycle.md` — Green/Blue deployment, enterprise tenant architecture, validation/linting, industry accelerators, data migration, deployment checklist
- `screen skill.md` → `fuuz-screens/references/screen-essentials.md` — MainFormContainer standard pattern, action pipelines (10 types), screen context functions, ISA-101 compliance, design checklist

**Skills updated:** fuuz-flows v1.2→v1.3, fuuz-platform v1.4→v1.5, fuuz-screens v1.2→v1.3, fuuz-packages v2.1.0→v2.1.1

**4 .skill files repackaged:**
- `fuuz-flows.skill` — 206,554 bytes (14 files)
- `fuuz-platform.skill` — 131,828 bytes (16 files)
- `fuuz-screens.skill` — 174,067 bytes (16 files)
- `fuuz-packages.skill` — 67,102 bytes (3 files)

### 2026-02-22 — Directory Consolidation

- Moved `fuuz-package-creator-skill.md` from `MES Batch Dev/` to `fuuz skills/fuuz-packages/`
- All skill files, source files, reference documents, .skill archives, and manifest now live under `fuuz skills/`
- Updated all manifest paths to reflect consolidated structure

### 2026-02-21 (Update 3) — Documentation Bake-in & Full Repackage

**All 7 .skill files repackaged as ZIP archives:**
- `fuuz-packages.skill` — 30,784 bytes (1 file)
- `fuuz-schema.skill` — 45,952 bytes (10 files)
- `fuuz-screens.skill` — 168,525 bytes (15 files)
- `fuuz-flows.skill` — 202,686 bytes (13 files)
- `fuuz-platform.skill` — 122,301 bytes (14 files)
- `fuuz-industrial-ops.skill` — 17,988 bytes (6 files)
- `fuuz-ml-telemetry.skill` — 18,144 bytes (6 files)

**`fuuz skills/fuuz-packages/SKILL.md`** — v2.0.0 → v2.1.0
- Added Golden Rules 64-71 (documentation bake-in, repackaging, customer instances, testing, bidirectional sync)
- Added from fuuz-schema: TTL indices, high-volume model metadata, field/model naming standards, column ordering standard, mutation standards
- Re-synced deployed copy from source file

**`fuuz skills/fuuz schema/SKILL.md`** — v1.1.0 → v2.0.0
- Synced with fuuz-packages v2.1: relationship TRIPLETS (3-part), usable/active distinction, data retention tiers
- Expanded Critical Rules from 12 → 24
- Fixed dataModelTypeId: "setup" for ALL lookups (was incorrectly null)
- Fixed enterpriseId: developer-provided (was hardcoded "mfgx")
- Expanded Output Checklist: lookup/enum, naming convention, inverse relation validation

**`fuuz skills/fuuz screens/SKILL.md`** — v1.0.0 → v1.2.0
- Added 2 new reference files: oee-dashboard-domain.md (OEE/TEEP/MTBF/MTTR formulas), stimulsoft-reports.md (report architecture, 7+ report types)
- Added Stimulsoft Reports cross-reference in Related Skills section

**`fuuz skills/fuuz flows/SKILL.md`** — v1.1.0 → v1.2.0
- Added 3 critical rules: JSONata iteration (`$[].{}` vs `$.{}`), duration storage as milliseconds, memory wrapping for Array nodes
- Added 2 new reference files: flow-design-patterns-advanced.md (875 lines), flow-context-reference.md (848 lines)

**`fuuz skills/fuuz-platform/SKILL.md`** — v1.0.0 → v1.4.0
- Added 4 new reference files: integration-landscape.md (500+ systems), jsonata-bindings-complete.md (294 bindings), graphql-syntax-patterns.md, fuuz-platform-development.md

**`fuuz skills/fuuz-industrial-ops/SKILL.md`** — v1.1.0 → v1.2.0
- Added: Time Window Best Practices, OPC UA Quality Codes, Ideal Cycle Time Fallback Pattern, OEE Domain Formulas

**11 new reference files created** from Documentation/ folder content (now fully baked into skills).

### 2026-02-21 (Update 2)

**`fuuz skills/fuuz-packages/SKILL.md`** — v1.0.0 → v2.0.0
- Deployed v2.0.0 content from MES Batch Dev source file
- Added YAML frontmatter with updated description
- 63 golden rules (up from 10)
- All sections from source file now in deployed skill
- Made self-contained: removed all external file path references
- Skill is now ready for `.skill` repackaging and Claude admin console import

**`fuuz-packages/fuuz-package-creator-skill.md`** — v2.0.0 (additional rules)
- Added Golden Rules 55-63: skill deployment, self-containment, screen generator, split concerns, flow training data, documentation bake-in
- Removed external file path references (Documentation/ folder, SLM training data paths)
- All content now self-contained within skill

### 2026-02-21

**`fuuz-packages/fuuz-package-creator-skill.md`** — v1.0.0 → v2.0.0
- Added Golden Rules 11-54 (from developer interview sessions)
- Added sections: Data Retention Tiers, System Fields, Naming Conventions
- Added sections: Model Triggers (8a), Screen Development Guidance (8b)
- Added sections: Custom Fields (10a), Import File Generation (14)
- Added sections: Package Promotion (15), Roles & Policies (16), Common Mistakes (17), Data Flow Node Reference (18)
- Updated manifest template: enterpriseId/environmentId now developer-provided
- Updated definition.json: external model selections, dataTransform "$" context
- Updated checklist: 60+ validation items including naming cross-check
- Updated validation script: enum detection, inverse relation validation
- Comprehensive relationship triplet documentation with worked examples
- Comprehensive lookup/enum model documentation (usable vs active, color, default, descriptions)
- FK requiredness guidelines, deletionReferenceBehavior decision table
- Auto-indexing rules, sequence linking, ID generation patterns
- Data flow node catalog reference (50+ nodes across 12 categories)
- GraphQL naming conventions for screens and flows

### 2026-02-17

**`fuuz-packages/fuuz-package-creator-skill.md`** — v0.0.0 → v1.0.0
- Initial creation from ISA-88 batch package development session
- 10 golden rules covering core package structure
- Basic package file structure, field metadata shapes
- Validation checklist and script

---

## Deployment Targets

| Target | Description | How to Deploy |
|--------|------------|--------------|
| `claude-admin-console` | **Primary deployment method** — Claude admin console | Go to admin console → select `.skill` file → import. Skills are loaded one at a time. |
| `fuuz-skills project` | Source directory for skill development | Update SKILL.md and references/ in the skill subdirectory, then repackage `.skill` file |

### Deployment Process

1. **Update source:** Edit `SKILL.md` (or `fuuz-package-creator-skill.md` for fuuz-packages) in the appropriate `fuuz skills/{skill-name}/` directory
2. **Repackage:** Create the `.skill` binary archive from the skill directory contents
3. **Import:** Go to the Claude admin console, select the `.skill` file, and import it
4. **Verify:** Skills are loaded individually (one at a time) — each must be self-contained

### Critical Requirements for Deployed Skills

- **Self-contained:** Skills CANNOT reference files outside the skills directory. All content must be baked in.
- **Shared externally:** Skills are distributed to employees, partners, and customers — they must be comprehensive.
- **No external documentation dependency:** The `Documentation/` folder is NOT distributed. All useful content must be inside skill files.
- **No size limits known:** No practical limits on skill file size for deployment.

---

## Versioning Rules

1. **Semantic versioning:** `major.minor.patch`
   - **Major** (1.0.0 → 2.0.0): Significant structural changes, new sections, many new rules
   - **Minor** (1.0.0 → 1.1.0): New rules added, existing sections updated
   - **Patch** (1.0.0 → 1.0.1): Typo fixes, clarifications, minor corrections
2. **Update this manifest** whenever a skill file changes
3. **Record in Change Log** what changed and why
4. **Update status** when deployed to a target
5. **Skills in `fuuz skills/`** follow their own internal version headers — keep this manifest in sync
