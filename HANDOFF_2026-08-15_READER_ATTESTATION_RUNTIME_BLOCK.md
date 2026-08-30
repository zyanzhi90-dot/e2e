# ARIS E2E checkpoint — native reader compatibility continuation

## Current formal run state

- Run: `impedance-control-landscape-e2e`
- Stage: `PAPER_READING`
- Papers: 442
- Read events: 98 total, 82 complete
- Accepted Evidence Cards: 21
- Waiting-for field: null
- Formal state last update: `2026-08-15T06:23:08Z`
- The 61 user-supplied PDFs and their completed new read-event bindings remain
  recorded in `READ_EVENT_MANIFEST_V3.json`.
- All existing accepted Evidence Cards remain unchanged.
- No new human full-text gap exists.

The current run was migrated with the existing compatible workflow migration:

- migration type: `COMPATIBLE_WORKFLOW_MIGRATION`
- migration ID: `7a5cb20abbca41baa99654fe55322298`
- old workflow SHA-256: `a14fb14c0ff5b19a1d02dc22f4e26adcee31f0fba61035f4c105133a19c7523a`
- current workflow SHA-256: `4831b786a0338636b8902c8ddca74b9a340a88a9b70d7e668b522a7279272dec`
- checked/executed phase: `landscape`

The current E2E project `.codex` layer was resynchronized from the current ARIS
source through `install_project_codex_layer()`. `ARIS_CONTROLLER_LAYER.json`
now records 13 managed files, and every target hash matches both the manifest
and its current ARIS source. `AGENTS.md` was preserved unchanged. This includes
`pre_tool_use_policy.py`, `subagent_attestation.py`, and all managed agent
configurations.

The first new binding remains:

- paper/source ID: `HVvmYj1jhCIJ`
- read event: `cc391045ad45465995ab92970a0ef369`
- content SHA-256:
  `86a02756ecf06b2949c6b02c8cc24de9f02b830937ceece3ff5fdd37aca6ec49`
- PDF:
  `source-materials/user-batch-v2/Impedance_Variation_and_Learning_Strategies_in_HumanRobot_Interaction.pdf`

There is intentionally no live receipt at
`.aris/agent-attestations/paper_reader/cc391045ad45465995ab92970a0ef369.json`
for this read event. The previously returned but unattested
`HVvmYj1jhCIJ` Card must not be reused.

## Completed Harness fixes

1. Query-plan submission no longer resets exact same-plan terminal query
   events. The formal reconciliation action restored 28 complete and 3 failed
   events, after which retrieval legally advanced to `PAPER_READING`.
2. The PreToolUse policy now permits literal read-only inspection of protected
   project inputs while retaining write denial.
3. Reader/reviewer provenance now listens to natural `Stop` as well as
   explicit `SubagentStop`. A natural `Stop` resolves the configured role and
   agent ID from Codex transcript `session_meta`; duplicate lifecycle events
   are idempotent and consumed proof is not recreated.
4. The project installer now deploys `result_to_claim_reviewer.toml`. The live
   project `.codex` layer was resynchronized from the merged ARIS source, and
   all 13 managed-file hashes match its manifest.
5. Reader/reviewer compatibility now supports current-turn native generic
   children for `paper_reader` and `coverage_reviewer` when the runtime cannot
   select the configured custom role. Formal receipts remain transcript-,
   binding-, payload-hash-, and lifecycle-attested; generic dispatch is
   recorded honestly and never represented as a configured profile.

Verification completed: Controller/CLI `108 passed`; project setup `2 passed`;
focused hook/policy tests passed; live literal PDF hash and full-text reads
passed. The live old lifecycle configuration failed closed after a real reader
returned a strong card, proving that no un-attested candidate or formal state
mutation leaked through.

## Current reader/reviewer dispatch

For the already-started Goal, reader/reviewer work remains in the current
active Codex turn. Prefer a directly selectable configured native role. If the
runtime cannot select that custom role, use the formal native generic
compatibility path only for `paper_reader` or `coverage_reviewer`:

- the compatibility child uses `fork_turns = none` and follows the current
  Skill / AGENTS binding, role-contract, and attestation rules;
- weekly usage reaching 100% is not an ARIS continuation checkpoint;
- never use nested `codex exec`, a new CLI session, or a new top-level Codex
  turn for reader/reviewer work.

## Exact continuation

`HVvmYj1jhCIJ → formal reader → attestation → submit-evidence → remaining papers → finish-reading → Active Field Map → Coverage Review`

1. Continue from the current `PAPER_READING` checkpoint with `HVvmYj1jhCIJ`.
2. Prefer configured `paper_reader`; if the native runtime cannot select it,
   use the formal native generic compatibility path directly.
3. Require the real child lifecycle/transcript to create the attestation and
   verify the read-event / paper / content-hash bindings, exact returned
   payload hash, and zero tool calls for a generic compatibility reader.
4. Only after the formal receipt exists, save that actual exact candidate and
   run normal `submit-evidence`.
5. Verify one-time receipt consumption and unchanged original read-event/hash
   binding.
6. Process the remaining 60 papers through the same canonical chain without
   proactively stopping for weekly usage state.
7. After all are accepted, run `finish-reading` and rebuild the Active Field
   Map from all accepted Evidence.
8. Complete formal Coverage Review; use the same native compatibility path
   only if the configured coverage reviewer cannot be selected.
9. If Coverage Review returns `CONTINUE`, follow the canonical retrieval,
   Evidence, Map, and Review loop until sufficient or a genuinely necessary
   new human full-text gap appears.

## Still prohibited

- Reusing the prior unattested `HVvmYj1jhCIJ` Card.
- Manually manufacturing an attestation, receipt, `Stop` / `SubagentStop`, or
  any other lifecycle event; the real native child lifecycle must produce them.
- Directly editing formal run state or weakening the Controller attestation
  requirement.
