# ARIS formal research project

- Formal research-lit state changes only through `python -m arisctl`, except for a one-time, explicitly user-authorized manual maintenance edit whose exact State fields and verification scope are specified in the active request. An agent must never make such a direct State edit without that authorization.
- Main performs query planning and Field Map synthesis.
- Spawn `paper_reader` and only the reviewer role named by the live Controller request. The native-subagent preflight checks the supplied/current runtime working directory against this formal project and verifies the managed `.codex` layer; it does not discover, repair, or establish the Codex runtime-owned project root. A parent-workspace or other-project root is a hard stop, so formal continuation must be launched in a Codex session whose actual project root is `impedance-control-e2e`. Do not start formal native work from an unverified root or treat the diagnostic `preflight-native-subagent` CLI as a substitute for Controller authorization. Keep reader/reviewer work in the current active Codex turn: prefer the configured native role; only when this runtime cannot select it, use the formally attested native generic compatibility path (`fork_turns = none`) for `paper_reader` or `coverage_reviewer`. Never use nested `codex exec`, a new CLI session, or a new top-level turn to simulate a configured role.
- Every scientific-core reviewer performs its Controller-issued judgment directly in its configured Codex CLI role and reports that Codex session's actual model identifier. A reviewer remains independent through its configured role and fresh context; same-family verdicts are accepted by the Controller.
- Human Gates require confirmation in the Codex interface.

## Current continuation

- This is a continuation of the existing `impedance-control-landscape-e2e` run, not a new run.
- `CURRENT_CONTINUATION.md` is the run's sole valid continuation-instruction entry point.
- The formal continuation must execute in a session whose actual Codex project root is `impedance-control-e2e`. This is a launch/runtime condition; this file records and constrains that condition but does not establish it.
- The older handoff, runtime-block, and changelog files are historical context only and are not current execution instructions.


For long-running asynchronous work:
- Empty `write_stdin` polls MUST use `yield_time_ms >= 180000`;
  prefer `300000` when intermediate output is not needed.
- `functions.wait` MUST use `yield_time_ms >= 180000`.
- `functions.exec` MUST set its outer `@exec yield_time_ms` at least
  30000 ms longer than the longest nested tool wait, so the outer
  code cell does not yield first.
- Do not apply the long wait to non-empty `write_stdin` calls that
  send interactive input.
- These tools return early when the process or cell completes.
  Do not wake the model merely to report that work is still running.

## Completion and Git hygiene

- Before declaring a task complete, run the relevant tests or validation checks for
  the changes made and report any checks that cannot be run.
- Inspect the final `git diff` and `git status` to confirm that only intended
  repository changes are included.
- Commit the completed, validated changes with a descriptive message and push the
  resulting branch and any task-created tags to the configured GitHub remote.
