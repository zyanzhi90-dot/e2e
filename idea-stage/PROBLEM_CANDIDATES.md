# Problem Candidate — P-SOFTSCAN-STATE-01 v2

## Primary research question

How can a robot select time-varying normal–tangential impedance online during continuous soft-contact scanning to minimize a joint force-regulation, trajectory-tracking, and contact-loss or slip objective while respecting wrench and interaction-energy constraints across material, geometry, sliding-condition, and contact-history shifts absent from its calibration data?

## Why this is the problem—not the method

The central object is the online impedance decision and its closed-loop force–motion–contact consequence. Contact models, state representations, estimators, critics, MPC, uncertainty bounds, passivity devices, and safety filters are possible explanations or later design choices; none is assumed to be the answer.

The previously accepted question asked which extra contact-information components were useful. That is retained only as a possible downstream diagnostic or ablation. It no longer defines the scientific objective.

## Evidence-backed gap

- Optimal-impedance learning exists for a previously identified scalar Hunt–Crossley environment, but abrupt contact shifts, continuous normal–tangential scanning, enforceable wrench or energy limits, safe exploration, and solver failure remain outside its demonstrated envelope.
- Continuous finite-area robotic-ultrasound and deformable-contact MPC exist, but their impedance is predefined rather than selected online for a held-out joint objective.
- Strong model-agnostic six-dimensional compliant control exists, but it does not solve optimal gain selection and warns that ideal time-varying impedance can lose passivity under constraint.
- Online constrained stiffness-and-damping planning and uncertainty-tightened predictive control exist, but not with the complete continuous-soft-contact problem and held-out failure-inclusive comparison.

## Scope

Rigid ultrasound-like probes; continuous sliding over compliant finite-area surfaces; bounded normal and tangential stiffness/damping; matched data, sensing, compute, tuning, objective, inner-loop, and safety budgets; held-out material, geometry, speed, direction, history, tangential-load, and disturbance shifts; quantitative force/wrench, motion, contact-failure, energy, real-time, and independent scanning outcomes.

Excluded: universal tissue modeling, unrestricted physical exploration, universal clinical or injury claims, and any precommitment to HC, PFC, MPC, a critic architecture, or a particular safety mechanism.

## Decisive test

An online selector must outperform the strongest matched fixed, scheduled, model-agnostic, contact-aware predefined-impedance, and uncertainty-aware comparators on held-out joint performance while satisfying every predeclared wrench, contact, energy, actuator, and computation limit. Timeouts, infeasibility, and fallback events count against it. Equivalence of the strongest conventional baselines falsifies the claimed need for a new general principle in the stated envelope.

## Formal evidence anchors

`j4KJ1EdUyYAJ`, `QPuQ4sfxSTIJ`, `USER-TASE-2023-3282974`, `USER-TRO-2022-3216078`, `ZDySGaNsMn8J`, `_8NXx8-VkcgJ`, `USER-TUFFC-2011-1961`, `Ubx3Xkv4y2kJ`, and `9vRJMR2dgG4J`.

## User-material provenance

The problem was re-centered from the user's 2026-08-22 SoftScan synthesis, especially the optimal-impedance draft, contact-model draft, contact-model survey, and literature–method relation map. Those materials define the high-value hypothesis and system intent; formal scientific claims remain bound to accepted Controller evidence.
