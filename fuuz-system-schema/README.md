# Fuuz System Schema Skill

**Version:** 1.1.0
**Release Date:** February 26, 2026
**File:** `fuuz-system-schema.skill`

## Overview

Complete System API GraphQL schema reference for the Fuuz Industrial Operations Platform (fuuz.com). Contains **158 system model definitions** with **2,676 fields** that exist in every Fuuz environment.

**Covers:** Built-in system/platform models only (User, Role, DataFlow, Screen, DataModel, Connection, Device, Module, Package, Calendar, Schedule, Document, Export, Notification, Visualization, etc.).

**Does NOT cover:** Application-specific models (WorkOrder, Inventory, Asset, Workcenter, Product, SalesOrder, etc.) — these vary per customer/partner deployment.

## Installation

### For Individual Users

1. Download `fuuz-system-schema.skill`
2. Open Claude (https://claude.ai)
3. Go to **Settings → Skills**
4. Click **"Upload Skill"**
5. Select `fuuz-system-schema.skill`
6. Skill is now available in all your Projects

### For Teams

**Option A: Shared Storage**
- Place `fuuz-system-schema.skill` in shared location (SharePoint, network drive, etc.)
- Share location with team members
- Each user follows individual installation steps above

**Option B: Direct Distribution**
- Email `fuuz-system-schema.skill` to team members
- Include installation instructions
- Users import into their Claude accounts

## What's Included

### Core Documentation

- **SKILL.md** - Model categories, usage patterns, system vs application model distinction
- **2 Reference Files** - Complete schema in JSON and markdown formats

### Reference Files

1. **system-schema.json** - Complete System API GraphQL schema (158 models, 2,676 fields)
2. **fuuz-models.md** - Authoritative markdown model reference with full field tables

## Key Features

- **158 System Models** - Every platform model with complete field definitions
- **Dual Format** - JSON for programmatic use, markdown for human reference
- **Clear Boundary** - Explicit separation of system vs application models
- **Field Type Reference** - GraphQL type notation explained
- **Auto-Generated Fields** - Documents which fields the platform adds automatically

## Triggers

The skill activates when you mention:
- Looking up system model field names or types
- Writing GraphQL queries against system entities (User, Connection, DataFlow, Screen, etc.)
- Need exact field definitions for system-level models
- Building flows or screens that interact with platform configuration models

## Companion Skills

This skill works best alongside other Fuuz accelerator skills:

| Skill | Purpose | When to Use |
|-------|---------|------------|
| **fuuz-flows** | Data flow development | When building flows that query/mutate system models |
| **fuuz-screens** | Screen creation & UI design | When building screens that display system model data |
| **fuuz-schema** | Data model design & package generation | When designing custom models that relate to system models |
| **fuuz-platform** | Shared platform reference | For connector configurations, seeded values, visualization library |

## Version History

### v1.1.0 (February 26, 2026)

- Corrected to 158 authoritative system models (removed 254 application-specific models)
- Added fuuz-models.md authoritative markdown reference

### v1.0.0 (February 26, 2026)

- Initial release — extracted from fuuz-flows for independent loading

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
