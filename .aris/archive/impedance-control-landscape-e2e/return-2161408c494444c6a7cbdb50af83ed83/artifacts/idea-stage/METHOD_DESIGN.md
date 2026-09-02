# Method Design — Candidate Principles

Design cycle: `MDCYCLE-SOFTSCAN-V2-R2`

This packet presents Candidate Principles for Human discussion and selection. It does not contain a test plan, execution set, or cost approval.

**Solution-space constraint.** CONSTRAINED: Both chains require one shared object and one applied-action relation: the model predicts optimizer-consumed margins; calibration acts on those margins; optimization applies them to coupled gains; rendering returns only the verified policy. Separate model, critic, safety filter, tank or timeout controller recreates an accepted failure. Encoder, set and solver choices remain implementations inside this principle.

## P-DECISION-CALIBRATED-TOTAL-IMPEDANCE · version 2

**Principle.** Represent soft contact by the smallest history-conditioned, action-conditional future-margin capacity tube preserving robust impedance decisions; calibrate it on declared shifts; use it inside one incumbent-preserving horizon program whose only rendered output is a verified feasible time-varying normal/tangential impedance policy.

**Mechanism.** Encode safe history; predict candidate-gain-conditioned force/moment, slip, loss, energy and actuator margins; calibrate their tube; shift/verify the prior policy; improve it in one robust program; commit verified candidates only; return the incumbent at deadline. This changes Environment identification plus controllers becomes one decision-calibrated capacity-to-feasible-impedance relation.. Histories are separated by decisions/margins they change; uncertainty, objective, hard limits, fallback and rendering share the same variables/policy.

**Provisional Scientific Delta.** Decision-calibrated feasible impedance co-designs the minimal contact representation and optimal law around one calibrated incumbent-preserving relation.

**Primary risks.** Safe history contains gain-relevant future-margin information. → Add sensing or narrow the claim.; A meaningful held-out population/support rule exists. → Coverage/robustness is vacuous.; A shifted policy or safe action remains feasible in the declared envelope. → Deadline totality fails.; Set construction, verification and return fit the update budget. → Not online executable.

**Substantive difference.** Not a richer model plus existing control: impedance regret selects model content; exact margins are calibrated/constrained; gains and limits are joint; only verified policies render.

**Killer-test concepts.** Incremental held-out regret/margin prediction from nested histories: Pattern A=Retained information changes gain ranking/outcomes; a smaller sufficient state matches full state. vs RIVAL_RCA ALT-MODEL-AGNOSTIC-FEEDBACK-SUFFICIENT Pattern B=Richer history equals scalar/memoryless state. under Held-out aliases occur.; Margin-tube coverage and violation rate: Pattern A=Coverage holds; support failures enter safe mode and reduce violations. vs RIVAL_RCA ALT-CALIBRATED-MEMORYLESS-MODEL-SUFFICIENT Pattern B=Miscoverage persists or memoryless is equivalent. under Shifts affect capacity.; Returned policy identity, feasibility and latency under forced interruption: Pattern A=Every deadline returns a verified incumbent; extra compute improves cost only. vs RIVAL_RCA ALT-POSTHOC-GUARD-EQUIVALENT Pattern B=Timeout yields no/unverified action or guard-altered output. under Interrupted near active margins.

**Status.** ACTIVE: The sole retained principle closes both causal chains through one shared object and one applied-action relation; separated component stacks are absorbed or rejected.
