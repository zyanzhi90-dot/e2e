# E2E runtime blocker — project-local hook discovery for native child

## Formal checkpoint

- Run: `impedance-control-landscape-e2e`
- Formal stage: `PAPER_READING`
- Target paper: `HVvmYj1jhCIJ`
- Required read event: `cc391045ad45465995ab92970a0ef369`
- Required content SHA-256: `86a02756ecf06b2949c6b02c8cc24de9f02b830937ceece3ff5fdd37aca6ec49`
- Required role: `paper_reader`

## Reproduction

A native generic compatibility child was dispatched in the current active Codex
turn with `fork_turns = none`, exactly one `ARIS_NATIVE_GENERIC_COMPAT` binding,
the verbatim configured `paper_reader` role contract, the original read-event and
content-hash binding, and no child tool calls. The child returned one JSON Evidence
Card with the required source, read-event, and content-hash values.

Native child transcript:

`C:\Users\user\.codex\sessions\2026\08\15\rollout-2026-08-15T15-55-08-01a0046a-dfac-7de0-8ede-502cdaac2155.jsonl`

The transcript's `session_meta` proves:

- `thread_source = subagent`;
- `agent_path = /root/paper_reader`;
- `agent_role = null` (native generic compatibility case);
- no tool-call records occurred;
- the final payload is present;
- `cwd = D:\桌面\科研Agent Harness设计`.

## Observed failure

No receipt was created at:

`.aris/agent-attestations/paper_reader/cc391045ad45465995ab92970a0ef369.json`

The task-level working directory is the parent workspace, while the managed
project hook configuration lives at:

`D:\桌面\科研Agent Harness设计\impedance-control-e2e\.codex\hooks.json`

The native child inherited the task-level parent working directory. Therefore
the runtime did not discover or invoke the project-local natural `Stop` or
`SubagentStop` hook, even though the child lifecycle and transcript satisfy the
compatibility contract.

## Classification and impact

- Ownership: Codex runtime / project-root binding.
- Formal impact: P0 for this run's continuation. `submit-evidence` must fail
  closed because the required one-time external attestation does not exist.
- ARIS reusable implementation: not modified.
- Formal run State, approvals, receipts, and accepted Evidence: not edited.
- The returned Evidence Card is not submitted or represented as formally
  accepted.

## Additional observation

During diagnosis, a repeated legal `arisctl read-user-fulltext` call created a
second complete read event, `f3729380d6b04bffb827b0aff371b8dd`, for the same
paper and content hash and incremented the full-text counter. The original
checkpoint event remains present and complete. No evidence was bound to the new
event. This duplicate is retained honestly in formal State and is not manually
removed or rewritten.

## Continuation boundary

Any work after this point that bypasses the missing receipt must be stored and
reported as E2E-specific test continuation only. It must not call formal
`submit-evidence`, mutate formal State, manufacture a receipt/lifecycle event,
or be counted as a canonical Harness pass.
