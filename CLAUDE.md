# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the **PI-308 Production Test Results App** — a browser-based replacement for an Excel-based production test reporting workflow used by Matrix TSL. Production staff currently use three Excel workbooks (IM0004, IM3214, IM6930) to record test results. The goal is a web app where they complete forms in a browser and reports are stored automatically to the company NAS.

**Status:** Initial planning phase. No source code exists yet — only requirements documentation and the source Excel files.

## Source Materials

Three Excel workbooks in the project root define the forms to be converted:
- `IM0004 Electrical Test Procedure.xlsx`
- `IM3214 Electrical Test Procedure.xlsx`
- `IM6930 Electrical Test Procedure.xlsx`

Any implementation must start with Phase 1 from `plan.md`: map all fields from these three files into `docs/form-field-mapping.md` before writing code.

## Planned Architecture

```
Browser Frontend (React + Vite)
        ↓ HTTPS
Backend API (Node.js + Express)
        ↓ restricted service account
NAS folder (controlled storage)
```

**Critical constraint:** The browser must never write directly to the NAS. All saves go through the backend.

## Planned Project Structure

```
pi-308-production-test-app/
├── frontend/src/          # React components, pages, styles
├── backend/               # Express server, routes/, services/, storage/
├── shared/pi308Schema.js  # Shared data model for one PI-308 report
└── docs/                  # form-field-mapping.md, deployment-notes.md, etc.
```

## Data Model

Each completed report will follow this shape (defined in `shared/pi308Schema.js`):

```json
{
  "reportId": "PI-308-0001",
  "createdAt": "ISO8601 timestamp",
  "operator": "",
  "product": "",
  "serialNumber": "",
  "buildReference": "",
  "sections": { "tab1": {}, "tab2": {}, "tab3": {} },
  "overallResult": "",
  "comments": ""
}
```

## Backend API Endpoints (planned)

```
GET  /api/health
POST /api/reports/save-draft
POST /api/reports/submit
GET  /api/reports/:reportId
```

## File Naming Convention

```
PI-308_[SerialNumber]_[YYYY-MM-DD]_[ReportID].json
```

## Development Versions

- **v0.1** — Frontend only, three form sections matching Excel tabs, local JSON export
- **v0.2** — Backend API, server-side saving, validation, report ID generation
- **v0.3** — PDF/Excel output, review page
- **v0.4** — NAS save path, restricted permissions, production file naming
- **v1.0** — Authentication, production deployment, real user testing

## Key Open Questions (resolve before implementing)

- Output format: JSON, PDF, Excel, CSV, or database?
- Authentication method: simple password, Microsoft 365, or domain auth?
- NAS access method: mapped folder, UNC path, or IT-managed upload endpoint?
- Should reports be editable after saving?
- Should report numbers be auto-generated?

## Author

Hamed Adefuwa — Electrical Engineering Product Manager
