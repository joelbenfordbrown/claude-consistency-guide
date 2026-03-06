# Project: [Project Name]
# Last Updated: [Date]

## Reference
Always load @PROJECT_CONTEXT.md at the start of each session for domain knowledge,
business logic, and user personas. When asked about any domain-specific topic,
consult PROJECT_CONTEXT.md before responding.

Also invoke it directly in your opening message:
@PROJECT_CONTEXT.md — please load this before we begin.

---

## Project Overview
[2–3 sentences on what this project is, its tech stack, and its purpose.
Claude needs this to make sensible decisions without asking.]

---

## Architecture Constraints
[Non-negotiable structural rules.]

- This project uses [framework/language/version]
- State management is handled by [X] — do not introduce alternatives
- All API calls go through [X service layer] — never call APIs directly from components
- File structure: [describe your conventions]

---

## Coding Standards
[Exact standards. The more specific, the fewer surprises.]

- Language: [e.g. TypeScript strict mode]
- Formatting: [e.g. Prettier with [config file] — never reformat files you're not editing]
- Naming: [e.g. camelCase for variables, PascalCase for components, SCREAMING_SNAKE for constants]
- Comments: [e.g. JSDoc for public functions; inline comments only for non-obvious logic]

---

## The Prime Directive: Minimum Viable Change

**This is the single most important rule.**

When making any edit:
1. Change ONLY what is necessary to achieve the stated goal
2. Do NOT refactor, rename, reorganise, or "improve" surrounding code
3. Do NOT remove existing functionality unless explicitly instructed
4. If you notice something unrelated that could be improved, note it in a comment — but do NOT change it

---

## Verification Protocol

Before executing any change, show your working in this format:

```
File: [filename]
Line(s): [line numbers]
Current code: [exact existing code]
Proposed change: [exact replacement code]
Reason: [one sentence explaining why]
```

Then **wait for confirmation** before proceeding.

---

## Locked Behaviours (Never Remove or Alter)
[List specific behaviours, functions, or patterns that must be preserved across all edits.]

- [Feature X] must always [do Y] — do not alter this logic
- [Function Z] signature must remain unchanged — it is consumed by external services
- Error handling pattern: always use [specific pattern]

---

## Explicitly Forbidden Actions

- Never install new packages without asking first
- Never delete files — move to `/archive` if removal is needed
- Never change environment variable names
- Never modify [specific config files] without explicit instruction

---

## Iteration Protocol

When a requested change produces an unexpected result:
1. **Stop** — do not attempt to fix by making additional changes
2. Explain what you changed and what the unexpected result is
3. Propose two alternative approaches before taking action
4. Wait for confirmation before proceeding

---

## Testing Requirements
[What Claude must do before considering a task complete.]

- Run existing test suite after changes: `[command]`
- Do not mark a task done if tests fail
- Confirm which tests you ran and their results

---

## How to Signal Completion

End every completed task with:
- Summary of exactly what changed (files + nature of change)
- What was deliberately NOT changed and why
- Any technical debt or follow-up items noticed (but not acted on)

---

## Priority Order When Constraints Conflict

Hard Stops → Non-Negotiable Behaviours → Prime Directive (Minimum Viable Change) → Coding Standards → Output Defaults
