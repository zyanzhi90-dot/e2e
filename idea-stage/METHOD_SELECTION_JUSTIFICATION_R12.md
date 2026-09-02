# R12 Method Selection Justification

## Overall decision

R12 selects **H-CATI**, the hereditary physical contact-capacity set plus total robust four-gain impedance relation, as the only surviving Method-design Candidate.

The fixed reference is accepted Problem `P-SOFTSCAN-STATE-01@2`, residual failure `RF-HELDOUT-JOINT-IMPEDANCE-REGRET`, RCA chains `CHAIN-CONTACT-STATE-TO-ACTION-ALIASING` and `CHAIN-GAIN-SELECTION-TO-FEASIBILITY-DECOUPLING`, and the two accepted RMCs. The method-fit decision does not alter those premises. Every decisive choice is judged by Causal Match, Problem Closure, Assumption–Target Fit, Simpler-Rival Necessity and Target Feasibility; a hard failure cannot be offset by novelty.

## Decisive choice 1 — contact representation

**Required change.** Histories and finite-area contacts that imply different future wrench/slip/contact-loss margins and four-gain decisions must not be aliased.

**Major candidates.** (a) calibrated memoryless HC/Kelvin-type model; (b) richer hereditary finite-area physical model; (c) structured learned predictive outcome model; (d) physical model plus learned residual.

The memoryless route fails Causal Match whenever matched instantaneous indentation/rate produces different relaxation or pre-slip capacity. `Ubx3Xkv4y2kJ` directly shows that finite-area viscoelastic memory changes normal force, contact area, pressure and tangential/moment limit surfaces. `_8NXx8-VkcgJ` also prevents an overclaim: HC can fit some soft-tissue transients well, so the Method does not assume HC is universally wrong; history/area coordinates survive only when they change the downstream action.

The learned route is feasible, and `imoj6X2imAgJ` demonstrates learned-model-to-MPVIC. Its advantage is not automatic calibration-out generalization. It adds dependence on calibration coverage, learned-state extrapolation and uncertainty calibration. The hybrid adds those assumptions plus physical/residual uncertainty composition. Current Evidence establishes neither physical-family noncontainment nor a persistent residual after the strongest bounded physical adaptation. Therefore learned and hybrid routes fail Simpler-Rival Necessity at Method selection: they solve no accepted residual MUST not already addressed by the bounded physical family.

The selected physical route uses only effective states with direct decision roles: nonnegative fading normal memory, patch/curvature proxy, tangential bristle/pre-slip, and load/area-dependent force-moment capacity. It is the most direct and least assumptive route under a bounded operating envelope. A persistent action-changing residual is an explicit killer that returns the choice to method_design; it is not pre-emptively patched with a neural module.

**Selection:** decision-pruned hereditary finite-area physical capacity.

## Decisive choice 2 — temporal state

Instantaneous contact variables fail the accepted history-aliasing RCA. Raw histories or a generic GRU/TCN can retain history but do not define which distinctions matter. A full viscoelastic field/FEM state is not safely identifiable or real-time necessary.

The selected state is a nonnegative generalized-Maxwell bank plus a bounded LuGre-type pre-slip state. These coordinates are causal, recursively updatable, have direct relaxation/friction semantics, and expose bounded parameters for robust prediction. The number of modes is not chosen by RMSE: the smallest bank preserving robust feasible sets and action ordering is retained. This fixes the scientific state while allowing only numerical order/relaxation constants to be identified from data.

**Selection:** explicit stable physical memory, pruned by downstream action equivalence.

## Decisive choice 3 — calibration-out adaptation

Fixed robust operation preserves limits but does not recover useful action ordering after a new in-envelope condition. Projected EW-RLS or a Gaussian point posterior updates a mean but supplies no containing authority for hard decisions. Multiple-model banks add discrete-mode assumptions; full Bayesian or set-MHE methods add trajectory/posterior state and computation not required when effective parameters enter the margin relation affinely.

Bounded set-membership directly performs the required change: it propagates admitted drift and intersects action/outcome consistency, so the uncertainty object consumed by robust selection changes after observed contact. A fixed-facet H-polytope is selected over ellipsoidal or zonotopic freedom because the local effective-parameter relation yields half-space consistency strips, intersections are direct, and fixed facets bound WCET. A single reset to `Theta_env` handles abrupt admitted changes; repeated inconsistency withdraws. `bRrwxfzJq_QJ` supports bounded time-varying set propagation in adaptive robust MPC, while `HUMAN-NEURIPS-2023-PREDICT-CALIBRATE` supports aligning uncertainty with downstream robust decisions. The adaptation does not identify material labels and never relaxes the physical envelope.

**Selection:** fixed-facet drift/reset set membership; set-MHE and multiple models remain strongest implementation-level rivals and must be matched in later evidence.

## Decisive choice 4 — impedance action class

Normal-only or scalar impedance cannot close a problem whose outcome includes tangential tracking and slip. A full SPD six-dimensional impedance and reference co-optimization is richer but adds poorly identifiable cross-couplings and a different task scope. In the moving contact frame, normal/tangential mechanics are represented in the predictor and coupled through finite-area wrench, energy and actuator constraints; four diagonal gains are the minimum action that can independently regulate normal force and tangential compliance.

**Selection:** positive `(k_n,d_n,k_t,d_t)`. Richer actions are admitted only if matched counterfactual optimization proves non-equivalence.

## Decisive choice 5 — decision mechanism and horizon

NNBO/HJB, direct learned policies and analytic schedules can optimize performance, but explicit composition of future force, contact, energy, actuator and gain-rate constraints would require them to encode the same predictive feasible relation implicitly. MPC is therefore selected for its causal match, not because it is newer. The 2022/2023/2025 MPVIC papers establish the practicality of interaction-model-to-predictive-impedance optimization; they do not supply R12's novelty.

A one-step constrained optimizer is the strongest simple rival. It passes only if all delayed states are action-equivalent. Relaxation, pre-slip, gain-rate reachability and accumulated energy can change the current feasible action beyond one update. R12 therefore uses the shortest horizon covering only delayed states that change the current decision, and collapses to `H=1` if equivalence holds.

RTI-SQP is specified for implementation because the chosen state is smooth, low dimensional and nonlinear, and completed iterates can be verified under a bounded WCET. Another solver would not change the scientific relation; the required Method choice is robust receding-horizon selection, not the solver brand.

**Selection:** shortest decision-relevant robust MPC horizon, concretely realized by warm-started multiple-shooting RTI-SQP.

## Decisive choice 6 — interaction energy

Positive gains and gain-rate bounds do not account for energy introduced by changing stiffness, a moving reference, delay or inner-loop mismatch. A post-hoc passivity controller can restore passivity but may replace the optimized gain, recreating accepted selection-to-realization decoupling. Purely passive parameterizations can exclude useful actions despite an available finite energy budget.

The minimum causal repair is a measured discrete energy-budget state inside prediction and robust feasibility. It reserves bounded unmodeled work and removes an action before selection if the energy margin is insufficient. The rendered gain is unchanged.

**Selection:** optimizer-consumed energy state; no downstream gain projection.

## Decisive choice 7 — infeasibility and deadline totality

Action hold and shifted incumbents are not automatically feasible after contact/support changes. A faster solver reduces timeout probability but does not make the action relation total. Full online viability over robot, contact-set and energy state is stronger and more expensive than required if unloading is available.

Within a declared withdrawal envelope, tangential stop plus rate-limited normal unloading is the simplest universally registered member. It is verified before optional optimization, and only fully checked scan actions are atomically published. Hence timeout or infeasibility affects cost, not action existence. `HUMAN-TAC-2019-GENERAL-SAFETY-FRAMEWORK` and the anytime-feasibility Evidence support the source mechanism; H-CATI adapts it to a gain/reference tuple whose energy and actuator transitions are checked before publication.

**Selection:** one verified `SCAN/WITHDRAW` relation. Feasible-incumbent bookkeeping is internal; viability becomes necessary only if withdrawal availability is falsified.

## Complete-method reintegration

The choices form one computational chain:

\[
\text{history/patch/wrench}
\rightarrow \Theta_k\text{-valued hereditary capacity}
\rightarrow \text{robust future margins for four gains}
\rightarrow \mathcal U_k^{total}
\rightarrow \text{unchanged applied gain or withdrawal}.
\]

No selected mechanism duplicates another. The physical state determines what is predicted; set membership determines which outcomes are authoritative; the horizon connects delayed state to the current gain; integrated constraints determine which gain is feasible; total publication determines which feasible member is applied. Removing any one re-exposes a named accepted failure.

## Baselines and closest prior

- **Genealogy:** NNBO, because the project begins from scalar HC environment identification followed by optimal impedance.
- **Closest structural prior:** 2025 adaptive/passivity-aware MPVIC, with 2022 Q-LMPVIC and 2023 deep MPVIC defining the broader model/learn-to-MPC family.
- **Closest contact-mechanics prior:** finite-area deformable-contact MPC (`ZDySGaNsMn8J`), which pre-estimates properties and leaves impedance predefined.

The scientific delta is not the conjunction “richer model + adaptation + MPC.” It is the intervention-level relation in which a decision-pruned hereditary finite-area contact-capacity **set** is adapted during contact and directly authorizes the same normal/tangential gain that satisfies accumulated energy, actuator and deadline-total applied-action constraints.

## Joint three-part verdict

### Problem fit — PASS

The hereditary capacity state repairs the accepted observation-history/contact-capacity aliasing. In-envelope set adaptation changes the prediction and gain ordering after calibration-out contact. Robust four-gain selection addresses the requested joint force/path/slip/contact objective, and the total relation addresses selection-to-realization decoupling.

### Method fit / necessity — PASS

Every decisive choice has a named strongest simple rival and a condition under which that rival would be equivalent. The selected physical route wins because no Evidence-supported residual currently necessitates learned or hybrid complexity; state, adaptation, horizon, energy and totality are retained only where their removal re-exposes a specific RMC failure. Numerical order, weights and tolerances remain experimental parameters, not unclosed Method choices.

### Scientific strength — PASS at Method-design level

H-CATI establishes a falsifiable scientific object beyond existing model-to-MPVIC: a hereditary finite-area capacity set defined by downstream robust action equivalence, an in-contact containing adaptation law, and a constraint/energy/deadline-total applied impedance relation. The Method is mathematically specified, implementable, analyzable and directly testable against its closest priors. This PASS does not assert empirical superiority, completed theorems or final literature novelty.

## Critical technical risks and next-stage evidence

1. Physical-family containment may fail; this kills the selected representation and reopens learned/hybrid Method design.
2. Safe scan data may not contract decision-relevant parameter directions before action relevance expires; this kills the adaptation claim.
3. Fixed-facet robust propagation may be too conservative or exceed WCET; matched set-MHE/zonotope alternatives must distinguish representation from implementation failure.
4. Four gains may be insufficient on strongly curved/an-isotropic contact; a full SPD/reference rival must be matched.
5. Energy accounting may omit inner-loop or reference work; this kills energy authority.
6. Withdrawal may not monotonically release contact throughout the declared geometry envelope; this kills deadline totality and may force a narrower envelope or viability mechanism.

These are explicit falsifiers for later Principle testing. They are not reported as already validated.
