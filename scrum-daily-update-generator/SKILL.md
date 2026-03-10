---
name: scrum-daily-update-generator
description: >
  Analyzes a technical conversation or work session and generates a concise, structured Daily Scrum update
  ready to be read aloud in under 30 seconds. Use this skill whenever the user mentions "daily", "standup",
  "scrum update", "what did I work on", "resumir mi trabajo", "preparar el daily", "daily update", or signals
  the end of a work session and wants to summarize it for the team. Also trigger when users ask to summarize
  a conversation for team communication purposes, or when they say things like "I need to explain this to
  my team", "help me write a status update", or "what should I say in the meeting". This skill transforms
  technical discussions, debugging sessions, feature implementations, and research conversations into
  clear, human-sounding standup bullets that a senior developer would actually say.
---

# Scrum Daily Update Generator

Transforms a technical conversation or work session into a Daily Scrum update formatted for fast,
clear team communication. Output is designed to be spoken aloud in under 30 seconds.

---

## Step-by-step process

### 1. Analyze the conversation

Scan the full conversation and extract:

- **Tasks worked on** — features, bugs, refactors, research, integrations, reviews
- **Decisions made** — architectural choices, library selections, approach changes
- **Solutions implemented** — actual code written, problems solved, PRs opened
- **Blockers or open questions** — unresolved dependencies, unclear requirements, waiting on someone
- **Planned next steps** — things explicitly mentioned as "next" or implied by incomplete work

> If the conversation is very long or covers multiple topics, group by feature/task area before summarizing.

### 2. Translate technical details into team-readable bullets

Apply these translation rules:

| Instead of... | Write... |
|---|---|
| "Fixed the NullPointerException in UserService.java line 247" | "Fixed a crash in the user service affecting login flow" |
| "Refactored the Redux store to use RTK slices" | "Cleaned up state management in the frontend" |
| "Investigated S3 presigned URL expiry issue with boto3" | "Investigated file upload expiry bug in cloud storage" |
| "Spent 2h reading Next.js docs on ISR" | "Researched rendering strategy options for the dashboard" |

**Rule:** Strip implementation noise. Keep business/feature context.

### 3. Generate the daily update

Use the standard format below. Choose between **Spanish** or **English** based on the user's language.

---

## Output format

### Standard (recommended for most teams)

```markdown
### Daily Update

**Ayer trabajé en**
- [bullet 1 — main task or feature]
- [bullet 2 — secondary task, bug, or investigation]
- [bullet 3 — optional: decision, review, or research]

**Hoy planeo**
- [bullet 1 — clear next action]
- [bullet 2 — optional: follow-up or dependent task]

**Bloqueos**
- [Describe blocker clearly, or: ninguno]
```

### English version (for international/mixed teams)

```markdown
### Daily Update

**Progress**
- [bullet 1]
- [bullet 2]

**Next**
- [bullet 1]
- [bullet 2]

**Blockers**
- [blocker or: none]
```

### Extended version (when impact context adds value)

Add an **Impacto / Impact** section after blockers when the work has clear team or product value:

```markdown
**Impacto**
- [One line: what this enables or unblocks for the team]
```

---

## Formatting rules

1. **Maximum 3–4 bullets per section** — ruthlessly prioritize
2. **Each bullet = 1 clear sentence** — no sub-bullets, no parentheticals
3. **Start bullets with a verb** — Implemented, Fixed, Investigated, Reviewed, Defined, Deployed
4. **No jargon without context** — translate technical terms into feature/outcome language
5. **Blockers must be actionable** — name what you need, who you're waiting on, or what's unclear
6. **"Ninguno / None"** is always valid for blockers — don't invent friction
7. **Hoy/Next section** — only include things you can realistically start today

---

## Multi-task grouping (for long or complex sessions)

If the session covered 3+ distinct areas, group before summarizing:

```
Area 1: Auth service refactor → 2 bullets
Area 2: Bug investigation → 1 bullet
Area 3: Code review → 1 bullet
```

Then merge into the most impactful 3–4 bullets for the final output. When in doubt, lead with the item
that most directly affects the sprint goal.

---

## Example outputs

### Example 1 — Feature implementation session

**Context:** Session where a developer built a skill for exporting AI conversation summaries.

```markdown
### Daily Update

**Ayer trabajé en**
- Implementación del skill para generar resúmenes de trabajo desde conversaciones con IA.
- Diseño de estructura reutilizable para skills de agentes en el repositorio del equipo.
- Definición del formato estándar para exportar avances desde sesiones de trabajo.

**Hoy planeo**
- Integrar el skill dentro del repositorio de AI skills del equipo.
- Documentar el formato de output para que otros devs puedan adaptarlo.

**Bloqueos**
- Ninguno.

**Impacto**
- Reduce el tiempo de preparación del daily para quienes trabajan con agentes IA.
```

---

### Example 2 — Debugging session

**Context:** Session investigating a slow API endpoint.

```markdown
### Daily Update

**Progress**
- Identified the root cause of slow response times on the /orders endpoint (N+1 query).
- Implemented eager loading fix and verified a 4x improvement in local tests.

**Next**
- Open PR for the query fix and request review.
- Monitor query performance in staging after merge.

**Blockers**
- Need DB access in staging to run EXPLAIN ANALYZE — waiting on DevOps.
```

---

### Example 3 — Research / investigation session

**Context:** Session evaluating auth libraries for a new project.

```markdown
### Daily Update

**Ayer trabajé en**
- Investigación de opciones de autenticación para el nuevo servicio (Auth0, Cognito, Keycloak).
- Comparación de costos, integración con nuestro stack y curva de aprendizaje del equipo.
- Decisión preliminar: Auth0 como opción principal, Keycloak como backup self-hosted.

**Hoy planeo**
- Presentar resumen de opciones al equipo en la sesión de arquitectura.
- Iniciar spike técnico con Auth0 si hay consenso.

**Bloqueos**
- Ninguno por ahora, pero la decisión final depende del presupuesto aprobado por producto.
```

---

## Tone calibration

| Team type | Tone | Example |
|---|---|---|
| Junior/mixed | Descriptive, clear | "Arreglé el bug de login que bloqueaba a usuarios nuevos" |
| Senior/technical | Concise, outcome-first | "Fixed auth regression in new user flow" |
| Cross-functional | Business-outcome focus | "Resolved issue preventing new user onboarding" |

Default to **descriptive and clear** unless the conversation context suggests a senior technical team.

---

## When context is ambiguous

If the conversation doesn't clearly indicate what was "yesterday" vs "today", use this heuristic:

- **Completed work / resolved problems** → Ayer / Progress
- **Partially done / still in progress** → split across both sections
- **Explicitly mentioned as next step** → Hoy / Next
- **Mentioned but stuck** → Bloqueos / Blockers

If the session is too short or lacks enough detail, ask the user: *"Do you want to add anything that wasn't covered in the conversation?"*

---

## Reference Guide

Load the corresponding reference file based on the generation need:

| Reference | Purpose | When to load |
|---|---|---|
| `references/output-examples.md` | Complete output examples by complexity level (trivial, standard, complex, multi-feature) and by team type (junior, senior, cross-functional) | Use as calibration targets when generating any update |
| `references/translation-rules.md` | Rules for translating implementation details into team-readable language, organized by domain (frontend, backend, infra, data, auth, testing) | Use when converting technical conversation content into standup bullets |