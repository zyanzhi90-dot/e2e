# Root-Cause Analysis — P-SOFTSCAN-STATE-01

- **Analysis ID**: RCA-P-SOFTSCAN-STATE-01-v1
- **Problem ID**: P-SOFTSCAN-STATE-01
- **Problem-contract SHA-256**: cac57b1ee7361c18ac6bf52386ce8c9df51a7c12b0f5aacebf3314f336925d8b
- **Evidence-capsule SHA-256**: 187cd6ac4b885567844fab5a983f5bdf2b911613a746d4f5df202e84c1782d81
- **Primary causal chains**: CHAIN-SPATIAL-SUFFICIENCY, CHAIN-TANGENTIAL-SUFFICIENCY, CHAIN-HISTORY-SUFFICIENCY, CHAIN-CLOSED-LOOP-ABSORPTION

## Diagnosis

The central mechanism is representation-dependent state aliasing, not a blanket failure of simple contact models. A simple instantaneous normal-contact representation can be sufficient when measured-wrench feedback and robust margins absorb contact mismatch before it changes constrained impedance actions. It becomes insufficient only when omitted spatial, tangential, or temporal contact state remains predictive after conditioning on the simple variables, survives the closed-loop rejection dynamics, changes the ranking of candidate impedance actions, and produces a material held-out outcome difference.

## CHAIN-SPATIAL-SUFFICIENCY

Changes in contact area, local curvature, thickness, or deformation can map distinct pressure and force distributions to the same scalar normal state. The action-relevant intermediate failure is a biased prediction of force, pressure, friction capacity, or contact-loss margin. The chain is falsified if area and geometry can be removed while impedance actions and all declared held-out outcomes remain within equivalence margins after data, compute, objective, safety, latency, and tuning resources are matched.

## CHAIN-TANGENTIAL-SUFFICIENCY

Near slip and moment-capacity limits, normal force alone does not locate the contact state on the tangential-force and normal-moment limit surface. Tangential force or moment is useful only if it changes impedance before a slip, contact, wrench, or task-quality failure and retains that advantage under friction control and matched resources. It is unnecessary where normal variables and kinematics already proxy the relevant margin.

## CHAIN-HISTORY-SUFFICIENCY

When dwell, reversal, speed change, repeated loading, or disturbance timescales overlap material relaxation or creep, identical instantaneous indentation, rate, and force can imply different future wrench and area evolution. The target is not a maximal constitutive parameter vector: it is the lowest-dimensional observable history statistic that removes action-relevant temporal aliasing. This chain is falsified if a calibrated memoryless or uncertainty-aware baseline remains decision- and outcome-equivalent, or if every useful history statistic fails observability and reproducibility.

## CHAIN-CLOSED-LOOP-ABSORPTION

Richer prediction does not imply richer control. When feedback bandwidth and authority dominate contact variation, disturbances are observable, constraints are inactive, and uncertainty margins cover residual mismatch, different models can produce equivalent impedance actions and outcomes. This chain defines the expected no-benefit region and prevents the diagnosis from presuming that complex representation is superior.

## Discriminating handoff

Downstream method design must preserve matched resources and nested component removal, measure impedance-action differences separately from prediction error, use sensor-grounded equivalence margins, and test independent transient, constraint, contact-stability, and scanning-quality endpoints. It must also distinguish genuine information value from sensor bias, friction or temperature drift, geometry error, estimator leakage, latency, and comparator weakness.

## Remaining uncertainty

The unresolved quantities are the regime boundaries, online observability of each candidate statistic, interaction or redundancy among information components, practical action/outcome equivalence margins, endpoint sensitivity, confound control, and bounded phantom-to-ex-vivo transfer. These uncertainties define discriminating tests; they do not require new literature before independent root-cause review.
