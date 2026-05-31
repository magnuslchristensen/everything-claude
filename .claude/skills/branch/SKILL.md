---
name: branch
description: Create a new branch following the project naming convention (<type>/<description>). Use when starting new work, a ticket, or a feature.
---

# New Branch

Create a well-named branch from the project base, ready for new work.

## When to Use

- "New branch for X"
- "Start working on Y"
- "Create a branch"
- Before beginning any new feature, fix, or task

## Process

1. Run these in parallel:
   - `git status` — confirm the working tree is clean; warn the user if it is not
   - `git branch -a` — list existing branches to avoid name collisions
   - `git log --oneline -5` — understand current position

2. Determine the base branch:
   - Use `develop` if it exists locally or on remote, otherwise use `main`
   - For `hotfix/*` branches, always use `main` regardless

3. Switch to base and pull latest:
   ```bash
   git checkout <base> && git pull
   ```

4. Create and switch to the new branch:
   ```bash
   git checkout -b <type>/<description>
   ```

5. Confirm creation and report the full branch name to the user.

## Naming Convention

Format: `<type>/<description>`

- All lowercase, kebab-case description
- 2–5 words, no filler words ("the", "a", "for")
- If a Linear / Jira ticket ID is available, prefix description with it:
  `<type>/ENG-123-short-description`

| Type | When to use |
|------|-------------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `refactor` | Code restructuring, no behavior change |
| `chore` | Tooling, dependencies, configuration |
| `docs` | Documentation only |
| `test` | Tests only |
| `hotfix` | Urgent production fix — always branches from `main` |

### Examples

```
feat/user-authentication
feat/ENG-42-oauth-login
fix/null-pointer-on-startup
fix/ENG-99-crash-checkout
refactor/extract-payment-service
hotfix/critical-data-loss
chore/upgrade-dependencies
```

## Rules

- Never branch from another feature branch without explicitly asking the user
- Do not push the branch to remote unless the user asks
- If the working tree is dirty, ask the user whether to stash, commit, or abort
- Never reuse an existing branch name — suggest an alternative if there is a collision
