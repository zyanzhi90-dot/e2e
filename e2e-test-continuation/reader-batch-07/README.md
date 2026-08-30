# Reader batch 07 — E2E test continuation

**TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**

This directory contains five evidence cards produced as an explicitly E2E-specific continuation after a recorded runtime blocker. The cards are scientific-reading outputs only. They are not a formal ARIS `paper_reader` dispatch, do not constitute lifecycle evidence, and must not be treated as attested or submitted artifacts.

Every result in this directory is **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**.

## Contents

- `evidence-sXzu51TTSB0J.json` — variable-impedance skill-prior reinforcement learning for three contact-rich insertion tasks; physical Franka experiments, but no formal stability or passivity guarantee.
- `evidence-VnY2KfSKIPgJ.json` — episodic natural actor-critic learning of a stiffness ellipse; analytical static-impedance conditions and planar simulations only.
- `evidence-ur6FQs3aZv0J.json` — event-triggered adaptive neural impedance control; conditional local-UUB and non-Zeno analysis, numerical simulation, and CoppeliaSim UR5 tests rather than physical hardware.
- `evidence-f7_NpbvAX4cJ.json` — fuzzy-neural impedance control with barrier Lyapunov functions; conditional constraint/boundedness results and four simulation cases only.
- `evidence-jF_ZhPcGGroJ.json` — adaptive neural impedance control with an auxiliary saturation-compensation system; conditional Lyapunov results and 2-DOF simulations only.

## Scientific-use cautions

- Learning curves, success rates, and stiffness adaptations in the two reinforcement-learning papers are empirical and task-distribution dependent; neither paper proves policy convergence, passivity, or safe exploration.
- The three adaptive-control papers prove boundedness or convergence to neighborhoods under stated model, approximation, gain, initial-condition, and sensing assumptions. These are not unconditional physical-interaction safety guarantees.
- The event-triggered paper calls its CoppeliaSim UR5 work an experimental test, but the full text identifies it as a virtual robotic system; it is therefore recorded as simulation evidence, not physical hardware evidence.
- No receipts, lifecycle events, or formal attestations were created.
