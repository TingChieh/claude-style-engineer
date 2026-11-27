# claude-style-engineer

A compact, single-agent, acceptance-driven coding workflow Skill for **Codex**, **Google Antigravity**, **OpenCode**, and other Agent Skills-compatible coding agents.

It distills practical day-to-day software engineering discipline into a low-overhead workflow (`SKILL.md` is ~45 lines; detailed verification rules load on demand) — without importing private memories, credentials, or project-specific context.

## Why this skill?

Many coding agents either over-interview on routine tasks or stop early after a green build without verifying actual behavior. `claude-style-engineer` guides an agent to:

- **Own the task end to end in one thread** — single-agent write ownership avoids conflicting concurrent edits, while still allowing parallel read-only exploration; stays lightweight unless heavier design ceremony is explicitly requested.
- **Read the real codebase first** — inspect `git status`, project instructions, nearby patterns, and actual behavior before editing; distinguish existing behavior from intended behavior.
- **Fix root causes, not symptoms** — no swallowed exceptions, broad type/lint suppressions, or unrelated dependency/config relaxations to fake a green build.
- **Verify against acceptance criteria and audit the diff** — run targeted checks first, then regressions; never delete, skip, or weaken assertions; inspect rendered UI and transitions for visual changes; review `git status`/`git diff` and report exact commands and outcomes honestly.
- **Respect shared workspaces and live services** — never restart the user's running dev servers, never `git reset`/`stash` someone else's uncommitted changes, and keep dependencies in project virtual environments.
- **Pivot when stuck & avoid needless interviewing** — proceed directly on small fixes; present 2–4 concrete options with a recommendation only at genuine product, policy, or destructive forks.

## Repository layout

```text
claude-style-engineer/
├── SKILL.md                              # Core engineering workflow (~45 lines)
├── README.md
├── LICENSE                               # MIT License
└── references/
    ├── verification-preferences.md       # Detailed testing, UI, failure-diagnosis, and deploy checks (loaded on demand)
    └── development-patterns.md           # Design rationale and anonymized workflow background (not loaded during tasks)
```

## Installation

### Option 1: Download from Releases (no Git required)

Download the latest `claude-style-engineer-<version>.zip` (or `.tar.gz`) from the [Releases](https://github.com/TingChieh/claude-style-engineer/releases/latest) page, extract it, and copy the `claude-style-engineer/` folder into your agent's skill directory:

```bash
# Codex / OpenCode (global)
mkdir -p ~/.agents/skills
cp -R claude-style-engineer ~/.agents/skills/

# Google Antigravity (global)
mkdir -p ~/.gemini/config/skills
cp -R claude-style-engineer ~/.gemini/config/skills/
```

### Option 2: Single-source Git clone + symlinks (recommended for updates)

Clone once into your projects folder and symlink into each agent's skill directory so updates stay in sync via `git pull`:

```bash
git clone https://github.com/TingChieh/claude-style-engineer.git ~/projects/claude-style-engineer

# Shared pool (Codex, OpenCode, and other ~/.agents/skills consumers)
mkdir -p ~/.agents/skills
ln -s ~/projects/claude-style-engineer ~/.agents/skills/claude-style-engineer

# Google Antigravity
mkdir -p ~/.gemini/config/skills
ln -s ~/projects/claude-style-engineer ~/.gemini/config/skills/claude-style-engineer
```

Update anytime with:

```bash
git -C ~/projects/claude-style-engineer pull --ff-only
```

### Option 3: Project-local installation

Copy or clone the folder into `<repo>/.agents/skills/claude-style-engineer/`.

## Usage

After installing, start a new agent session:

- **Codex:** reference `$claude-style-engineer` in a task, or make it your default coding workflow by adding a short note to `~/.codex/AGENTS.md`:
  ```markdown
  ## Default engineering workflow
  For feature implementation, bug fixes, refactoring, and testing tasks, prefer the `claude-style-engineer` skill (`~/.agents/skills/claude-style-engineer/SKILL.md`), except for pure Q&A, explanation-only, or hint-only requests.
  ```
- **Google Antigravity:** invoke `/claude-style-engineer` or let automatic skill discovery select it for implementation, debugging, refactoring, and verification tasks.
- **OpenCode:** discovered automatically from `~/.agents/skills/claude-style-engineer/`.

## Scope and boundaries

- **Not for Q&A or hint-only exercises:** skips automatically when you only want explanations, ideas, or step-by-step hints.
- **Coexists with heavier workflows:** stays fast and direct for routine fixes and clear features; defers to formal brainstorming/planning or multi-agent splits when you ask for them or project instructions require them.
- **Project rules take precedence:** this global skill does not override repository `AGENTS.md`/`CLAUDE.md` files, safety rules, or explicit user instructions.
- **No automatic production deployment:** a green `main` branch or passing test suite is never treated as implicit authorization to deploy.

## License

[MIT](LICENSE) © 2026 TingChieh
