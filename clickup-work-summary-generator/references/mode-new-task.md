# Mode: New Task — Template Reference

This reference contains the complete templates for the New Task mode. Use when the user wants to create a new task in ClickUp based on what was discussed in the technical session.

---

## New Task Principles

A New Task is a **work definition**, not a session summary. It must:

- Be executable — another developer can pick it up and start working without asking anything
- Have acceptance criteria — defines when the task is "done"
- Include sufficient context — the "why" is as important as the "what"
- Scale with complexity — a config task needs 5 lines, an architectural feature task needs complete sections with technical scope

---

## Template by complexity level

### Trivial (configuration task, cleanup, point fix)

```markdown
## [Descriptive task title]

**Objective:** [what should be achieved, in one sentence]

**Scope:**
- [concrete action 1]
- [concrete action 2]

**Acceptance criteria:**
- [ ] [verifiable criterion]
```

Example:

```markdown
## Update Flutter SDK version to 3.24.0 in CI

**Objective:** Update the Flutter version in the CI workflow to enable native test sharding.

**Scope:**
- Change `flutter-version` from `3.19.0` to `3.24.0` in `.github/workflows/ci.yml`
- Verify that all jobs pass with the new version

**Acceptance criteria:**
- [ ] CI pipeline passes green with Flutter 3.24.0
- [ ] No regressions in the 4 test shards
```

---

### Standard (feature, planned bugfix, single-module refactor)

```markdown
## [Descriptive task title]

### Context
[Why this task is needed. What problem it solves or what value it adds. 2–4 sentences. Include relevant technical background.]

### Objective
[What should be achieved when this task is complete. Clear expected result.]

### Technical scope
- [change or implementation 1]
- [change or implementation 2]
- [change or implementation 3]

### Files involved
- `path/to/file.dart` — [what needs to change]
- `path/to/other_file.dart` — [what needs to change]

### Acceptance criteria
- [ ] [verifiable criterion 1]
- [ ] [verifiable criterion 2]
- [ ] [verifiable criterion 3]
- [ ] Unit tests cover the change
- [ ] No regressions in existing tests

### Technical notes
- [relevant technical context for whoever picks up the task]
- [reference to technical decision made in the session, if applicable]

### Dependencies
- [depends on task/PR/service — if applicable]
```

Example:

```markdown
## Implement stream cancellation in ProductDetailBloc on dispose

### Context
The product detail screen crashes when rotating the device on Android. The error `StateError: Cannot emit new states after calling close` indicates the Bloc continues receiving events from the WebSocket after `dispose()`. This affects all Android users and occurs consistently on landscape → portrait rotation.

### Objective
The `ProductDetailBloc` must cancel its subscription to the WebSocket stream when closed, without breaking the data cache used when navigating between tabs.

### Technical scope
- Add `cancelOnDispose` from the `bloc_concurrency` package to the Bloc constructor
- Write a unit test that verifies no states are emitted after `close()`
- Verify that the product cache still works when navigating between tabs

### Files involved
- `lib/features/product/bloc/product_detail_bloc.dart` — add `cancelOnDispose`
- `test/features/product/bloc/product_detail_bloc_test.dart` — new dispose test

### Acceptance criteria
- [ ] No `StateError` is produced when rotating the device on the detail screen
- [ ] The product cache persists when navigating between tabs
- [ ] Unit test verifies no emissions after `close()`
- [ ] Manual smoke test on Android confirms the fix

### Technical notes
- `cancelOnDispose` was chosen instead of `autoDispose` because `autoDispose` would destroy the Bloc when leaving the widget, losing the cache
- Check if the same pattern applies to `CartBloc` which has a similar stream
```

---

### Complex (architectural feature, epics, investigation with implementation)

```markdown
## [Descriptive task title]

### Context
[Complete background of the problem or initiative. Include: motivation, current system state, why the current approach is insufficient. 4–6 sentences.]

### Objective
[Expected result upon completing the task. Must be measurable or verifiable.]

### Scope
**Includes:**
- [concept or component 1]
- [concept or component 2]
- [concept or component 3]

**Does not include (out of scope):**
- [explicit exclusion 1 — to avoid scope creep]
- [explicit exclusion 2]

### Technical design
[Description of the agreed technical approach. Include the decision and its rationale. If there were discarded alternatives, mention them briefly.]

### Technical scope
- [change or implementation 1 — with sufficient detail to execute]
- [change or implementation 2]
- [change or implementation 3]
- [change or implementation 4]

### Files involved
**New:**
- `path/to/new_file.dart` — [file purpose]

**Modified:**
- `path/to/existing_file.dart` — [what needs to change]

**Potentially deleted:**
- `path/to/old_file.dart` — [reason for deletion]

### Acceptance criteria
- [ ] [verifiable criterion 1]
- [ ] [verifiable criterion 2]
- [ ] [verifiable criterion 3]
- [ ] [testing criterion]
- [ ] [performance criterion, if applicable]

### Identified risks
- **[risk 1]** — [potential impact and suggested mitigation]
- **[risk 2]** — [potential impact]

### Related technical debt
- [existing debt that this task discovered or amplifies]

### Dependencies
- [depends on task/PR/service]
- [blocks task/feature]

### Suggested sub-tasks
If the task is large enough, propose a decomposition:
- [ ] Sub-task 1: [description]
- [ ] Sub-task 2: [description]
- [ ] Sub-task 3: [description]
```

---

## New Task Rules

1. **Actionable title** — the title should describe the work, not the problem. "Implement stream cancellation in ProductDetailBloc" > "The Bloc crashes".
2. **Context with "why"** — another developer needs to understand the motivation, not just the steps.
3. **Verifiable acceptance criteria** — each criterion must be verifiable with a yes/no. "The Bloc works well" is not verifiable. "No StateError is produced on rotation" is.
4. **Explicit scope** — in complex tasks, defining what is **out of** scope is as important as defining what is **in** scope.
5. **Files with full path** — another developer must be able to find the files without searching.
6. **Do not include detailed implementation** — the task describes the "what" and the "why", not the step-by-step "how". The developer who picks it up decides the exact approach.
7. **Remove empty sections** — if "Risks" does not apply, remove the entire section.
8. **One topic per task** — if the session covered 2+ topics, create a separate task for each one.