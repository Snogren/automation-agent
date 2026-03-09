---
name: sdet-auth
description: Manage users, login flow, and auth fixture infrastructure for Playwright tests.
---

# Auth Skill

Build and maintain the authentication layer so that every test project has authenticated browser sessions and API clients ready to use.

## When to Use This Skill

- Setting up authentication for a new Playwright project.
- Adding a new test user.
- Updating the login flow after authentication provider changes.
- Troubleshooting auth failures or expired sessions.

## Procedure

### Step 1 — Create the User Base Class

Create an abstract `User` class in `fixtures/User.ts` with:

- A `getRequired(envKey)` helper that reads from `process.env` and throws if missing.
- Abstract getters for `username`, `password`, and `authStateFile`.
- A method to generate environment-specific auth file paths (e.g., `getAuthStateFileForProject(projectName)`).

### Step 2 — Create Concrete User Classes

For each test user, create a class in `fixtures/` that extends `User`:

- Read credentials from `.env` using the naming convention defined in the infra skill.
- Each user class maps to a specific role or persona used in testing.

### Step 3 — Implement the Login Helper

Create a login helper class that performs the full browser-based login flow:

- Navigate to the application URL, which redirects to the identity provider.
- Fill credentials and submit the login form.
- Wait for successful redirect back to the application.
- This class is used only by the auth setup project, never by tests directly.

### Step 4 — Create auth.setup.ts

Create a Playwright setup file in `fixtures/auth.setup.ts`:

- Define one `setup()` call per test user.
- Each setup checks if the saved auth state file is still valid (cookies not expired).
- If expired or missing, perform the login flow using the login helper and save `storageState` to disk.
- The auth state files are JSON files at the project root (e.g., `{user}-{env}.json`).

### Step 5 — Create Per-User Fixtures

For each test user, create a fixture file in `fixtures/` that exports a `test` object with:

- An authenticated `Page` fixture — creates a new browser context loaded with the user's saved `storageState`.
- An authenticated `APIRequestContext` fixture — configured with the user's `storageState`, standard headers (`Accept: application/json`, `Content-Type: application/json`, CSRF token), and the `apiBaseURL` from project config.

### Step 6 — Create an Unauthenticated Fixture

Create a fixture that provides a bare `APIRequestContext` with no auth state. This is used for testing that endpoints properly return 401 for unauthenticated requests.

### Step 7 — Wire Up Projects

Ensure `playwright.config.ts` has setup projects as dependencies:

- `setup-{env}` runs `auth.setup.ts` and is listed as a dependency of the `{env}` test project.
- Test projects inherit `storageState` indirectly through fixtures, not through project-level config.
