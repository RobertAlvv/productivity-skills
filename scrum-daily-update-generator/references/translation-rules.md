# Translation Rules — Technical Detail to Team-Readable Language

This reference defines how to translate implementation-level details from a technical conversation into Daily Scrum bullets that any team member can understand. The goal is not to remove all technical content — it is to calibrate the abstraction level to the audience.

---

## Core Principle

A standup bullet should answer: **"What changed for the project?"** — not "What code did you write?"

The conversation says: "Added `cancelOnDispose` from `bloc_concurrency` to the `ProductDetailBloc` constructor"
The bullet says: "Fixed a crash on the product detail screen caused by background data not being cleaned up"

The implementation detail (`cancelOnDispose`, `bloc_concurrency`, constructor) is the *how*. The standup needs the *what* and the *why it matters*.

---

## Translation by Domain

### Frontend / Mobile UI

| Conversation detail | Standup bullet |
|---|---|
| "Refactored the Redux store to use RTK Query slices" | "Cleaned up state management in the frontend for easier maintenance" |
| "Fixed the `setState` called after `dispose` in the profile widget" | "Fixed a crash on the profile screen when navigating away quickly" |
| "Implemented `CustomScrollView` with `SliverAppBar` and `SliverList`" | "Built the scrollable header with collapsing effect on the product page" |
| "Added `RepaintBoundary` around the live chart widget" | "Optimized the live chart to reduce UI lag during real-time updates" |
| "Migrated from `Navigator 1.0` to `GoRouter` with shell routes" | "Upgraded the navigation system to support nested tab layouts" |
| "Wrapped the form in a `BlocListener` to handle validation errors" | "Connected the form validation to show error messages from the server" |

**Pattern:** Replace widget/class names with what the user sees or experiences. Keep the screen/feature name.

---

### Backend / API

| Conversation detail | Standup bullet |
|---|---|
| "Fixed N+1 query in the OrderRepository using `Include()` with eager loading" | "Fixed slow response times on the orders endpoint — 4x improvement" |
| "Added Redis caching layer to the product catalog endpoint" | "Added caching to the product catalog to reduce response times" |
| "Implemented rate limiting middleware with sliding window algorithm" | "Added rate limiting to the API to prevent abuse" |
| "Migrated from REST to GraphQL for the dashboard aggregation queries" | "Switched the dashboard data layer to reduce over-fetching and improve load time" |
| "Added database index on `orders.user_id` + `orders.created_at`" | "Optimized the order history query that was causing timeouts for users with many orders" |
| "Implemented retry logic with exponential backoff for the payment webhook" | "Added automatic retry for failed payment notifications to reduce missed transactions" |

**Pattern:** Replace the implementation mechanism with the user/system outcome. Include quantitative improvement when discussed.

---

### Infrastructure / DevOps / CI/CD

| Conversation detail | Standup bullet |
|---|---|
| "Split the monolith CI workflow into parallel matrix jobs" | "Parallelized the CI pipeline — total time dropped from 40 to 12 minutes" |
| "Added `actions/cache@v4` for Gradle dependencies in the Android build job" | "Added dependency caching to the CI pipeline to speed up Android builds" |
| "Configured ArgoCD to auto-sync the staging namespace from the `develop` branch" | "Set up automatic deployment to staging whenever code is merged to develop" |
| "Wrote a Terraform module for the RDS instance with multi-AZ failover" | "Provisioned the new production database with automatic failover" |
| "Added Prometheus metrics endpoint and Grafana dashboard for API latency" | "Set up monitoring dashboards for API response times" |
| "Implemented SOPS encryption for secrets in the Helm values files" | "Secured the deployment secrets with proper encryption" |

**Pattern:** Replace tool names with what the tool achieves. Keep metrics when available.

---

### Data / Database

| Conversation detail | Standup bullet |
|---|---|
| "Created a migration to add `JSONB` column for user preferences" | "Added flexible storage for user preferences in the database" |
| "Implemented a materialized view for the monthly report aggregation" | "Optimized the monthly report query from 12s to 200ms with pre-computed data" |
| "Fixed the schema mismatch between the analytics events table and the ingestion pipeline" | "Fixed the data format issue that was causing analytics events to be dropped" |
| "Set up read replicas with connection pooling via PgBouncer" | "Scaled the database to handle higher read traffic without affecting write performance" |

**Pattern:** Replace schema/SQL details with the data outcome. Keep performance numbers.

---

### Authentication / Security

| Conversation detail | Standup bullet |
|---|---|
| "Implemented PKCE flow with refresh token rotation for the mobile client" | "Implemented secure authentication for the mobile app with automatic session renewal" |
| "Added CSP headers and SRI hashes to the CDN-served assets" | "Hardened the security headers for externally hosted assets" |
| "Fixed JWT validation to reject tokens with `none` algorithm" | "Fixed a security vulnerability in the token validation logic" |
| "Migrated from API keys to OAuth2 client credentials for service-to-service auth" | "Upgraded service-to-service authentication to a more secure standard" |

**Pattern:** Never expose vulnerability details in a standup. Frame security work as positive defensive action.

---

### Testing

| Conversation detail | Standup bullet |
|---|---|
| "Added 15 unit tests for the `PaymentService` covering all edge cases" | "Added comprehensive test coverage for the payment processing logic" |
| "Set up Maestro flows for the checkout happy path and error recovery" | "Added automated end-to-end tests for the checkout flow" |
| "Fixed flaky test caused by race condition in the async setup" | "Fixed an intermittent test failure that was blocking CI" |
| "Implemented golden tests for the new dashboard widgets" | "Added visual regression tests for the dashboard components" |

**Pattern:** Replace test framework names with test purpose. "Golden test" → "visual regression test". "Maestro" → "automated end-to-end test".

---

## Abstraction Level Rules

### When to keep technical terms

Keep the technical term when:

- The **entire team** uses that term daily (e.g., "PR", "CI", "staging", "endpoint", "deploy")
- The term **is** the feature (e.g., "GraphQL migration", "WebSocket notifications")
- Removing it would make the bullet **too vague** to be actionable

Examples of acceptable technical terms in standups:
- PR, merge, branch, deploy, staging, production
- API, endpoint, webhook, database, migration
- CI/CD, pipeline, build, test suite
- Feature flag, rollback, hotfix

### When to strip technical terms

Strip the technical term when:

- It refers to a **specific library, class, or method** (e.g., `cancelOnDispose`, `SliverAppBar`, `RTK Query`)
- It is an **internal implementation pattern** (e.g., "repository pattern", "BLoC event", "materialized view")
- Only the **developer who wrote it** would know what it means
- It describes the **how** instead of the **what**

### Calibration by audience

| Audience | Abstraction level | Technical terms allowed |
|---|---|---|
| Senior dev team | Low abstraction | Class names OK when well-known in the codebase. Pattern names OK. |
| Mixed seniority team | Medium abstraction | Feature/screen names OK. No class names. Framework names only if universal. |
| Cross-functional (PM, design, QA) | High abstraction | Only universal terms (deploy, staging, bug). Frame everything as user/business impact. |

Default to **medium abstraction** unless the conversation context clearly indicates a senior team (heavy jargon used naturally) or cross-functional audience (user explicitly mentions non-dev attendees).

---

## Translation Process

For each item extracted from the conversation:

1. **Identify the outcome** — What changed for the project, product, or user?
2. **Identify the scope** — What screen, feature, module, or system was affected?
3. **Identify the impact** — Is there a measurable improvement? A risk reduced? A blocker removed?
4. **Compose the bullet** — `[Action verb] + [scope] + [outcome/impact]`

Example walkthrough:

- Conversation: "Spent 3 hours debugging why `SharedPreferences` was returning null for the auth token on first launch. Turned out the `init()` was being called after the first read. Moved the initialization to `main()` before `runApp()`."
- Outcome: auth token is now available on first launch
- Scope: login flow, first app launch
- Impact: fixes the bug where users had to login twice
- Bullet: "Fixed a bug where users had to log in twice on first app launch due to a storage initialization timing issue."

---

## Numbers and Metrics

When the conversation includes quantitative results, **always include them** — numbers make standups concrete:

| Without numbers | With numbers |
|---|---|
| "Improved the API response time" | "Improved the API response time from 2.3s to 400ms" |
| "Added tests for the payment module" | "Added 15 unit tests covering all payment edge cases" |
| "Reduced the CI pipeline time" | "Reduced the CI pipeline from 38 minutes to 12 minutes" |
| "Cleaned up unused code" | "Removed 1,200 lines of dead code from the legacy module" |

Only include numbers that were **explicitly mentioned** in the conversation. Do not calculate, estimate, or invent metrics.

---

## Verbs Reference

Start every bullet with an action verb. Choose the verb that best describes the nature of the work:

| Work type | Preferred verbs |
|---|---|
| New implementation | Implemented, Built, Created, Added, Set up |
| Bug fix | Fixed, Resolved, Corrected |
| Investigation | Investigated, Diagnosed, Identified, Analyzed |
| Optimization | Optimized, Improved, Reduced, Accelerated |
| Refactoring | Refactored, Cleaned up, Restructured, Migrated |
| Review / collaboration | Reviewed, Approved, Discussed, Aligned on |
| Decision | Defined, Decided, Selected, Chose |
| Documentation | Documented, Updated, Wrote |
| Deployment | Deployed, Released, Rolled out, Configured |

Avoid:
- "Worked on" — too vague, says nothing about what was done
- "Tried to" — implies failure without stating the outcome
- "Started" — acceptable only in Next/Hoy section, never in Progress
- "Looked at" — use "Investigated" or "Reviewed" instead
