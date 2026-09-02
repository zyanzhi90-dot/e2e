# Root-Cause Analysis — P-SOFTSCAN-STATE-01 v2

Analysis ID: `RCA-P-SOFTSCAN-STATE-01-V2`

Problem: `P-SOFTSCAN-STATE-01`

Problem contract: `0b107ec8b177e29e4a1770ff1a6b3a2765f06a506d1b7093171c96c6321fd9f5`

Evidence capsule: `2a11f572f53aa2d7bf82f56826f61cb35bcc88f0df0aae4be1cdd77a76027b32`

Necessity binding: `NEC-P-SOFTSCAN-STATE-01-V2`; closure `8cdffde60027bbd8d5695ec2d7817fa3015c36f6c9a04043cf05ab427d7bfc81`; verdict `a8d4752676604f84ad6bf7fa19c12f1e`; verdict artifact `a7598220a221586b353fdd46e87c4824dfb889d132d3981ee76a0190c61d1e29`; residual `RF-HELDOUT-JOINT-IMPEDANCE-REGRET`.

## Diagnosis outcome

The residual is explained by two linked but independently falsifiable mechanism failures. First, held-out material, relaxation, geometry, friction, sliding, and loading history can make the controller's current representation alias contact conditions with different future wrench or capacity relations. If those distinctions change feasible or joint-cost-minimizing impedance, a fixed, scheduled, or contact-agnostic selector returns a non-equivalent action. Second, a nominally desirable online gain can still be unrealizable or unsafe when gain selection is separated from rendered interaction energy, finite-area wrench/contact capacity, actuator and gain-rate reachability, solve deadline, infeasibility, and fallback state.

## Primary causal chains

### CHAIN-CONTACT-STATE-TO-ACTION-ALIASING

Held-out continuous-contact changes alter latent contact mechanics → a calibration-fixed or memoryless state aliases conditions with different future wrench/capacity → prediction or uncertainty becomes biased or overconfident → the same apparent state maps to a gain that is not action-equivalent → joint force-motion-contact regret, contact loss/slip, hard-limit activation, or inability to choose coupled normal/tangential gains follows.

Intervention target: the observation-to-decision relation must retain only contact-capacity/history coordinates that demonstrably change feasible gains or held-out outcomes. Strong alternatives remain live: six-dimensional feedback may already be sufficient; matched tuning may erase the residual; a calibrated memoryless finite-area model may be enough.

Falsifier: an oracle contact-capacity/history state changes neither selected gains nor any primary held-out outcome beyond equivalence margins under matched sensing, compute, tuning, objective, inner-loop, and safety budgets.

### CHAIN-GAIN-SELECTION-TO-FEASIBILITY-DECOUPLING

Online normal/tangential gains are optimized near active limits → performance selection is separated from rendered energy and physical/real-time feasibility → the nominal gain approaches wrench/slip/moment boundaries, injects energy, exceeds actuator or gain-rate capacity, or misses its deadline → post-hoc projection or fallback changes the realized action → violation, oscillation, timeout, fallback, or excess joint cost follows.

Intervention target: evaluate gain choice in the same update-time feasibility relation that includes wrench/contact capacity, interaction energy, actuator and gain-rate reachability, deadline, infeasibility, and deterministic fallback.

Falsifier: joint feasibility never changes the realized action or fallback decision and never improves any primary outcome or violation rate over a separated planner with matched standard post-hoc guards.

## Boundary carried into Method Design

This RCA does not preselect a contact model, estimator, critic, MPC, passivity device, or learning method. It requires candidate principles to repair one or both causal relations with a minimal intervention, and it rejects extra contact variables that improve fit but do not change impedance actions or held-out outcomes. The user's pre-existing SoftScan files remain an ordinary candidate only and have not influenced the priority or status of either chain.
