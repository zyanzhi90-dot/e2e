# R10 Method Selection Justification — Problem fit, Method fit / necessity, and Scientific strength

## Decision and fixed scientific context

**Decision:** select the R10 structured-hybrid, support-indexed, total-action method as the only surviving Method-design route. This is a Method-design conclusion, not a claim that its empirical performance or final literature novelty has already been established.

The comparison holds fixed accepted Problem `P-SOFTSCAN-STATE-01@2`, residual failure `RF-HELDOUT-JOINT-IMPEDANCE-REGRET`, RCA chains `CHAIN-CONTACT-STATE-TO-ACTION-ALIASING` and `CHAIN-GAIN-SELECTION-TO-FEASIBILITY-DECOUPLING`, both accepted RMCs, the four target gains, and the declared material/geometry/sliding/history, interaction-energy, actuator and update-time envelope. A route fails if any of five hard criteria fails: **Causal Match, Problem Closure, Assumption–Target Fit, Simpler-Rival Necessity, Target Feasibility**. No weighted score can compensate for a failure.

The accepted failure is not generic contact-model error. It is **decision aliasing**: observation histories that look equivalent to a calibration-fixed model can imply different future wrench/contact capacity and different feasible or optimal impedance. The second failure is not merely slow optimization. It is **realized-action decoupling**: performance gains can be altered, rejected or delayed by physical and computational checks that were absent from the relation which selected them.

## Decisive-choice inventory

| ID | Decisive choice | Why it changes the scientific relation |
|---|---|---|
| C1 | physical, learned, or structured-hybrid contact representation | determines which cross-condition distinctions and invariants reach impedance selection |
| C2 | temporal-state semantics | determines whether loading history and pre-slip are represented or aliased |
| C3 | uncertainty authority after support loss | determines which relation may authorize an applied scan action |
| C4 | calibration-out adaptation | determines whether changes are repaired, merely detected, or conservatively refused |
| C5 | impedance action class | determines the decision space and minimum sufficient contact information |
| C6 | instantaneous versus multistep selection | determines whether delayed relaxation, slip, rate and energy effects enter the current action |
| C7 | passive versus decision-gated information acquisition | determines whether unresolved decision ambiguity can be reduced without leaving the hard relation |
| C8 | passivity/energy mechanism | determines whether gain-change energy is optimized or corrected after selection |
| C9 | applied-action totality | determines whether timeout and infeasibility are members of the same scientific relation |

GRU versus TCN versus a stable state-space realization, ellipsoid versus zonotope, RTI-SQP versus another interruptible nonlinear solver, numerical horizon length, network width and filtering constants are **implementation choices**. They do not alter an RMC, operating envelope, hard-constraint semantics or scientific claim and therefore are not promoted into Method-level necessity claims.

## C1 — Why a structured hybrid contact operator

### Mechanism candidates

1. **Richer physical-only model:** finite-area viscoelastic normal contact plus friction/limit-surface tangential mechanics, with online parameter identification.
2. **Pure learned predictive model:** a causal neural transition or outcome model trained across contact conditions.
3. **Structured hybrid:** an explicit finite-area, fading-memory and tangential-capacity skeleton plus a bounded learned residual that is trained and adapted only for decision-relevant outcome/margin error.

The strongest simple rival is (1). It directly improves on NNBO's scalar Hunt–Crossley contact and retains interpretability. Finite-area viscoelastic work shows why patch pressure, relaxation and frictional moment capacity matter (`Ubx3Xkv4y2kJ`); deformable-contact MPC shows that such mechanics can support real-time prediction when geometry and material are known (`ZDySGaNsMn8J`). But this rival hard-fails **Problem Closure** and **Assumption–Target Fit**: those models require a specified geometry/material law or pre-estimated properties, while the accepted envelope includes held-out material, patch geometry, sliding and contact history. The tissue-model comparison (`_8NXx8-VkcgJ`) further shows the observability limitation: only the most sensitive stiffness parameter was adapted online because ordinary contact did not sufficiently excite all viscoelastic parameters. Adding more physical parameters therefore does not make them identifiable during safe continuous scanning.

The pure learned rival addresses model mismatch, and deep MPVIC (`imoj6X2imAgJ`) demonstrates that a probabilistic ensemble can support online stiffness MPC across objectives. It nevertheless hard-fails **Assumption–Target Fit** and **Problem Closure** for this problem: its learned relation is free-space, unconstrained, offline/fine-tuned, translational-stiffness-only and deployed at 5 Hz; it does not carry finite-area contact capacity, in-contact shift adaptation, hard interaction energy or a total applied-action guarantee. More generally, a black-box state selected by average prediction error has no reason to preserve no-contact, compressive-force, dissipative-friction and finite-area capacity structure under sparse held-out contact data.

The structured hybrid passes all five criteria. It does not combine two already sufficient methods. The physical class leaves an unavoidable residual under the accepted shifts; the learned class has an avoidable structural and data burden. The hybrid retains only the physical state needed for the decision—contact frame/patch proxy, stable relaxation modes and tangential pre-slip/capacity—and learns only the remaining H-step margin/outcome residual. This is also consistent with evidence that end-to-end physical-plus-recurrent residual models outperform either a two-step hybrid or a black-box model under compliant low-gain control (`NmJOT6QG-tUJ`), while its free-space boundary is explicitly not transferred as contact evidence.

**Selection:** structured hybrid. A physical-only or pure learned model remains a falsifying rival, not an ablation added for completeness.

## C2 — What history representation is scientifically required

The problem requires a state that distinguishes histories only when they change the feasible four-gain set or its ordering. Three classes were compared: instantaneous physical variables; a generic unrestricted recurrent latent; and explicit stable contact-memory coordinates plus a bounded residual causal state.

Instantaneous variables hard-fail **Causal Match** because the accepted RCA already identifies paired histories with different future capacity. A generic recurrent latent can represent history but fails **Simpler-Rival Necessity**: no Evidence distinguishes GRU from TCN or another causal realization, and an architecture label provides neither the state semantics nor minimum sufficiency.

R10 therefore fixes the scientific state semantics, not the architecture: nonnegative stable relaxation modes summarize normal fading memory; a bounded pre-slip/capacity state summarizes tangential loading; measured patch/curvature proxies and the energy state are explicit; a residual realization is retained only if its removal changes a hard margin or impedance ranking. The state is trained with multistep outcome, active-margin and decision-regret losses, and pruned by feasible-set/action equivalence. GRU, TCN or a stable state-space model may implement the residual contract.

**Selection:** explicit physical memory plus decision-pruned residual state; architecture remains free.

## C3 — Why support-indexed authority rather than one uncertainty treatment

Candidates were: a single calibrated tube everywhere; one worst-case envelope everywhere; and a state-indexed authority with `SUPPORTED`, `ADAPTING-IN-ENVELOPE`, and `WITHDRAWAL`.

A single calibrated tube hard-fails **Assumption–Target Fit** after calibration support is lost. An all-envelope robust controller is the strongest simpler rival and can protect hard limits, but hard-fails **Problem Closure** for the joint performance problem when it cannot use supported sharpness or update the decision under an encountered change. The state-indexed relation is minimal because the validity assumptions genuinely differ.

`SUPPORTED` uses an independently calibrated episode-level simultaneous one-sided tube, gated only by a pre-outcome support test. `ADAPTING-IN-ENVELOPE` always enforces an independently valid envelope action set `A_env`; the online residual set may shape nominal cost, information value and add restrictions, but it cannot relax `A_env`. `WITHDRAWAL` applies when the envelope, representation or action set is invalid. After support loss, R10 forbids mid-episode re-entry to the calibrated authority. A sharper supported relation may be established only for a later episode through a predeclared independent delayed audit.

**Selection:** three-state authority. Its extra state is necessitated by different validity assumptions, not by software organization.

## C4 — Why drift-expanded, change-triggered set adaptation

Candidates were: fixed robust operation with no identification; projected EW-RLS or a single Gaussian posterior; moving-horizon/Bayesian latent identification; multiple-model banks; and bounded set membership with gradual-drift propagation plus one innovation-triggered reset.

Fixed robust operation is simpler but hard-fails **Problem Closure** because it cannot change the predicted optimum when a new material or patch is observed. A single EW-RLS estimate is computationally attractive but hard-fails **Causal Match** for abrupt material/geometry transitions and supplies no containment relation for hard decisions. Full Bayesian/MHE and multiple-model banks can express richer changes but fail **Simpler-Rival Necessity**: the accepted Method needs a bounded decision residual, not material identity or a posterior over every constitutive mode.

Set-membership adaptive MPC literature establishes the relevant mechanism: bounded time-varying uncertainty is propagated by an admitted rate and intersected with observations, while robust constraints are maintained; fixed-complexity set approximations make the update tractable. R10 adds only the residual needed by the target: if the bounded-drift continuation is falsified but the observation remains inside the declared envelope, reset once to `Theta_env`, intersect the current consistency strip, and continue. A second inconsistency or empty set withdraws. The set center is not a second estimator; it is a deterministic nominal representative used only for cost.

This mechanism actually handles calibration-out change: it alters future-outcome prediction and impedance ordering during the same scan, can invalidate additional actions, and may select a different envelope-safe action. It does not claim arbitrary-shift coverage or use estimator confidence to weaken `A_env`.

**Selection:** drift-expanded set membership with one change-triggered envelope reset. Projected EW-RLS is removed from the Method core.

## C5 — Why four contact-frame gains

Candidates were fixed scalar/normal impedance, four diagonal contact-frame gains `(k_n,d_n,k_t,d_t)`, a full 6-D coupled SPD impedance, and joint reference-plus-impedance co-optimization.

Scalar/normal impedance hard-fails **Problem Closure** because the target joint outcome contains tangential path error and slip. A full SPD matrix and reference co-optimization fail **Simpler-Rival Necessity**: the moving contact frame captures the dominant normal/tangential separation; fixed lateral/rotational gains can still be included in wrench and actuator checks; and the accepted problem supplies the scan/force reference rather than asking the Method to redesign it. Cross-coupling effects are predicted by the contact operator even when off-diagonal gains are not action variables.

**Selection:** four positive diagonal contact-frame gains. Expand the action class only if this class is falsified by action-equivalence analysis.

## C6 — Why short-horizon MPC, but not why RTI-SQP

The MPVIC family establishes that model/interaction prediction followed by MPC is a workable variable-impedance paradigm. The 2022 Q-LMPVIC uses a learned HRI model and predictive impedance optimization; 2023 deep MPVIC reuses a learned probabilistic Cartesian model across task costs; 2025 MPVIC identifies a nonlinear Hunt–Crossley environment online, optimizes variable impedance with constraints, and adds passivity via an energy tank. These precedents prove usability, not necessity for R10.

The strongest simpler rival is a one-step constrained gain program, supported by existing one-step impedance MPC and analytic constrained gain planners (`xlNjdqnC2fMJ`, `USER-TRO-2022-3216078`). It passes when all decision-relevant consequences are instantaneous. It hard-fails **Causal Match** under the accepted RCA because relaxation, pre-slip/contact loss, gain-rate reachability and energy accumulation can make two current actions equivalent at the next sample but non-equivalent before the state can be corrected. A static policy or model-free direct gain law also cannot explicitly compose multiple future hard constraints without encoding the same predictive relation implicitly.

R10 therefore uses the shortest horizon covering the identified dominant contact-memory/resource interval. The horizon is scientifically required; RTI-SQP is not. Any interruptible solver may be used if it preserves the verification and timing contract.

**Selection:** short multistep robust receding-horizon gain selection; solver implementation free.

## C7 — Why information is a feasible tie-breaker, not a reward module

Passive adaptation is the strongest simpler route and is sufficient whenever normal scan motion excites every decision-relevant residual direction. Unrestricted exploration hard-fails **Assumption–Target Fit** because it can sacrifice contact safety or task quality. A weighted information bonus also lacks necessity because its trade-off weight can reverse the primary objective.

R10 activates information only when current residual hypotheses induce different feasible decisions and a robustly feasible action can distinguish them. Among actions within `epsilon` of the robust task optimum, it lexicographically maximizes worst-case decision separation. If no such action exists, the method remains robust or withdraws; it does not claim recovery. This is the smallest repair for the accepted possibility that passively safe observations are insufficient before decision relevance expires.

**Selection:** conditional lexicographic, authority-feasible decision disambiguation; otherwise passive operation.

## C8 — Why an optimizer-consumed energy state

Positive gains and gain-rate limits do not bound energy injected by time-varying stiffness. A post-hoc passivity observer/controller can correct the action, but hard-fails **Causal Match** for the second RMC because the realized gain differs from the optimized gain. Passive parameterizations are possible, but can unnecessarily exclude useful nonpassive instantaneous actions that are feasible under a finite interaction-energy budget.

An energy tank/budget state inside the predictive relation directly constrains cumulative gain-change, reference, delay and actuator work. It is supported by constrained variable-impedance MPC precedent (`xlNjdqnC2fMJ`) and the 2025 MPVIC, but is not claimed as a standalone contribution.

**Selection:** optimizer-consumed discrete interaction-energy state; no post-hoc gain correction.

## C9 — Why a total scan/withdraw relation

Candidates were post-hoc guards, persistent feasible incumbents, online viability/barrier sets, and a total action set containing verified scan actions plus a preverified withdrawal action.

Post-hoc guards hard-fail **Causal Match**. Feasible incumbents are useful solver bookkeeping but a shifted previous action can become infeasible. High-dimensional online viability is scientifically stronger than required and can be intractable for the hybrid learned contact state. Inside the declared withdrawal envelope, normal unloading and tangential stopping provide a simpler universally available member. Atomic publication of completed scan verification plus that member makes optimizer convergence affect performance, not action existence.

**Selection:** one total `SCAN/WITHDRAW` relation. Incumbent publishing is an implementation mechanism; full viability is unnecessary unless withdrawal totality is falsified.

## Closest-prior and baseline determination

NNBO (`j4KJ1EdUyYAJ`) remains the **genealogical baseline** because R10 begins from its contact-model-to-optimal-impedance relation and changes every target-relevant limitation: scalar point contact becomes structured finite-area/history state; trial-fixed identification becomes support-indexed online adaptation; one-axis optimal interaction becomes four-gain normal–tangential multistep selection; matched uncertainty and unconstrained realization become calibrated/envelope uncertainty and one total hard-constrained action relation.

The **closest structural priors** are the MPVIC family, not NNBO alone. R10 inherits the model-to-predictive-impedance-control paradigm, then addresses what that family does not jointly solve: decision-sufficient soft-contact memory, held-out material/geometry/sliding/history adaptation with valid authority, coupled normal–tangential gains, simultaneous wrench/contact/energy/actuator constraints, and deadline-total applied action. AuSoScan/deformable-contact MPC are complementary closest priors for finite-area scanning mechanics but assume calibrated properties or do not select impedance.

Accordingly, experiments must include both genealogy and structural baselines: NNBO; the strongest model-agnostic wrench-feedback/admittance baseline (`USER-TASE-2023-3282974`); representative one-step/constrained MPVIC; deep MPVIC; 2025 adaptive/passive MPVIC; and finite-area scan MPC where the action spaces can be matched.

## Joint convergence judgment

### Problem fit — PASS

Every core relation maps directly to an accepted failure: structured memory repairs state aliasing; support-indexed set adaptation handles admitted calibration-out changes; multistep four-gain selection addresses joint normal/tangential performance; integrated margins and total fallback repair realized-action decoupling. No core module serves only generic accuracy or novelty.

### Method fit / necessity — PASS

All nine decisive choices pass the five hard criteria. The strongest simple rivals are retained explicitly, and each loses on a specific accepted condition. Unjustified specificity has been removed: network, point-estimator, solver and set-shape choices are free; mid-episode conformal re-entry and unrestricted information bonuses are deleted; full 6-D gains and viability certification are not added.

### Scientific strength — PASS at Method-design level

The Method creates a nontrivial scientific object: a structured hybrid contact state defined by downstream robust action equivalence, a validity-indexed adaptation relation, and a short-horizon total applied-action map. It supports falsifiable representation-minimality, set-containment/authority, delayed-decision and deadline-totality claims; contains explicit assumptions and killer criteria; and is concrete enough to implement and test. This PASS does not assert empirical success, theorem completion, or final literature novelty; those remain for Principle testing, evaluation and later novelty review.

## Known technical risks that remain validation questions, not open Method choices

1. The declared physical/residual envelope may be too narrow for useful held-out adaptation or too broad for nonvacuous `A_env`.
2. Safe scan excitation may not identify decision-relevant residual directions before the action must change.
3. The selected horizon may not fit the measured worst-case computation budget after robust propagation.
4. Conservative finite-area/friction/moment bounds may make continuous scanning unavailable even when withdrawal remains feasible.
5. Energy accounting may miss reference or inner-loop work; this would invalidate the interaction-energy claim.
6. Withdrawal may not monotonically release contact on every admitted geometry; this is a fatal envelope assumption.

These risks have explicit falsification consequences. None justifies adding another Method module before evidence is collected.
