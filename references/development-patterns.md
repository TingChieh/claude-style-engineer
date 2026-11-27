# Development patterns and rationale

How each rule in `SKILL.md` was derived from recurring real-world coding sessions (multi-project prompts, tool-call transcripts, and workflow retrospectives). Project-specific facts, private infrastructure, and personal data are intentionally excluded.

Tags: **Stated** = explicitly written into user instructions or preferences. **Repeated** = observed across multiple sessions and projects. **Safeguard** = added as a general engineering discipline.

## Stated preferences

| Preference | Basis |
|---|---|
| Reply in the user's language, including a Simplified Chinese summary for Chinese requests. | Repeatedly requested across multiple projects |
| Do not start, stop, or restart the user's existing dev servers unprompted. | Explicitly stated preference |
| Warn before disruptive changes to any live service or shared runtime the user is actively using. | Explicitly stated operational preference |
| Preserve recovery access during maintenance or destructive changes (never cut off the active connection path before completion). | Explicitly stated safety rule |
| When deployment is explicitly requested, carry it through end to end rather than deferring it to another session. A green `main` is a prerequisite, not blanket authorization for unrequested deployment. | Explicitly stated deployment workflow |
| Deploy from a clean clone rather than disturbing a dirty shared workspace. | Explicitly stated deployment rule |
| Commit using the repository's configured author identity; keep private infrastructure and personal details out of public repos, issues, and PRs. | Explicitly stated Git hygiene rule |
| Keep project dependencies inside project virtual environments rather than installing globally. | Explicitly stated environment guardrail |
| Keep files in tidy subfolders and avoid leaving loose backups in the repository root. | Explicitly stated organization rule |
| Commit only the current task's changes when other work is in progress. | Explicitly stated Git workflow rule |
| Respect hint-only and additive-only constraints (e.g., explain the approach without pasting full code, or add code without rewriting existing sections). | Explicitly stated across study and maintenance tasks |
| No fabricated test results and no weakened assertions; single-agent continuous ownership by default. | Core design goal of this skill |

## Repeated behaviour

- **Short, directive prompts with visual context.** Follow-up turns are often brief directives (e.g., "continue", "option B", "merge and push") relying on the agent to maintain context and proceed on the agreed plan when unambiguous.
- **UI iteration via screenshots.** Visual and motion regressions (abrupt transitions, misaligned origins, template-looking layouts) recur when agents rely solely on type checks or builds; hence the explicit visual verification rules.
- **Concrete option menus.** Presenting 2–4 labeled choices with a recommendation lets the user decide quickly at real forks without lengthy back-and-forth.
- **Proportional planning.** Large features benefit from a concise plan up front; small bug fixes should go straight to investigation and editing.
- **Targeted-first command-line verification.** Run fast, targeted checks (`pytest`, Playwright, `vitest`, `node --test`, linters, config validators) before broader regression suites, and verify actual service/page behavior rather than exit codes alone.
- **File-backed continuity.** Long-running work survives compactions and session resumes reliably only when verified against live files, `git status`, commits, and runbooks rather than stale memory.
- **Shared worktrees.** Multiple sessions may share a checkout or local ports; temporary test processes should use free ports and clean up only what they started.

## Common failure modes addressed

1. Claiming a fix works when the rendered UI, animation, or end-to-end flow still fails.
2. Unrequested side effects: restarting running servers, flipping user settings, or editing files during hint-only requests.
3. Mismatched summary language or inflated verification claims.
4. Generic template-like UI changes that ignore existing components and design tokens.
5. Stopping halfway or deferring an explicitly requested deployment or verification step.

## Single-agent default: rationale

In routine coding work, a single main agent with continuous context avoids conflicting concurrent edits, duplicated exploration, and handoff drift. Parallel read-only lookups remain useful for speed, and delegation to subagents or workers is still allowed whenever the user requests it or project instructions specify it.

## Deliberately excluded

- Hard-coded coverage targets or rigid TDD mandates where copy/doc changes do not warrant new tests.
- Fixed retry counters (switching diagnostic strategy when stuck is more effective than arbitrary attempt caps).
- Any project-specific architecture, server inventories, hostnames, credentials, deploy scripts, or personal notes — those belong in each project's own instructions.
