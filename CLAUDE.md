# CLAUDE.md — Meta Guide for the dotclaude Repository

This file is the meta guide for working on the `dotclaude` repository itself. It is active when Claude Code is run inside this repository, and it governs how the repository is maintained.

It is **not** the user-facing template. The user-facing principles live in `templates/dev/.claude/CLAUDE.md` (added in stage 1), which is what gets symlinked into real projects.

## Purpose of this repository

`dotclaude` is a personal baseline of Claude Code settings. It is built up one stage at a time, and each stage is a small, self-contained addition. The repository is small on purpose — it holds principles and configuration, not application code.

## How this repository is built

The work proceeds in numbered stages:

- **Stage 0** — Repository scaffold and this meta guide.
- **Stage 1** — `templates/dev/.claude/CLAUDE.md`: the user-facing principles.
- **Stage 2** — `code-reviewer` subagent.
- **Stage 3** — `test-runner` subagent.
- **Stage 4** — Stop hook (invokes `code-reviewer`, conditional exit loop).
- **Stage 5** — PostToolUse hook (invokes `test-runner` after each modification).
- **Stage 6** — SessionStart hook.
- **Stage 7** — Slash commands.

Stages are completed in order. Each completed stage is tagged in git as `stage-0`, `stage-1`, and so on, so any historical state can be checked out later.

## Principles

The principles below govern work on this repository. They are listed in order of priority — **when principles conflict, the lower-numbered principle takes precedence.**

### 1. When in doubt, ask

If a request or a situation is ambiguous, ask before acting. The cost of one clarifying question is always smaller than the cost of undoing work built on a wrong assumption.

### 2. Self-review before declaring done

Before announcing that a task is finished, review the work against four categories: missing requirements, refactoring opportunities, side effects, and consistency with what already exists. This applies to documentation and scripts, not just code. A self-judgment is not perfectly reliable, but an explicit checklist catches most regressions.

### 3. Check git status before ending; confirm commit intent

Before ending a response, run `git status`. If there are uncommitted changes, do not commit silently. Surface the changes to the user, propose a commit message in Conventional Commits format, and wait for explicit confirmation. The user decides when something is ready to commit.

### 4. Preserve context with Grep and Glob first

Before reading a whole file, use Grep (for content patterns) or Glob (for filename patterns) to locate the parts that actually matter. Reading entire files when only a few lines are relevant wastes the context window and degrades reasoning quality. Read the full file only when the surrounding context truly matters.

### 5. Conventional Commits

Commit messages follow the Conventional Commits format: `type: short subject`. The types used in this repository are:

- `feat:` — a new capability (a new principle, a new subagent, a new hook)
- `fix:` — a correction to existing behavior or wording
- `refactor:` — restructuring without changing meaning
- `docs:` — README, comments, or documentation-only changes
- `chore:` — repository maintenance (gitignore, license, scaffolding)
- `test:` — adding or adjusting tests (rare in this repository)

Subjects are short, imperative, and lowercase. Example: `feat: add code-reviewer subagent`.

### 6. Stage discipline

Work on one stage at a time. Do not skip stages. Do not start a new stage until the current one is committed and tagged. If a change does not belong to the current stage, note it for a future stage instead of folding it in.

### 7. Tag on stage completion

When a stage's work is finished and committed, tag it:

```bash
git tag -a stage-N -m "Stage N: short description"
git push origin stage-N
```

Tagging is part of completing a stage, not an optional extra.

### 8. Update README's Current Stage indicator

The `Current stage:` line in `README.md` is the single source of truth for "where this repository is right now." When a new stage is completed, update that line in the same commit (or in an adjacent commit) so that the README never lies about the current state.

## Notes on path conventions

When future stages add hooks or scripts that reference paths inside `.claude/`, use **relative paths** rooted at the project's `.claude/` directory (for example, `.claude/hooks/review-gate.sh`). Claude Code runs hook commands from the project root, so these paths resolve correctly. Avoid absolute paths and machine-specific values — they break the moment this repository is cloned to a different machine.

## How to start a new session on this repository

When opening a fresh Claude Code session in this repository:

1. Read this file (`/CLAUDE.md`) and `README.md`.
2. Check the `Current stage:` line in `README.md` to see where work last stopped.
3. Run `git status` and `git log --oneline -10` to see the recent history.
4. State out loud what stage is current and what the next stage should be, before beginning any work.

This routine takes thirty seconds and prevents the most common failure mode: starting work without knowing what state the repository is in.