---
name: code-review
description: Review staged/unstaged changes or specified files across three dimensions — correctness, standards, and security — using parallel subagents. Synthesises findings into a prioritised report. Use when the user asks to review code, check for issues, or validate changes before committing or opening a PR.
---

# Code Review

Fan out three specialised review agents in parallel, then synthesise their findings into a single prioritised report.

## When to Use

- "Review my changes"
- "Check this before I commit"
- "Review these files: X, Y"
- Proactively before `/pr` when changes are significant

## Process

### 1. Determine scope

If the user specified files or a path, use those. Otherwise default to unstaged + staged changes:

```bash
git diff HEAD          # unstaged
git diff --cached      # staged
```

If the diff is empty, check the most recent commit:
```bash
git diff HEAD~1..HEAD
```

Report what scope you are reviewing before proceeding.

### 2. Fan out three parallel subagents

Spawn all three simultaneously using the Agent tool. Pass each agent:
- The full diff or file contents
- The language/framework detected from the files
- Any relevant context from CLAUDE.md

| Agent | Focus |
|-------|-------|
| `correctness-reviewer` | Bugs, logic errors, edge cases, missing error handling |
| `standards-reviewer` | Naming, structure, patterns, code style, dead code |
| `security-reviewer` | Injection, auth, secrets, data exposure, OWASP Top 10 |

### 3. Synthesise findings

Collect results from all three agents and produce a single report:

```
## Code Review: <scope description>

### Critical  (fix before merging)
- [BUG] file.py:42 — <issue> (correctness-reviewer)
- [SEC] auth.py:17 — <issue> (security-reviewer)

### Important  (should fix)
- [STYLE] utils.py:88 — <issue> (standards-reviewer)

### Minor  (consider fixing)
- [STYLE] main.py:5 — <issue> (standards-reviewer)

### Passed
- No security issues found beyond those listed above
- Logic and error handling look correct in X, Y, Z
```

Only include a section if it has findings. If all three agents return clean, say so explicitly.

## Rules

- Always report the review scope at the top (which files, which diff)
- Never suppress findings — report everything with confidence ≥ 80
- If CLAUDE.md defines project-specific standards, pass them to the standards-reviewer
- Do not auto-fix findings — report only, unless the user explicitly asks for fixes
- If a finding appears in multiple agents (e.g. a bug that is also a security risk), report it once under the higher severity
