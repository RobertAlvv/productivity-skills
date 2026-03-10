# Mode: Task Update — Template Reference

This reference contains the complete templates for the Task Update mode. Use when the user wants to log progress on a task that already exists in ClickUp.

---

## Task Update Principles

A Task Update is a **progress record**, not a task rewrite. It must:

- Be additive — it is appended to the task history, not replacing it
- Have a date — each update is a temporal snapshot
- Be self-contained — another developer can understand what happened without reading previous updates
- Scale with complexity — a trivial bugfix needs 3 lines, an architectural refactor needs complete sections

---

## Template by complexity level

### Trivial (one-line bugfix, config change, typo)

```markdown
## Update — [YYYY-MM-DD]

**What was done:** [description in one sentence]

**Files:** `path/to/file.ext`
```

Example:

```markdown
## Update — 2026-03-10

**What was done:** Fixed typo in the login button label ("Inicar" → "Iniciar").

**Files:** `lib/features/auth/presentation/widgets/login_button.dart`
```

---

### Standard (simple feature, bugfix with diagnosis, single-module refactor)

```markdown
## Update — [YYYY-MM-DD]

### Context
[What was being resolved or implemented. 1–3 sentences.]

### What was done
- [bullet 1 — concrete action]
- [bullet 2 — concrete action]
- [bullet 3 — concrete action]

### Technical decisions
- **[decision]** — [reason as discussed]

### Modified files
- `path/to/file.dart` — [what changed in this file]
- `path/to/other_file.dart` — [what changed]

### Commits
- `hash` — `commit message`

### Open items
- [ ] [concrete open item 1]
- [ ] [concrete open item 2]

### Risks / Notes
- [relevant risk or note]
```

Example:

```markdown
## Update — 2026-03-10

### Context
The product detail screen was crashing when rotating the device on Android. The error appeared as `StateError: Cannot emit new states after calling close`.

### What was done
- Diagnosed that the `ProductDetailBloc` was still emitting states after `dispose()` because the WebSocket stream was not being canceled
- Added `cancelOnDispose` from the `bloc_concurrency` package to the Bloc
- Wrote a unit test that verifies no states are emitted after `close()`

### Technical decisions
- **Use `cancelOnDispose` instead of `autoDispose`** — `autoDispose` destroys the Bloc when leaving the widget, but we need to preserve the cache when the user navigates between tabs

### Modified files
- `lib/features/product/bloc/product_detail_bloc.dart` — added `cancelOnDispose` in the constructor
- `test/features/product/bloc/product_detail_bloc_test.dart` — new test `emits nothing after close`

### Commits
- `a1b2c3d` — `fix(product): cancel stream on bloc dispose to prevent StateError`

### Open items
- [ ] Verify that the same pattern applies to `CartBloc` (it has a similar stream)
- [ ] Add golden tests for the detail screen in landscape mode

### Risks / Notes
- If the backend switches to SSE instead of WebSocket, `cancelOnDispose` may not handle the disconnection correctly
```

---

### Complex (architectural refactor, multi-module feature, extensive technical investigation)

```markdown
## Update — [YYYY-MM-DD]

### Context
[Description of the problem or initiative. Include why this work was started and what motivated it. 3–5 sentences.]

### Diagnosis / Investigation
[Analysis performed. What was discovered, what data was reviewed, what hypotheses were tested.]

### Explored alternatives
| Alternative | Evaluation | Result |
|---|---|---|
| [Option A] | [pros/cons as discussed] | Discarded — [reason] |
| [Option B] | [pros/cons as discussed] | **Chosen** — [reason] |
| [Option C] | [pros/cons as discussed] | Discarded — [reason] |

### What was done
- [bullet 1 — concrete action with sufficient context]
- [bullet 2 — concrete action]
- [bullet 3 — concrete action]
- [bullet 4 — concrete action]

### Technical decisions
- **[decision 1]** — [reason as discussed, including tradeoffs]
- **[decision 2]** — [reason]

### Modified files
**New:**
- `path/to/new_file.dart` — [purpose]

**Modified:**
- `path/to/file.dart` — [what changed and why]

**Deleted:**
- `path/to/old_file.dart` — [reason for deletion]

### Commits
- `hash1` — `commit message 1`
- `hash2` — `commit message 2`

### Quantitative data
- [metric 1: e.g. "The pipeline dropped from 40 min to 10 min"]
- [metric 2: e.g. "340 lines of duplicate code were removed"]

### Identified technical debt
- [debt 1 — what was discovered that needs fixing but was not resolved in this session]
- [debt 2]

### Open items
- [ ] [concrete open item 1]
- [ ] [concrete open item 2]
- [ ] [concrete open item 3]

### Risks
- [risk 1 — condition and potential impact]
- [risk 2]

### Dependencies
- [depends on PR / task / external service]
```

---

## Task Update Rules

1. **Always include a date** — the update is a temporal snapshot.
2. **Use the correct complexity level** — do not generate a complex template for a trivial bugfix.
3. **Files with full path** — never "the bloc file", always `lib/features/auth/bloc/login_bloc.dart`.
4. **Open items as checkboxes** — ClickUp renders them as interactive subtasks.
5. **Remove empty sections** — if "Risks" does not apply, remove the section. Do not write "N/A".
6. **Each bullet must be self-contained** — another developer must understand the bullet without reading the conversation.
7. **Do not repeat the original task description** — the update is new progress, not inherited context.