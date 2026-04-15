# CLAUDE.md — Project Principles

These principles govern how Claude Code works in this project. They are listed in order of priority — when principles conflict, the lower-numbered principle takes precedence. Principles 1–5 are about behavior during work (what to do and how); principles 6–10 are about process and delivery (tests, commits, scope).

## 1. When in doubt, ask

If a request or situation is ambiguous, ask before acting. The cost of one clarifying question is always smaller than the cost of undoing work built on a wrong assumption.

## 2. Summarize project state at session start

At the beginning of every session, before doing any work, assess and report the current project state:

- Current branch and recent commits
- Uncommitted changes (staged and unstaged)
- The project's purpose and what was being worked on most recently

Wait for the user's confirmation before proceeding. This ensures both sides share the same mental model before any work begins.

> This will be automated by a SessionStart hook in Stage 6. Until then, perform this step manually.

## 3. Read before you write

Before modifying any file, read the relevant parts first. Use Grep (for content patterns) or Glob (for filename patterns) to locate what matters, rather than reading entire files. Read the full file only when the surrounding context truly matters.

## 4. Test-first development (TDD)

When adding new behavior, write a failing test before writing the implementation. The test defines "done" — it anchors the work to an observable expectation and prevents drift into plausible but unverified code.

When fixing a bug, start with a test that reproduces the bug. The fix is complete when the test passes.

For pure refactoring (no behavior change), verify that the area being touched is already covered by existing tests before you begin. If it is not, add coverage first.

## 5. Minimal, focused changes

Do exactly what was asked. Do not add features, refactor surrounding code, insert comments, or "improve" things beyond the scope of the request. A bug fix does not need a docstring rewrite. A new feature does not need speculative abstractions. Three similar lines are better than a premature helper function.

## 6. Self-review before declaring done

Before announcing that a task is finished, review against these categories:

- **Correctness** — Does the change do what was asked? Passing tests verify code correctness, not feature correctness — for user-facing behavior, manually verify or state that manual verification is needed.
- **Side effects** — Does it break anything else?
- **Consistency** — Does it follow the patterns already in the codebase?
- **Security** — Does it introduce any OWASP Top 10 vulnerabilities?
- **Refactoring** — Is there refactoring that is necessary right now, or that would be strategically valuable given the current direction of the project? Perform it if the cost of deferring clearly outweighs the cost of doing it now. This does not override principle 5 — it applies only within the scope of the current task, not to unrelated surrounding code.

When performing this self-review, invoke the `code-reviewer` subagent via the Task tool rather than reviewing inline. The subagent runs in isolated context and returns a structured report covering the five categories above. When invoking it, always provide the original user request (verbatim or briefly summarized) and a short description of what changed and why. For complex or ambiguous tasks, additionally provide your interpretation and plan, so the reviewer can verify that the implementation matches the user's intent.

## 7. Check git status before ending; confirm commit intent

Before ending a response, run `git status`. If there are uncommitted changes, do not commit silently. Instead:

1. List the changed files explicitly.
2. Propose a commit message in Conventional Commits format (see principle 8).
3. Wait for the user's explicit confirmation before committing.

The user decides what to commit and when. Do not make that decision on their behalf.

> Once Stage 4 is complete, a Stop hook will conditionally automate this step (gated on code review and test passage).

## 8. Conventional Commits

Commit messages follow the format `type: short subject` (imperative, lowercase). Common types:

- `feat:` — new capability
- `fix:` — bug fix
- `refactor:` — restructuring without changing behavior
- `docs:` — documentation only
- `chore:` — maintenance (dependencies, config, scaffolding)
- `test:` — adding or adjusting tests

## 9. Delegate test execution to the test-runner subagent

Do not run test commands (`./gradlew test`, `npm test`, `pytest`, etc.) directly in the main session. Error logs and build output pollute the context window and degrade reasoning quality for subsequent work.

The test-runner subagent (Stage 3) executes tests in an isolated context and returns a summarized result. The PostToolUse hook (Stage 5) will fully automate this.

<!-- TODO: remove this exception in Stage 3 -->
Until Stage 3 is complete, you may run tests directly in the main session.

## 10. Respect the existing stack

Before making changes, understand the existing conventions, naming patterns, and project structure. Use the libraries, frameworks, and architectural patterns already in the project. Do not introduce new dependencies or architectural patterns without asking first. When in doubt, follow the conventions you see in the surrounding code.
