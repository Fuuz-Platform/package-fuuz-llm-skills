# Fuuz Data Flow Development Skill

**Version:** 1.0  
**Release Date:** February 8, 2026  
**File:** `fuuz-flows-v1.skill`

## Overview

Comprehensive skill for building production-ready data flows on the Fuuz Industrial Operations platform. Includes complete node catalog, JSONata/JavaScript reference, GraphQL patterns, flow examples, debugging guides, and system schema documentation.

## Installation

### For Individual Users

1. Download `fuuz-flows-v1.skill`
2. Open Claude (https://claude.ai)
3. Go to **Settings → Skills**
4. Click **"Upload Skill"**
5. Select `fuuz-flows-v1.skill`
6. Skill is now available in all your Projects

### For Teams

**Option A: Shared Storage**
- Place `fuuz-flows-v1.skill` in shared location (SharePoint, network drive, etc.)
- Share location with team members
- Each user follows individual installation steps above

**Option B: Direct Distribution**
- Email `fuuz-flows-v1.skill` to team members
- Include installation instructions
- Users import into their Claude accounts

## What's Included

### Core Documentation

- **SKILL.md** - Main skill file with architecture, patterns, and best practices
- **10 Reference Files** - Comprehensive documentation covering all aspects of flow development

### Reference Files

1. **node-catalog.md** - All 50+ node types with complete configurations
2. **jsonata-bindings.md** - 294+ Fuuz custom JSONata functions
3. **graphql-patterns.md** - Query/mutation patterns, operators, aggregations
4. **flow-patterns.md** - 5 complete flow examples with every node configured
5. **time-schedule-bindings.md** - Schedule and moment timezone functions
6. **common-pitfalls.md** - Critical debugging patterns and error solutions
7. **system-schema.json** - Complete System API GraphQL schema (49k lines)
8. **system-seeded-values.md** - All platform seeded values (2k+ lines)
9. **visualization-library.md** - Fusion Charts configuration reference

## Key Features

✅ **Dual API Support** - System API vs Application API patterns  
✅ **Complete Node Reference** - Every node type with examples  
✅ **Performance Optimizations** - Query node vs $query(), timeout strategies  
✅ **Error Solutions** - Common pitfalls with debugging strategies  
✅ **Production Patterns** - 5 complete flow examples  
✅ **System Schema** - Full System API reference included  
✅ **Naming Standards** - Required conventions for flows and nodes  
✅ **Saved Transforms** - Reusability patterns

## Use Cases

This skill helps with:
- Building scheduled ETL flows
- Creating reactive data change flows
- Developing web flow APIs for dashboards
- Implementing OEE calculations
- Warehouse automation logic
- External API integrations
- Telemetry aggregation
- Production tracking systems

## Triggers

The skill activates when you mention:
- "fuuz flow", "data flow", "web flow", "backend flow", "gateway flow"
- "rfc" (remote function call), "fuuz api"
- JSONata expressions
- Industrial workflows (OEE, warehouse logic, API integration)

## Updating

When a new version is released:
1. Download the new `.skill` file (e.g., `fuuz-flows-v1.1.skill`)
2. Upload it via Settings → Skills (replaces previous version)
3. Check version history in SKILL.md for changes

## Version History

### v1.0 (February 8, 2026)

**Initial Release**

- Complete node catalog with 50+ node types
- JSONata custom bindings (294+ functions)
- GraphQL System API & Application API patterns
- 5 complete flow pattern examples
- Time/schedule binding reference
- Critical pitfalls and debugging guide
- System schema (49k lines)
- System seeded values reference
- Fusion Charts visualization library

## Companion Skills

This skill works best alongside other Fuuz accelerator skills:

| Skill | Purpose | When to Use |
|-------|---------|------------|
| **fuuz-schema** | Data model design & package generation | When building flows that query/mutate custom data models |
| **fuuz-screens** | Screen creation & UI design | When building web flows (Screen type) that support dashboards, HMIs, or custom screen actions |
| **fuuz-platform** | Shared platform reference | For connector configurations, device driver details, system seeded values, and cross-skill task routing |

## Support

For questions or feedback:
- Contact Fuuz Development Team
- Visit https://fuuz.com
- Internal documentation portal

## License

Internal use for Fuuz engineers and authorized partners.

---

**Maintained by:** Fuuz Development Team  
**Platform:** Fuuz Industrial Operations (fuuz.com)
