# Output Examples — By Complexity and Team Type

This reference contains complete output examples organized by session complexity and team communication style. Use these as calibration targets when generating Daily Scrum updates.

---

## By Complexity Level

### Trivial (config change, typo fix, dependency bump)

Session signals: conversation < 10 messages, single file changed, no decisions or investigation.

```markdown
### Daily Update

**Progress**
- Updated the Flutter SDK from 3.19 to 3.24 in the CI workflow.

**Next**
- Monitor that all CI jobs pass with the new version.

**Blockers**
- None.
```

Note: trivial sessions produce 1 bullet per section maximum. No Impact section. If there are no meaningful blockers or next steps, it's acceptable to have a single-section update.

---

### Standard (feature, bugfix with investigation, single-module refactor)

Session signals: conversation 10–30 messages, 2–5 files changed, at least one decision made.

```markdown
### Daily Update

**Progress**
- Fixed the crash on the product detail screen when rotating the device on Android.
- Root cause was the state manager not canceling its data stream on screen disposal.
- Added a unit test to prevent regression on this flow.

**Next**
- Verify whether the same pattern affects the shopping cart module.
- Add visual regression tests for the detail screen in landscape mode.

**Blockers**
- None.
```

Note: 2–3 bullets in Progress, 1–2 in Next. Technical details are translated to feature/outcome language but the diagnosis is preserved at a high level because the team needs to know it was investigated, not just patched.

---

### Complex (architectural refactor, multi-module feature, extensive investigation)

Session signals: conversation > 30 messages, multiple files across modules, alternatives explored, tradeoffs discussed.

```markdown
### Daily Update

**Progress**
- Completed the migration of the notification system from polling to real-time WebSocket events.
- Evaluated three approaches: native WebSocket, Socket.IO wrapper, and Firebase Cloud Messaging — chose native WebSocket for lower overhead and no new dependencies.
- Implemented the connection manager with automatic reconnection and exponential backoff.
- Updated 4 modules that consumed the old polling service to use the new event stream.

**Next**
- Write integration tests for the reconnection logic under network instability.
- Coordinate with backend team to enable WebSocket support in the staging environment.

**Blockers**
- Staging environment does not support WebSocket connections yet — waiting on DevOps to update the load balancer configuration.

**Impact**
- Notification latency drops from 30s (polling interval) to sub-second, directly improving the real-time collaboration experience.
```

Note: 3–4 bullets in Progress covering the decision, implementation, and scope. Blockers are specific and name who/what is blocking. Impact section is included because the work has measurable team/product value.

---

### Multi-feature (session covered 2+ independent topics)

Session signals: conversation shifted topics clearly, work items are unrelated to each other.

When a session covers multiple independent areas, group by area first, then select the most impactful 3–4 bullets. Do NOT mix unrelated items without grouping context.

```markdown
### Daily Update

**Progress**
- **Auth module:** Fixed the token refresh race condition that caused random logouts on slow connections.
- **Dashboard:** Implemented the new weekly summary chart with data from the analytics API.
- **Code review:** Reviewed and approved PR #87 for the payment service error handling refactor.

**Next**
- Deploy the auth fix to staging and run the automated session stability test suite.
- Start wiring the dashboard chart filters based on the design specs from last sprint.

**Blockers**
- The analytics API returns inconsistent date formats between endpoints — opened an issue with the backend team.
```

Note: each Progress bullet is prefixed with the area for clarity. The agent should ask the user if 3+ topics should be split across separate standups (if the user attends multiple team standups).

---

## By Team Communication Style

### Junior / Mixed Seniority Team

Prioritize clarity. Explain what things are, not just what was done. Avoid abbreviations.

```markdown
### Daily Update

**Progress**
- Fixed a bug where the app crashed when users rotated their phone on the product detail screen.
- The issue was caused by background data still being sent to the screen after it was closed.
- Added a test to make sure this specific crash does not come back.

**Next**
- Check if other screens in the app have the same kind of bug.
- Start working on the landscape layout for the product detail page.

**Blockers**
- None.
```

Characteristics:
- "background data still being sent" instead of "stream not canceled after dispose"
- "test to make sure this crash does not come back" instead of "regression test for the bloc lifecycle"
- No acronyms (PR, CI, N+1) without explanation

---

### Senior / Technical Team

Concise, outcome-first. Technical terms are fine. Skip obvious context.

```markdown
### Daily Update

**Progress**
- Fixed stream disposal race condition in ProductDetailBloc — StateError on rotation.
- Added cancelOnDispose instead of autoDispose to preserve navigation cache.
- Unit test covers post-close emission.

**Next**
- Audit CartBloc for same pattern.
- Golden tests for detail screen landscape.

**Blockers**
- None.
```

Characteristics:
- Specific class/component names are acceptable
- Tradeoff reasoning is compressed ("instead of X to preserve Y")
- Bullets are shorter — assumes the team can fill in context
- No "the app" or "users" — jumps straight to the component

---

### Cross-functional Team (product, design, QA present)

Business-outcome focus. What does this mean for the product or users? Skip internal technical details.

```markdown
### Daily Update

**Progress**
- Resolved the crash that affected all Android users when rotating the device on the product detail screen.
- The fix preserves the browsing experience when users switch between tabs without losing their place.

**Next**
- Verify the fix does not affect other screens with similar behavior.
- Begin layout improvements for the product detail page in landscape mode.

**Blockers**
- None.
```

Characteristics:
- "all Android users" frames the impact scope
- "browsing experience when users switch between tabs" explains the cache preservation in user terms
- No class names, no package names, no test types
- Each bullet answers "so what?" for a non-developer

---

## Edge Cases

### Research-only Session (no code changes)

```markdown
### Daily Update

**Progress**
- Evaluated three authentication providers for the new service: Auth0, Cognito, and Keycloak.
- Compared integration complexity, cost structure, and team familiarity.
- Preliminary recommendation is Auth0, with Keycloak as a self-hosted fallback.

**Next**
- Present the comparison to the team in the architecture session.
- Start a technical spike with Auth0 if team agrees.

**Blockers**
- Final decision depends on budget approval from product.
```

---

### Continuation of Multi-day Work

When the user has been working on the same initiative across multiple days:

```markdown
### Daily Update

**Progress**
- Continued the CI pipeline optimization — implemented test sharding across 4 parallel runners.
- Total pipeline time dropped from 38 minutes to 12 minutes after sharding + dependency caching.

**Next**
- Add the artifact upload step for coverage reports.
- Document the new pipeline structure in the team wiki.

**Blockers**
- None.
```

Note: "Continued the..." is a valid opening when work spans multiple days. It signals continuity without re-explaining the full context.

---

### Very Short Session (< 5 messages, minimal work)

```markdown
### Daily Update

**Progress**
- Quick config fix: updated the API base URL for the staging environment.

**Next**
- Continue the payment module integration from yesterday.

**Blockers**
- None.
```

Note: when the session is too short for a full update, one compound bullet is enough. Do not pad with filler.

---

## Anti-patterns

### Too verbose for a trivial change

Bad:
```markdown
**Progress**
- Investigated the staging environment configuration issue that was causing API calls to fail.
- After thorough analysis, determined that the base URL was pointing to the old staging server.
- Updated the configuration file with the correct URL and verified the fix locally.
- Confirmed that all API endpoints respond correctly with the new configuration.
```

Good:
```markdown
**Progress**
- Fixed the staging API URL that was pointing to the old server.
```

---

### Too compressed for complex work

Bad:
```markdown
**Progress**
- Migrated notifications to WebSocket.
```

Good:
```markdown
**Progress**
- Completed the migration of the notification system from polling to real-time WebSocket events.
- Evaluated three approaches and chose native WebSocket for lower overhead.
- Implemented connection manager with automatic reconnection and backoff.
```

---

### Invented blocker

Bad:
```markdown
**Blockers**
- Could potentially have issues if the backend changes their API contract.
```

Good:
```markdown
**Blockers**
- None.
```

A hypothetical future risk is NOT a blocker. Blockers are concrete, present, and actionable.
