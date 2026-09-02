# R11 Method Selection Justification

Scope: formal Method-fit audit under `Problem fit + Method fit / necessity + Scientific strength`. This document justifies choices; it does not report validation and does not select a Principle for the human.

## Overall verdict

**Problem fit: PASS.** The common Method directly repairs the accepted aliasing and selection–feasibility decoupling under continuous soft-contact scanning, including real in-envelope calibration-out adaptation.

**Method fit / necessity: PASS at the relation level; deliberately UNDERCONSTRAINED at one mechanism axis.** Decision-sufficient action-conditional outcomes, authority-correct adaptation, joint constrained impedance choice, accumulated-energy internalization and a total scan/withdraw map each survive the removal and strongest-simple-rival tests. Present Evidence does not establish whether structured physical, structured learned or physical–residual hybrid contact representation is uniquely best. All three are therefore retained as substantively different falsifiable Candidates.

**Scientific strength: conditional PASS for Human Selection.** Each Candidate is a complete, analyzable Method and has a separate causal delta from its closest prior. Top-venue strength is conditional on the Candidate-specific representation prediction and the common adaptation/totality assumptions; none is claimed established before testing.

## Governing comparison rule

For every decisive choice R11 asks five questions from `Method-fit 具体执行准则.txt`: what exact failure is repaired; what major mechanism classes could repair it; what is the strongest simpler rival; what accepted constraint discriminates them before results; and what observation would kill the choice. If no accepted constraint discriminates credible rivals, R11 preserves alternatives instead of writing “A/B both work” or manufacturing uniqueness.

## Decisive-choice audit

| Choice | Major viable classes | Strongest simple rival | Selection and necessity result | Killer / disposition |
|---|---|---|---|---|
| Contact information target | full physical reconstruction; next-state prediction; material classification; action-conditional decision outcomes | a calibrated memoryless force model | The RMC is decision aliasing, so information is necessary only if it changes robust feasible gains or ranking. Decision outcomes are the minimal direct target; generic reconstruction and classification are rejected. | If a memoryless calibrated model is action-equivalent on paired histories, history representation is removed. |
| Contact inductive bias | finite-area physical; structured learned; physical–residual hybrid | bounded finite-area physical model | No formal Evidence independently eliminates any of the three. The prior R10 assertion that hybrid was uniquely necessary failed review. | `UNDERCONSTRAINED`; retain P/L/H and compare decision error, validity, data need and adaptation recovery. |
| Temporal representation | explicit relaxation/pre-slip state; finite-window TCN; recurrent state; neural state-space model | explicit low-order fading-memory state | History is required only over the shortest interval that changes the decision. No evidence fixes GRU or TCN. | Causal stable finite-memory contract retained; architecture demoted to implementation. |
| Calibration semantics | point uncertainty; conformal one-sided tube; Bayesian posterior; deterministic envelope set | a calibrated tube on the deployment population | Supported conformal-style simultaneous margins are appropriate only on a declared applicable population. They cannot authorize post-support extrapolation. | Use calibrated authority only in SUPPORTED; arbitrary-shift coverage is rejected. |
| Calibration-out response | detection/refusal; fixed robust envelope; bounded online set adaptation; unconstrained fine-tuning | fixed envelope-robust impedance | The accepted problem asks online selection under admitted changes, so detection or permanent refusal does not solve it. Unconstrained fine-tuning cannot support hard margins. A containing outcome/parameter set must change predictions and ordering while envelope authority remains independent. | If observations do not recover useful valid ordering before relevance expires, adaptation fails and the scientific claim must narrow. |
| Adaptation estimator | set membership; set-valued MHE; multiple-model union; projected EW-RLS; Bayesian point update | any bounded set estimator maintaining containment | The necessary object is a containing adaptive outcome set, not set-membership algebra or EW-RLS specifically. Multiple implementations can satisfy the same contract. | Exact estimator and set geometry demoted. Point estimates cannot authorize hard constraints. |
| Active information | decision-gated excitation; lexicographic information tie-break; passive scan data | passive adaptation | Nothing in the accepted RMC requires deliberate excitation, and contact excitation can consume force/slip/energy margin. | Removed from core. It may be studied later only if passive identifiability is falsified and a safe informative action is necessary. |
| Impedance action class | one normal stiffness; normal stiffness/damping; four contact-frame normal/tangential gains; full 6-D matrix plus reference | four diagonal contact-frame gains | Four gains are the smallest class that directly answers the accepted normal–tangential impedance question while letting the predictor account for fixed cross-axis settings. | A richer matrix is not a Contribution. It becomes necessary only if four gains cannot realize the accepted joint objective. |
| Decision paradigm | policy/RL; myopic robust optimization; MPC; offline gain schedule; black-box NNBO | one-step robust constrained optimization | The task has delayed relaxation, pre-slip/contact loss, gain-rate and accumulated energy, and multiple hard constraints. A finite-horizon constrained relation directly represents these; policy learning would still require a separate hard-authority layer, and NNBO is not naturally update-time constraint-total. | Use the shortest decision-relevant horizon and retain H=1 when equivalent; “MPC” is not novel by itself. |
| Numerical optimizer | RTI-SQP; sequential convexification; sampling; dynamic programming | any interruptible constrained method with verified publication | The RMC fixes timely relation membership, not a solver brand. RTI-SQP has practical precedent but no necessity evidence here. | Solver demoted to implementation; measured WCET and verification are required. |
| Uncertainty in objective versus constraints | posterior-only chance constraint; envelope robust constraint; posterior cost plus envelope constraint | envelope robust constraint only | During adaptation, independently valid envelope outcomes must constrain every scan action. A posterior can improve ranking or add restrictions but cannot relax that authority. | Any posterior-envelope composition that admits an action forbidden by the envelope kills the claim. |
| Interaction energy | soft energy penalty; post-hoc passivity layer; optimizer-consumed energy state; passive-by-construction policy | post-hoc tank/passivity correction | The accepted question includes interaction energy as a hard constraint and the RMC forbids changing the selected action later. Accumulated energy must therefore be state inside the same relation. | If a post-hoc device is action-equivalent in all active cases it can replace the internal state; otherwise post-hoc correction violates totality. |
| Deadline/infeasibility | fast solver only; previous action hold; feasible incumbent; viability/barrier; registered withdrawal plus verified publication | fast solver with action hold | Speed alone does not make the applied-action map total. Barrier or viability is unnecessary unless withdrawal feasibility fails. A preverified rate-limited unload plus atomic publication is the simplest current repair. | Any admitted state without feasible on-time withdrawal kills totality and requires claim narrowing or a stronger lower-level certificate. |

## Why not simply upgrade Hunt–Crossley?

Upgrading point Hunt–Crossley is not rejected. It is Candidate P, but only after changing what “better model” means. The physical route must represent finite contact area, relaxation/history, tangential pre-slip/friction and moment capacity; keep bounded parameter uncertainty; and be pruned by decision equivalence. If that family predicts the robust four-gain feasible set and ordering on admitted shifts, it is preferable to learned complexity.

The learned route becomes necessary only if a reproducible residual from material, geometry or contact history changes active margins or the chosen impedance after the best bounded physical adaptation. Its advantage is not presumed generalization. It is freedom from an incorrect constitutive decomposition while retaining a stable causal and structurally constrained outcome relation. Hybrid is necessary only if it improves validity, data need or adaptation recovery over both physical-only and learned-only. Thus R11 does not infer “HC fails, therefore neural network.”

## Why the temporal state is a contract, not GRU/TCN

The task requires memory when two contacts with the same current wrench and motion have different future force relaxation, pre-slip evolution, contact loss or energy consequences under the same gain. The minimal state must span the shortest such interval and be causal, stable and update-time computable. Physical modes, TCN, GRU and stable state-space realizations can meet this. Current evidence does not distinguish them independently of representation class, so naming one as necessary would reverse the reasoning. Architecture remains implementation freedom subject to matched-budget decision-sufficiency ablation.

## Why bounded adaptation, and what is not fixed

Calibration-out changes must be handled in three semantically different ways: retain calibrated authority when supported; adapt a containing set when unsupported but still inside the declared envelope; and withdraw outside it. A fixed envelope alone preserves feasibility but cannot recover the changed outcome relation or action ordering. Point fine-tuning can adapt means but cannot justify hard-margin authority. This selects bounded set-valued adaptation as the method class.

It does not uniquely select projected EW-RLS, a polytope, an ellipsoid or a reset rule. Set-valued MHE and multiple-model unions are equally admissible if they preserve containment, do not relax (\mathcal M_{\rm env}), fit the deadline and actually improve decision ordering. This is a target-derived mechanism, not a claimed Source migration.

## Why constrained finite-horizon optimization, but not necessarily RTI-SQP

The closest MPVIC work establishes that interaction-model prediction followed by online variable-impedance MPC is a viable robotics paradigm. R11's justification is narrower. Current gain decisions affect later force relaxation, slip/contact loss, gain-rate reachability and accumulated energy; normal and tangential gains share wrench, friction and actuator constraints; and the output must be a feasible applied action. A constrained receding-horizon relation expresses exactly these couplings. Offline schedules, unconstrained policy learning and NNBO need a second authority relation; a one-step optimizer is sufficient only when delayed effects do not change the current action.

RTI-SQP is merely a plausible implementation. The Method requires interruptible search, robust feasibility verification, atomic publication and measured deadline reserve. Any solver meeting that contract is acceptable.

## Baseline and closest-prior hierarchy

**Genealogy / original baseline:** NNBO. It is essential for showing the intended departure from a scalar trial-fixed Hunt–Crossley environment model and one-axis optimal interaction, but it is no longer the only or closest system-level prior.

**Closest structural priors:**

- Roveda et al. (2022), Q-learning-based MPVIC: learned interaction dynamics and MPC-based variable impedance for physical collaboration.
- Anand, Gravdahl and Abu-Dakka (2023), model-based variable impedance learning control: deep interaction modeling followed by predictive variable-impedance selection.
- Xue et al. (2025), safe MPVIC: online unknown-environment estimation, constrained variable-impedance prediction and passivity-aware interaction.

These works cover the broad model/identify-to-MPC paradigm. AuSoScan and finite-area contact MPC are closer for scanning/contact geometry. Accordingly, R11's scientific separation cannot be “learning + MPC” or “online environment estimation.” It is the joint relation among decision-sufficient finite-area/history outcomes, authority-correct in-contact adaptation to admitted calibration-out material/geometry/sliding/history changes, and a four-gain constraint-total scan action.

## Candidate-specific Method fit and Scientific Delta

**P — physical.** Most natural and simplest if the bounded constitutive family is sufficient. Scientific strength comes from making finite-area/history physical capacity decision-sufficient, online adaptive and action-authoritative rather than preidentified. It fails if model-form residual changes decisions.

**L — structured learned.** Most natural if the best physical family remains decision-misspecified and a stable bounded outcome state is learnable. Scientific strength comes from decision-targeted representation and authority-correct in-contact adaptation, not network novelty. It fails if physical-only is action-equivalent or learned containment is not credible.

**H — hybrid.** Most natural only under demonstrated complementarity: physical support materially reduces data/uncertainty and a smaller residual removes physical decision error. Scientific strength comes from the complementary uncertainty composition inside the applied-action relation. It fails if either simpler route is equivalent.

## Closure statement

R11 contains no core structure whose necessity is currently defended by implementation preference. It removes decision-gated excitation, EW-RLS, GRU/TCN and RTI-SQP from the scientific core; makes numerical horizon adaptive to decision relevance; retains the four-gain class as the minimal problem-defined action; and makes energy and deadline totality explicit because their removal re-exposes the accepted RMC.

The only unresolved scientific choice is the contact representation inductive bias. Because no accepted constraint or current Evidence eliminates its three credible classes, preserving P/L/H is the correct Method-fit conclusion and the proper input to `principle_human_selection`. Human Selection must choose which falsifiable representation claim the subsequent test cycle should evaluate; it must not enter Principle Test Design before that choice.
