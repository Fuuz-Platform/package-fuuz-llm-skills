---
name: fuuz-packages
description: >
  Create, manage, deploy, and troubleshoot Fuuz packages (.fuuz files) for the Fuuz Industrial Operations Platform
  (fuuz.com). Trigger this skill whenever the user mentions Fuuz packages, .fuuz files, package deployment,
  Green/Blue deployments in Fuuz, package versioning, package linting, environment promotion (build to QA to
  production), tenant deployment, package rollback, industry accelerators, application packaging, or any work
  involving bundling and deploying Fuuz application components. Also trigger when the user discusses multi-tenant
  enterprise architecture, data migration between environments, or creating distributable Fuuz application bundles.
---

# Fuuz Packages Skill

> **Purpose:** Guide Claude in creating, managing, and deploying Fuuz packages — the primary mechanism for bundling, distributing, and deploying applications on the Fuuz Industrial Operations Platform.

## 1. What Is a Fuuz Package?

A Fuuz package (`.fuuz` file) is a **TAR archive with a `.fuuz` extension** that contains all application components needed to deploy a Fuuz application or update to a tenant.

### Key Facts
- File format: `.tar` renamed to `.fuuz`
- Contents: Pure JSON — no executable code, nothing harmful
- Components: Data models, screens, data flows, roles, scripts, queries, setup data, and more
- Deployment: Install into any Fuuz tenant (build, QA, production)
- Safety: Always install in build/trial environment first before promoting to production

### What a Package Can Include
| Component | Description | Required? |
|-----------|-------------|-----------|
| Data Models (Schema) | Table/collection definitions with fields, relationships, constraints | Usually yes |
| Screens | UI definitions (JSON-based screen designs) | Usually yes |
| Data Flows | Automation workflows, integrations, scheduled jobs | Usually yes |
| Roles & Permissions | User access control definitions | Optional |
| Scripts | JavaScript/JSONata helper scripts | Optional |
| Saved Queries | Reusable GraphQL query definitions | Optional |
| Documents/Reports | Stimulsoft report templates | Optional |
| Setup Data | Reference data, configuration data, seed data | Optional |
| Webhooks | Inbound integration endpoint definitions | Optional |
| Custom Fields | User-defined field extensions | Optional |

## 2. Package Creation

### Full Application Package
Includes all components — used for initial deployments or major releases:
1. Navigate to **Package Management** in Fuuz
2. Select **Create New Package**
3. Choose **Full Application Package**
4. All models, screens, flows, scripts, queries, integrations, and roles are bundled
5. Optionally include setup/reference data
6. Set version identifier and description
7. Run validation (automatic linting)
8. Export `.fuuz` file

### Partial Package (Patch)
Includes only selected components — used for targeted updates or bug fixes:
1. Navigate to **Package Management**
2. Select **Create New Package**
3. Choose specific components (e.g., just one screen and its dependent flow)
4. System identifies dependencies automatically
5. Set version identifier (e.g., patch version)
6. Run validation
7. Export `.fuuz` file

### Versioning
Fuuz provides versioning mechanics but the versioning strategy is **user-controlled**:
- Semantic versioning recommended (e.g., `v2.1.0` for releases, `v2.1.1` for patches)
- Always include comprehensive descriptions and change logs
- Tag packages with environment targets (DEV, QA, PROD)
- Track version history for rollback reference

## 3. Package Validation (Automatic Linting)

Before deployment, Fuuz runs comprehensive validation that catches:

| Check | What It Validates | Severity |
|-------|-------------------|----------|
| Missing Dependencies | All referenced models, flows, scripts are included | CRITICAL |
| Data Model Conflicts | Schema compatibility and relationship integrity | CRITICAL |
| Missing Data Elements | Required fields that reference missing components | CRITICAL |
| Integration Requirements | External system connections properly configured | HIGH |
| Screen Dependencies | All required flows and data sources available | HIGH |
| Access Control | User permissions and security configurations valid | MEDIUM |

### Validation Results
- **✅ Green** — Package ready for deployment
- **⚠️ Yellow** — Warnings to review, don't block deployment
- **❌ Red** — Critical issues that must be resolved before deployment

## 4. Fuuz Enterprise Architecture

Understanding the deployment hierarchy is essential for package management:

```
Fuuz Enterprise
├── Development Tenant (Build Environment)
│   └── Where packages are created and initially tested
├── QA Tenant (Testing Environment)
│   └── Where packages are validated before production
├── Production Tenant (Blue Environment — Current Live)
│   └── Serving real users and operations
├── Staging Tenant (Green Environment — New Version)
│   └── New package deployed here for UAT before cutover
└── Integration Tenant (Equipment Connections)
    └── Dedicated tenant for gateway/equipment connections
        that can buffer data during transitions
```

### Tenant Types
- **Build**: Development workspace, unrestricted changes
- **QA**: Controlled testing with representative data
- **Production**: Live operations, change-controlled
- **Staging**: Mirror of production for Green/Blue deployment

## 5. Green/Blue Deployment Strategy

Fuuz's multi-tenant architecture enables true Green/Blue deployments for zero-downtime updates in manufacturing environments.

### Concept
- **Blue** = Current production tenant serving live users
- **Green** = New version deployed in a separate tenant for validation
- Both run simultaneously
- User access controls manage the cutover (no traffic routing needed)
- Instant rollback: just redirect users back to Blue

### Deployment Process

#### Phase 1: Prepare
1. Create and validate your package in the Build environment
2. Run comprehensive linting — resolve all red/critical issues
3. Document the change log and version

#### Phase 2: Deploy to Green
**Option A — New Tenant Deployment** (recommended for major updates):
- Create a new staging tenant
- Deploy package to the new tenant
- Benefits: zero risk to production, complete isolation, instant rollback

**Option B — In-Place Upgrade** (suitable for minor patches):
- Deploy package to existing staging/QA tenant
- Benefits: simpler data management, no user migration
- Risk: requires more careful planning

#### Phase 3: Validate (UAT)
1. Run automated tests in Green environment
2. Perform user acceptance testing (UAT)
3. Validate data integrity and integration connections
4. Test with representative production scenarios
5. Verify all screens, flows, and reports function correctly

#### Phase 4: Cutover
1. Notify users of planned maintenance window (even though it's zero-downtime)
2. Manage user access — grant access to Green, revoke from Blue
3. Verify equipment integrations are flowing to Green
4. Monitor Green environment under production load
5. Keep Blue running as immediate rollback option

#### Phase 5: Decommission Blue
1. After confidence period (days/weeks), Blue can be repurposed
2. Blue becomes the new staging environment for next deployment
3. Archive Blue state for compliance/audit purposes

### Rollback
If issues arise in Green:
- Redirect users back to Blue (instant — just access control changes)
- Blue is still running with the previous version
- Data created in Green during the cutover period needs manual reconciliation
- Investigate and fix issues, then redeploy Green

## 6. Data Migration

### Including Data in Packages
Fuuz uniquely allows **data to be packaged alongside schema and application components**:
- **Reference/Setup Data**: Lookup tables, status codes, default configurations
- **Configuration Data**: Tenant settings, integration parameters
- **Seed Data**: Initial records needed for the application to function

This mitigates data migration risks — a common pain point in traditional deployments.

### Cross-Environment Data Sync
Data synchronization between Blue and Green environments is **user-managed**:
- Replicate specific data sets as needed
- Build migration data flows for transformation (when schema differs between versions)
- Use Fuuz's data flow designer to automate data sync workflows
- Real-time equipment data can be buffered via the Integration Tenant during transitions

## 7. Industry Accelerators

Industry Accelerators are pre-built Fuuz packages for common manufacturing use cases:

### What They Include
- Screens, schema, data flows, roles, and other app components
- 100% built with Fuuz Application Designer tools (fully customizable)
- Pure JSON — nothing embedded that could cause issues
- Some are fully baked out-of-the-box; others are templates/starting points

### Available Accelerator Types
| Accelerator | Coverage |
|-------------|----------|
| MES | Work orders, OEE, production tracking, scheduling |
| WMS | Warehouse management, inventory, picking, shipping |
| CMMS | Maintenance management, work orders, asset tracking |
| Quality | Inspection, SPC, non-conformance, CAPA |
| Integration Broker | ERP sync, equipment connectivity, data normalization |
| Track & Trace | Genealogy, lot tracking, serialization |

### Accelerator Best Practices
1. Read documentation thoroughly before installing
2. Always install in build or trial environment first
3. Perform your own testing before promoting to production
4. Accelerators are fully extensible — modify anything post-install
5. Community-created accelerators available through partner ecosystem

## 8. Package JSON Structure

A `.fuuz` file, when extracted (it's a TAR), contains a structured set of JSON files:

```
package.fuuz (TAR archive)
├── manifest.json           # Package metadata, version, dependencies
├── models/                 # Data model definitions
│   ├── workOrder.json
│   ├── product.json
│   └── inventory.json
├── screens/                # Screen UI definitions
│   ├── workOrderList.json
│   └── workOrderDetail.json
├── flows/                  # Data flow definitions
│   ├── oeeCalculation.json
│   └── inventoryMove.json
├── roles/                  # Permission/role definitions
│   └── operatorRole.json
├── queries/                # Saved query definitions
├── data/                   # Setup/seed data
│   └── statusCodes.json
└── reports/                # Stimulsoft report templates
```

### Critical Rules for Package JSON
- **GP-001**: Never hallucinate Fuuz JSON schema formats — validate against samples or ask questions
- **GP-002**: Never create a `.fuuz` package without validating the schema against a sample
- **GP-004**: Fuuz packages are `.tar` files with `.fuuz` extension and must follow strict formatting
- **GP-005**: GraphQL mutations on data model version collection require specific JSON format — never assume
- **GP-006**: Always use system schema first — do not create custom schema unless absolutely necessary
- **GP-007**: If fields are missing from system schema, suggest adding custom fields rather than creating new models

## 9. Common Package Anti-Patterns

- **Never deploy directly to production** — Always go through Build → QA → Production pipeline
- **Never skip linting** — Resolve all critical validation issues before deployment
- **Never assume data compatibility** — Schema changes may require data migration flows
- **Never create partial packages without checking dependencies** — The linter helps, but verify
- **Never version without a change log** — Future you (and your team) will thank you
- **Never ignore Yellow warnings** — They may not block deployment but could cause issues
- **Never deploy accelerators to production without testing** — Always install in build first
- **Never modify production packages directly** — Make changes in build, repackage, redeploy

## 10. Deployment Checklist

Before deploying any package:
1. ✅ Package linting passes (no red/critical issues)
2. ✅ Yellow warnings reviewed and documented
3. ✅ Version identifier set with descriptive change log
4. ✅ Tested in build environment
5. ✅ Tested in QA environment with representative data
6. ✅ Data migration strategy defined (if schema changed)
7. ✅ Rollback plan documented
8. ✅ User communication sent (if production deployment)
9. ✅ Integration/equipment connections verified in target environment
10. ✅ Post-deployment validation plan ready
