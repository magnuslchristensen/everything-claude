# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A collection of Claude Code skills, subagents, and tooling conventions — used as a testing ground and reference implementation for workflow automation.

## Project Skills

These skills are available in this project (invoke with `/skill-name`):

| Skill | Purpose |
|-------|---------|
| `/branch` | Create a new branch following `<type>/<description>` naming |
| `/pr` | Open a pull request with a structured description via `gh` |
| `/code-review` | Fan out three parallel subagents and synthesise a prioritised review report |
| `/commit` | Stage and commit with a Conventional Commits message (user-level skill) |

## Subagents

Three specialist agents live in `.claude/agents/` and are invoked in parallel by `/code-review`:

| Agent | Focus |
|-------|-------|
| `correctness-reviewer` | Bugs, logic errors, edge cases, missing error handling |
| `standards-reviewer` | Naming, structure, dead code, project conventions |
| `security-reviewer` | OWASP Top 10, injection, auth, secrets, cryptography |

Each agent can also be invoked directly via the Agent tool when a focused single-dimension review is needed.

## Conventions

### Commit messages

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <short summary>
```

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `perf`, `style`

### Branch naming

```
<type>/<short-description>
<type>/TICKET-123-short-description
```

`hotfix/*` always branches from `main`. All other types default to `develop` if it exists, otherwise `main`.

### Pull requests

PR titles follow the same Conventional Commits format. PR bodies use the structured template in `.claude/skills/pr/SKILL.md`.

## Project Principles

- Skills and agents are the primary deliverables — keep them focused and single-purpose.
- Skill files are instructions for Claude, not documentation for humans. Write them as precise operational procedures.
- Agent files define subagent personas with clear scope boundaries so they don't overlap or second-guess each other.
