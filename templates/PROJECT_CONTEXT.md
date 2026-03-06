# PROJECT_CONTEXT.md
# [Project Name] — Domain Knowledge & Context
# Last Updated: [Date]

> This file contains domain knowledge, business logic, and contextual information.
> It is loaded on-demand using @PROJECT_CONTEXT.md — not auto-loaded like CLAUDE.md.
> Keep CLAUDE.md for behavioural rules; keep this file for knowledge.
>
> Ensuring Claude actually reads it: Mentioning the file alone is not sufficient.
> Use the @ file reference syntax and include an explicit instruction at the top of CLAUDE.md.
> Also invoke it directly in your opening message:
> @PROJECT_CONTEXT.md — please load this before we begin.

---

## What This Project Is

[1–3 sentences on the project's purpose, the problem it solves, and who uses it.]

---

## Users & Personas

[Who uses this product? What do they care about? What do they struggle with?]

**Primary User:** [Name/role] — [goals, frustrations, technical level]
**Secondary User:** [Name/role] — [goals, frustrations, technical level]

---

## Business Logic & Domain Rules

[The rules that govern how this system behaves — not implementation details, but the "why".]

- [Rule 1]: [explanation]
- [Rule 2]: [explanation]
- [Rule 3]: [explanation]

---

## Key Decisions & Rationale

[Architectural or product decisions already made — and why. Prevents Claude from re-litigating settled questions.]

| Decision | Why |
|---|---|
| [Decision 1] | [Rationale] |
| [Decision 2] | [Rationale] |

---

## Glossary

[Project-specific terms, acronyms, or domain vocabulary Claude needs to understand.]

| Term | Definition |
|---|---|
| [Term] | [Definition] |

---

## Known Constraints & Limitations

[Technical debt, known issues, or constraints Claude should be aware of before suggesting changes.]

- [Constraint 1]
- [Constraint 2]

---

## Out of Scope

[Things explicitly NOT part of this project that Claude might otherwise suggest.]

- [Item 1]
- [Item 2]
