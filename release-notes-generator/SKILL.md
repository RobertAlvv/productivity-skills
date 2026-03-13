---
name: release-notes-generator
description: |
  Generate polished, stakeholder-friendly release notes from git tags when deploying to production. ALWAYS use this skill when the user mentions "release note", "notas de versión", "notas de release", "pase a PRD", "deploy a producción", "salida a producción", "nuevo tag", "versión nueva", or asks what changed between versions. Also trigger for: "genera el release note del tag X", "qué cambió en la versión X", "prepara las notas de la release", "documenta el pase a producción", "release notes for v2.x", or any similar phrase. This skill extracts git history between two tags, filters all technical noise, and produces a structured non-technical document — with executive summary, impact levels, affected modules, known issues, and platform notes — ready to be shared with product owners, C-level, clients, or QA leads.
compatibility: requires git CLI available in the working directory or project path
---

# Release Notes Skill

Produces professional, stakeholder-ready release notes from git history. The audience is **non-technical**: product owners, executives, clients, or QA leads. The output must contain zero technical jargon, zero commit hashes, zero internal identifiers — only user-facing value, business impact, and honest transparency.

**Project context (fixed — do not ask the user):**
- **Platform**: Android only
- **Technology**: Flutter

Read `references/writing-guide.md` before writing any content.  
Read `references/templates.md` to select and fill the right template.  
Read `references/mermaid-flows.md` before deciding whether to include visual flow diagrams.

---

## Step 1 — Gather context

Before touching git, collect the following. Infer from context when possible; ask only for what's missing:

| Field | How to obtain |
|---|---|
| **App name** | Ask the user, or infer from `pubspec.yaml` → `name:` field |
| **New tag** | Provided by user, or auto-detect (see Step 2) |
| **Previous tag** | Provided by user, or auto-detect (see Step 2) |
| **Target audience** | Default: Product stakeholders. Ask if context suggests exec/client/internal |
| **Release type** | Infer from version bump: Major/Minor = features, Patch = fix/hotfix |

Do not proceed to Step 2 until app name and both tags are confirmed or auto-detected.

---

## Step 2 — Resolve git tags

If the user provided both tags, skip to Step 3.

**Auto-detect the previous tag** when only the new tag is given:
```bash
git tag --sort=-version:refname | head -20
```
Confirm the detected previous tag with the user before continuing.

**Auto-detect both tags** when none are given:
```bash
git tag --sort=-version:refname | head -2
```

**Verify tags exist**:
```bash
git tag -l | grep -E "^<tag_name>$"
```
If a tag isn't found, stop and report to the user. Do not proceed with unverified tags.

**Infer release type from semver**:
- `vX.0.0` → Major release (significant features or breaking changes)
- `vX.Y.0` → Minor release (new features)
- `vX.Y.Z` where Z > 0 → Patch (bug fixes, hotfix)

---

## Step 3 — Extract raw git history

Pull commit data between the two tags:
```bash
git log <previous_tag>..<new_tag> --pretty=format:"%H|%s|%b" --no-merges
```

Pull merge commit messages (often contain PR descriptions with richer context):
```bash
git log <previous_tag>..<new_tag> --pretty=format:"%H|%s|%b" --merges
```

Check for a CHANGELOG if present:
```bash
cat CHANGELOG.md 2>/dev/null | head -150
```

**Extract ticket references** silently (do not include in output to stakeholders):
Look for patterns: `JIRA-\d+`, `CU-[a-z0-9]+`, `#\d+`, `fixes #\d+`, `closes #\d+`  
Store them as an internal list for traceability if requested separately.

---

## Step 4 — Classify and filter commits

For each commit, silently decide: **keep** or **discard**.

**DISCARD** (invisible to stakeholders):
- Dependency updates: `bump`, `upgrade`, `update deps`, `chore(deps)`, `pubspec.lock`, `pubspec.yaml` (version-only changes)
- CI/CD: `ci:`, `github actions`, `pipeline`, `workflow`, `.yml` changes, `Dockerfile`, `fastlane`
- Pure refactors: `refactor:`, `cleanup`, `rename`, `restructure` — **unless** they fix something user-visible
- Build system: `build:`, `gradle`, `build.gradle`, `gradle.properties`, `key.properties`
- Flutter/Dart tooling: `lint`, `format`, `style:`, `analysis_options.yaml`, `dart fix`
- Code generation only: `build_runner`, `*.g.dart`, `*.freezed.dart`, `*.gr.dart` — unless a new feature triggered the generation
- Dev docs: `docs: update readme`, `add comments`, `update CONTRIBUTING`
- Test-only: `test:`, `add unit tests`, `add widget tests`, `add integration tests`
- Empty/noise merge commits: `merge branch X into Y` with no body

**KEEP** (user-facing value):
- New features, screens, flows: `feat:`, `add`, `new`, `implement`
- Bug fixes affecting users: `fix:`, `hotfix:`, `resolve`, `patch`
- Performance users perceive: `perf:`, `optimize loading`, `reduce time`, `faster`
- UX/UI changes: `improve`, `redesign`, `update UI`, `accessibility`
- Content/copy visible to users
- Third-party integrations now available to users
- Behavioral changes users will notice

**Ambiguous commits**: lean toward keeping if any doubt of user impact. Then transform conservatively.

**Poor-quality commits** (e.g., just `"fix"` or `"update"`): attempt to infer meaning from the changed files or PR body. If insufficient context exists, note it as "minor improvements" grouped with others. Never fabricate specifics.

---

## Step 5 — Classify by module and impact

Group kept items by **user-facing module** (not by commit type).  
See `references/writing-guide.md` → "Module/area naming conventions" for mapping code names to UI names.

Assign impact level to each item:
- 🔴 **Alto impacto**: Affects critical flows (payments, login, data), many users, or resolves an active incident
- 🟡 **Mejora notable**: Significant but non-critical improvement
- *(unlabeled)*: Minor enhancement — include only if it genuinely adds value to the note

Determine release narrative: What is the **one sentence** that describes what this release is about?  
Example: *"Esta versión se enfoca en estabilidad del flujo de pagos y mejoras de velocidad en la pantalla principal."*

---

## Step 5.5 — Detect flow-impacting changes (Mermaid)

Read `references/mermaid-flows.md` fully before this step.

Run the following to identify changed Flutter files:
```bash
git diff --name-only <previous_tag>..<new_tag> | grep -iE \
  "(screen|page|view|route|router|navigation|nav|flow|wizard|onboarding)"
```

For each flagged file, inspect the diff:
```bash
git diff <previous_tag>..<new_tag> -- <file_path>
```

Evaluate each module against the trigger conditions in `references/mermaid-flows.md` → "When to include a flow diagram".

Build an internal list: **modules that require a diagram** (can be empty — that is valid and expected for fix/hotfix releases).

For each module in the list:
1. Reconstruct the user journey from the diff context + commit messages
2. Draft the `flowchart TD` diagram following the rules in `references/mermaid-flows.md`
3. Apply green styling to new screens, yellow to modified ones
4. Write a 1–2 sentence plain-language caption beneath the diagram

If no modules require a diagram, skip the visual flows section entirely — do not include an empty section.

---

## Step 6 — Select template and write

Read `references/templates.md` and select the right template based on release type.

Then fill it following the writing rules in `references/writing-guide.md`:
- Executive summary first: narrative before bullets
- Group by module, not by fix/feature type
- Each bullet: benefit/outcome framing, not action framing
- Include `Consideraciones` section only if relevant (breaking changes, known issues, platform-specific)
- Known issues: be honest and specific. *"Estamos al tanto de X y lo resolveremos en la próxima versión"* builds more trust than silence

**Language**: Match the user's language. Default is Spanish for this project context.

---

## Step 7 — Quality gate

Before saving, verify every item in this checklist:

- [ ] **Executive summary present** — 2–4 sentences, narrative tone, non-technical
- [ ] **Zero technical terms** — no function names, no package names, no error codes, no commit hashes
- [ ] **Every item is benefit-framed** — describes what the user can now do or no longer suffers from
- [ ] **Sections are non-empty** — remove any section with no content
- [ ] **Impact labels accurate** — only mark HIGH for genuinely critical items
- [ ] **Known issues section honest** — if the team is aware of pending issues, they must be declared
- [ ] **Version and date correct** — tag name matches exactly, date is today
- [ ] **Platform header shows "Android"** — always, no exceptions
- [ ] **Mermaid diagrams present when required** — every module with a new/modified flow has a diagram; hotfix releases have none
- [ ] **Mermaid diagrams are stakeholder-readable** — no class names, no technical node labels, max 12 nodes per diagram, caption present

If any item fails: fix it before saving. Do not skip the gate.

---

## Step 8 — Save and present

Save as:
```
release-notes-{version}-{YYYY-MM-DD}.md
```

Present via `present_files`.

Then offer: *"¿Quieres también una versión en .docx para compartir por correo o en canales no técnicos?"* — use the `docx` skill if accepted.

---

## Edge cases

| Situation | Handling |
|---|---|
| No commits between tags | Verify tags, report to user. Do not generate empty notes. |
| Squash merge strategy | Use PR titles and bodies as commit messages. `git log --merges` is the main source. |
| 50+ commits | Group heavily by module. Aim for 8–12 bullets max in the final document. |
| Hotfix (patch version) | Use Template B. Keep document short — 1 paragraph + 1–3 bullets is ideal. |
| First release ever | Use `git rev-list --max-parents=0 HEAD` as the base. |
| Commits with no body, just `"fix"` | Group as *"Se resolvieron varios problemas menores de estabilidad"* — never fabricate details. |
| Mixed Spanish/English commits | Always write output in a single language (Spanish by default). |