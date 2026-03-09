---
name: sdet-ui
description: Generate high-quality page objects and UI tests using the Page Object Model pattern.
---

# UI Automation Skill

Create page objects and UI tests that are reliable, readable, and maintainable. Use the application context (codebase, MCP browser, or user input) to generate accurate locators and meaningful test coverage.

## When to Use This Skill

- Automating a new UI feature or page.
- Adding test coverage for a user journey.
- Updating page objects after UI changes.
- Reviewing or improving existing UI test quality.

## Procedure

### Step 1 — Understand the Target

Before writing any code, understand the page or feature:

- Use the `sdet-explore` skill to navigate the running app via MCP and observe the DOM structure.
- Read application source code to understand component hierarchy and data-testid attributes.
- Identify user actions and expected outcomes for the feature.

### Step 2 — Create the Page Object

Create a page object class in `pages/`.

**Locator strategy** (in priority order):

1. `getByRole()` — buttons, links, headings, textboxes, etc.
2. `getByTestId()` — when role-based locators are ambiguous.
3. Component selectors — for framework-specific components (e.g., Angular custom elements).
4. CSS selectors — last resort, keep them minimal and stable.

**Structure rules**:

- Declare all locators as `readonly` properties initialized in the constructor.
- Group locators logically (navigation, filters, grid, detail view, etc.).
- Create action methods for composite user interactions (e.g., `search(term)` that fills the input and clicks the button).
- Use locator factories for dynamic/repeating elements (e.g., `row(index)`, `section(name)`).
- Never put assertions in page objects.

**Inheritance**: If multiple pages share common patterns (e.g., search + grid), create an abstract base page and extend it.

### Step 3 — Write the Test Spec

Create a test file in `tests/ui/`:

- Import the `test` object from the appropriate user fixture (not from `@playwright/test` directly, unless testing unauthenticated flows).
- Instantiate page objects at the start of each test or in a `beforeEach`.
- Structure tests as: navigate → act → assert.
- Use descriptive test names that state the expected behavior.
- Tag tests appropriately (e.g., `@Smoke`, `@Regression`, `@Journey`).

**Assertion guidelines**:

- Assert on visible outcomes, not implementation details.
- Use `toBeVisible()`, `toHaveText()`, `toHaveCount()` over checking internal state.
- For async content, use `expect()` auto-retrying matchers — never use `waitForTimeout()`.

### Step 4 — Organize Journey Tests

For multi-step user journeys that cross multiple pages, create files in `tests/ui/critical-journeys/`:

- Each journey test exercises a realistic end-to-end flow.
- Compose multiple page objects within a single test.
- These tests prioritize breadth over depth — they validate the happy path through connected features.

### Step 5 — Validate

Run the new tests to confirm they pass reliably:

```bash
npx playwright test {test-file} --headed
```

Run at least 3 times to check for flakiness. If a test is flaky, fix the root cause (usually a timing issue or non-deterministic locator) rather than adding retries.
