# Claude Code – 35 Useful Commands, Tips & Workflows

I’ve been using Claude Code every day for months. These are the techniques that actually make a big difference.

Most developers only use basic features. That’s maybe 20% of what Claude can do.
The remaining 80% is hidden in docs or learned through experience.

This guide collects everything that truly improves productivity.

---

# 🔹 Essential Commands (1–8)

## 1. Plan Mode (Shift + Tab)

Before writing code, use plan mode.
Claude will analyze your codebase and suggest a plan first.
This helps avoid mistakes early.

## 2. Compact (/compact)

After long chats, things get messy.
Use `/compact` to summarize everything and keep focus.

## 3. Clear (/clear)

Starting something new? Use `/clear`.
Mixing contexts leads to confusion.

## 4. Init (/init)

Run this at project start.
It creates a `CLAUDE.md` file with your project structure and rules.

## 5. Cost Check (/cost)

Shows how many tokens you’ve used.
Good for tracking usage and avoiding surprises.

## 6. Memory (/memory)

Add rules Claude will remember forever.
Example:

* Always use TypeScript strict mode
* Always add comments

## 7. Terminal Commands (!)

Use `!` to run terminal commands directly.
Example:

```
!npm test
```

## 8. Multi-Model Use

* Use **Opus** → for planning (smart but costly)
* Use **Sonnet** → for coding (fast and cheaper)

---

# 🔹 Productivity Techniques (9–18)

## 9. Reference File Trick

Instead of explaining style, show a file:

> “Follow the same pattern as this file”

More accurate results.

## 10. Screenshot Debugging

UI issue? Just paste a screenshot.
Faster than explaining in text.

## 11. Test-First Approach

Write tests first, then code.
This ensures correct implementation.

## 12. Build Step-by-Step

Don’t build everything at once.

Better approach:

* Create DB
* Test
* Build API
* Test
* Add UI

## 13. Understand Before Coding

Ask:

> “Explain this module before I modify it”

Prevents bad design decisions.

## 14. Diff Review

After changes, ask:

> “Show me what changed”

Helps catch mistakes.

## 15. Paste Full Errors

Always share complete error logs.
Not summaries.

## 16. Git Checkpoints

Before big changes:

```
git commit -m "checkpoint"
```

Easy rollback if something breaks.

## 17. Parallel Sessions

Use separate sessions:

* One for backend
* One for frontend

Keeps context clean.

## 18. Auto Documentation

After building:

> “Generate documentation”

More accurate than writing later.

---

# 🔹 Architecture Techniques (19–26)

## 19. Architecture Planning

Ask Claude:

* Give 2 design options
* Compare pros/cons
* Recommend one

## 20. Dependency Check

Before adding a library:

* Is it maintained?
* Is it secure?
* Is it heavy?

## 21. Pattern Rules

Define coding patterns in `CLAUDE.md`.
Claude will follow them automatically.

## 22. Migration Planning

For DB changes:

* Ask Claude to list all affected files first
* Then apply changes

## 23. API Review

Check:

* Naming consistency
* Missing validation
* Security

## 24. Security Scan

Ask Claude to check:

* SQL injection
* XSS
* Missing validation

## 25. Performance Check

Find:

* Slow queries
* Large bundles
* Unnecessary re-renders

## 26. Refactoring Plan

Before refactoring:

> “Show plan only”

Never start blindly.

---

# 🔹 Workflow Automation (27–31)

## 27. Git Hooks

Auto-check before commit:

* Lint
* Types
* Console logs

## 28. CI Pipeline

Automate:

* Tests
* Build
* Lint
* PR comments

## 29. Setup Script

Create script for new developers:

* Install dependencies
* Setup DB
* Run tests

## 30. Release Notes

Generate changelog from git history.

## 31. Database Seed

Create realistic test data:

* Users
* Projects
* Edge cases

---

# 🔹 Debug & Recovery (32–35)

## 32. Reproduce Bugs

* Write steps
* Create failing test
* Fix code

## 33. Git Blame Analysis

Find which commit broke things.

## 34. Dependency Fix

Analyze version conflicts and suggest minimal fix.

## 35. Recovery Mode

If stuck:

* Go back to working version
* Start fresh

---

# 🔹 Recommended Setup Workflow

When starting a project:

1. `/init`
2. Update `CLAUDE.md`
3. `/memory` for rules
4. Plan architecture
5. Build step-by-step

---

# 🔹 Summary

These 35 techniques cover:

* Commands → better control
* Productivity → faster development
* Architecture → better design
* Automation → consistent quality
* Debugging → faster recovery

Claude Code is powerful — but only if used correctly.

---

Save this. You’ll use it often.

