---
name: sdet-infra
description: Manage infrastructure research, setup, and maintenance to equip tests with a working testable environment.
---

# Infrastructure Skill

Set up and maintain the test environment infrastructure so that tests have a reliable, repeatable foundation to execute against.

## When to Use This Skill

- Scaffolding a new Playwright project from scratch.
- Adding support for a new target environment (e.g., local, dev, staging).
- Troubleshooting environment connectivity or configuration issues.
- Updating test infrastructure after application architecture changes.

## Procedure

### Step 1 — Discover Target Environments

Identify all environments the tests will run against. For each environment, determine:

- Application base URL (UI).
- API base URL (if separate from the UI origin).
- Authentication provider URL and flow type.
- Any required VPN, proxy, or network configuration.

### Step 2 — Initialize the Project

If scaffolding a new project:

```bash
npm init playwright@latest
```

Install additional dependencies as needed (e.g., `zod`, `@faker-js/faker`, `dotenv`).

### Step 3 — Configure playwright.config.ts

Set up the configuration with:

- A paired `setup-{env}` and `{env}` project for each target environment, where the setup project is a dependency of the test project.
- Custom options (e.g., `apiBaseURL`) via module augmentation on `PlaywrightTestOptions`.
- Explicit timeouts for tests, actions, and navigation.
- Trace capture on first retry.
- Reporter configuration (HTML at minimum; add custom reporters as needed).

### Step 4 — Configure Environment Variables

Create a `.env` file with credentials and configuration for each test user. Use a consistent naming convention:

```
{USER_NAME}_USERNAME=...
{USER_NAME}_PASSWORD=...
{USER_NAME}_AUTH_FILE=...
```

Add `.env` to `.gitignore`. Provide a `.env.example` with placeholder values for onboarding.

### Step 5 — Verify Connectivity

Run a minimal smoke test or script to confirm:

- The application is reachable at each configured URL.
- Authentication endpoints respond.
- API endpoints are accessible from the test runner.

Document any setup prerequisites (VPN, tokens, local services) in the project README.
