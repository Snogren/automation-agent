---
name: sdet-explore
description: Run tests, read code, and use MCP to learn about the application and provide context to other skills.
---

# Explore / Debug Skill

Investigate the application under test to build context. Use browser MCP, test execution, code reading, and API exploration to learn how the app works and feed that knowledge to other skills.

## When to Use This Skill

- Starting work on a new feature or domain with unfamiliar behavior.
- Debugging a failing test to determine root cause (test issue vs application issue).
- Gathering context before creating page objects or API tests.
- Capturing HAR files or Swagger specs for API test scaffolding.
- Verifying application behavior matches documentation or acceptance criteria.

## Procedure

### Step 1 — Explore via Browser MCP

Use the browser MCP tools to interact with the running application:

- Navigate to the target page and take a snapshot to understand the DOM structure.
- Identify elements, component hierarchies, test-id attributes, and ARIA roles.
- Observe network requests to understand API interactions.
- Capture screenshots for documentation or bug reports.

### Step 2 — Read Application Code

When MCP access is unavailable or insufficient:

- Read frontend source code to understand component structure, routing, and data flow.
- Read backend source code or OpenAPI specs to understand API contracts.
- Identify data-testid attributes and custom component selectors available for locators.

### Step 3 — Run Targeted Tests

Execute existing tests to observe behavior:

```bash
npx playwright test {test-file} --headed --debug
```

Use `--debug` mode to step through tests and inspect state at each point. Use `--trace on` to capture full traces for post-run analysis.

### Step 4 — Capture API Context

For API automation, capture context artifacts:

- Download Swagger/OpenAPI specs to `api/{domain}/{endpoint}/context/`.
- Use the context writer tool (if available) to capture live API responses as structured context files.
- Record HAR files during browser sessions to capture real request/response pairs.

### Step 5 — Synthesize Findings

Summarize what you learned in a form that other skills can consume:

- For `sdet-ui`: Describe the page structure, available locators, and user interactions.
- For `sdet-api`: Provide endpoint details, expected request/response shapes, and status codes.
- For `sdet-document`: Identify new domains, features, or test coverage opportunities.
- For `sdet-auth`: Describe the authentication flow, identity provider, and user roles.

When debugging a test failure, determine whether the root cause is:

- **Test issue**: Flaky locator, race condition, missing wait, incorrect assertion.
- **Application issue**: Genuine bug, regression, or environment problem.

Report findings clearly so the appropriate skill or engineer can act on them.
