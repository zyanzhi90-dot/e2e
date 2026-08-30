# New-window continuation prompt

Copy the following into a new Codex window opened with the actual project root
set to `D:\桌面\科研Agent Harness设计\impedance-control-e2e`:

```text
Continue the existing ARIS/Harness run `impedance-control-landscape-e2e`; do not
create a new run.

Before taking any formal action:

1. Verify that this Codex session's actual project root is the E2E project root.
2. Read AGENTS.md completely.
3. Read CURRENT_CONTINUATION.md completely as the sole formal continuation
   entry point.
4. Read the live Controller State, current workflow, and live allowed-actions
   through python -m arisctl.
5. Continue the existing run under live Controller authority, using
   CURRENT_CONTINUATION.md only for the last handoff checkpoint and stable
   scientific/recovery context.
6. Do not reset the run, rerun completed phases, directly edit formal State,
   modify accepted artifacts, bypass a Gate, or use historical handoff files as
   current instructions.

Treat live Controller State as the sole lifecycle authority. If it differs from
the handoff checkpoint, follow the live State and allowed-actions, then update
CURRENT_CONTINUATION.md at the next formal stop or cross-window handoff.
```

