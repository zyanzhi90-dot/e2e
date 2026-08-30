# Current continuation — impedance-control-landscape-e2e

This is the sole formal continuation entry point for the existing run
`impedance-control-landscape-e2e`. It records the latest formal handoff
checkpoint and stable recovery rules. It is not a real-time copy of Controller
State, workflow lifecycle status, or `allowed-actions`.

Formal continuation must run in a Codex session whose actual project root is
`D:\桌面\科研Agent Harness设计\impedance-control-e2e`.

## Authority and recovery order

Controller State remains the only lifecycle authority. On every new-window
recovery, use this fixed order before any formal action:

1. read `AGENTS.md` and this file completely;
2. read the live Controller State and its current workflow with
   `python -m arisctl status impedance-control-landscape-e2e`;
3. read live `allowed-actions` with
   `python -m arisctl allowed-actions impedance-control-landscape-e2e`;
4. continue the existing run only under that live Controller authority, using
   this file for its handoff checkpoint and scientific context.

Any lifecycle value below is explicitly a value at handoff. It must be
re-checked rather than treated as current permission. Do not persist an action
list in this file. Update this checkpoint whenever formal work stops or is
handed off across windows; it need not mirror every Controller transition in
real time.

## Operational handoff checkpoint

- Run: `impedance-control-landscape-e2e`
- Checkpoint kind: reviewed Candidate Principles awaiting Human selection
- Workflow at handoff: `idea-discovery-v4`
- Canonical workflow SHA-256 at handoff:
  `1e0e076e78dab213a839b05323e6dd7e9bf04155672b4a1a904f98e3e8b1b410`
- Scientific-core status at handoff: `ACTIVE`
- Scientific-core phase at handoff: `principle_human_selection` — `pending`
- Root-cause Gate at handoff: `accepted / DIAGNOSIS_READY`
- Method Design at handoff: `accepted / PRINCIPLE_PACKET_READY`, verdict ID
  `2c8d7f3a5d914df78b44f63e6dcae218`, reviewed by the configured
  `independent_method_reviewer` using `gpt-5.6-sol`.
- Human principle-selection request at handoff:
  `a3b6d5e1df4f4edabd570500868cc31b`.

The one-time pre-alignment was scoped only to this run and preserved the
executed prefix through `root_cause_gate`. Its pre-change State snapshot is
`manual-maintenance/impedance-control-landscape-e2e.state.pre-candidate-only-alignment.20260829T072630138Z.2927283ba0fb.json`
(`2927283ba0fb289126e7696288198b7832460f1bae16ef921e82a03e82ac4296`).
Controller migration `651b7c2f5b764768b9a1735c6de50638` rebuilt the pending suffix under the
canonical workflow above.

The authoritative State is
`.aris/runs/impedance-control-landscape-e2e.json`. Never edit it directly.

## Accepted scientific checkpoint

The migration preserved the accepted Field Map, Research Contract, Problem,
Root Cause Analysis, Root Cause verdict, canonical Evidence, and formal
Evidence/history. Reuse these accepted results; do not regenerate or rerun the
completed Problem or RCA phases.

- Accepted Field Map:
  `idea-stage/ACTIVE_FIELD_MAP.md`
  (`c1cb1d60c0aca8329465f6c33837ce28c200dd5591b8cb46453b6d744f9a48d7`)
- Accepted Problem: `P-SOFTSCAN-STATE-01`
- Research Contract:
  `idea-stage/RESEARCH_CONTRACT.md`
  (`cac57b1ee7361c18ac6bf52386ce8c9df51a7c12b0f5aacebf3314f336925d8b`)
- Problem Evidence Capsule:
  `idea-stage/PROBLEM_EVIDENCE_CAPSULE.md`
  (`187cd6ac4b885567844fab5a983f5bdf2b911613a746d4f5df202e84c1782d81`)
- Root Cause Analysis:
  `idea-stage/ROOT_CAUSE_ANALYSIS.json`
  (`d3f6796790a24b3e421bd34ce92886e754caa98a093839692bf8745bbda85525`)
- Root Cause Analysis view:
  `idea-stage/ROOT_CAUSE_ANALYSIS.md`
  (`4f77c9c0fa464a8327317a4c706d313ef2209a4c3975e6475393bfc3c3666fdd`)
- Accepted Root Cause verdict:
  `idea-stage/ROOT_CAUSE_VERDICT.json`
  (`a1ba6b2833d1136ce579592050f4a00d7d0298eed19513ec851fa82f66732b16`),
  verdict ID `ROOT-CAUSE-VERDICT-ee49d1c09f2f47b0918fba9a6d05b82e`.

The accepted diagnosis is representation-dependent, not a blanket claim that
complex contact models are superior. Its four primary causal chains are:

- `CHAIN-SPATIAL-SUFFICIENCY`: area and geometry can create action-relevant
  spatial aliasing.
- `CHAIN-TANGENTIAL-SUFFICIENCY`: tangential wrench or moment can reveal
  action-relevant slip and capacity constraints.
- `CHAIN-HISTORY-SUFFICIENCY`: loading history can create temporal aliasing,
  while overly detailed history state can be unidentifiable.
- `CHAIN-CLOSED-LOOP-ABSORPTION`: sufficient feedback, margins, and observable
  disturbances can make richer representations decision-irrelevant.

All prospective work must preserve the Research Contract's decision criterion:
matched resources; impedance-action changes beyond implementation resolution;
repeatable held-out improvement on at least one prespecified safety or task
endpoint beyond a justified equivalence margin; and no material loss on other
endpoints. A simpler representation remains an allowed and informative result.

The accepted candidate-only Method Design artifacts are:

- `idea-stage/METHOD_DESIGN_PACKET.json`
  (`05be720376b2762deff8b11e4694990fe8f21c0bf65fff17e93af0456a5dc7d9`)
- `idea-stage/METHOD_DESIGN.md`
  (`37c066ffd791c00b1963c3e223f5c837eb63789d7e722e2e41188def0223fb7a`)
- `idea-stage/METHOD_DESIGN_REVIEW.json`
  (`e19ed54e3dc1dcc789cb37d40961148f3e910dde16656d5d03d64752cc38bc28`)

The packet reuses the accepted RMCs, capabilities, obligations, Principle
Search, Evidence, and history. It contains no test plan, execution set, or
cost. The superseded Method Design bindings were invalidated by the one-time
pre-alignment. Historical Method Test Evidence contains only `PROPOSED`
records: no execution set was approved, handed off, or executed. At handoff,
`selected_for_testing` and `method_test_cycle` are null, and neither
`idea-stage/PRINCIPLE_TEST_PLAN.json` nor
`idea-stage/SELECTED_PRINCIPLE.yaml` exists.

## Next scientific task from this checkpoint

Subject to the live Controller State and live `allowed-actions`, the scientific
continuation is the Human Candidate Gate. The Human may select one reviewed
Candidate, request a modification, combine Candidates, or reject/return them.
Do not infer or perform that choice automatically, and do not enter Test Design
until Controller has consumed an explicit Human decision in the Codex UI.

The reviewed Candidates, in plain language, are:

- `SP-DECISION-SUFFICIENT-QUOTIENT@1`: keep the coarsest contact description
  that separates states only when they imply different future wrench, action,
  or outcome. This is the broad spatial anti-aliasing Candidate and does not
  commit to memory or a particular capacity model.
- `SP-WRENCH-FEASIBILITY-MARGIN@1`: represent the direction-dependent
  remaining tangential/moment capacity before a limit is reached. This is the
  narrower physics-relative feasibility Candidate.
- `SP-PREDICTIVE-CONTACT-STATE@1`: retain the lowest-order predictive state
  needed to make action-relevant contact futures conditionally sufficient.
  This is the only Candidate that may require finite temporal memory; a
  zero-order state remains admissible if history adds no value.
- `SP-FEEDBACK-GATED-SIMPLICITY@1`: default to the simplest representation and
  add structure only where feedback cannot absorb the mismatch and the richer
  information changes actions or independent outcomes. This is the closed-loop
  value gate and can legitimately conclude that no richer representation is
  needed.

Their primary distinction is causal locus: spatial aliasing, remaining
feasibility slack, temporal non-Markovity, or closed-loop decision value.

## Stable boundaries

Do not create a new run, reset this run, rerun completed phases, directly edit
formal Controller State, modify accepted artifacts, or bypass a Gate. Formal
research-state changes occur only through `python -m arisctl` and only when the
live Controller exposes the required action.
