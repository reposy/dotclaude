# dotclaude

Personal Claude Code defaults I reuse in every new project.

This is my own setup. I share it because it works for me — feel free to adjust it to fit your own.

This repository collects the Claude Code configuration I want to reuse across every project I work on: guiding principles, subagents, hooks, and slash commands. Instead of copying files into each project, I clone this once and symlink `templates/dev/.claude` into each project's root. A single `git pull` updates every project that links to it.

**Current stage: `stage-2` — `code-reviewer` subagent**

## Installation

Clone the repository somewhere stable:

```bash
git clone https://github.com/reposy/dotclaude.git ~/dev/dotclaude
```

For each project where you want these settings active, symlink the template into the project root:

```bash
cd ~/path/to/your-project
ln -s ~/dev/dotclaude/templates/dev/.claude .claude
```

Claude Code will pick up `.claude/CLAUDE.md` and `.claude/settings.json` automatically when run from the project root.

> If you forked this repository, replace the clone URL with your fork's URL.

## Updating

Pull the repository once, and every project that symlinks into it is updated at the same time:

```bash
cd ~/dev/dotclaude
git pull
```

No per-project update step is needed — that is the whole point of the symlink approach.

## Roadmap

The repository is built up one stage at a time. Each stage is tagged (`stage-0`, `stage-1`, ...) so you can check out any historical state.

- **Stage 0** — Repository scaffold, license, and meta guide (`/CLAUDE.md`) for working on this repository itself.
- **Stage 1** — `templates/dev/.claude/CLAUDE.md`: the core principles that apply when using these settings in a real project.
- **Stage 2** — `code-reviewer` subagent: an isolated reviewer that evaluates code without the main session's bias.
- **Stage 3** — `test-runner` subagent: runs tests in an isolated context and returns a summarized result.
- **Stage 4** — Stop hook: automatically invokes `code-reviewer` before a session ends, with a conditional exit loop.
- **Stage 5** — PostToolUse hook: invokes `test-runner` after every file modification.
- **Stage 6** — SessionStart hook: summarizes project state and prior work at the start of each session.
- **Stage 7** — Slash commands: `/kickoff`, `/review`, `/wrapup`, and other reusable prompts.

## Repository layout

```
dotclaude/
├── README.md              ← this file
├── LICENSE                ← MIT
├── .gitignore
├── CLAUDE.md              ← meta guide for working on this repository
├── .claude/
│   └── settings.json      ← Claude Code settings for this repository itself
└── templates/
    └── dev/
        └── .claude/       ← what you symlink into your projects (stage 1+)
```

Two `CLAUDE.md` files coexist on purpose:

- `/CLAUDE.md` is the **meta guide**. It is active when Claude Code is run inside the `dotclaude` repository itself, and governs how this repository is maintained.
- `/templates/dev/.claude/CLAUDE.md` (from stage 1) is the **user template**. It is active when Claude Code is run inside a project that symlinks to this repository, and governs how code is written in that project.

They share the same philosophy but serve different audiences.

## License

MIT — see [LICENSE](./LICENSE).