---
name: sdet-cicd
description: Configure how to run Playwright tests in CI/CD pipelines.
---

# CI/CD Skill

Configure pipeline execution so that Playwright tests run reliably in CI and provide actionable feedback on every build.

## When to Use This Skill

- Setting up Playwright tests in a CI pipeline for the first time.
- Adding new test stages (smoke, regression, nightly).
- Configuring test sharding for parallel execution.
- Troubleshooting CI-specific test failures.
- Setting up test reporting and artifact collection.

## Procedure

### Step 1 — Define the Test Execution Strategy

Determine how tests should run in CI:

- **Smoke/Healthcheck**: Run `@Healthcheck` tagged tests on every PR. Fast feedback.
- **Regression**: Run the full suite on merge to main or on a schedule.
- **Sharding**: Split tests across parallel runners for large suites using `--shard=N/M`.

### Step 2 — Configure the CI Workflow

Set up the workflow with these elements:

- Install dependencies (`npm ci`).
- Install Playwright browsers (`npx playwright install --with-deps chromium`).
- Run tests with appropriate project and tag filters.
- Upload test results as artifacts (HTML report, CTRF JSON, traces).
- Set appropriate timeouts for the CI job.

### Step 3 — Configure Environment

Ensure CI has access to:

- Test user credentials (via CI secrets mapped to environment variables matching `.env` keys).
- Target environment URLs.
- Any required network access (VPN, service mesh).

### Step 4 — Configure Reporting

Set up reporters that produce CI-friendly output:

- HTML reporter for human review of failures.
- JSON reporter (e.g., CTRF) for machine-readable results.
- Custom reporters for notifications (e.g., Teams, Slack) as needed.
- Upload trace files as artifacts for failed tests.

### Step 5 — Handle Auth State in CI

Auth setup in CI follows the same pattern as local:

- The `setup-{env}` project runs first and saves auth state files.
- Tests consume the saved state through fixtures.
- Ensure auth state files are not cached between CI runs (they should be fresh each time).

### Step 6 — Validate

Run the full CI workflow and verify:

- Tests execute successfully in the CI environment.
- Failures produce useful artifacts (traces, screenshots, HTML report).
- Results are reported to the appropriate channels.
