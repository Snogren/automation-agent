---
name: sdet-api
description: Generate high-quality API automation code and tests organized by domain following the class/schema/test pattern.
---

# API Automation Skill

Create API client classes, schemas, and tests organized by domain. Follow strict naming and structural conventions to maintain consistency across the entire API test suite.

## When to Use This Skill

- Adding test coverage for a new API endpoint.
- Scaffolding API automation for a new domain.
- Updating schemas or tests after API contract changes.
- Creating E2E API journey tests that compose multiple endpoints.

## Procedure

### Step 1 — Gather Endpoint Context

Before writing code, collect information about the endpoint:

- Use the `sdet-explore` skill to gather context.
- Identify the HTTP method, URL pattern, path/query parameters, request body, and response shape.
- Save context artifacts (swagger excerpts, HAR captures) to `api/{domain}/{endpoint}/context/`.

### Step 2 — Create the Directory Structure

```
api/{domain}/{endpoint}/
├── {Domain}_{Endpoint}_API.ts
├── {Domain}_{Endpoint}_RequestSchema.ts   # if endpoint accepts a JSON body
├── {Domain}_{Endpoint}_ResponseSchema.ts  # if endpoint returns a JSON body
├── {Domain}_{Endpoint}_Parameters.ts      # if reusable parameters are needed
├── {Domain}_{Endpoint}_Test.spec.ts
└── context/                               # swagger excerpts, HAR captures
```

Use PascalCase for the domain and endpoint in file names. Match the application's domain language exactly.

### Step 3 — Create the API Class

Create `{Domain}_{Endpoint}_API.ts`:

- Constructor accepts `APIRequestContext`.
- One method per HTTP operation (e.g., `get(id)`, `post(request)`, `search(request)`).
- Methods return raw `APIResponse` — never parse or assert inside the API class.
- For search endpoints that follow a common pattern, extend a shared generic base class (e.g., `SearchAPI<TResponse>`) from `api/common/`.

### Step 4 — Create Schema Files

Create TypeScript interfaces for request and response bodies:

- `{Domain}_{Endpoint}_RequestSchema.ts` — models the JSON request body.
- `{Domain}_{Endpoint}_ResponseSchema.ts` — models the JSON response body.
- Source interface definitions from Swagger/OpenAPI specs when available.
- Include source attribution as a comment (e.g., `// Source: {domain}Swagger.json`).

### Step 5 — Write the Test Spec

Create `{Domain}_{Endpoint}_Test.spec.ts`:

**Required**: At least one `@Healthcheck` test that:

- Calls the endpoint with valid parameters.
- Asserts on the response status using the custom `toHaveApiStatus()` matcher.
- Verifies at least one meaningful property of the response.

**Additional test categories**:

- `@Security` — Verify proper 401/403 responses for unauthenticated or unauthorized users. Use `mergeTests()` to combine fixtures from multiple user roles.
- `@Validation` — Test input validation (malformed requests, missing required fields).
- `@E2E` — Multi-step tests that compose multiple API calls (place these in `api/E2E/`).

**Test patterns**:

- Import `test` from the appropriate per-user fixture.
- Instantiate the API class with the fixture's authenticated `APIRequestContext`.
- Use `expect.poll()` for eventually-consistent state (e.g., after creating a resource, poll until it appears in search results).
- Use `pollApiResponse()` utility when polling with automatic 5xx error detection.

### Step 6 — Create Common Utilities

When a pattern repeats across domains, extract it to `api/common/`:

- Search base class with generic type parameter for the response schema.
- Request builders with static factory methods (e.g., `simpleSearch()`, `advancedSearch()`).
- Parameter types with static factories (e.g., `equals()`, `contains()`, `in()`).

### Step 7 — Validate

Run the new tests:

```bash
npx playwright test {test-file}
```

Verify healthcheck tests pass consistently. For security tests, confirm that each role receives the expected status code.
