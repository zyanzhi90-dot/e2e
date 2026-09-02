# Method Design — Candidate Principles

Design cycle: `MDCYCLE-SOFTSCAN-V2-R3`

This packet presents Candidate Principles for Human discussion and selection. It does not contain a test plan, execution set, or cost approval.

**Solution-space constraint.** UNDERCONSTRAINED: The accepted RCA requires a decision-sufficient capacity object and a total feasible applied-action relation, but it does not determine whether deadline totality should arise from verified-incumbent persistence or from a viability-certified admissible set with an internal backup action. Both candidates integrate all four obligations and differ causally, not merely by solver choice.

## P-CAPACITY-CONDITIONED-FEASIBLE-INCUMBENT · version 1

**Principle.** Represent continuous soft contact by a minimal calibrated action-conditional capacity state, and use it inside an anytime robust horizon program whose verified feasible incumbent is the only action that may be rendered at any deadline.

**Mechanism.** Encode safe history; predict and calibrate candidate-impedance-conditioned future margins; form one robust horizon problem over coupled normal-tangential stiffness/damping; shift or construct and verify an initial feasible policy; commit only fully verified improvements; render the incumbent on timeout, failure or deadline. This changes Environment modeling and optimal impedance become one capacity-to-verified-incumbent applied-policy relation.. Decision-equivalent histories determine the exact robust feasible set, while incumbent persistence makes intended and applied feasible policies identical under interruption.

**Provisional Scientific Delta.** Decision-calibrated contact capacity and feasible-incumbent persistence are synthesized into one applied-policy relation.

**Primary risks.** Safe history contains future-margin information that changes impedance decisions. → Narrow the claim or add sensing.; The declared held-out population supports useful one-sided capacity calibration and abstention. → Robustness claims become invalid.; A robustly feasible incumbent can be initialized and verified for every declared update context. → Deadline totality fails.; Capacity construction and full incumbent verification fit the update budget. → The method is not online executable.

**Substantive difference.** Unlike the certificate candidate, totality depends on persistence and verification of a horizon-policy incumbent; usefulness can survive without a compact controlled-invariant certificate but requires feasible warm starts.

**Killer-test concepts.** Incremental held-out impedance regret and margin prediction from nested histories: Pattern A=Retained history changes gain ranking or outcome, and a smaller state matches the full state within tolerance. vs RIVAL_RCA ALT-CALIBRATED-MEMORYLESS-MODEL-SUFFICIENT Pattern B=Richer history adds no reproducible decision value. under Held-out aliases occur.; One-sided coverage of each optimizer-consumed future margin on the declared held-out population: Pattern A=Calibrated sets meet the declared simultaneous or componentwise coverage target; detected unsupported contexts abstain. vs RIVAL_RCA ALT-CALIBRATED-MEMORYLESS-MODEL-SUFFICIENT Pattern B=A calibrated memoryless capacity model attains the same held-out margin coverage and downstream feasibility without retained history. under Capacity-affecting shifts are present.; Applied-policy identity, robust feasibility and latency under forced interruption: Pattern A=Every deadline returns the last verified incumbent; extra compute improves cost only. vs PRINCIPLE P-CAPACITY-CONDITIONED-VIABILITY-RELATION Pattern B=Incumbent initialization or verification fails where a certificate backup remains directly evaluable. under Forced interruption near active margins.

**Status.** ACTIVE: Closes both accepted causal chains with one shared capacity object and an incumbent-based total applied-action mechanism; remains active until discriminated from certificate totality.

## P-CAPACITY-CONDITIONED-VIABILITY-RELATION · version 1

**Principle.** Represent continuous soft contact by a minimal calibrated action-conditional capacity state, and let it parameterize one viability-certified admissible impedance relation containing both the performance-selected action and a directly evaluable certified backup.

**Mechanism.** Learn and calibrate the same action-conditional future-margin state; augment controller state with contact, interaction-energy and previous-gain variables; define a capacity-conditioned controlled-invariant or barrier-certified impedance set; optimize joint performance only within this set; if optimization is incomplete, render the certified backup action that is already a member of the same decision relation. This changes A nominal controller plus external safety override becomes one capacity-conditioned certified admissible-action relation.. Decision-equivalent histories determine the certificate parameters, while the nonempty certified action set makes the selected/backup action feasible and deadline-total without relying on optimizer incumbent persistence.

**Provisional Scientific Delta.** Decision-calibrated contact capacity and viability-certified impedance selection are synthesized into one admissible-action relation.

**Primary risks.** Safe history contains future-margin information that changes impedance decisions. → Narrow the claim or add sensing.; The declared held-out population supports useful one-sided capacity calibration and abstention. → Certificate robustness is invalid.; A nonempty, useful capacity-conditioned invariant set and backup impedance exist over the declared envelope. → The candidate is unsafe, infeasible or vacuously conservative.; Certificate and backup evaluation fit the update budget. → Deadline totality fails.

**Substantive difference.** Unlike the incumbent candidate, totality follows from an invariant admissible set and directly evaluable backup; it can survive optimizer cold starts but risks certificate conservatism and high-dimensional intractability.

**Killer-test concepts.** Incremental held-out impedance regret and viable-set prediction from nested histories: Pattern A=Retained history changes the certified feasible set or joint outcome, and a smaller state matches the full state within tolerance. vs RIVAL_RCA ALT-CALIBRATED-MEMORYLESS-MODEL-SUFFICIENT Pattern B=Richer history adds no reproducible set or decision value. under Held-out aliases occur.; One-sided coverage of each margin used by the certificate on the declared held-out population: Pattern A=Calibrated margins meet the declared target and unsupported contexts invoke the conservative certified set. vs RIVAL_RCA ALT-CALIBRATED-MEMORYLESS-MODEL-SUFFICIENT Pattern B=A calibrated memoryless capacity model attains the same held-out certificate-margin coverage and downstream feasibility without retained history. under Capacity-affecting shifts are present.; Nonemptiness, conservatism, feasibility and latency of the certified action set under forced interruption: Pattern A=A certified backup exists and is returned at every deadline while optimized actions improve joint cost inside the same set. vs PRINCIPLE P-CAPACITY-CONDITIONED-FEASIBLE-INCUMBENT Pattern B=The certificate is empty/vacuous or too conservative where an incumbent horizon policy remains feasible and performant. under Cold starts and active physical margins.

**Status.** ACTIVE: Closes both accepted causal chains with one shared capacity object and a certificate-based total applied-action mechanism; remains active until discriminated from incumbent totality.
