---
name: fuuz-rfp-responder
description: Answer RFPs, RFIs, security questionnaires, and vendor assessments about the Fuuz Industrial Operations Platform (fuuz.com). Use this skill whenever the user mentions RFP, RFI, questionnaire, security assessment, vendor evaluation, compliance questionnaire, or asks Claude to fill in/complete/answer questions about Fuuz capabilities. Also trigger when users upload Excel/Word/PDF documents containing vendor questions, mention SOC 2, ISO 27001, GDPR, security questionnaire, or reference specific RFP documents. Even if the user just says "help me respond to this" with an attached questionnaire, use this skill. This skill contains verified SOC 2 Type 2 audited information and comprehensive Q&A covering security, architecture, manufacturing capabilities, implementation, pricing, and compliance.
---

# Fuuz RFP and Security Questionnaire Response Skill

## Purpose

Enable Claude to accurately answer RFPs, RFIs, and security/compliance questionnaires about the Fuuz Industrial Operations Platform for sales and pre-sales teams with **100% accuracy** based on verified reference materials.

## Quick Reference - Company Basics

| Field | Value |
|-------|-------|
| Company | Fuuz (by MFGx, LLC) |
| Platform | Fuuz Industrial Operations Platform |
| Website | fuuz.com |
| Headquarters | Rochester Hills, Michigan, USA |
| Founded | 2018 (7+ years) |
| Employees | 60+ |
| Customers | 200+ companies, 1,300+ locations worldwide |

## Quick Reference - Key Certifications

| Certification | Status |
|---------------|--------|
| SOC 2 Type 2 | ✓ Certified (Oct 2024 - Sep 2025 audit period) |
| ISO 27001 | ✓ Aligned |
| TISAX | ✓ Aligned |
| GDPR/CCPA/PIPL | ✓ Compliant |
| 21 CFR Part 11 | ✓ Capable |
| HIPAA | ⚠ Available with BAA |

## Quick Reference - Technical Highlights

| Aspect | Details |
|--------|---------|
| Architecture | 100% cloud-native, multi-tenant AWS |
| Uptime SLA | 99.9% availability guarantee |
| User Licensing | Unlimited users (no per-seat fees) |
| API | 100% GraphQL |
| Connectors | 70+ pre-built (ERP, industrial protocols) |
| UI | Browser-based (no desktop installation) |

## Quick Reference - Implementation

| Metric | Value |
|--------|-------|
| Timeline | 24-32 weeks typical |
| Client FTE | 0.6-1.5 total |
| IT Infrastructure | Zero overhead for cloud deployments |
| Methodology | Water-Scrum-Fall hybrid |

## Workflow

1. **Identify document type**: Security questionnaire, RFP, RFI, feature checklist
2. **Read appropriate reference file(s)** from `references/` directory based on question categories
3. **Extract questions** from the uploaded document
4. **Match to reference materials** and populate answers
5. **Flag unknowns** clearly - never guess or speculate

## Reference Files

Read the appropriate reference file(s) based on question categories:

| File | Use When Questions Cover |
|------|-------------------------|
| `references/01-company-info.md` | Company history, size, customers, ownership, references |
| `references/02-security-compliance.md` | Security controls, SOC 2, encryption, access control, certifications, incident response, backups |
| `references/03-architecture-integration.md` | Cloud architecture, APIs, connectors, SSO, PLC integration, databases |
| `references/04-manufacturing-capabilities.md` | MES, WMS, QCS, CMMS features, OEE, traceability, quality |
| `references/05-implementation-support.md` | Deployment timeline, methodology, training, support model |
| `references/06-pricing-licensing.md` | Pricing models, licensing, discounts, contract terms |
| `references/07-functional-requirements.md` | Detailed feature matrix, Yes/No capability responses |

## Response Guidelines

### Accuracy Requirement
**All answers must be based solely on reference materials.** If information is not found:
- State clearly: "This information is not available in our reference materials"
- Never speculate or provide potentially incorrect information
- Suggest the sales team verify with internal SMEs

### Confidence Indicators
Use these when responding:
- ✓ **Verified**: Answer directly matches reference materials
- ⚠ **Needs Verification**: Related information exists but specific answer should be confirmed
- ❓ **Unknown**: Cannot answer from available reference materials

### Format Matching
- Match the source document format (checkboxes, columns, tables)
- Keep responses concise and professional
- For Yes/No questions, lead with Yes/No then brief explanation

## Common Question Patterns

### Security Questions → Read `references/02-security-compliance.md`
- Data encryption (TLS 1.2+, AES-256)
- SOC 2 certification details
- Password policies (14 char, 180 day, complexity)
- Backup frequency (daily/weekly)
- Incident response (72-hour notification)
- Penetration testing (annual third-party)

### Architecture Questions → Read `references/03-architecture-integration.md`
- Cloud provider (AWS)
- SSO support (SAML 2.0, OAuth 2.0, Entra ID)
- API type (100% GraphQL)
- PLC connectivity (OPC UA, MQTT, Modbus TCP)
- ERP integration (SAP, Oracle, NetSuite, Dynamics)

### Manufacturing Questions → Read `references/04-manufacturing-capabilities.md`
- OEE calculation and tracking
- Lot/serial traceability
- 21 CFR Part 11 support
- Quality management (SPC, NCR)
- Barcode/RFID support

### Implementation Questions → Read `references/05-implementation-support.md`
- Timeline (24-32 weeks)
- Methodology (Water-Scrum-Fall)
- Resource requirements (0.6-1.5 FTE)
- Support hours (Mon-Fri 8AM-5PM EST standard)
- Update frequency (monthly, zero-downtime)

### Pricing Questions → Read `references/06-pricing-licensing.md`
- Licensing model (unlimited users, module-based)
- Typical costs ($150K-$350K ARR single site)
- Implementation ($200K-$320K per site)
- Volume discounts (10-25% for multi-site)

## Example Usage

**User**: "Fill out this security questionnaire for Fuuz"

**Claude**:
1. Read `references/02-security-compliance.md`
2. For each security question, find the verified answer
3. Format responses to match questionnaire structure
4. Flag any questions outside reference material scope
5. Return completed questionnaire

## Maintenance Notes

- Reference materials based on SOC 2 Type 2 report (audit period Oct 2024 - Sep 2025)
- Update when certifications, features, or pricing change
- Employee/customer counts are approximate and may need verification for specific proposals
