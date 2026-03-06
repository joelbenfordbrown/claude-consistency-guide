# Opening Message Template — Claude Code Sessions
# Use at the start of every significant Claude Code session.
# Takes 30 seconds. Prevents hours of regression cleanup.

---

## Standard Session Opener

```
@PROJECT_CONTEXT.md

Today's task: [specific, bounded goal — one thing only]

Constraints for this session:
- Minimum changes only — do not touch anything I haven't asked you to touch
- Do not modify [X, Y, Z — list specific files or areas]
- Preserve all existing [behaviours / tests / patterns]
- Check with me before installing any new packages
- Check with me before deleting any files

When done, provide:
1. Summary of exactly what changed (files + nature of change)
2. What was deliberately NOT changed and why
3. Any technical debt or follow-up items noticed (but not acted on)
```

---

## For High-Stakes Sessions — Add This

```
Before making any change, show me:
- The file and line numbers affected
- The exact current code
- The exact proposed replacement
- One sentence explaining why

Wait for my confirmation before proceeding.
```

---

## Re-Anchor Message
# Use when you notice drift mid-session

```
Reminder: apply all rules from CLAUDE.md.
Specifically:
- Minimum viable change only
- Do not modify [X]
- [Restate any rule that has been violated]
```

---

## Debug Message
# Use when behaviour is unexpected

```
Before we continue — what rules from CLAUDE.md are you currently following?
What custom instructions are active?
```

---

## Oscillation Fix
# Use when Claude achieves A then loses it while adding B

```
This version is correct: [paste the last known-good version of the function/section]
Add only [new behaviour] to this. Do not alter anything else.
```
