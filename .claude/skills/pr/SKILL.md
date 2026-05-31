---
name: pr
description: Create a pull request with a structured, reviewable description. Summarises commits, drafts the body, and opens the PR via the gh CLI. Use when the user is ready to open a PR or says "create a PR", "open a pull request", or "submit for review".
---

# Create Pull Request

Open a pull request from the current branch with a comprehensive, structured description that gives reviewers everything they need.

## When to Use

- "Create a PR"
- "Open a pull request"
- "Submit this for review"
- "Push and PR"

## Process

1. Run these in parallel:
   - `git status` — confirm the working tree is clean
   - `git log <base>..HEAD --oneline` — list all commits on this branch
   - `git diff <base>..HEAD --stat` — summarise files and lines changed
   - `git branch -r | grep HEAD` or `git rev-parse --abbrev-ref --symbolic-full-name @{u} 2>/dev/null` — check if branch is pushed

2. Determine the base branch: `develop` if it exists on remote, otherwise `main`.

3. If the branch is not pushed to remote:
   ```bash
   git push -u origin <branch>
   ```

4. Check for an existing PR to avoid duplicates:
   ```bash
   gh pr view 2>/dev/null
   ```
   If one exists, report its URL and stop.

5. Analyse all commits and the diff stat to write the PR description.

6. Create the PR:
   ```bash
   gh pr create --title "<title>" --base <base> --body "$(cat <<'EOF'
   ...
   EOF
   )"
   ```

7. Report the PR URL to the user.

## PR Title

Follow the same Conventional Commits format used for commits:

```
<type>(<scope>): <short summary>
```

- Imperative mood, no period, ≤72 characters
- Scope is the primary area affected (omit if truly cross-cutting)

## PR Body Format

```markdown
## Summary

- <what changed and why — 1–3 bullets>

## Changes

- <key technical change — file or component level>
- <key technical change>

## Test plan

- [ ] <specific thing to verify manually>
- [ ] <edge case to check>
- [ ] <regression to confirm still works>

## Notes

<optional: migration steps, deployment considerations, follow-up tickets, known limitations, screenshots>
```

## Rules

- If `gh` is not authenticated, tell the user to run `gh auth login` and stop
- If there are no commits ahead of the base branch, abort with a clear message
- Do not merge the PR — only create it
- Do not include "Co-Authored-By" in the PR description (that belongs in commits)
- If the branch has a Linear/Jira ticket ID in its name (e.g. `feat/ENG-42-...`), reference the ticket number in the Summary
- Target `develop` if it exists on remote, otherwise `main` — never assume
