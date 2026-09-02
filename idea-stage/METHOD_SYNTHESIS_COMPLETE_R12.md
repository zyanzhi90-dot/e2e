# Complete R12 Method synthesis

## Method identity and concise technical route

**H-CATI: Hereditary Contact-capacity Adaptive Total Impedance control.**

H-CATI is a physical, set-valued, predictive variable-impedance method for continuous soft-contact scanning. Its route is

> decision-pruned hereditary finite-area normal/tangential contact capacity  
> → bounded-drift set-membership adaptation after calibration-out change  
> → robust multistep selection of `(k_n,d_n,k_t,d_t)` with wrench/contact/energy/actuator constraints  
> → one deadline-total `SCAN/WITHDRAW` applied-action relation.

The design deliberately does **not** add a learned residual. Current Evidence shows that calibrated scalar HC is incomplete for history-dependent finite-area capacity, but it does not establish a residual that remains decision-relevant after the strongest bounded physical model is adapted. Under the strongest-simple-rival rule, adding a learned model would add data, extrapolation and uncertainty-composition assumptions without a proved residual MUST. Physical-family noncontainment is therefore a fatal, testable boundary rather than a reason to pre-install a neural component.

## 1. Decision space and closed-loop object

At high-level update `k`, define a moving contact frame

\[
R_k^c=[n_k,t_k,b_k],
\]

where `n_k` is the last trusted local normal, `t_k` is the commanded scan direction projected onto the tangent plane, and `b_k=n_k\times t_k`. A faster inner Cartesian impedance loop renders the high-level command. The decision is

\[
a_k=[\ell_{k_n},\ell_{d_n},\ell_{k_t},\ell_{d_t}]^\top,
\qquad
(k_n,d_n,k_t,d_t)=\exp(a_k).
\]

The log parameterization guarantees positivity. Normal stiffness/damping control force regulation and contact retention; tangential stiffness/damping control path error, pre-slip and gross slip. Lateral-binormal and rotational gains are fixed for the study but their six-axis wrench and actuator consequences remain constrained. A full SPD impedance or reference co-optimization is excluded unless matched action-equivalence analysis shows that four gains cannot realize a required joint outcome.

The Method predicts consequences of candidate gain sequences through the robot/contact closed loop. It does not treat the nominal impedance equation as an environment model.

## 2. Decision-sufficient hereditary finite-area contact capacity

### 2.1 Observable history

The robot-side observation is

\[
y_k=(\delta,\dot\delta,v_t,\xi_t,\hat A,\hat\kappa,w^c,c,s,a_{k-1},E,r)_k,
\]

containing indentation and normal rate, tangential velocity and displacement, patch-area/curvature proxies, six-axis contact wrench, contact/slip indicators, previous gains, interaction-energy state and scan references. Material identity is not assumed.

### 2.2 Normal hereditary state

Use the smallest nonnegative generalized-Maxwell/Prony bank that survives downstream decision ablation:

\[
\rho_{j,k+1}=\lambda_j\rho_{j,k}+(1-\lambda_j)\phi_n(\delta_k,\dot\delta_k,\hat A_k,\hat\kappa_k),
\quad 0<\lambda_j<1,
\]

\[
f^n_k=\theta_0\phi_n(y_k)+\sum_{j=1}^{L}\theta_j\rho_{j,k}+w^n_k,
\quad \theta_j\ge0.
\]

The basis `phi_n` is a finite-area indentation feature, not a point-contact material label. Nonnegative modes encode fading relaxation/creep while retaining compressive force and dissipative structure. Candidate relaxation times are fitted offline, then modes are removed whenever their removal leaves both the robust feasible action set and action ranking inside a predeclared decision-regret tolerance.

### 2.3 Tangential pre-slip and wrench capacity

A bounded LuGre-type bristle state summarizes tangential loading history:

\[
z^t_{k+1}=\Pi_{\mathcal Z(f^n_k,\hat A_k)}
\left[z^t_k+h\left(v^t_k-\frac{\|v^t_k\|}{g(v^t_k;\vartheta_k)}z^t_k\right)\right],
\]

\[
f^t_k=\sigma_0 z^t_k+\sigma_1\dot z^t_k+\sigma_2 v^t_k+w^t_k.
\]

The area- and load-dependent limit surface couples tangential force and contact moment,

\[
\left(\frac{\|f^t\|}{\mu f^n}\right)^2+
\left(\frac{|m_n|}{r_A\mu f^n}\right)^2\le1,
\]

with `r_A` derived from the patch proxy and bounded geometry parameters. This retains the decision-relevant effect identified by finite-area viscoelastic Evidence: relaxation or creep changes pressure/contact area and therefore tangential-force and torsional-moment capacity, not only scalar normal force.

The complete effective parameter vector `theta` contains only quantities that change predicted force/path/contact/slip/energy/actuator margins. Detailed pressure fields, material identity and FEM states are excluded because they are neither safely identifiable online nor necessary for the four-gain decision.

## 3. Calibration-out adaptation with hard authority

Before deployment, define a physically admitted envelope `Theta_env` and bounded outcome error `W_env` from robot/tool geometry, material-property ranges, maximum patch deformation, friction/capacity bounds and verified withdrawal conditions. “Calibration-out” means outside the data/calibration population but still inside this declared physical envelope; arbitrary shifts are not claimed.

Use a fixed-facet H-polytope. With bounded parameter drift `D_rho`, propagate

\[
\Theta_k^-=(\Theta_{k-1}\oplus D_\rho)\cap\Theta_{env}.
\]

Because controller-consumed outcomes are affine or locally affine in the effective parameters, the new observation defines a consistency strip

\[
C_k=\{\theta: y^{cap}_k-\Phi_k\theta\in W_{env}\}.
\]

For an ordinary change,

\[
\Theta_k=\operatorname{Outer}_{F}
(\Theta_k^-\cap C_k),
\]

where `Outer_F` is a conservative fixed-facet outer approximation. If this intersection is empty but the observation is still inside the declared broad envelope, treat the event as an abrupt condition change and reset once:

\[
\Theta_k=\operatorname{Outer}_{F}(\Theta_{env}\cap C_k).
\]

A second inconsistency, an empty reset, or an envelope violation selects `WITHDRAWAL`.

This mechanism handles calibration-out change rather than merely detecting it. Set contraction changes the predicted force/capacity tube and may change both the robustly feasible gain set and the best gain during the same scan. The Chebyshev center is used only for nominal cost; every member of `Theta_k` is used for hard constraints. Thus estimator optimism cannot relax authority. A richer set-MHE or multiple-model bank is not retained because no accepted condition requires latent mode identity or a posterior trajectory; if the fixed-facet set is containing and contracts on time, those alternatives implement the same RMC with more state and computation.

## 4. Robust multistep normal-tangential impedance selection

Choose the shortest horizon covering every delayed state that changes the current action:

\[
T_H\ge\max(T_{95}^{relax},T^{preslip},T^{energy},T^{gain\ reach}),
\qquad H=\lceil T_H/T_s\rceil.
\]

If one-step and multistep actions are equivalent throughout the admitted envelope, collapse to `H=1`; otherwise a multistep horizon is necessary because a one-step program aliases delayed relaxation, slip/contact-loss, gain-rate reachability or energy depletion.

For the active uncertainty set `Omega_k=(Theta_k,W_env)`, solve

\[
\min_{a_{0:H-1}}\max_{\omega\in\Omega_k}
\sum_{i=0}^{H-1}
\left(q_f e_{f,i}^2+q_t\|e^t_i\|^2+q_s\ell_{slip/contact,i}
+\|\Delta a_i\|_R^2\right)+V_f.
\]

At every predicted step require

\[
\begin{aligned}
&a_{min}\le a_i\le a_{max},\quad |a_i-a_{i-1}|\le\Delta a_{max},\\
&f^n_{min}\le f^n_i\le f^n_{max},\quad (f^t_i,m^n_i)\in\mathcal L(f^n_i,\hat A_i,\Theta_k),\\
&g_{contact,i}\ge0,\quad g_{slip,i}\ge0,\\
&\tau_{min}\le\tau_i\le\tau_{max},\quad \dot q_{min}\le\dot q_i\le\dot q_{max},\\
&E_i\ge E_{min}.
\end{aligned}
\]

Finite-area capacity, robot Jacobian and actuator limits create normal/tangential coupling even though the gain matrix is diagonal.

### Interaction-energy state

The optimizer carries

\[
E_{i+1}=E_i+hP_{diss}(a_i)-hP_{inj}(a_i,a_{i-1},r_i)-E^{res}_i,
\]

where `P_inj` includes bounded work from gain variation, moving reference, delay and inner-loop mismatch, and `E_res` is a robust reserve. The selected gain is applied unchanged. A downstream passivity observer may measure the state but may not project or replace a gain called feasible; if the energy bound cannot authorize the action, that action is absent from the robust set.

Warm-started direct multiple shooting with RTI-SQP is the concrete realization because the selected low-order physical state is smooth and nonlinear, the four-gain action is small, and a completed SQP iterate exposes constraints for verification. This solver is an implementation choice, not a scientific contribution. Its worst-case nonoptional time is measured; no convergence claim substitutes for action availability.

## 5. Deadline-total applied-action relation

Before optional optimization, construct and verify

\[
u_k^{wd}=(WITHDRAW,a_k^{wd},r_k^{wd}),
\]

which stops tangential reference motion, moves gains within rate and energy limits to a dissipative setting, and unloads along the last trusted normal until contact force falls below the release threshold. Its availability is claimed only inside a declared withdrawal envelope.

Define

\[
\mathcal U_k^{total}=
\{(SCAN,a,r):\text{all current robust checks completed}\}
\cup\{u_k^{wd}\}.
\]

The update is:

1. latch observation, contact frame and energy state;
2. update the parameter polytope, performing at most one envelope reset;
3. verify and register `u_wd`;
4. reverify any shifted warm start;
5. run RTI-SQP and atomically publish only fully checked scan members;
6. at deadline `T_g`, apply the lowest-cost published scan member, otherwise `u_wd`.

Therefore support loss, set inconsistency, infeasibility and timeout are outcomes of the same action relation. A feasible incumbent is useful solver state but not a separate Principle; a high-dimensional online viability set is unnecessary while the withdrawal-envelope assumption holds.

## 6. Conditional analysis obligations

The Method is constructed to support, but does not yet claim as proved, four results:

1. **Physical-set containment.** If the true effective parameters remain in `Theta_env`, drift is inside `D_rho`, disturbances are in `W_env`, and the outer approximation contains its argument, then `theta* in Theta_k` after every non-withdrawal update.
2. **Robust applied-margin implication.** If the contact/robot prediction and uncertainty sets are containing, every published `SCAN` member satisfies the declared wrench, capacity, contact, energy, actuator and gain-rate margins.
3. **Energy-authorized rendering.** If the energy balance upper-bounds all gain/reference/delay/inner-loop work, `E_i>=E_min` bounds the declared interaction-energy expenditure without downstream gain modification.
4. **Deadline totality.** If latch/update/withdrawal verification/publication WCET is below the reserved deadline and the withdrawal envelope holds, every update returns a member of `U_total` independently of optimizer convergence.

Failure of an assumption narrows or rejects the corresponding claim. Software traces alone are not presented as proofs.

## 7. Offline and online algorithms

### Offline

1. Collect bounded gain-excitation episodes spanning calibration materials, patch geometries, speeds/directions, dwell and loading histories.
2. Fit candidate nonnegative relaxation banks and tangential bristle/capacity parameters.
3. Prune contact-state coordinates by robust feasible-set and action-order equivalence, not prediction RMSE alone.
4. Fix `Theta_env`, `D_rho`, `W_env`, polytope facets, energy reserve and withdrawal envelope from physical bounds and held-out episodes.
5. Determine the shortest decision-relevant horizon and reject any envelope whose robust propagation plus nonoptional update cannot meet WCET.

### At each update

```text
y_k, E_k <- latch observation and contact frame
Theta_k <- drift propagation intersected with the new consistency strip
if empty and still inside broad envelope: one reset to Theta_env intersect strip
if still empty or outside envelope: choose WITHDRAWAL
u_wd <- verify and register withdrawal member
if not withdrawing:
    reverify warm start
    solve robust four-gain horizon with integrated energy state
    publish only completed robust SCAN checks
apply best published SCAN before T_g, otherwise u_wd
```

## 8. Baseline and closest-prior positioning

NNBO is the **genealogical baseline**: it motivates the contact-model-to-optimal-impedance question and supplies the scalar trial-fixed HC starting point. It is not the sole closest structural prior.

The closest structural baseline is the **2025 adaptive/passivity-aware MPVIC** family member, complemented by 2022 Q-LMPVIC and 2023 deep MPVIC. They already establish that an identified or learned interaction model can drive online predictive variable-impedance optimization. Deformable-contact MPC is the closest prior for finite-area scanning mechanics but pre-estimates properties and leaves impedance predefined.

H-CATI therefore does not claim “model plus MPC.” Its separation is the following real computational change: a decision-pruned **hereditary finite-area normal/tangential capacity set is adapted during contact and is the hard authority for the same four-gain, energy-aware, deadline-total applied scan action**.

## 9. Contributions naturally implied by the Method

1. **A decision-sufficient hereditary contact-capacity representation for variable impedance.** H-CATI replaces scalar point HC by the smallest nonnegative normal-memory and tangential pre-slip/finite-area capacity state that changes robust gain feasibility or ordering. The finite-area viscoelastic mechanism is adapted from contact mechanics: history-dependent pressure/area changes are reduced to controller-consumed force/moment/slip margins instead of a complete material model.

2. **Set-valued calibration-out adaptation of soft-contact capacity.** A fixed-facet drift/reset set-membership relation updates effective physical parameters after material, geometry, sliding or history changes inside a declared envelope. Unlike point EWRLS or unsupported detection, the update changes the impedance decision while all set members remain hard-authoritative; unlike arbitrary-OOD claims, an empty or out-of-envelope set withdraws.

3. **A total robust normal-tangential impedance decision.** Four positive contact-frame gains are optimized over the shortest decision-relevant horizon with finite-area wrench/contact, interaction-energy, actuator and gain-rate constraints. Fully verified scan actions and a preverified unloading member form one deadline-total relation, so solver failure and safety enforcement cannot silently replace the impedance action selected by the Method.

No contribution is claimed for generalized Maxwell, LuGre, H-polytopes, MPC, RTI-SQP, energy tanks or withdrawal individually.

## 10. Evidence required for the next stage

The Principle test stage must at minimum discriminate:

1. calibrated memoryless point/finite-area models versus the hereditary state on paired histories with matched instantaneous variables;
2. physical-only versus structured learned and physical-residual hybrid models under matched data/compute, using set containment, active-margin error, feasible-set disagreement and selected-action regret;
3. fixed-envelope robust operation, point EWRLS, fixed-facet set membership and a credible set-MHE/multiple-model alternative under gradual and abrupt admitted changes;
4. four gains versus a richer SPD/reference action class under matched constraints;
5. one-step versus the shortest memory-covering horizon without changing uncertainty, energy or action class;
6. optimizer-consumed energy versus an action-equivalent post-hoc passivity mechanism;
7. total action versus action hold/fast solver/feasible-incumbent alternatives under forced interruption;
8. end-to-end comparison with NNBO, model-agnostic wrench feedback, 2022/2023/2025 MPVIC and finite-area deformable-contact MPC.

Until those tests are executed, H-CATI is a complete, implementable and falsifiable Method design—not a validated `FINAL_METHOD_PACKET`.
