
# R14 Method Selection Justification

## Overall judgment

R14 preserves the accepted Problem and both RMCs. They force two interventions: a history-bearing, action-conditional contact-outcome uncertainty object that can change after an admitted calibration-out condition, and one total relation whose applied normal–tangential impedance already satisfies contact, wrench, energy, actuator and deadline constraints.

Current Evidence closes the second intervention but does not eliminate physical (P), structured learned (L), or physical-plus-residual hybrid (H) representations. The evidence-honest result is therefore an UNDERCONSTRAINED Human Selection among three complete Methods. P is the strongest-simple priority; L and H survive only in explicit, falsifiable necessity regions.

## Baselines and closest priors

NNBO is the genealogy: it supplies the scalar point-contact HC model followed by optimal impedance from which the project began. The closer structural baseline is the MPVIC family:

- imoj6X2imAgJ: learned interaction model followed by predictive variable-impedance optimization;
- xlNjdqnC2fMJ: adaptive/passivity-aware MPVIC with online environment estimation and constraints;
- j4KJ1EdUyYAJ: identified scalar contact followed by optimal impedance;
- ZDySGaNsMn8J: finite-area deformable-contact prediction inside MPC with predefined impedance.

Thus R14 cannot claim novelty for model/identification-to-MPC. Its delta must be the exact calibration-authorized history/finite-area normal–tangential outcome relation and its deadline-total applied action.

## Decisive choice 1 — representation family

The accepted history-aliasing chain requires separating histories only when they change future wrench, contact, slip/moment capacity or robust gain order.

The major candidates are memoryless HC/Kelvin contact, hereditary finite-area physical state P, structured learned temporal outcomes L, physical-plus-residual H, and a high-fidelity field/FEM state. Memoryless contact fails under decision-changing relaxation and pre-slip history; _8NXx8-VkcgJ nevertheless prevents the false universal claim that HC never fits. Full FEM retains excessive distributed state that robot-side signals cannot reliably identify or propagate robustly at the update deadline, so it remains an offline simulator or feature source.

Ubx3Xkv4y2kJ supports P's finite-area viscoelastic and force–moment-capacity coordinates. P therefore has the fewest data/model assumptions and direct constraint semantics. But no Evidence proves physical-family containment over all admitted material, geometry, sliding and history changes. Learned MPVIC proves feasibility of learned-model-to-MPC, not automatic generalization. L is necessary only if P is noncontaining/action-biased and a calibrated learned tube preserves hard-margin coverage. H is necessary only if a persistent action residual exists and the physical skeleton materially reduces support/data burden relative to L.

Selection is consequently exact rather than arbitrary:

- choose P if physical containment and action sufficiency hold;
- choose L if flexible temporal outcomes are required and the physical skeleton adds no measurable value;
- choose H only in the strict complementarity interval.

## Decisive choice 2 — temporal realization

The task needs a causal statistic over the shortest history that changes the decision, not a universally preferred sequence network.

P uses generalized-Maxwell/Prony modes and a LuGre-type bristle state because their coordinates directly represent relaxation and pre-slip/capacity constraints. Their orders are pruned by robust action equivalence, not prediction RMSE.

L/H use a dilated causal TCN as the concrete initial realization because full-window inference is parallel, memory is static and WCET is measurable. Inputs, action conditioning, outputs, hard structural layers, multistep decision-regret loss and simultaneous calibration are all fixed. TCN is not elevated to scientific necessity: a GRU or stable state-space model replaces it only if action-equivalence, coverage and WCET Evidence dominates.

Selection: explicit physical recursion for P; fixed-window causal TCN for L/H as executable default, with architecture a falsifiable implementation choice.

## Decisive choice 3 — calibration-out adaptation

One offline worst-case envelope may preserve limits but cannot recover useful action order. Point EW-RLS, posterior means or uncalibrated neural updates do not supply containing authority for hard constraints.

The selected Method mechanism is bounded set/tube adaptation under a full-envelope hard-authority invariant. The independently calibrated envelope constrains every hard margin in every SCAN cycle. A contraction affects nominal performance or adds restrictions only; it never removes an envelope constraint. SUPPORTED allows that performance use, ADAPTING_IN_ENVELOPE ignores it while adapting, and OUTSIDE_ENVELOPE withdraws.

This closes the pre-detection interval: even if an abrupt admitted change is not observable before publication, the applied action remains full-envelope feasible. Fixed-facet recursive membership is selected as the strongest-simple contraction update. A bounded set-MHE is formally registered and rejected as a core Candidate absent action-relevant improvement at equal authority/WCET; every active Candidate's adaptation prediction is bound to that same rival.

## Decisive choice 4 — impedance action class

Normal-only/scalar impedance cannot address tangential tracking and slip. Full six-dimensional SPD impedance and reference co-optimization add unestablished cross-axis decisions and substantial robust-optimization burden. A moving contact frame with coupled wrench/capacity constraints captures physical coupling while four gains independently control normal and tangential stiffness/damping.

Selection: positive (k_n,d_n,k_t,d_t), replaced only if matched counterfactual optimization shows persistent robust action-order or joint-outcome non-equivalence versus richer SPD/cross-axis action.

## Decisive choice 5 — predictive optimizer and horizon

Analytic schedules, NNBO/HJB, direct learned policies and contextual bandits can rank gains, but this Problem requires candidate-action-conditioned future contact prediction, delayed relaxation/pre-slip/energy states, a joint objective and simultaneous hard constraints. Encoding these outside the decision recreates selection-to-realization decoupling.

MPC is selected for causal match; MPVIC establishes feasibility, not novelty. One-step optimization remains the strongest simple rival. The horizon is the shortest one for which relaxation, pre-slip, gain-rate or energy memory changes the first action and collapses to H=1 under action equivalence.

Warm-started multiple-shooting RTI-SQP is the executable default for the smooth low-dimensional problem. Fixed work, independent verification and WCET are required; solver brand is not.

## Decisive choice 6 — uncertainty consumption

Nominal objectives plus confidence penalties cannot authorize hard force/torque, contact, energy and actuator limits. R14 separates cost and authority: a center and supported contraction shape performance or add restrictions, while every member of the independently calibrated full-envelope set/tube constrains every hard margin in every SCAN cycle. Adaptation therefore improves ranking inside the hard-feasible set rather than relaxing safety constraints.

Selection: nominal-center objective plus robust authorized-set constraints.

## Decisive choice 7 — energy

Positive gains and gain-rate bounds do not account for energy introduced by stiffness change, moving reference, delay or inner-loop mismatch. A downstream passivity layer can alter the selected gain and recreate the accepted decoupling. The direct repair is a measured energy-budget state inside robust feasibility, with bounded reserves.

Post-hoc passivity remains the simple rival and wins if it supplies the same guarantee and the same applied action. Selection: optimizer-internal energy state, conditional on conservative coverage and non-equivalence to that rival.

## Decisive choice 8 — timeout and infeasibility

A faster solver does not define an action; hold-last or shifted incumbents can become invalid after contact/support change. Full viability is stronger than necessary while verified unloading is available. The simplest total relation registers a rate-limited dissipative withdrawal before optional optimization and atomically publishes only a fully verified scan action or withdrawal.

Selection: verified SCAN/WITHDRAW total action. Feasible-incumbent bookkeeping is internal; viability becomes a core mechanism only if withdrawal availability is falsified.

## Candidate-specific closest-prior separation

P differs from j4KJ1EdUyYAJ and xlNjdqnC2fMJ by making an online hereditary finite-area containing capacity set and three-state authority internal to the same total four-gain action. ZDySGaNsMn8J supplies finite-area prediction but predefined impedance. P's delta survives only if physical-family sufficiency holds.

L is closest to imoj6X2imAgJ. It adds a causal decision-trained normal–tangential outcome tube, simultaneous controller-margin calibration, bounded in-contact support adaptation and total applied-action semantics. It fails if ordinary learned MPVIC plus a simpler margin mechanism is action-equivalent.

H restricts learning to a zero-collapsible action residual of the hereditary physical skeleton and conservatively calibrates the dependent composition. Its novelty is not generic hybridity; it is the strict complementarity regime. H fails if either component can be removed without changing supported actions or outcomes.

## Source Mechanism accounting

No Source Mechanism is formally retained in R14. VES and SPO inform Target-side derivations of action-equivalence scoping and decision-aware training, but the available provenance does not satisfy the required pre-Source discovery–terminology–search ordering, so no migrated genealogy or PASS alignment is claimed. Predict-then-calibrate contributes split-calibration discipline but no direct hard-safety guarantee. Box-FDDP may inform solver implementation only.

## Joint fit assessment

### Problem fit — PASS

Every Candidate repairs history/contact-capacity aliasing and uses in-envelope evidence to recover decisions after calibration-out change. The common robust four-gain relation addresses force, path, contact/slip, wrench/energy/actuator limits and real-time action existence.

### Method fit / necessity — PASS for Human-Selection readiness

No simpler route is silently excluded. Memoryless contact, fixed robust control, point adaptation, one-step choice, normal-only action, post-hoc energy correction and hold-last behavior each has a named failure or equivalence condition that demotes the richer mechanism.

The remaining representation choice is genuinely underconstrained by present Evidence. P/L/H have mutually discriminating necessity regions and complete methods; claiming uniqueness now would be method-fit overreach.

### Scientific strength — PASS as a Candidate set, conditional on later validation

Each route has an executable state/model, uncertainty object, update law, gain-generation map, hard constraints, online algorithm, assumptions, closest priors and killers. The delta is intervention-level, not a module conjunction. No future performance, novelty or theory result is represented as established.

R14 is PRINCIPLE_PACKET_READY only as a reviewed set for Human Selection. It is not a FINAL_METHOD_PACKET and does not authorize Principle Test Design before the user selects.

## R14 reviewer-issue closure

- **Pre-detection abrupt change:** closed by the full-envelope hard invariant; no alarm-latency assumption remains.
- **Adaptation rival identity:** closed by the formal rejected Candidate P-BOUNDED-SET-MHE-ADAPTATION-RIVAL@1; P/L/H adaptation predictions name this exact Principle and compare identical authority, data and WCET.
- **Candidate-by-prior novelty:** the complete 3×4 intervention matrix in METHOD_SYNTHESIS_COMPLETE_R14.md and the per-Candidate novelty fields compare all four named priors by variable/relation, causal position, direction, activation, actual prior effect and Target residual.
