# FUUZ Skills

Claude Code skills for building applications on the [FUUZ](https://fuuz.app) Industrial Intelligence Platform — covering data modeling, package generation, screen design, data flows, GraphQL, JSONata, documents, industrial operations, ML telemetry, and more.

## What Are FUUZ Skills?

FUUZ Skills are [Claude Code skill files](https://docs.anthropic.com/en/docs/claude-code/skills) (`.skill` archives) that teach Claude how to build, deploy, and manage applications on the FUUZ platform. Each skill is self-contained and can be imported into any Claude Code environment via the admin console.

They're designed for **FUUZ developers, partners, and customers** who want Claude to understand FUUZ platform conventions, data model patterns, and best practices out of the box.

## Skills

### Fuuz Platform Skills (How to Build on Fuuz)

| Skill | Version | Description |
|-------|---------|-------------|
| **fuuz-platform** | v1.6.0 | Platform fundamentals — architecture, glossary, JSONata bindings, integration landscape, cross-skill routing. |
| **fuuz-schema** | v2.0.0 | Data model design — relationship triplets, field types, naming conventions, data retention tiers, 24 critical rules. |
| **fuuz-packages** | v2.1.1 | Package generation — 71 golden rules for building valid `.fuuz` package files (manifest, definition, package-data). |
| **fuuz-flows** | v1.4.0 | Data flow design — 50+ node types with full per-node specs, JSONata/JS transforms, flow patterns. |
| **fuuz-screens** | v1.4.0 | Screen builder — 75 element types across 5 categories with per-element specs, Craft.js patterns, action pipelines. |
| **fuuz-graphql** | v1.0.0 | **NEW** — GraphQL essentials — query syntax, mutations, aggregations, where operators, edges/node pattern, flow and screen patterns. |
| **fuuz-documents** | v1.0.0 | **NEW** — Document designer — Stimulsoft Reports integration, band types, expressions, manufacturing document patterns. |
| **fuuz-mcp-tools** | v1.2.0 | MCP tool patterns — building data flows as MCP tools for LLM agents, context window optimization. |
| **fuuz-system-schema** | v1.1.0 | System schema reference — 158 system model definitions with 2,676 fields. |

### Domain Knowledge Skills (Industry Patterns)

| Skill | Version | Description |
|-------|---------|-------------|
| **fuuz-industrial-ops** | v1.2.0 | Industrial operations — UNS, alarm management, OEE/ISO 22400, workcenter states, ERP integration. |
| **fuuz-ml-telemetry** | v1.1.0 | ML and telemetry — EWMA baselines, Z-score anomaly detection, Pearson correlation, forecast pipelines. |
| **fuuz-wms** | v1.0.0 | **NEW** — WMS reference schema — 37 models, 556 fields, 124 relationships across 7 warehouse modules. |

### Business Skills

| Skill | Version | Description |
|-------|---------|-------------|
| **fuuz-rfp-skill** | v1.0.0 | RFP/RFI responder — security questionnaires, compliance assessments, capability matrices. |

## Repository Structure

```
fuuz-skills/
├── README.md
├── SKILLS_VERSION_MANIFEST.md
├── .gitignore
│
├── fuuz-platform/                # Platform fundamentals
│   ├── SKILL.md
│   └── references/               #   22 reference files (incl. JSONata, connectors, integration)
│
├── fuuz schema/                  # Data model design
│   ├── SKILL.md
│   └── references/               #   8 reference files
│
├── fuuz-packages/                # Package generation
│   ├── SKILL.md
│   └── references/
│
├── fuuz flows/                   # Data flow design
│   ├── SKILL.md
│   └── references/               #   22 reference files (incl. per-node specs)
│
├── fuuz screens/                 # Screen builder
│   ├── SKILL.md
│   └── references/               #   16 reference files (incl. per-element specs)
│
├── fuuz-graphql/                 # GraphQL essentials (NEW)
│   ├── SKILL.md
│   └── references/               #   3 reference files
│
├── fuuz-documents/               # Document designer (NEW)
│   ├── SKILL.md
│   └── references/               #   1 reference file (Stimulsoft)
│
├── fuuz-mcp-tools/               # MCP tool patterns
│   ├── SKILL.md
│   └── references/               #   3 reference files
│
├── fuuz-system-schema/           # System schema reference
│   ├── SKILL.md
│   └── references/               #   2 reference files
│
├── fuuz-industrial-ops/          # Industrial operations (domain)
│   ├── SKILL.md
│   └── references/               #   5 reference files
│
├── fuuz-ml-telemetry/            # ML and telemetry (domain)
│   ├── SKILL.md
│   └── references/               #   5 reference files
│
├── fuuz-wms/                     # WMS reference schema (NEW, domain)
│   ├── SKILL.md
│   └── references/               #   1 reference file
│
├── NewTrainingData/              # Incoming training data staging area
│
└── *.skill                       # Packaged skill archives (ZIP)
```

## Installation

1. Download the `.skill` file for the skill you need
2. Open the **Claude Code admin console**
3. Import the `.skill` file
4. Skills are loaded one at a time — each is fully self-contained

## Updating Skills

New training data can be added via the `NewTrainingData/` workflow:

1. Drop new `.md` files into `NewTrainingData/`
2. Review and compare against existing skill content
3. Copy to the appropriate `{skill}/references/` directory
4. Update the skill's `SKILL.md` to reference the new file
5. Repackage the `.skill` archive
6. Update `SKILLS_VERSION_MANIFEST.md`
7. Re-import into Claude Code admin console

## Versioning

All skills follow [semantic versioning](https://semver.org/):

- **Major** — significant structural changes, new sections, many new rules
- **Minor** — new rules added, existing sections updated
- **Patch** — typo fixes, clarifications, minor corrections

See [`SKILLS_VERSION_MANIFEST.md`](SKILLS_VERSION_MANIFEST.md) for the complete version history and change log.

## About FUUZ

[FUUZ](https://fuuz.app) is an Industrial Intelligence Platform that supports building Manufacturing Execution Systems (MES) supporting ISA-88 batch processing, ISA-95 enterprise integration, OEE tracking, and industrial IoT — built for manufacturers who need flexible, configurable production management. With Fuuz you can build and deploy apps from the edge to cloud, for any operational requirement.

## Resources

| Resource | Link | Description |
|----------|------|-------------|
| **Free Trial** | [fuuz.app](https://fuuz.app) | Request your free trial of FUUZ |
| **Get Started** | [getstarted.fuuz.com](https://getstarted.fuuz.com) | Introductory videos and walkthroughs to get up and running |
| **FUUZ Academy** | [academy.fuuz.com](https://academy.fuuz.com) | Online LMS with structured courses and certifications |
| **Support & Community** | [support.fuuz.com](https://support.fuuz.com) | Knowledge base, documentation, and community |
