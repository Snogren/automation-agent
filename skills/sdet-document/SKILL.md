---
name: sdet-document
description: Maintain a lightweight model of the application under test to organize and track test coverage.
---

# Document Skill

Keep an intuitive, lightweight model of the application under test. Use it to organize existing tests, identify coverage gaps, and guide where to invest automation effort next.

## When to Use This Skill

- Starting automation on a new project and need to map the application surface.
- Reviewing current test coverage to identify gaps.
- Prioritizing which features to automate next.
- Reporting on coverage status to stakeholders.

## Procedure

### Step 1 — Map the Application Domains

Build a domain model by surveying:

- Application routes and navigation structure (UI).
- API endpoint groups organized by domain (API).
- User roles and their permissions.
- Key business workflows and journeys.

Record this model in a `COVERAGE.md` file at the project root or in a `docs/` directory.

### Step 2 — Inventory Existing Tests

Survey all existing test files and map each to a domain and feature:

- UI tests in `tests/ui/` — map to pages and features.
- API tests in `api/{domain}/{endpoint}/` — map to endpoints.
- Journey tests — map to end-to-end workflows.

### Step 3 — Identify Coverage Gaps

For each domain and feature, assess:

- Is there a healthcheck test for every API endpoint?
- Are critical UI flows covered by journey tests?
- Are security boundaries tested (auth, role-based access)?
- Are there areas of high business risk with no automation?

### Step 4 — Prioritize

Rank gaps by:

1. **Business risk** — What is the cost of a regression in this area?
2. **Change frequency** — How often does this area change?
3. **Automation feasibility** — Can it be reliably automated?

### Step 5 — Maintain the Model

Update the coverage model as tests are added or the application changes. Keep it lightweight — a simple table or checklist is better than an elaborate document that falls out of date.
