# R11 complete Method synthesis for Human Selection

Status: non-final Method Design decision material. It is not a `FINAL_METHOD_PACKET`, contains no experimental result, and does not authorize Principle Test Design before Human Selection.

## 1. Scientific route that all surviving Candidates share

R11 does not begin by choosing a neural network. It begins from the accepted failure: continuous soft scanning loses distinctions in finite-area geometry, tangential capacity and fading contact history that change the feasible or preferable normal–tangential impedance; after calibration support is lost, a detector or a conservative refusal does not solve the requested online adaptation; and a performance gain later modified by a guard is not the applied action selected by the Method.

The minimal common route is therefore

\[
h_k \longrightarrow \mathcal Y_{k:k+H}(h_k,a_{k:k+H-1};\Theta_k,q_k)
\longrightarrow \mathcal U_{\rm total}(h_k,\Theta_k,q_k)
\longrightarrow a_k^{\rm applied},
\]

where (h_k) is safely observable contact history, (a_k=(\log k_n,\log d_n,\log k_t,\log d_t)), (\mathcal Y) is an action-conditional set of future controller-consumed outcomes, (q_k\) is the support state, and (\mathcal U_{\rm total}\) is the single scan-or-withdraw action relation. The scientific state is defined by decision sufficiency: two histories are equivalent only when every admissible gain probe produces the same robust feasible set and cost ordering within a declared tolerance. Force reconstruction that does not change either is not retained.

The evidence fixes this relation, but does not yet fix the best inductive bias for (\mathcal Y). R11 therefore preserves three complete and directly comparable realizations: bounded structured physical, bounded structured learned, and bounded physical–residual hybrid. GRU versus TCN, set polytope versus ellipsoid, RTI-SQP versus another interruptible constrained solver, and an exact numerical horizon are implementations, not Contributions.

## 2. Common support-indexed adaptation and authority

At every update the Method is in exactly one state.

**SUPPORTED.** A pre-outcome context–action support rule admits the independent calibration population. A simultaneous one-sided tube for the exact force, path, slip/contact-loss, energy and actuator margins used by the optimizer is applied. The guarantee is population-relative; no arbitrary-shift claim is made.

**ADAPTING-IN-ENVELOPE.** Support has failed, but observable context and innovation remain inside a declared physical/residual envelope. Let (\Theta_k^-\) be the previous containing set propagated by the declared drift/change model and (\mathcal C(y_k,h_k,a_{k-1})\) the parameters or latent coefficients consistent with the new observation. The update contract is

\[
\Theta_k \supseteq (\Theta_k^-\cap\mathcal C_k), \qquad
\Theta_k\subseteq\Theta_{\rm env}.
\]

A fixed-complexity sound outer approximation may implement this contract. A nominal center may shape cost, but robust scan feasibility is imposed for every member of the independently valid envelope-containing outcome set. Posterior belief can add restrictions; it cannot shrink the authority set in a way that relaxes an envelope constraint. The Method is adaptive only if successive observations change predicted outcomes or action ordering and recover useful performance before the changed condition loses relevance. No information-seeking impedance perturbation is part of the core Method: passive scanning data are used; if they are insufficient, robustness or withdrawal is honest and simpler.

**OUTSIDE-ENVELOPE/WITHDRAWAL.** Set inconsistency outside the admitted envelope, loss of a trustworthy normal/contact frame, or absence of a robust scan action activates a pre-registered withdrawal member. It stops tangential reference motion, moves gains within rate and actuator limits to a dissipative setting, and unloads along the last trusted normal. The accepted operating envelope must make this action feasible and monotonically contact-releasing. Otherwise the Method claim is invalid for that state rather than silently delegated to an unspecified safety layer.

The exact online estimator is not elevated into a Principle. Set membership, set-valued moving-horizon estimation or a multiple-model union are acceptable only if they maintain the same containment and deadline contracts. Point EW-RLS, Bayesian means or conformal scores alone are insufficient authority.

## 3. Common constrained impedance decision

For a candidate gain sequence (A=(a_k,\ldots,a_{k+H-1})), each representation produces an outcome set containing normal force, tangential tracking, slip/contact state, finite-area wrench/moment capacity, actuator load and interaction power. (H) is the shortest interval over which measured relaxation, pre-slip evolution, gain-rate or energy accumulation can change the current action; (H=1) is retained when it is decision-equivalent. Thus MPC is used for delayed coupling, not by convention.

The scan member solves

\[
\min_A\; \max_{Y\in\mathcal Y(A)}
 \sum_{i=0}^{H-1}\bigl(
 w_f\|F_{n,i}-F_n^*\|^2+w_x\|e_{t,i}\|^2
 +w_s\ell_{\rm slip/loss}(Y_i)+w_a\|\Delta a_i\|^2\bigr)
\]

subject, for every authoritative outcome, to contact retention, friction and finite-patch moment capacity, force/torque bounds, joint torque/velocity, gain magnitude/rate and the discrete interaction-energy balance

\[
E_{i+1}=E_i+hP_{\rm diss}(a_i)-hP_{\rm inj}(a_i,a_{i-1},r_i)-E_{\rm reserve},
\qquad E_i\ge E_{\min}.
\]

Energy is optimizer-consumed state, not a later passivity correction. Four diagonal contact-frame gains are the smallest action class matching the accepted normal–tangential question. Fixed lateral/rotational gains remain in the predicted wrench and actuator margins. A richer full matrix is introduced only if this class is falsified, not to inflate the Method.

The total relation is

\[
\mathcal U_{\rm total}=\mathcal U_{\rm scan}^{\rm verified}\cup\{u_{\rm wd}^{\rm verified}\}.
\]

The withdrawal member is verified before optimization. Only completed robust scan verification is published atomically; at the deadline the best verified member, otherwise withdrawal, is applied. This is the implementation-level totality mechanism required by the RMC. The scientific claim is not a new solver or incumbent data structure.

## 4. Candidate P — structured physical decision outcomes

The contact state contains nonnegative exponential/Prony relaxation modes for the normal response, local geometry/patch variables connecting indentation to finite-area load, and a bounded tangential pre-slip/bristle state whose yield surface supplies friction and torsional/moment capacity. Parameters such as relaxation weights and times, effective patch geometry, tangential stiffness and friction capacity are sets, not trial-fixed point estimates.

Offline calibration defines (\Theta_{\rm env}^P). Online passive scan observations propagate and intersect this set with normal wrench, tangential wrench, displacement and slip/contact consistency. Candidate sequences are propagated through all members. This route is strictly more task-aligned than upgrading Hunt–Crossley merely for force prediction: added physics survives only when it changes feasible gains or ordering.

Candidate P is selected only if it is action-equivalent to richer representations under the admitted shifts. A systematic residual that changes an active margin or selected gain kills it. Its strengths are interpretability, lower sample demand and structurally meaningful extrapolation; its decisive risk is model-family misspecification.

**Contributions if selected.**

1. A finite-area, fading-memory, normal–tangential contact capacity state derived and pruned by impedance-decision sufficiency rather than generic model fidelity.
2. Online containing-set adaptation that moves the physical contact relation from pre-identification into calibration-out constrained impedance choice.
3. A total four-gain scan/withdraw relation in which physical uncertainty, delayed interaction energy and execution constraints govern the rendered action.

## 5. Candidate L — structured learned decision outcomes

This route does not name material parameters. A stable causal state (z_{k+1}=f_\phi(z_k,o_k,a_k)) predicts the same controller-consumed outcome vector and uncertainty set. Training combines multistep outcome loss, active-margin error and constrained decision regret. Outputs are parameterized to preserve no-contact zero wrench, compressive normal force, opposing tangential dissipation and admissible friction/moment support. These are constraints on the learned relation, not an assumption that a neural network generalizes by itself.

Only a bounded low-dimensional coefficient, last layer or latent correction set is adaptable online. The base representation is frozen within an episode; observation consistency contracts or resets the coefficient set inside (\Theta_{\rm env}^L). The network mean influences nominal task cost, while the containing set authorizes actions. GRU, TCN and stable neural state-space models are interchangeable realizations until evidence distinguishes their history bias.

Candidate L is selected only when the physical candidate leaves decision-relevant mismatch and the learned relation remains valid and adaptable with acceptable data. Physical action-equivalence kills it as unnecessary; loss of containment kills it as invalid.

**Contributions if selected.**

1. A causal contact representation trained for constrained impedance-decision sufficiency, not one-step dynamics or material identification.
2. Support-indexed bounded latent/coefficient adaptation that actually changes out-of-calibration prediction and gain ordering while preserving independent envelope authority.
3. Direct integration of the learned outcome set into a total normal–tangential constrained action relation, separating this route from mean-model MPVIC.

## 6. Candidate H — complementary physical–residual decision outcomes

Candidate H uses Candidate P as nominal structure and learns only the bounded residual in future controller-consumed outcomes:

\[
\mathcal Y^H(A)=\mathcal Y^{P}(A;\Theta_k^P)\oplus
\Delta\mathcal Y_\phi(h_k,A;\Theta_k^R).
\]

The residual is trained on the physical model's systematic active-margin and decision errors, not on reconstructing the whole contact process. Physical and residual sets are jointly adapted and conservatively composed before optimization. Hence physical structure cannot be bypassed by a confident residual mean.

Hybrid is not the default “more advanced” answer. It is selected only if physical-only leaves a reproducible decision-relevant residual and learned-only needs materially more data, yields wider authority sets, or violates structural margins, while the hybrid residual is measurably lower complexity. If either simpler route is action-equivalent, hybrid complexity is rejected.

**Contributions if selected.**

1. A complementary finite-area physics–residual contact state in which learning is restricted to missing decision-relevant outcome structure.
2. A joint containing-set adaptation and authority composition that permits calibration-out recovery without allowing a residual posterior to relax physical envelope limits.
3. A unified multistep four-gain action relation connecting representation error, contact capacity, energy and deadline-total application.

These mechanisms are Evidence-informed Target derivations. R11 does not claim that the finite-area skeleton, residual learning or MPVIC paradigm was imported as a formally novel cross-domain Source Mechanism.

## 7. Closest priors and scientific separation

NNBO remains the genealogy and a required baseline: it represents the original scalar, trial-fixed Hunt–Crossley-model-to-optimal-impedance route. The closer structural priors are the 2022 Q-learning MPVIC, 2023 deep MPVIC and 2025 adaptive/passivity-aware MPVIC papers because they already instantiate “interaction model or identification → predictive variable-impedance optimization.” R11 therefore cannot claim this overall paradigm.

Its provisional scientific delta is narrower and causal: continuous finite-area/history contact is represented only to preserve joint impedance decisions; an in-envelope calibration-out event changes an authority-correct predictive set rather than only a point estimate or rejection flag; and the same outcome set governs four coupled gains, wrench/contact/energy/actuator constraints and the actually applied scan-or-withdraw action. Final novelty is intentionally deferred.

## 8. Human Selection decision boundary and key risks

All three Methods satisfy Problem fit by construction. Their common adaptation and total-action mechanisms have Method-fit justification from the accepted RMC and hard constraints. The representation class remains genuinely underdetermined by existing Evidence; pretending otherwise would fail Method fit.

The Human Selection question is therefore not “physics or AI” in the abstract. It is which falsifiable inductive-bias claim should define the paper:

- choose P if the physical family is action-equivalent and its bounded adaptation is timely;
- choose L if constitutive mismatch matters and structured learning remains valid with no benefit from a physical skeleton;
- choose H only if physical and learned components show measurable complementarity against both simpler routes.

Common high-risk assumptions are: the declared adaptation/withdrawal envelope is nonempty and credible; passive contact data recover useful ordering before relevance expires; the outcome sets are conservative but not vacuous; the four-gain class is sufficient; accumulated interaction energy is correctly bounded; and verified publication plus withdrawal fits the measured deadline. None is reported as already validated.
