# Human Selection Decision Brief: Paper-Level Method Shapes

Run: `impedance-control-landscape-e2e`

Design cycle: `MDCYCLE-SOFTSCAN-V2-R5-PAPER-SHAPE`

Formal review request: `b87e9df9eef546d684435106e04db4e6`

Reviewed packet candidate: `idea-stage/METHOD_DESIGN_PACKET.json`

Packet SHA-256 submitted for review: `d15917e3c7dbaa648b4c4654905548cdcb160b56ab56cb794731c542f47f22f1`

Independent Method Reviewer: `gpt-5.6-sol`

Verdict ID: `4151853f624d44a2b2b96e347c33dd3b`

Decision: `PRINCIPLE_PACKET_READY`

Controller state after acceptance: `PRINCIPLE_HUMAN_SELECTION`

## Status and scope

This document is Human Selection decision material. It explains the prospective paper-level Methodology and Contributions that could grow from each formal Candidate Principle. It is not `FINAL_METHOD_PACKET`, not a Principle Test Plan, and not evidence that any theorem, calibration property, real-time bound, constraint guarantee, or performance result has already been established. The authoritative scientific objects remain the Controller-bound Candidate packet and its independent review.

The supplied T-RO paper is used only as a maturity reference: a selectable method should expose its physical variables, computational objects, control relation, learning/calibration relation, theoretical conditions, algorithmic data flow, and contributions clearly enough that a later full Method section is foreseeable. Its DRL feedforward architecture and couch model are not copied into either Candidate.

## Accepted problem-to-method spine

The accepted research problem asks for online time-varying normal-tangential impedance selection during continuous soft-contact scanning under out-of-calibration material, geometry, sliding, speed and contact-history changes, while jointly optimizing force regulation, trajectory tracking, contact retention and slip and respecting wrench, interaction-energy, actuator and real-time constraints.

The accepted RCA leaves two mechanism changes:

1. Replace memoryless or prediction-oriented contact models with the smallest safely observable history state that preserves the action-conditional capacity information needed for robust impedance decisions.
2. Replace performance-first gain proposal followed by correction, timeout handling or override with one total applied-action relation in which performance, all hard limits and computation/fallback availability determine the command.

Both surviving Candidates share the first repair and differ only in the causal mechanism that makes the second relation total at every deadline.

## Shared methodological foundation

### Decision-sufficient contact history

At update \(k\), let \(h_k\) contain safely observable recent contact-frame motion, six-axis wrench, normal-tangential slip kinematics, local contact extent or geometry descriptors, dwell/loading history, interaction-energy ledger and previously applied gains. The encoder

\[
z_k = E_\phi(h_k)
\]

is not trained to reconstruct the material or terrain. Its fidelity is defined by whether two histories lead to the same robust feasible impedance set, the same gain ranking and the same joint force/trajectory/contact/slip outcome within a declared tolerance.

The online impedance action is

\[
a_k=(k_n,k_t,d_n,d_t),
\]

with positive normal-tangential stiffness and damping, hardware-compatible magnitude limits and gain-rate limits. The contact frame supplies the normal/tangential decomposition; the restricted action class is part of the research question and also makes online verification or certification structurally plausible.

### Action-conditional contact capacity

For a history state and a proposed impedance, the capacity relation

\[
\mathcal C_\theta(z_k,a_k)
\]

returns horizon-indexed lower-confidence margins rather than a point material model. Its margin vector covers:

- normal force regulation and overload headroom;
- contact wrench and joint-torque headroom;
- friction/slip reserve and contact-retention reserve;
- interaction-energy or passivity budget;
- actuator and gain-rate feasibility.

Training combines future-margin error with downstream robust impedance regret. This makes an error important when it changes feasibility, gain ranking or joint outcome, rather than merely when it increases state-prediction error.

### Calibration, support and abstention

Prediction/representation training, residual calibration and controller tuning use disjoint information. One-sided residual calibration is applied to the exact margin vector consumed by the controller on a predeclared population of in-envelope post-training episodes and probed impedance actions. The intended claim is population-level coverage under the stated sampling assumptions, not arbitrary-shift or pointwise conditional coverage.

Before observing the outcome, a fixed covariate-action applicability rule determines whether the residual law is supported for \((z_k,a_k)\). Supported pairs use the calibrated capacity set. Unsupported but still in-envelope pairs use an envelope-conservative physical margin set. This is not an external override: the conservative set parameterizes the same Candidate action relation.

## Candidate A: Capacity-Conditioned Feasible Incumbent

Formal identifier: `P-CAPACITY-CONDITIONED-FEASIBLE-INCUMBENT@3`

### Paper-level thesis

Continuous soft-contact impedance can be made deadline-total by combining a decision-calibrated action-capacity state with an anytime robust horizon program in which only a fully verified feasible incumbent is eligible to become the applied command.

The method has one causal chain:

\[
h_k
\rightarrow z_k
\rightarrow \mathcal C(z_k,A_k)
\rightarrow \text{robust horizon relation}
\rightarrow \text{full-sequence verification}
\rightarrow A_k^{\mathrm{inc}}
\rightarrow a_k^{\mathrm{applied}}.
\]

There is no independently generated nominal gain followed by a safety filter. A trial optimizer iterate is not a robot command.

### Robust impedance-horizon relation

The decision is a short impedance sequence

\[
A_k=(a_{k|k},\ldots,a_{k+H-1|k}).
\]

The horizon objective jointly represents force-regulation error, trajectory error, contact-loss risk and slip risk. The exact same sequence is constrained by worst-case calibrated capacity margins, wrench/torque limits, interaction-energy limits, actuator bounds and gain-rate limits.

A shifted previous incumbent, completed by the declared conservative terminal action, initializes the update. A numerical improvement is committed only after the full sequence is checked against the exact capacity/support binding and every hard margin. The verified sequence and its binding are stored atomically.

### Deadline semantics

At the update deadline:

- if a newly improved sequence has completed verification, it is the incumbent;
- if optimization times out, fails numerically or produces an unverifiable sequence, the last verified incumbent remains active;
- if support changes to unsupported, the conservative capacity relation is constructed before verification, so an incumbent bound to a stale supported set cannot be rendered;
- only the first action of the current verified incumbent is applied.

Under the fatal assumption that every declared update context admits an initialized and timely verified incumbent, more compute changes performance cost but not existence or declared feasibility of the command.

### Prospective Methodology organization

1. Contact-frame variable-impedance dynamics, decision variables and joint objective.
2. Decision-equivalent history encoder and action-capacity learner.
3. One-sided residual calibration, population/support definition and conservative unsupported relation.
4. Robust impedance-horizon program and exact full-sequence verifier.
5. Shifted initialization, atomic incumbent commit and deadline renderer.
6. Conditional statements for recursive feasibility, population-scoped capacity coverage and applied-action identity.

### Contributions

1. **Decision-sufficient action-capacity representation.** The method advances beyond environment reconstruction and memoryless gain tuning by learning only the history-dependent future margins that determine robust normal-tangential impedance feasibility and ranking.
2. **Support-aware calibration of controller-consumed margins.** It introduces population-scoped one-sided calibration for the nonlinear action-conditional margin vector and keeps unsupported pairs inside an envelope-conservative version of the same admissible-action relation.
3. **Verified-incumbent-only applied impedance.** It defines an anytime performance-feasibility relation whose hard constraints act on the same sequence being optimized and whose timeout/failure semantics preserve the identity of the applied feasible command.

### Source-Mechanism migration account

| Source | Mechanism actually migrated | Target adaptation | What it is not credited with |
|---|---|---|---|
| NeurIPS 2022 VES | Define representation fidelity by downstream value/policy effect | Robust impedance feasible-set, ranking and outcome equivalence replace Bellman equivalence | Calibration or contact control |
| Management Science 2021 SPO | Weight prediction errors by induced decision regret | Errors may alter nonlinear sequential feasibility as well as objective cost | Fixed-feasible-set assumptions are not retained |
| NeurIPS 2023 Predict-then-Calibrate | Disjoint residual calibration and optimizer-consumed uncertainty set | One-sided nonlinear, action-conditional constraint margins plus pre-outcome support handling | Joint feasibility, invariance or deadline totality |
| BOX-FDDP | Warm-started feasibility-driven update as a possible solver precedent | Add exact contact-capacity constraints, full verification, atomic incumbent commit and deadline return | It is not the Source of interruption-safe totality |

### Principal scientific risk

The method fails as a deadline-total relation if a robustly feasible incumbent cannot be initialized or reverified after a capacity/support change within the update budget. This is the central scientific vulnerability, not an implementation footnote.

## Candidate B: Capacity-Conditioned Viability Relation

Formal identifier: `P-CAPACITY-CONDITIONED-VIABILITY-RELATION@3`

### Paper-level thesis

Continuous soft-contact impedance can be made deadline-total by allowing the calibrated action-capacity state to parameterize one viability-certified admissible impedance relation that contains both the performance-selected command and a directly evaluable backup command.

Its single causal chain is

\[
h_k
\rightarrow z_k,\quad
s_k=(x_k,z_k,e_k,a_{k-1})
\rightarrow \mathcal A_v(s_k)
\rightarrow
\begin{cases}
a_k^{\mathrm{perf}}\in\mathcal A_v(s_k),\\
a_k^{\mathrm{backup}}\in\mathcal A_v(s_k)
\end{cases}
\rightarrow a_k^{\mathrm{applied}}.
\]

The method does not produce a nominal action outside the set and subsequently filter it.

### Capacity-conditioned viable impedance set

The augmented state contains measured robot/contact state \(x_k\), capacity state \(z_k\), remaining interaction-energy budget \(e_k\), and previous gains \(a_{k-1}\). The robust viable set contains states for which at least one bounded normal-tangential impedance keeps all current margins nonnegative and the worst-case successor inside the set.

At a viable state, the admissible action relation is conceptually

\[
\mathcal A_v(s_k)=
\left\{
a:
\underline m_i(s_k,a)\ge 0\ \forall i,
F(s_k,a,\mathcal C(z_k,a))\in\mathcal V_{\mathrm{cap}}
\right\},
\]

where the margins jointly encode contact/wrench, slip, energy, actuator and gain-rate requirements.

The performance selector minimizes the joint objective over \(\mathcal A_v\). The backup selector chooses, from that same low-dimensional set, the action maximizing the minimum certified margin with minimum gain change. The backup is therefore an internal selector of the scientific relation, not a second controller module.

### Deadline semantics

The backup selector is evaluated before or independently of interruptible performance optimization and stored with the exact capacity/support binding. A completed performance solution is used only if it belongs to the current \(\mathcal A_v\). Otherwise, timeout, infeasibility or incomplete optimization returns the stored same-set backup.

Unsupported context changes the capacity set that parameterizes \(\mathcal A_v\); it does not activate an unrelated guard. Conditional on a nonempty nonvacuous certified set and timely backup evaluation, the applied action remains a member of the one declared relation at every deadline.

### Prospective Methodology organization

1. Contact-frame action class and augmented contact-capacity-energy-gain state.
2. Decision-equivalent history encoder, capacity learner, calibration and support relation.
3. Capacity-conditioned robust viability/barrier relation and its conditional invariance semantics.
4. Low-dimensional admissible impedance set, performance selector and same-set maximum-margin backup selector.
5. Deadline renderer covering unsupported context and interrupted optimization.
6. Conditional statements for set nonemptiness, invariance, coverage and applied-action membership.

### Contributions

1. **Viability-defined decision-sufficient contact state.** Representation fidelity is tied to preservation of calibrated viable impedance sets and robust joint outcomes, rather than complete environment prediction.
2. **Support-aware calibrated certificate margins.** The method makes calibration/support semantics act directly on the nonlinear capacity margins used by the certificate, with unsupported pairs mapped to an internal conservative certified relation.
3. **One performance-and-backup impedance relation.** It removes nominal-policy/safety-override separation by making force/trajectory performance, contact/slip, wrench, energy, actuator, gain-rate and computation availability jointly determine membership in the applied-action set.

### Source-Mechanism migration account

| Source | Mechanism actually migrated | Target adaptation | Boundary |
|---|---|---|---|
| NeurIPS 2022 VES | Downstream-decision equivalence | Equality of viable sets, impedance selections and joint outcomes | Does not supply certificates or calibration |
| Management Science 2021 SPO | Decision-regret learning | Loss includes viable-set membership, backup availability and robust outcome | Source assumes a fixed known feasible set |
| NeurIPS 2023 Predict-then-Calibrate | Independent residual calibration | Nonlinear action-conditional certificate margins and support-aware replacement | Does not establish invariance or totality |
| TAC 2019 HJ safety framework and SCBF-MPC evidence | Controlled-invariance and predictive barrier mathematics as formal basis | Capacity, energy and previous-gain states enter the certificate; performance and backup are generated inside one relation | Not claimed as a full Source migration because the HJ nominal/override intervention and Target relation are not isomorphic |

### Principal scientific risk

The method fails if the capacity-conditioned viable set is empty, too conservative to retain the required joint performance envelope, invalid under the learned transition relation, or too expensive for direct backup evaluation. Its conceptual unity is stronger than Candidate A, but so is its certificate-feasibility burden.

## Human choice: causal difference

| Decision dimension | Candidate A: feasible incumbent | Candidate B: viability relation |
|---|---|---|
| Source of deadline totality | Persistence and full verification of a feasible horizon policy | Nonempty certified action set and directly evaluable same-set backup |
| Cold-start dependence | Requires an initial feasible incumbent | Can tolerate optimizer cold start if the viable set and backup exist |
| Performance representation | Explicit receding-horizon sequence | Current certified set with performance selector; may embed shorter prediction |
| Main conservatism source | Robust horizon margins and terminal completion | Viability/certificate set itself |
| Main computational risk | Full-sequence verification before deadline | Certificate construction and backup evaluation |
| Main scientific killer | Any declared update has no timely verified incumbent | Any declared state has no useful timely certified action |
| Closest-prior pressure | Real-time constrained MPC and warm-started feasibility updates | SCBF/HJ safe-set control and energy-aware predictive impedance |
| Distinct proposed delta | Applied command can only descend from an atomically committed feasible incumbent | Performance and backup are co-members of one capacity-conditioned viable impedance relation |

The two Candidates should not be combined merely to hedge failure: using a viability backup for an incumbent controller would create a two-mechanism stack unless a later mechanism-level synthesis proves that both are necessary parts of one relation. Human Selection should therefore choose the totality mechanism whose fatal assumption is scientifically worth testing first, or explicitly request a new synthesis with a causal necessity argument.
