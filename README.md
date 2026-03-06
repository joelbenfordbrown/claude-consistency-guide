# Enforcing Consistent Claude Behaviour Across Claude Chat & Claude Code

🤖 **A Practical Field Guide for Getting Reliable, Repeatable Results across Claude Chat and Claude Code.**

*A personal distillation of countless iterations — not a definitive authority, simply compiled and shared in the hope it helps others.*

---

## The Core Problem

Claude doesn't have persistent memory by default. Every session is a blank slate unless you deliberately inject your requirements. This is the root cause of the "oscillating behaviours," instruction drift, and feature regression that frustrate so many users. The solution isn't hoping Claude remembers — it's engineering your context so it never has to.

---

## Contents

- [Part 1: Claude Chat](#part-1-claude-chat-claudeai)
- [Part 2: Claude Code](#part-2-claude-code-the-cli-agentic-tool)
- [Part 3: Making Both Work Together](#part-3-making-both-work-together)
- [Part 4: Diagnosing & Fixing Oscillation](#part-4-diagnosing--fixing-oscillation)
- [Quick Reference Checklist](#quick-reference-checklist)
- [Templates](#templates)

---

## Part 1: Claude Chat (claude.ai)

### How It Works

Claude Chat runs in a conversational interface. Your "persistent" lever here is **Custom Instructions** (also called a System Prompt), which is prepended to every conversation automatically.

### Where to Configure It

> **Settings → Custom Instructions** (or Settings → Profile, depending on your plan tier)

> ⚠️ **Custom Instructions apply across ALL Claude sessions — including Claude Code.** This is powerful but carries risk: a rule you write for one context will affect every other. Keep Custom Instructions truly universal (tone, character, hard stops). Put anything project-specific in `CLAUDE.md` instead.

### What to Put in Your System Prompt

Think of your system prompt as a **contract**, not a preference list. Write it with this structure:

```
## Identity & Role
[Who Claude is in this context. Be specific. e.g.,
"You are a senior technical writer assisting a solo founder building a SaaS product.
You understand both technical and non-technical audiences."]

## Non-Negotiable Behaviours  ← PUT THIS FIRST OR LAST, NEVER IN THE MIDDLE
[Hard rules that must NEVER be broken, regardless of what the user asks mid-conversation.]
- Always do X
- Never do Y
- When Z occurs, respond with W

## Tone & Character
[How Claude should sound. Be precise — "professional but warm" is vague;
"direct, dry wit, no corporate filler, concise answers unless depth is requested" is actionable.]

## Formatting Rules
- Use headers only when response exceeds 3 sections
- No bullet points for conversational replies
- Code always in fenced blocks with language specified
- Never bold random phrases for emphasis

## Output Defaults
- Default response length: concise
- Default format: prose, not lists
- Always ask one clarifying question before tackling ambiguous briefs

## Hard Stops  ← PUT THIS FIRST OR LAST, NEVER IN THE MIDDLE
[Things Claude must refuse or redirect, specific to your use case.]
```

### Priority Order When Constraints Conflict

> **Hard Stops → Non-Negotiable Behaviours → Formatting Rules → Output Defaults → Tone & Character**

### Critical System Prompt Writing Rules

- **Be declarative, not aspirational.** "You are concise" works better than "Please try to be concise."
- **Use explicit negative constraints.** Don't just say what you want — say what you *don't* want. Claude's training optimises for helpfulness and elaboration. If you don't explicitly forbid something, it may add it thinking it's being helpful — hence the need for negative constraints.
- **Anchor your character with examples if needed.** A short example of a good vs. bad response is worth more than a paragraph of description.
- **Keep it under ~800 words.** Longer system prompts dilute in long conversations. Prioritise ruthlessly.
- **Place your most critical rules at the very beginning or very end.** Claude's context window compresses from the middle in long conversations — the middle of your system prompt is the most likely to be deprioritised. Hard stops and non-negotiable behaviours belong at the top or bottom, never buried in the middle.

### Managing Long Conversations

Even with a solid system prompt, Claude's adherence degrades over very long conversations as earlier context gets compressed. Mitigations:

- Start a new conversation for new topics rather than endlessly extending one thread.
- Use a **"re-anchor" message** when you notice drift: *"Reminder: apply all rules from our working agreement. Specifically: [rule]."*
- Pin critical constraints to your most recent message when asking for complex iterative work: *"As always: minimum changes only, do not touch [X], preserve [Y]."*

---

## Part 2: Claude Code (the CLI Agentic Tool)

### How It Works Differently

Claude Code operates as an autonomous agent with access to your file system, terminal, and codebase. It reads files, writes code, runs commands, and iterates. The conversational System Prompt approach from claude.ai applies here too, but Claude Code adds a project-level persistent context mechanism: the **`CLAUDE.md` file**.

### The CLAUDE.md File

Place a file named `CLAUDE.md` in the root of your project repository. Claude Code reads this automatically at the start of every session. It's your **persistent project contract** — the equivalent of your System Prompt, but scoped to a specific codebase and version-controlled alongside your work.

```
Location: /your-project-root/CLAUDE.md
```

> ⚠️ **CRITICAL: CLAUDE.md Must Be in Your Current Working Directory**
>
> Claude Code looks for `CLAUDE.md` only in the directory where you launch it. If you're in `project/src/utils/` and run `claude`, it won't see the `CLAUDE.md` in `project/root/`.
>
> Always launch from your project root:
> - Navigate to root first: `cd /project/root && claude`
> - Or use an alias/script that ensures you're always in the right place
>
> Every session starts fresh — no memory across terminal sessions, no searching parent directories.

> ⚠️ **CLAUDE.md is read at session start only.** If you edit `CLAUDE.md` while a Claude Code session is running, Claude will not see those changes until you end the session and restart. Always restart after updating your project contract.

### CLAUDE.md vs @filename — Key Distinction

| Mechanism | When it loads | How to refresh |
|---|---|---|
| `CLAUDE.md` auto-load | Once at session initialisation | Restart session |
| `@filename` on-demand | Whenever invoked mid-session | Re-invoke `@PROJECT_CONTEXT.md` |

**Practical implication:** If you need to update domain knowledge during a session, edit `PROJECT_CONTEXT.md` and re-invoke it with `@PROJECT_CONTEXT.md`. If you need to change behavioural rules in `CLAUDE.md`, you must restart the session.

### Recommended CLAUDE.md Structure

See [`templates/CLAUDE.md`](templates/CLAUDE.md) for the full populated template.

| Section | Purpose |
|---|---|
| **Reference** | Instruction to load `@PROJECT_CONTEXT.md` |
| **Project Overview** | 2–3 sentences: what, stack, purpose |
| **Architecture Constraints** | Non-negotiable structural rules |
| **Coding Standards** | Language, formatting, naming, comments |
| **The Prime Directive** | Minimum Viable Change — the most important rule |
| **Verification Protocol** | Show-your-working format before making changes |
| **Locked Behaviours** | Specific functions/patterns that must be preserved |
| **Explicitly Forbidden Actions** | What Claude must never do |
| **Iteration Protocol** | What to do when something unexpected happens |
| **Testing Requirements** | What must pass before a task is complete |
| **How to Signal Completion** | Standard completion summary format |

### The Single Most Important Claude Code Rule

> **Make only the minimum change necessary. Do not touch code you were not asked to touch. If you notice something to improve, mention it — don't change it.**

This single instruction eliminates ~80% of the regression and oscillation problems most users encounter.

### The Project Compass: Adding PROJECT_CONTEXT.md

For anything beyond a small project, split your context across two files to prevent `CLAUDE.md` from becoming bloated:

| File | Purpose | Contains |
|---|---|---|
| `CLAUDE.md` | Behavioural contract | How Claude should act: rules, standards, forbidden actions, protocols |
| `PROJECT_CONTEXT.md` | Domain knowledge | Business logic, user personas, domain rules, glossary, key decisions |

See [`templates/PROJECT_CONTEXT.md`](templates/PROJECT_CONTEXT.md) for the full populated template.

Ensuring Claude actually reads it — include this at the top of your `CLAUDE.md`:

```
## Reference
Always load @PROJECT_CONTEXT.md at the start of each session.
When asked about any domain-specific topic, consult PROJECT_CONTEXT.md before responding.
```

And invoke it in your opening message:
```
@PROJECT_CONTEXT.md — please load this before we begin.
```

> **Reusing successful configurations:** Once you've refined a CLAUDE.md that works well for a given project type (Node API, React frontend, etc.), save a copy as a template. When starting a new project of the same type, copy and adapt it rather than starting from scratch.

### Priority Order When Constraints Conflict

> **Hard Stops → Non-Negotiable Behaviours → Prime Directive (Minimum Viable Change) → Coding Standards → Output Defaults**

### Going Further: Additional Persistence Mechanisms

- **`.claude/commands/`** — Store custom slash commands that persist across sessions. Useful for frequently repeated workflows (e.g. `/run-tests`, `/deploy-staging`).
- **`.claude/settings.json`** — Global settings including preferred shell and editor configuration.
- **`claude init`** — Run this in a new project to generate a `CLAUDE.md` template scaffolded to your codebase automatically.

Start with `CLAUDE.md` and `PROJECT_CONTEXT.md`. Explore the `.claude/` directory once your core workflow is stable.

---

## Part 3: Making Both Work Together

### The Layered Context Model

Think of it as three layers, each reinforcing the last:

| Layer | Tool | Scope | Persistence |
|---|---|---|---|
| **Identity & Character** | System Prompt (Custom Instructions) | ALL sessions — including Claude Code | Permanent until changed |
| **Project Contract** | `CLAUDE.md` + `PROJECT_CONTEXT.md` | Per-repository | Version-controlled |
| **Session Anchors** | Opening message each session | Per-session | That session only |

For maximum consistency, populate all three layers. Because Custom Instructions are global across all projects, keep them strictly universal. All project-specific rules belong in `CLAUDE.md`.

### When System Prompt and CLAUDE.md Conflict

More specific context overrides broader context. If your System Prompt says "always use TypeScript" but `CLAUDE.md` specifies JavaScript for a legacy module, `CLAUDE.md` wins because it is project-specific. Use your System Prompt for universal character and behaviour; `CLAUDE.md` for all project-specific overrides.

> **A note on the `--system` flag in Claude Code CLI:** This flag is well-suited to session-specific overrides. It does not persist across sessions like the web UI's Custom Instructions, so do not rely on it for consistent behavioural rules — use `CLAUDE.md` for those instead.

### Context Window Awareness

Keep your combined **System Prompt + CLAUDE.md + PROJECT_CONTEXT.md under ~2,000 words total.**

| What goes where |
|---|
| **System Prompt:** Identity, tone, universal formatting rules, hard stops that apply everywhere |
| **CLAUDE.md:** Project-specific rules, architecture constraints, coding standards, locked behaviours |
| **Opening message:** Task-specific constraints, today's goals, anything that changes per session |

If persistent context creeps past 2,000 words, refactor — move task-specific detail into session openers rather than expanding the persistent files.

> **Multi-project note:** Each project should have its own `CLAUDE.md` in its own root. Tools like `direnv` can automate ensuring you're always in the correct working directory before launching Claude Code, eliminating the risk of loading the wrong project's rules.

### Your Opening Message Template

```
@PROJECT_CONTEXT.md

Today's task: [specific, bounded goal]

Constraints for this session:
- Minimum changes only
- Do not modify [X, Y, Z]
- Preserve all existing [behaviours/tests/patterns]
- Check with me before installing anything or deleting files

When done, list every file changed and what changed in each.
```

This takes 30 seconds and prevents hours of regression cleanup.

### Debugging What Claude Is Actually Using

When a session behaves unexpectedly, verify what context Claude has loaded before assuming a rule is being ignored:

```
"What rules from CLAUDE.md are you following?"
"What custom instructions are currently active?"
```

Claude will surface what it has loaded, confirming whether your configuration is actually being applied or whether a context issue is the root cause.

---

## Part 4: Diagnosing & Fixing Oscillation

The back-and-forth oscillation — where Claude achieves A, then loses it while adding B — has a specific cause and cure:

**Cause:** Claude is regenerating code from its understanding of intent rather than making surgical edits. Each regeneration reinterprets your requirements slightly differently.

**Cure:**
1. Never ask Claude to "redo" or "rewrite" — always ask it to *"add X to the existing code without changing anything else."*
2. If oscillation starts, paste the last good version of the specific function or section into your message and say: *"This version is correct. Add only [new behaviour] to this. Do not alter anything else."*
3. Use version control commits as checkpoints — commit working states so you can always return to them, removing the pressure of trying to recover lost behaviours from conversation alone.

### Common Failure Modes

| Symptom | Likely Cause | Fix |
|---|---|---|
| Claude keeps adding features not requested | Missing "minimum change" directive | Add Prime Directive to `CLAUDE.md` + re-anchor in session opener |
| Tone or character drifts mid-conversation | System prompt too vague | Add explicit negative constraints + example of desired tone |
| Claude Code refactors unrelated files | No locked behaviours defined | List protected files and functions in `CLAUDE.md` |
| Responses get longer and more elaborate over time | No output length constraint | Add explicit length default to System Prompt |
| Claude contradicts an earlier decision | Context window compression in long sessions | Start fresh session; re-state key constraints in opener |
| Rules followed early but ignored later | System prompt dilution in long context | Re-anchor mid-session; move critical rules to `CLAUDE.md` |
| Domain knowledge gaps / wrong decisions | `PROJECT_CONTEXT.md` not being loaded | Use `@PROJECT_CONTEXT.md` explicitly in session opener |
| `CLAUDE.md` changes not being respected | File edited mid-session | Restart session — `CLAUDE.md` is read at start only |
| Unexpected behaviour across projects | Global Custom Instructions bleeding in | Move project rules to `CLAUDE.md`; keep Custom Instructions universal |

---

## Quick Reference Checklist

### Before Starting a Claude Chat Session

- [ ] System prompt is configured in Settings
- [ ] System prompt includes explicit negative constraints (what NOT to do)
- [ ] Tone and formatting rules are specific, not vague
- [ ] Most critical rules are at the top or bottom — not the middle
- [ ] System prompt + any persistent context is under ~2,000 words total
- [ ] Custom Instructions contain only truly universal rules

### Before Starting a Claude Code Session

- [ ] Launching from project root directory
- [ ] `CLAUDE.md` exists in project root
- [ ] `CLAUDE.md` includes the Minimum Viable Change directive
- [ ] `CLAUDE.md` includes the Verification Protocol
- [ ] `CLAUDE.md` lists locked behaviours that must be preserved
- [ ] `PROJECT_CONTEXT.md` created if domain knowledge is needed
- [ ] Opening message includes `@PROJECT_CONTEXT.md` reference
- [ ] Opening message states today's specific, bounded goal
- [ ] Recent working state is committed to version control

### When Things Go Wrong

- [ ] Don't ask Claude to "try again" — explain specifically what regressed
- [ ] Paste the last known-good version of affected code and anchor from there
- [ ] Ask *"What rules from CLAUDE.md are you following?"* to verify context is loaded
- [ ] If `CLAUDE.md` was edited mid-session, restart — changes won't apply until you do
- [ ] For character/tone drift in Chat, re-paste your key system prompt rules in the message
- [ ] For persistent issues, start a fresh session with a refined system prompt
- [ ] Consult the Common Failure Modes table above

---

## Templates

Ready-to-use templates are in the [`/templates`](templates/) directory:

| File | Purpose |
|---|---|
| [`templates/CLAUDE.md`](templates/CLAUDE.md) | Full behavioural contract — drop into any project root |
| [`templates/PROJECT_CONTEXT.md`](templates/PROJECT_CONTEXT.md) | Domain knowledge companion file |
| [`templates/system-prompt.md`](templates/system-prompt.md) | Claude Chat Custom Instructions starter |
| [`templates/opening-message.md`](templates/opening-message.md) | Session opener, re-anchor, and debug templates |

---

## Related Work

If you found this guide useful, you may also be interested in the **Minimum Viable Prompt (MVP)** — a single prompt that auto-pilots your AI chat session setup in under 2 minutes, for any AI platform, no technical knowledge required.

[github.com/joelbenfordbrown/MVP](https://github.com/joelbenfordbrown/MVP)

---

## Author

**Joel Benford-Brown** is a cognitive architect and applied psychologist, and founder of the GHOST AI Framework — a theoretical framework for artificial personality, synthetic sentience and ethical agency. He works at the nexus of psychology and technology. [ghostai.fr](https://ghostai.fr)

LinkedIn: [linkedin.com/in/joelbb](https://www.linkedin.com/in/joelbb/)

---

*Free to use and share with attribution — please link back to the original repo: https://github.com/joelbenfordbrown/claude-consistency-guide*
