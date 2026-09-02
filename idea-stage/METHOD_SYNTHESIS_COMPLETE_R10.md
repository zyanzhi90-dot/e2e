# Complete R10 Method Synthesis

## Method identity and scope

**DASH-TAC:** Decision-sufficient Adaptive Structured-Hybrid contact modeling for Total-Action normal–tangential impedance control.

DASH-TAC addresses continuous soft-contact scanning in which material, local patch geometry, sliding condition and loading history can leave the calibration population while the robot must continue choosing normal and tangential impedance under wrench, interaction-energy, actuator, gain-rate and update-time limits. It does not estimate a universal tissue constitutive law. It constructs the smallest contact state needed to decide the impedance action, updates the unmodeled part without granting unsupported authority, and makes the gain that is optimized, verified and applied the same object.

The complete causal route is:

> structured finite-area/contact-memory state + bounded decision residual → support-indexed outcome authority → short-horizon robust four-gain selection → total verified `SCAN/WITHDRAW` action.

This is one method, not a pipeline of interchangeable modules. The contact representation is defined by the constrained impedance decision; the adaptation state determines which margin relation the optimizer may consume; and the optimizer's output is defined only through the applied-action relation.

## 1. Interaction and decision variables

At the high-level update time `k`, construct a moving contact frame

\[
R_k^c=[n_k,t_k,b_k],
\]

where `n_k` is the last trusted local normal, `t_k` is the commanded scan direction projected onto the tangent plane, and `b_k=n_k\times t_k`. The faster inner loop renders positive task-space impedance. The high-level action is

\[
a_k=[\ell_{k_n},\ell_{d_n},\ell_{k_t},\ell_{d_t}]^\top,
\qquad
(k_n,d_n,k_t,d_t)=\exp(a_k),
\]

so positivity is intrinsic. Lateral-binormal and rotational gains are fixed during one study; their wrench and actuator consequences remain in the constraints. This four-gain action is the minimum class that can jointly trade normal force regulation and tangential path/slip behavior. A full SPD gain matrix or reference co-optimization is not part of the Method unless this action class is falsified.

The inner-loop target relation in the contact frame is

\[
M_d\ddot e + D(a_k)\dot e + K(a_k)e=f^\star-f_c,
\]

with force reference in the normal direction and geometric scan reference in the tangent direction. The Method predicts the closed-loop consequences of candidate gain sequences rather than assuming that this nominal relation alone determines contact force.

## 2. Structured, decision-sufficient contact state

### 2.1 Measured state

The safely available observation is

\[
y_k=(\delta_k,\dot\delta_k,v^t_k,\xi^t_k,\hat\kappa_k,\hat A_k,
w^c_k,c_k,s_k,a_{k-1},E_k,r_k),
\]

where indentation/normal speed, tangential speed/displacement, local curvature or patch proxies, six-axis contact wrench, contact/slip indicators, previous gains, interaction-energy state and reference/scan variables are used. Material identity is not assumed.

### 2.2 Physical memory skeleton

The physical state is deliberately low order:

\[
x^p_k=(\delta_k,\dot\delta_k,\hat A_k,\hat\kappa_k,
\rho_{1:L,k},\zeta^t_k,\gamma_k).
\]

Normal fading memory is represented by stable nonnegative modes

\[
\rho_{j,k+1}=\lambda_j\rho_{j,k}+(1-\lambda_j)\phi_n(\delta_k,\dot\delta_k,\hat A_k),
\quad 0<\lambda_j<1,
\]

which form a discrete Prony/standard-linear-solid approximation without requiring every tissue parameter to be identified online. `\zeta^t_k` is a bounded pre-slip state driven by tangential displacement and released in gross sliding. `\gamma_k` parameterizes finite-area friction and moment capacity. The physical output `o^p(x^p,a)` obeys four structural contracts:

1. zero contact produces zero contact wrench;
2. normal contact force is compressive;
3. tangential contact is dissipative within the declared sliding convention;
4. tangential force and contact moment lie inside a finite-area capacity/limit-surface envelope coupled to normal load and patch proxy.

These contracts retain the mechanisms supported by finite-area viscoelastic and deformable-contact work while avoiding a claim that one calibrated geometry/material law remains exact across the target envelope.

### 2.3 Bounded decision residual

The remaining mismatch is represented as

\[
z_{k+1}=F_\psi(z_k,y_k,a_k),\qquad
o_{k+i|k}=o^p_{k+i|k}+\Phi_\psi(z_{k+i|k},y_{k+i|k},a_{k+i|k})\theta_k+w_{k+i},
\]

where `o` contains only quantities consumed by the decision: normal force error, tangential path error, contact-loss/slip indicators, finite-area wrench/capacity margins, interaction-energy terms and actuator load. `theta` is low dimensional and bounded; `w` is a declared residual set. `F_psi` may be a GRU, TCN or stable state-space realization. Its architecture is not a scientific claim.

The offline objective is

\[
\mathcal L=
\mathcal L_{H\text{-outcome}}+
\lambda_m\mathcal L_{\text{active-margin}}+
\lambda_d\big[J(\hat a; o)-J(a^\star;o)\big]_+
+\lambda_s\mathcal L_{\text{structure}},
\]

where the third term is constrained decision regret under a teacher or high-fidelity replay. A coordinate is removed if ablation leaves both the robust feasible action set and action ordering unchanged within tolerance. Thus the representation is tested for **decision sufficiency**, not rewarded for reconstructing decision-irrelevant material detail.

## 3. Validity-indexed uncertainty and online adaptation

### 3.1 Supported relation

For independent calibration episode `e`, define an overoptimism score over the horizon, hard-margin outputs and a predeclared action cover:

\[
S_e=\max_{k,i,j,a\in\mathcal A_{cal}}
\frac{\hat g_{j,k+i|k}(a)-g^{obs}_{j,k+i}(a)}{\sigma_{j,k+i|k}(a)},
\]

where every hard constraint is written as true margin `g_j\ge0`. The finite-sample quantile `q_{1-\alpha}` yields

\[
\underline g^{cal}_{j,k+i|k}(a)=
\hat g_{j,k+i|k}(a)-q_{1-\alpha}\sigma_{j,k+i|k}(a).
\]

The simultaneous episode score avoids presenting pointwise accuracy as joint horizon/action validity. `SUPPORTED` may be entered only at an episode boundary when a pre-outcome feature-leverage/neighborhood rule holds relative to independent calibration episodes. Once invalidated, supported authority cannot be restored during that episode using the same adaptation data.

### 3.2 In-envelope hard authority

Before deployment, define `A_env(y_k,E_k)` from robot-side gain/rate, wrench, energy, actuator and withdrawal bounds that remain valid for every admitted calibration-out condition. This relation is deliberately coarser than the calibrated tube but independent of the online estimator.

In `ADAPTING-IN-ENVELOPE`, the admissible scan relation is

\[
\mathcal A_k^{adapt}=
\mathcal A_{env}(y_k,E_k)
\cap
\mathcal A_{model}(\Theta_k,W_{env}).
\]

Crucially, no contraction of `Theta_k` may remove a constraint in `A_env`; the adaptive model may change nominal cost and add restrictions, never relax the independently valid envelope relation. A sharper calibrated relation may be created only for a later episode by an independent delayed audit with predeclared sample/dependence requirements.

### 3.3 Gradual and abrupt change

For gradual residual change, propagate

\[
\Theta^-_k=(\Theta_{k-1}\oplus D_\rho)\cap\Theta_{env},
\]

then form the consistency strip

\[
C_k=\{\theta:\;o_k-o^p_k-\Phi_k\theta\in W_{env}\}.
\]

If `Theta^-_k\cap C_k` is nonempty,

\[
\Theta_k=\operatorname{Outer}_{N_f}(\Theta^-_k\cap C_k),
\]

where `Outer_Nf` is a fixed-complexity conservative ellipsoid, zonotope or polytope. If the intersection is empty but a predeclared innovation test says the observation remains inside the broader adaptation envelope, treat it as an abrupt change and reset once:

\[
\Theta_k=\operatorname{Outer}_{N_f}(\Theta_{env}\cap C_k).
\]

A second inconsistency, an empty reset intersection, or an envelope violation enters `WITHDRAWAL`. The Chebyshev/analytic center `\bar\theta_k` is used only for nominal cost and value-of-information calculations; there is no separate EW-RLS estimator whose confidence could be confused with authority.

This adaptation changes the predicted force/path/capacity response and therefore the optimum among envelope-safe actions during the scan. It is not merely unsupported-input detection. Its scientific boundary is equally explicit: it cannot authorize actions excluded by `A_env`, and it cannot handle arbitrary changes outside `Theta_env`.

### 3.4 Decision-gated information

Let `A^\epsilon_k` be robustly admissible actions whose task cost is within `epsilon` of the robust optimum. Only when hypotheses in `Theta_k` imply different impedance rankings, select

\[
a_k=\arg\max_{a\in\mathcal A^\epsilon_k}
\min_{\theta_1,\theta_2\in\Theta_k\atop d(a^\star(\theta_1),a^\star(\theta_2))>\eta}
\|\hat o(a,\theta_1)-\hat o(a,\theta_2)\|.
\]

Otherwise select the robust task optimum. If no feasible separating action exists, the Method does not explore; it remains conservative or withdraws. This lexicographic rule avoids introducing an arbitrary information/task weight.

## 4. Short-horizon robust normal–tangential impedance selection

For horizon `H` covering the dominant measured relaxation/contact time, solve

\[
\min_{a_{0:H-1}}
\max_{\omega\in\Omega_{\sigma_k}}
\sum_{i=0}^{H-1}
\big(
q_f e^2_{f,i}+q_t\|e^t_i\|^2+q_c\ell_{contact/slip,i}
+\|\Delta a_i\|^2_R
\big)+V_f,
\]

where `Omega_SUPPORTED` is the calibrated simultaneous set and `Omega_ADAPTING` is composed with the envelope relation above. A nominal-center approximation may be used for the objective, but the hard constraints are checked against the active authority.

For every predicted step, require:

\[
\begin{aligned}
&a_{min}\le a_i\le a_{max},\quad |a_i-a_{i-1}|\le \Delta a_{max},\\
&f_{min}\le f^n_i\le f_{max},\quad \|f^t_i\|\le \mu_i f^n_i-m^f_i,\\
&\ell^{contact}_i\ge0,\quad \ell^{slip}_i\ge0,\quad \ell^{moment}_i\ge0,\\
&\tau_{min}\le\tau_i\le\tau_{max},\quad \dot q_{min}\le\dot q_i\le\dot q_{max},\\
&E_i\ge E_{min}.
\end{aligned}
\]

Finite-area friction/moment and contact-retention constraints couple normal and tangential gains even though the gain matrices are diagonal.

The interaction-energy state is inside the optimization:

\[
E_{i+1}=E_i+hP_{diss}(a_i)
-hP_{inj}(a_i,a_{i-1},r_i)-E^{res}_i,
\]

where `P_inj` includes bounded energy from gain variation, moving reference, delay and actuator/inner-loop mismatch. `E_res` is an uncertainty reserve. This prevents a passivity observer from silently replacing the optimized gain after selection.

The horizon is a Method requirement because delayed relaxation, pre-slip/contact loss, gain-rate reachability and energy accumulation can change the current action. RTI-SQP is not. Sequential convexification, RTI-SQP or another interruptible solver is acceptable if it exposes completed robust checks and meets the measured timing reserve.

## 5. Total applied-action relation

At every update define

\[
\mathcal U_k^{total}=
\{(SCAN,a,r):a\text{ has completed every active robust check}\}
\cup\{u^{wd}_k\}.
\]

The preverified withdrawal member

\[
u^{wd}_k=(WITHDRAW,a^{wd}_k,r^{wd}_k)
\]

stops tangential reference motion, moves gains within rate limits toward a dissipative setting, and unloads along the last trusted normal until force falls below a release threshold. It is valid only inside a declared withdrawal envelope; arbitrary out-of-envelope safety is not claimed.

The update sequence is:

1. latch measurements and classify `SUPPORTED`, `ADAPTING-IN-ENVELOPE`, or `WITHDRAWAL`;
2. update/check the active uncertainty relation;
3. register and verify `u_wd` before optional optimization;
4. revalidate any shifted warm start under the current relation;
5. solve and atomically publish only complete scan verifications;
6. at deadline `T_g`, apply the lowest-cost valid scan member if one exists, otherwise `u_wd`.

Thus solver timeout, infeasibility, support loss and set inconsistency are decisions in the same action relation. A feasible incumbent is useful implementation state, but not the scientific Principle; online high-dimensional viability is unnecessary while the withdrawal-totality assumption holds.

## 6. Conditional analysis claims

The Method is designed to support four conditional results; they are obligations for later derivation/validation, not claims already proved.

1. **Supported simultaneous-margin proposition.** Under independent episode calibration and the declared dependence/exchangeability condition, the calibrated tube bounds all covered horizon/action margin overoptimism at the stated episode-level risk.
2. **Adaptive authority lemma.** If the true residual remains in the drift/reset envelope and disturbances remain in `W_env`, fixed-complexity outer approximation preserves containment; because `A_env` is always intersected, adaptive estimates cannot authorize an action outside the independently valid envelope.
3. **Energy-bounded rendering proposition.** If the discrete energy accounting upper-bounds all gain/reference/delay work and `E_i>=E_min`, rendered time-varying impedance respects the declared interaction-energy budget.
4. **Deadline totality proposition.** If measurement/update/verification/publication WCET is below the reserved deadline and the withdrawal envelope holds, every update returns one member of `U_total` independent of optimizer convergence.

These statements are conditional. Failure of their assumptions narrows or rejects the claim; no theorem is inferred from software operation alone.

## 7. Online algorithm

**Offline**

1. collect bounded gain-excitation episodes spanning calibration materials, geometries, speeds/directions, dwell and histories;
2. fit physical skeleton and residual jointly under structural, multistep-margin and decision-regret losses;
3. prune state by robust feasible-set/action equivalence;
4. define `Theta_env`, `W_env`, `A_env`, withdrawal envelope and energy reserve;
5. calibrate the supported simultaneous tube on independent episodes;
6. measure WCET reserve for all nonoptional update operations.

**At each update**

```text
y_k, E_k <- latch observation and energy state
sigma_k <- support/envelope classifier
if sigma_k == SUPPORTED:
    Omega_k <- calibrated simultaneous outcome set
else if sigma_k == ADAPTING:
    Theta_k <- drift update; if falsified, one envelope reset
    if inconsistent: sigma_k <- WITHDRAWAL
    Omega_k <- envelope authority plus adaptive prediction restrictions
u_wd <- verify withdrawal member
if sigma_k != WITHDRAWAL:
    revalidate warm start
    optimize four-gain horizon; optionally use feasible epsilon-information tie-break
    publish only fully checked SCAN members
apply best published SCAN member before T_g, else u_wd
```

## 8. Contributions naturally implied by the Method

1. **A decision-sufficient structured hybrid soft-contact representation.** Relative to NNBO's scalar trial-fixed Hunt–Crossley model and deep MPVIC's generic learned Cartesian transition, DASH-TAC makes finite-area geometry, fading memory and tangential capacity explicit and confines learning to the bounded residual that changes robust four-gain feasibility or ordering. The migrated mechanisms are finite-area viscoelastic/contact-capacity modeling and decision-focused/value-equivalent representation; the target adaptation is to learn H-step constraint/output residuals rather than material identity.

2. **Authority-correct calibration-out contact adaptation.** The Method separates supported calibrated margins, in-envelope hard authority and withdrawal, and couples bounded-drift set membership with an innovation-triggered reset for gradual and abrupt condition changes. The migration from robust adaptive/set-membership MPC is modified so estimator contraction cannot relax the independently valid contact envelope; its target effect is online change of impedance prediction and ranking without converting confidence into safety.

3. **A multistep total-action normal–tangential impedance relation.** The Method extends the MPVIC model-to-MPC paradigm from normal/free-space or one-dimensional settings to four contact-frame gains whose future force, path, slip/contact, finite-area wrench, energy and actuator consequences are optimized together. Energy and fallback are not external corrections: fully verified scan actions and a preverified withdrawal action are members of one deadline-total relation.

No contribution is claimed for using a GRU/TCN, EW-RLS, RTI-SQP, conformal prediction, an energy tank or a feasibility incumbent by itself.

## 9. Evidence required before the next scientific stage can accept the Principle

The next stage must be able to discriminate, at minimum:

1. physical-only finite-area, pure learned and structured-hybrid models under matched data/compute, using active-margin calibration, feasible-set disagreement and selected-action regret—not prediction RMSE alone;
2. calibrated memoryless versus history-state models on paired histories with identical instantaneous observations and different delayed force/slip capacity;
3. gradual and abrupt held-out shifts, auditing residual-set containment, envelope-relation membership and recovery of a useful action change versus conservative fixed impedance;
4. one-step versus multistep selection under delayed relaxation, pre-slip, gain-rate and energy activation;
5. total relation versus matched post-hoc guards under forced infeasibility, support switches and deadline interruptions;
6. full energy balance and withdrawal-envelope validity on the physical platform;
7. end-to-end comparison against the model-agnostic wrench-feedback baseline, NNBO genealogy baseline, representative 2022/2023/2025 MPVIC, and finite-area scan MPC with matched action/constraint accounting.

Until those results exist, DASH-TAC is a complete and test-ready Method design, not a validated final method.
