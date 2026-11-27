# Verification preferences

Load when a change alters behavior, fixes a failure, touches UI, or involves a release/deployment.

## Acceptance first

- For nontrivial changes, derive a small set of **observable acceptance criteria** from the latest request and applicable project rules. Distinguish current implementation from intended behavior.
- Cover the primary success path and the failure/boundary cases genuinely relevant to the modified logic (invalid inputs, empty states, permissions, state transitions, duplicate submissions, transaction consistency, concurrency where relevant). Do not force irrelevant categories onto every feature.
- Compare the end result to the original request, not just existing tests. Compilation, bundling, and type checks alone are not proof that behavior is correct.

## Proportion

- Start with the smallest relevant check, then adjacent regressions, then the full suite when shared code changed or before shipping. Avoid running a multi-minute full suite as the very first command when targeted checks exist.
- Add or update tests when behavior changes and a suitable test harness exists; do not require new tests for pure copy, styling, or documentation edits.
- Exercise the changed user flow at least once when safe and practical; if impossible, state the unverified part explicitly.
- Reuse existing project commands, fixtures, and test conventions instead of inventing throwaway harnesses.

## Honest reporting and pre-handoff audit

- Before reporting done, review `git status` and `git diff` to verify there are no leftover debug logs, unintended formatting diffs, stray untracked files, or leaked local paths/secrets.
- State the **exact commands run and their actual outcomes**; separate passed, failed, blocked, and not run. Only say "all tests pass" when a full suite genuinely ran and passed.
- Never delete, skip, `xfail`, or loosen an assertion, widen tolerance, or mock away the behavior under test merely to get green. If an existing test expectation is genuinely wrong, explain the evidence and update it transparently.
- Check exit status, not just the tail of a pipeline: trailing `grep` or `tail` can mask a failed push, build, or test command.

## Failure diagnosis

- Classify failures first: implementation bug, wrong test expectation, pre-existing failure, environmental/dependency issue, or flaky test. Gather reproducing evidence before changing code.
- Do not silence compiler, type, or lint errors with broad suppressions (`@ts-ignore`, `# type: ignore`, `any`), swallowed exceptions, or unrelated dependency/config relaxations unless justified and called out explicitly.
- Fix flaky tests at the root cause (clock/timers, background workers, port conflicts, actionability/scrolling), not via blind retries or inflated timeouts.
- Re-run a CI job once only when the failure matches a known unrelated flake, the diff does not touch that area, and the workflow has completed; otherwise investigate.
- After a fix, re-run the failed check and its closest neighbors. If repeated attempts yield no new evidence, **switch diagnostic strategy**. Stop and report only when blocked or when further steps would be speculative/unsafe, stating the next useful check.
- Assume other sessions may use local ports or services. Prefer free ports for temporary test runs, verify which listeners you created, and clean up only your own resources.

## UI and interaction

Visual and motion regressions are frequently missed by builds and unit tests.

- When safe browser tooling is available, inspect the rendered page at both a narrow mobile width (~360px) and desktop width, plus dark mode if supported.
- Inspect intermediate states and transitions, not only the final frame: motion should originate naturally from its trigger, ease cleanly, and avoid popping, flashing, or unnecessary full-page rerenders.
- Reuse existing components, spacing, and design tokens; avoid generic template-like UI or unrequested broad redesigns.
- Do **not** start, stop, or restart the user's running dev server without being asked. An isolated temporary test server on an unused port is fine if it does not interfere and is cleaned up afterward.
- When asked for design direction, present 2–4 labeled options with a recommendation so the user can choose cleanly.

## Shipping and deploy

- Passing tests or a green `main` branch is **not** implicit authorization to deploy. When the user explicitly requests deployment, own it end to end rather than handing it off to another window.
- Before deploying, verify CI/revision status, use a clean deploy source, check backup and recovery paths, inspect migrations, and run any dry-run check the project provides. Stop and report if a hard precondition is missing.
- If the working tree is shared or dirty with unrelated work, deploy from an isolated clean clone rather than stashing or resetting those changes; remove only the temporary clone you created.
- Irreversible or production data migrations require explicit approval after explaining impact and recovery.
- Validate service/proxy configs with project tools (e.g., `caddy validate`, `nginx -t`) before applying, and verify externally via a live endpoint/page after deployment rather than trusting exit code 0 alone.
