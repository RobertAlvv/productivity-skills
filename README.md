# productivity-skills

AI skills focused on developer productivity — tools that automate documentation, task tracking, and workflow management so engineers spend less time on admin and more time building.

## Available Skills

### 📋 ClickUp Work Summary Generator

Converts technical work conversations into structured documentation ready to paste directly into ClickUp.

**Use for:** documenting completed work sessions, creating new tasks from technical discussions, registering progress updates, capturing architectural decisions, and tracking pending items and technical debt.

**Modes:**
- **Task Update** — register progress on an existing task
- **New Task** — create a complete new task from what was discussed

**References:** [mode-task-update](clickup-work-summary-generator/references/mode-task-update.md) · [mode-new-task](clickup-work-summary-generator/references/mode-new-task.md)

---

### 📝 Release Notes Generator

Generates polished, stakeholder-friendly release notes for Flutter Android apps, directly from git history and tags. Designed for product owners, executives, clients, and QA leads, it filters technical noise and presents only user-facing value, business impact, and honest transparency.

**Use for:** documenting production releases, summarizing changes between versions, communicating business impact, and presenting known issues or platform notes.

- Filters out technical jargon, commit hashes, and internal identifiers
- Groups changes by user-facing modules and impact level
- Includes executive summary and visual flow diagrams (when relevant)
- Adapts language and tone for non-technical audiences (Spanish by default)
- Templates and writing guide ensure professional, consistent output

**References:** [templates](release-notes-generator/references/templates.md) · [writing-guide](release-notes-generator/references/writing-guide.md) · [mermaid-flows](release-notes-generator/references/mermaid-flows.md)

---

## Repository Structure

```
productivity-skills/
├── README.md
├── clickup-work-summary-generator/
│   ├── SKILL.md
│   └── references/
│       ├── mode-task-update.md
│       └── mode-new-task.md
└── release-notes-generator/
    ├── SKILL.md
    └── references/
        ├── templates.md
        ├── writing-guide.md
        └── mermaid-flows.md
```
