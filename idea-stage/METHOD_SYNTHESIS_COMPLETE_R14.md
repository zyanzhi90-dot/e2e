# Complete R14 Method synthesis

## Scientific decision

R14 retains three complete paper-level Methods because current Evidence does not eliminate any representation family:

- P: hereditary finite-area physical contact-capacity set;
- L: structure-constrained learned causal outcome tube;
- H: the same physical skeleton plus a bounded decision-residual tube.

All three share a full-envelope hard-authority invariant and one robust, energy-aware, deadline-total normal–tangential impedance action. P is the strongest-simple priority. L is selected only if P is persistently noncontaining or action-biased and learned tubes preserve coverage. H is selected only if such a residual exists and the physical skeleton materially improves support or data efficiency relative to L.

The common chain is: robot-side contact history and candidate gain sequence -> authorized multistep wrench/contact/slip/energy/actuator outcome set -> robust constrained selection of four impedance gains -> exactly one verified SCAN or WITHDRAW action before deadline.

## 1. Closed-loop action

At each high-level update a moving contact frame is constructed from the last trusted normal and commanded scan tangent. The action is

    a = [log(k_n), log(d_n), log(k_t), log(d_t)]

and elementwise exponentiation gives positive normal/tangential stiffness and damping. Explicit gain-rate limits enforce realizability. The normal pair regulates force and retention; the tangential pair regulates path error and pre-slip. Six-axis wrench, torsional moment, joint and actuator effects remain constrained.

Four scan-frame diagonal gains are the selected minimal class. A scalar or normal-only action cannot address tangential tracking/slip. Full SPD or reference co-optimization replaces it only if matched counterfactual optimization repeatedly changes robust feasibility/action order or materially improves the joint outcome.

The causal observation window contains robot configuration/velocity, Cartesian pose/twist, indentation/rate, tangential motion, six-axis wrench, contact normal/curvature/area proxies, reference, applied gains, contact/slip indicators and interaction energy. Material identity is not required.

## 2. Full-envelope hard authority

Every representation supplies an independently calibrated full-envelope object C_env and may maintain a tighter online contraction C_k.

Full-envelope invariant: C_env constrains every hard margin in every SCAN cycle, including SUPPORTED and the undetected interval following an abrupt admitted change. A contraction may shape nominal cost and may add constraints, but can never remove or relax a full-envelope constraint.

- SUPPORTED: a re-entry-certified contraction may shape cost and add restrictions while C_env remains hard-authoritative.
- ADAPTING-IN-ENVELOPE: contraction-derived performance authority is discarded while the same C_env continues to constrain every hard margin. Scanning continues only if full-envelope robust feasibility holds.
- OUTSIDE-ENVELOPE: if the observation is incompatible with C_env or learned support, only WITHDRAW is permitted.

Safety therefore does not depend on alarm latency. Online adaptation recovers action ranking and adds condition-specific restrictions inside the full-envelope hard-feasible set; it never purchases safety by narrowing the hard uncertainty set. If the full envelope is too conservative for useful scanning, that is a declared fatal risk rather than a reason to let a stale contraction authorize action.

## 3. Candidate P: hereditary finite-area physical Method

P uses the lowest-order nonnegative generalized-Maxwell/Prony normal bank that preserves downstream robust actions:

    rho[j,k+1] = lambda[j] rho[j,k]
                 + (1-lambda[j]) phi_n(indentation, rate, area, curvature)

    f_n[k] = theta[0] phi_n(y[k]) + sum_j theta[j] rho[j,k] + noise

with 0 < lambda[j] < 1 and nonnegative modal weights. Modes are retained by robust feasible-set/action-order equivalence, not RMSE.

Tangential history is a bounded LuGre-type pre-slip bristle recursion projected onto a load/area-dependent admissible set. Tangential force combines bristle deflection, bristle rate and viscous terms. The finite-area force–moment limit surface enforces

    (||f_t||/(mu f_n))^2 + (|tau_n|/(gamma mu f_n r_A))^2 <= 1.

Offline calibration supplies a containing physical envelope Theta_env. Bounded drift propagation and wrench/kinematic consistency strips form a candidate contraction; a fixed-facet outer polytope preserves containment with fixed complexity. Its center affects cost and the contraction may add restrictions, but Theta_env always constrains hard margins.

Fixed-facet recursive membership is selected because the effective-parameter realization yields half-space consistency strips and predictable WCET. Projected EW-RLS is only a center computation. A bounded set-MHE is formally registered as a rejected rival and replaces this update only if identical-history comparison shows materially better containment/action recovery at acceptable WCET without weakening the full-envelope invariant.

P wins if the physical family maintains coverage/action order with no persistent action-changing residual and matches L/H outcomes at lower burden. It is killed by repeatable family noncontainment or by a calibrated L/H residual that changes the robust action or joint outcome.

## 4. Candidate L: structured learned outcome Method

L uses a dilated causal TCN with fixed history window as the executable default because full-window inference is parallel, memory is static and WCET is measurable. Inputs are the robot-side history plus candidate gain sequences. Outputs are multistep normal/tangential wrench, contact-retention margin, coupled slip/torsional-capacity margin, interaction-energy increment and actuator margins:

    outcome_hat[k+1:k+H]
      = F_psi(history[k-W+1:k], candidate_gains[k:k+H-1]).

Hard output layers impose zero wrench outside contact, compressive normal support, nonnegative dissipative allowance and coupled finite-area capacity. Training combines multistep fit, robust impedance decision regret and simultaneous one-sided margin loss. The retained latent state is therefore decision-sufficient, not reconstruction-sufficient.

Split calibration constructs a simultaneous action-conditional full-envelope tube O_env(history, action). Online adaptation is restricted to a bounded low-dimensional affine last layer and bias set. Recursive consistency updates a contraction; a point neural output never authorizes action. O_env remains hard-authoritative under all supported in-envelope changes.

The TCN is a concrete default, not a Contribution. A GRU or stable state-space encoder replaces it only if it has better held-out simultaneous coverage and action equivalence under the same WCET.

L wins only if it preserves hard-margin coverage and improves action/outcome where P is biased, while the physical skeleton provides no measurable support/data advantage. P kills L by matching at lower burden; H kills L if the skeleton materially reduces calibration data or uncertainty inflation.

## 5. Candidate H: physical skeleton plus decision residual

H predicts

    outcome = F_phys(history, candidate_gains; theta)
              + Delta_psi(history, candidate_gains; eta).

F_phys is P's hereditary finite-area model. Delta_psi is a zero-centered causal TCN trained only on controller-consumed multistep residuals and penalized by both magnitude and decision effect. It is constrained by the same contact/dissipation structure and collapses exactly to P when its removal preserves robust feasible sets and action order.

The physical parameter set and residual last-layer set use shared scenario/support indices so their dependence is preserved; naive independent interval addition is forbidden. The independently calibrated full composed envelope always constrains hard margins, while contractions affect cost or add restrictions.

H survives only in a strict complementarity interval: P must exhibit a persistent action-relevant residual, and the skeleton must materially reduce support/data burden or uncertainty inflation relative to L. Otherwise the simpler P or L route is selected.

## 6. Robust normal–tangential impedance selection

For candidate C in {P,L,H}, let O_C(history, gains) be its full-envelope-authorized outcome set. The controller minimizes the worst-case horizon sum of:

- normal-force error;
- tangential path error;
- contact-loss and slip risk;
- gain variation.

For every member of O_C it enforces:

- six-axis force/torque and finite-area tangential/torsional capacity;
- contact-retention and slip margins;
- joint torque/velocity, actuator saturation and gain/rate limits;
- an internal interaction-energy budget;
- availability of the registered withdrawal transition.

The energy state follows

    E_next = E
             - contact_work
             - reference_work
             - variable_stiffness_work
             - delay_reserve
             - inner_loop_reserve,

with E_next >= 0. This prevents a downstream passivity layer from changing a gain already called feasible. Post-hoc passivity remains the strongest simple rival and wins if it gives the same guarantee and applied action.

The horizon is the shortest one for which relaxation, pre-slip, gain-rate or energy memory changes the first action; it collapses to H=1 under action equivalence. Warm-started direct multiple shooting with RTI-SQP is the concrete smooth low-dimensional implementation. Solver brand, numerical horizon and facet count are WCET-qualified implementation choices, not Contributions.

## 7. Deadline-total action

Before optional optimization, every cycle verifies a rate-limited withdrawal tuple: stop tangential reference motion, move to dissipative gains and unload along the last trusted normal. Completed scan candidates are independently checked against the same full-envelope and energy objects.

At the deadline:

- apply the lowest-cost fully verified SCAN action if one completed;
- otherwise apply the registered WITHDRAW action.

Timeout and infeasibility change performance, not action existence. Hold-last and shifted incumbents are not assumed feasible after contact/support change. Full viability becomes necessary only if withdrawal availability is falsified.

## 8. Online procedure

1. Form the contact-frame history and update the chosen representation.
2. Enforce C_env for all hard margins.
3. Decide whether a supported contraction may shape cost/add restrictions, must be ignored during adaptation, or support is outside.
4. Verify and register WITHDRAW.
5. Propagate candidate gain sequences through the full-envelope outcome set.
6. Solve the fixed-work robust MPC and independently verify completed candidates.
7. Atomically publish SCAN or WITHDRAW.
8. Log coverage, contractions, action order, active constraints, energy and WCET for later Principle tests.

## 9. Candidate-by-prior intervention matrix

| Candidate | imoj6X2imAgJ learned MPVIC | xlNjdqnC2fMJ adaptive/passivity MPVIC | j4KJ1EdUyYAJ scalar identified-contact optimizer | ZDySGaNsMn8J finite-area predefined-impedance MPC |
|---|---|---|---|---|
| P | Prior changes a learned point predictor before MPVIC; P changes the representation-to-constraint object to a hereditary finite-area containing physical set. Activation: decision-changing relaxation/pre-slip history. Residual: invariant full-envelope total four-gain action. | Prior adapts environment estimates and passivity/safety around MPVIC; P adapts history-bearing force/moment capacity while full-envelope constraints never relax. Activation: admitted calibration-out contact. | Prior identifies scalar contact then optimizes impedance; P replaces scalar trial/point identification by online hereditary finite-area set prediction before joint feasibility. | Prior predicts finite-area contact with predefined impedance; P makes normal/tangential impedance the constrained decision and adapts capacity in contact. |
| L | Closest structural prior: point learned dynamics before MPVIC. L instead predicts an action-conditional simultaneous outcome tube trained for robust action/margin equivalence. Activation: history-dependent mismatch. Residual: calibrated hard authority, bounded online adapter and total action. | Prior estimates named/point environment quantities; L uses a causal outcome tube at the constraint-authority interface. Activation: support-preserving calibration-out history shift. | Prior's scalar constitutive bottleneck is replaced by the minimum learned causal statistic that changes robust gain order. | Prior's finite-area predictor assumes a predefined action; L predicts capacity conditioned on candidate impedance and optimizes that impedance. |
| H | Prior learns a complete interaction model; H learns only the zero-collapsible decision residual of a hereditary skeleton and calibrates their dependence. | Prior adapts environment estimates; H jointly adapts physical capacity and residual outcomes under an invariant full composed envelope. | Prior's scalar contact is replaced by hereditary finite-area physics plus only the missing action residual. | Prior keeps impedance predefined; H corrects finite-area outcome bias and makes the gain a constrained decision. |

Each cell specifies the changed variable/relation, causal position and direction, activation condition, demonstrated prior effect and remaining Target delta. This is an Early Target causal-equivalence closure, not a final novelty verdict.

## 10. Contributions and validation basis

Shared contribution: a full-envelope-invariant adaptive outcome relation and deadline-total four-gain decision in which calibration-out updates can improve performance but can never relax wrench/contact/energy/actuator constraints, even before change detection.

Candidate-specific conditional contributions:

- P: a decision-pruned hereditary finite-area contact-capacity set for joint normal–tangential impedance choice;
- L: a structure-constrained causal outcome tube trained for robust gain/margin equivalence, not generic point dynamics prediction;
- H: a zero-collapsible physical-plus-decision-residual representation with dependent calibration and strict complementarity;
- all routes: shortest decision-relevant robust MPC and verified SCAN/WITHDRAW rendering.

No formal cross-domain Source Mechanism is claimed. VES and SPO inform Target derivations of action-equivalence scoping and decision-aware training; predict-then-calibrate informs calibration discipline only.

Later validation must discriminate P/L/H, fixed-facet membership versus the registered bounded set-MHE rival, four gains versus richer SPD action, multistep versus H=1, internal energy versus post-hoc passivity, and withdrawal versus hold/incumbent under forced timeout/support loss.

Major risks are unusable full-envelope conservatism, physical noncontainment, learned-tube undercoverage, hybrid uncertainty inflation, withdrawal infeasibility and missed WCET. None is represented as already resolved by experiment.

R14 is a Human-Selection decision packet, not a FINAL_METHOD_PACKET.

