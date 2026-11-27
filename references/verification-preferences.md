# Verification preferences

Read when a change needs testing, a check fails, UI is changed, or a release/deployment is requested. Evidence tags: **[E]** stated by the user or project instructions/memory, **[R]** repeated in sessions, **[Q]** stated in the request that originally created this skill, **[S]** added as an engineering safeguard (not a historic preference).

## Acceptance first [Q][S]

- For nontrivial changes, derive a small set of **observable acceptance criteria** from the user's latest request plus relevant project rules. Distinguish current implementation from intended business behavior. [S]
- Cover the primary success path and the failure/boundary cases genuinely relevant to the modified logic (invalid inputs, empty states, permissions, state transitions, duplicate submissions, transaction consistency, concurrency where relevant). Do not force all categories onto every feature. [S]
- Compare the end result to the original request, not just to existing tests. Passing compilation/build/type checks alone is not proof that a behavior is correct. [Q][R]

## Proportion [R][Q]

- Start with the smallest relevant check, then adjacent regressions, then the full suite if shared code changed or before shipping. A multi-minute full suite is not usually the first command. [R]
- Add or update tests when behavior changes and a suitable test harness exists; do not require new tests for pure copy, style or documentation changes. [Q]
- Check the changed user flow at least once when safe and practical; if impossible, report the unverified part honestly. [R][S]
- Prefer existing project commands, fixtures and test structure. Avoid creating throwaway test frameworks without need. [S]

## Honest reporting [Q]

- State the **actual command and result**; separate passed, failed, blocked and not run. "All tests pass" is only for a full run that really passed.
- Never delete, skip, xfail or loosen an assertion, widen tolerance, or mock away the behavior under test merely to get green. If a test expectation is genuinely wrong, explain the evidence and update it transparently.
- Check exit status, not just the tail of a pipeline: trailing `grep` or `tail` may hide failure of an earlier push or test command.

## Failure diagnosis [R][E][S]

- Classify: implementation bug, wrong expectation, pre-existing failure, environmental/dependency issue, or flaky test. Seek reproducing evidence before changing the code. [R][S]
- Fix flaky tests at the cause (clock, background worker, port conflict, actionability/scroll), not via repeated retries or inflated timeouts. [E]
- Re-run a CI job once only if the error matches a documented unrelated flake, the diff does not touch the area, and the workflow has finished; otherwise investigate. [E]
- After a fix, rerun the failed check and its closest relevant neighbors. If repeated attempts yield no new information, **switch diagnostic strategy**. Stop and report only when progress is blocked or further steps would be speculative/unsafe; state the next evidence-gathering action. [S]
- Another session may use the same ports/services. Prefer free ports in temporary test configuration and clean up only your own test resources. Check for listeners you created. [E]

## UI and interaction [R]

The user often evaluates visible results with screenshots and iterates when the output does not meet expectations.

- When safely available, inspect the rendered page in browser at a narrow phone width (about 360px) and desktop; also dark mode if supported.
- Inspect intermediate states/transitions, not just the final frame: motion should originate appropriately from its triggering control, ease naturally and avoid popping, flashing or unnecessary full-page rerenders.
- Reuse existing components, spacing and design tokens; do not introduce generic template-like UI or unrequested broad redesigns.
- Do **not** start/stop/restart the user's running dev server without being asked. Isolated temporary test service on an unused port is acceptable only if it won't interfere and is cleaned up. [E][S]
- When explicitly asked for design direction, present 2–4 labeled options with a recommendation; the user often answers by letter.

## Shipping and deploy [E][S]

- Deployment is **not automatically authorized** just because tests pass or `main` is green. If the user requests deployment, the agent owns the end-to-end execution; do not send the user to another window merely to finish the same requested job. [E][S]
- Before deployment, check applicable CI/revision status, use a clean deploy source, verify backup and recovery, inspect potential migrations, and perform a dry run if the project's deployment workflow provides one. If a hard precondition is missing, stop and report it. [E][S]
- If the working tree is shared/dirty with someone else's work, prefer an isolated clean clone for deployment over stashing or resetting their changes; remove only the temporary clone that you created. [E]
- Irreversible or production data migrations need explicit approval after their effects and recovery path are explained. [S]
- Validate service/proxy configuration with project tools (for example `caddy validate`, `nginx -t`) before applying; after deployment verify externally via a key endpoint/page, not just exit code 0. [E]
