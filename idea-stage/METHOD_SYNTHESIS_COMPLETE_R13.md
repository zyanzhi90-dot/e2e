# Complete R13 Method synthesis

## Decision status and scientific route

R13 does not force a representation choice that present Evidence cannot justify. It specifies three paper-level, executable Methods over the same accepted Problem and RMCs:

- **P — H-CATI-P:** a hereditary finite-area physical contact-capacity set;
- **L — H-CATI-L:** a structure-constrained learned causal outcome tube;
- **H — H-CATI-H:** the physical set plus a bounded decision-residual tube.

Each route is a complete Method, not a model placeholder. They share the same action, hard constraints, uncertainty-authority state machine and deadline-total publication relation. They differ only at the scientifically unresolved causal repair: what history-bearing outcome representation can both contain calibration-out behavior and preserve the distinctions that change the impedance decision. P is the strongest-simple priority; L and H remain because current Evidence does not establish physical-family containment.

The common route is

> robot-side contact history + candidate gain sequence  
> → authorized multistep wrench/contact/slip/energy/actuator outcome set  
> → robust constrained selection of normal–tangential impedance  
> → exactly one verified SCAN or WITHDRAW action before deadline.

## 1. Closed-loop decision object

At update (k), a contact frame (R_k^c=[n_k,t_k,b_k]) is formed from the last trusted normal and commanded scan tangent. The high-level action is

[
a_k=[ell_{k_n},ell_{d_n},ell_{k_t},ell_{d_t}]^	op,qquad
(k_n,d_n,k_t,d_t)=exp(a_k).
]

A faster inner Cartesian impedance loop renders these gains. Log coordinates guarantee positivity; explicit increment limits enforce realizability. The normal pair controls force/contact retention and the tangential pair controls path error/pre-slip. Six-axis wrench, torsional moment, joint and actuator consequences remain hard constraints.

Four scan-frame diagonal gains are the selected minimal action class. Normal-only/scalar action cannot address tangential tracking and slip. Full SPD impedance or reference co-optimization is not assumed necessary: it replaces this action class only if matched counterfactual optimization repeatedly changes feasibility/action order or materially improves the joint outcome.

The observation history contains pose/twist, indentation and normal rate, tangential motion, six-axis contact wrench, reference, applied gains, contact/slip indicators, local normal/curvature/area proxies and the interaction-energy state. Material identity is not required.

## 2. Common calibration authority

Every route maintains an independently calibrated full-envelope uncertainty object (mathcal C_{m env}) and, when justified, a tighter online object (mathcal C_k). These objects are parameter sets for P, outcome tubes for L, and a dependent physical-plus-residual set for H. Their hard authority is governed by the same state machine.

**SUPPORTED.** (mathcal C_k) may constrain action only after all new observations are contained and the proposed contraction passes a re-entry audit: it must preserve coverage and its robust feasible-set/action ordering must be stable relative to the containing audit set.

**ADAPTING-IN-ENVELOPE.** An alarm, active-set expansion, abrupt admitted change or unresolved consistency event changes authority before action publication. Hard constraints immediately use (mathcal C_{m env}), not the stale contraction. Online data may update a new contraction, but that contraction cannot authorize action until re-entry is certified. Scanning may continue only if robustly feasible for the full envelope; otherwise the action is WITHDRAW.

**OUTSIDE-ENVELOPE.** If observations are incompatible with (mathcal C_{m env}) or learned support, predictive scan authority is absent and only WITHDRAW is permitted.

Thus calibration-out is handled constructively inside the declared envelope: the method first remains safe under a containing envelope, then uses new observations to recover a less conservative decision. Unsupported extrapolation is not called adaptation. In particular, an abrupt change cannot exploit the delay before inconsistency detection to let an obsolete tight set relax a margin.

## 3. Candidate P: hereditary finite-area physical Method

### 3.1 Contact state

Normal fading memory is represented by the smallest nonnegative generalized-Maxwell/Prony bank that preserves downstream decisions:

[
ho_{j,k+1}=lambda_jho_{j,k}+(1-lambda_j)phi_n(delta_k,dotdelta_k,hat A_k,hatkappa_k),
quad 0<lambda_j<1,
]

[
f^n_k=	heta_0phi_n(y_k)+sum_{j=1}^{L}	heta_jho_{j,k}+w_k^n,qquad 	heta_jge 0.
]

The finite-area feature (phi_n) depends on indentation, area and curvature proxies. Modes are removed if their removal preserves the robust feasible action set and action order within a declared decision-regret tolerance; order (L) is therefore a falsifiable realization, not a Contribution.

Tangential loading history is represented by a bounded LuGre-type bristle state

[
z_{k+1}^{t}=Pi_{mathcal Z(f_k^n,hat A_k)}
!left[z_k^t+hleft(v_k^t-rac{|v_k^t|}{g(v_k^t;artheta)}z_k^tight)ight],
]

with force (f_k^t=sigma_0z_k^t+sigma_1dot z_k^t+sigma_2v_k^t+w_k^t). A load/area-dependent limit surface jointly bounds tangential force and torsional moment, e.g.

[
(|f^t|/mu f^n)^2+(|	au_n|/gammamu f^n r_A)^2le 1.
]

This is the minimum physical state that explicitly carries the accepted normal relaxation, finite-area geometry and pre-slip/contact-history mechanisms.

### 3.2 Online set adaptation

Let (	heta) collect effective relaxation, geometry/capacity and friction parameters. Offline calibration supplies a containing (Theta_{m env}). Bounded drift propagation and each wrench/kinematic observation create consistency strips. Their intersection is outer-approximated by a fixed-facet H-polytope:

[
Theta_{k+1}^{m cand}supseteq
(Theta_koplusmathcal D)cap
{	heta:|F(y_k,a_{k-1};	heta)-w_k|learepsilon}.
]

The Chebyshev center shapes the nominal cost; every authorized member constrains action. Set containment is the Method choice because hard authorization needs a set, not a mean. Fixed facets and projected EW-RLS-like center updates are the concrete real-time realization. A bounded set-MHE or multiple-model bank remains the strongest estimator rival and replaces this realization if it achieves equal-or-better containment and action recovery within no greater WCET.

### 3.3 What would select or kill P

P wins if it retains held-out coverage and robust action ordering with no persistent action-changing residual, while matching L/H outcomes at lower data/model burden. It is killed by repeatable physical-family noncontainment or by an L/H residual that preserves hard-margin coverage and materially changes the chosen action or joint outcome.

## 4. Candidate L: structured learned outcome Method

### 4.1 Concrete causal predictor

L uses a dilated causal TCN with a fixed history window as the initial executable realization because the whole window can be evaluated in parallel with deterministic memory and WCET. The input at each sample is

[
x_k=(q,dot q,x,dot x,w^c,delta,dotdelta,hat n,hatkappa,hat A,
r,a_{k-1},c,s,E)_k.
]

For a candidate sequence (a_{k:k+H-1}), the network predicts multistep normal/tangential wrench, contact-retention margin, coupled slip/torsional-capacity margin, energy increment and actuator margins:

[
hat o_{k+1:k+H}=mathcal F_psi(x_{k-W+1:k},a_{k:k+H-1}).
]

Hard output layers enforce zero wrench outside contact, compressive normal support, nonnegative dissipative allowance and coupled finite-area tangential/torsional capacity. Training combines multistep prediction with robust impedance decision regret and simultaneous one-sided margin loss. Hence the learned state is retained for downstream action equivalence, not reconstruction accuracy alone. TCN is not itself the scientific claim: a GRU or stable state-space encoder replaces it only if it demonstrates better held-out action equivalence and coverage under the same WCET.

### 4.2 Simultaneous tubes and adaptation

Split calibration over the declared material/geometry/sliding/history envelope constructs a simultaneous action-conditional tube (mathcal O_{m env}(h,a)) for all controller-consumed outcomes. Online adaptation is limited to a low-dimensional affine last layer and bias state (etainmathcal E_k). Consistency strips update a bounded fixed-complexity set; a point neural prediction never authorizes action. The nominal center affects cost and the full authorized tube constrains action.

The three-state authority applies unchanged. During adaptation the hard tube reverts to (mathcal O_{m env}); if the current history is outside learned support, the method withdraws. Thus L's potential advantage is not presumed “neural generalization.” Its possible necessity is the ability to represent decision-relevant history/geometry/friction interactions that the bounded physical family cannot contain.

### 4.3 What would select or kill L

L wins only if it maintains simultaneous hard-margin coverage and improves action equivalence/joint outcome where P is biased, while a physical skeleton gives no measurable support or data-efficiency benefit. P kills L by matching coverage/action/outcome at lower burden. H kills L if the physical skeleton materially reduces calibration data or uncertainty inflation while retaining the improvement.

## 5. Candidate H: physical skeleton plus decision residual

H propagates P's physical state and learns only the residual of controller-consumed multistep outcomes:

[
o_{k+1:k+H}=
F_{m phys}(h_k,a_{k:k+H-1};	heta)
+Delta_psi(h_k,a_{k:k+H-1};eta).
]

The residual uses the same concrete causal TCN realization as L, but is zero-centered, regularized by magnitude and decision effect, and constrained by the same contact/dissipation support. If its removal preserves robust feasible sets and action order, H collapses exactly to P.

The physical parameter polytope and residual last-layer set are propagated with shared scenario/support indices so dependence is preserved; naive independent interval addition is forbidden. Their calibrated composed set alone has hard authority. During ADAPTING-IN-ENVELOPE, the full independently calibrated physical-plus-residual envelope governs all constraints.

H is not retained because hybrids appear richer. It survives only in a strict complementarity interval: P must have a persistent action-relevant residual, and the physical skeleton must materially reduce support/data burden or uncertainty inflation relative to L. Otherwise H is eliminated in favor of the appropriate simpler route.

## 6. Shared robust normal–tangential impedance selection

For candidate (Cin{P,L,H}), let (mathcal O_C(h_k,a_{k:k+H-1})) be the currently authorized outcome set. The controller solves

[
min_{a_{k:k+H-1}};
max_{oinmathcal O_C}
sum_{i=1}^{H}
left(
q_f e_{f,i}^2+q_p|e_{t,i}|^2
+q_c,ell_{m contact}(o_i)
+q_s,ell_{m slip}(o_i)
+|Delta a_i|_R^2
ight)
]

subject, for every authorized outcome member, to:

- six-axis force/torque bounds and finite-area tangential/torsional capacity;
- contact-retention and slip margins;
- joint torque/velocity, actuator saturation and gain/rate limits;
- a discrete interaction-energy account;
- terminal availability of the registered withdrawal transition.

The energy state is internal:

[
E_{i+1}=E_i-h,w_i^{c	op}v_i^c-W_{m ref,i}-W_{m varK,i}
-ar W_{m delay,i}-ar W_{m inner,i},qquad E_ige0.
]

Putting this account inside feasibility prevents a downstream passivity filter from changing a gain already declared feasible. If a simpler post-hoc passivity mechanism produces identical applied actions and the same guarantee, the internal mechanism is demoted.

The horizon is the shortest one covering measured relaxation, pre-slip, gain-rate and energy states that change the first action. It collapses to (H=1) when one-step action equivalence is demonstrated. Warm-started direct multiple shooting with RTI-SQP is the initial low-dimensional smooth realization. Solver brand, horizon number and facet count are selected by accuracy/WCET evidence and are not Contributions.

## 7. Deadline-total applied action

At the beginning of every cycle, before optional optimization, the controller verifies a withdrawal tuple that stops tangential reference motion, transitions within gain-rate limits to dissipative gains and unloads along the last trusted normal. Completed scan candidates are checked against the same authorized uncertainty and energy objects. At the deadline:

[
u_k=
egin{cases}
	ext{lowest-cost fully verified SCAN}, & 	ext{if one completed};\
	ext{registered WITHDRAW}, & 	ext{otherwise}.
end{cases}
]

Timeout and infeasibility therefore change performance, not action existence. Holding the previous gain or shifting an incumbent is not assumed safe after contact/support change; it remains a rival. Full viability is unnecessary while withdrawal availability holds and becomes relevant only if that assumption is falsified.

## 8. Online algorithm

For each control update:

1. Build the contact-frame observation and update representation state.
2. Evaluate support/consistency before authorizing any contraction.
3. Select SUPPORTED, ADAPTING_IN_ENVELOPE or OUTSIDE_ENVELOPE hard authority.
4. Verify and register WITHDRAW under that authority.
5. Generate the candidate-specific multistep outcome set for gain sequences.
6. Warm-start robust MPC and accept only completed, fully verified candidates.
7. Atomically publish SCAN or WITHDRAW at the deadline.
8. Log containment, active constraints, energy, timing and action-order diagnostics needed by the later Principle tests.

The method is executable without deciding the eventual scientific winner: P, L and H have explicit states, update laws, outputs, constraints, authority and publication semantics.

## 9. Natural Contributions

The shared contribution is a **calibration-authority-preserving total impedance relation**: in-envelope changes revert hard authority to an independently valid outcome envelope before any action, recover useful adaptation only after certified re-entry, and bind wrench/contact/energy/actuator/deadline constraints to the gain actually applied.

Candidate-specific contributions are conditional claims for Human Selection:

- **P:** a decision-pruned hereditary finite-area contact-capacity set that turns normal relaxation, pre-slip and force–moment capacity into an online containing object for joint normal–tangential impedance choice.
- **L:** a structure-constrained causal outcome tube trained for robust impedance decision equivalence and simultaneous hard-margin coverage, rather than a point learned dynamics model placed before conventional MPVIC.
- **H:** a zero-collapsible physical-plus-decision-residual representation with dependent joint calibration and a strict complementarity criterion, rather than unconstrained model stacking.
- Across all routes, a shortest decision-relevant robust MPC and verified SCAN/WITHDRAW relation prevents model/optimization/realization decoupling without elevating solver or incumbent bookkeeping to the scientific Principle.

VES and SPO are used as Evidence-informed Target derivations: downstream decision equivalence scopes the retained contact state and decision-aware loss shapes the learned alternatives. R13 does not claim a formal Source Mechanism transfer because the required pre-Source discovery/terminology/search provenance is not validly ordered. Predict-then-calibrate contributes calibration discipline only and supplies no hard-safety guarantee. No neural, estimator or solver genealogy is fabricated.

## 10. Required validation basis and technical risks

The next phase must discriminate, not merely tune:

- **P/L/H representation:** held-out simultaneous coverage, physical-family noncontainment, action-order difference, joint outcome and data/model burden.
- **Adaptation realization:** fixed-facet membership versus bounded set-MHE/multiple-model under abrupt in-envelope change, including the interval before detection and certified re-entry.
- **Action class:** four gains versus richer coupled/SPD impedance.
- **Horizon:** multistep versus one-step action equivalence.
- **Energy:** internal account versus post-hoc passivity, measured at the actually applied action.
- **Totality:** forced timeout, infeasibility, support loss and withdrawal feasibility.

The highest risks are an unusably conservative full-envelope authority, unobservable physical parameters, learned-tube undercoverage, hybrid uncertainty inflation, invalid withdrawal assumptions and missed WCET. These are explicit failure conditions; no experiment or theoretical property is claimed as already established.

R13 is a Human-Selection decision packet, not a FINAL_METHOD_PACKET and not evidence that any Candidate has passed its future tests.
