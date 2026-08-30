# Problem Candidate Index

## P-SOFTSCAN-STATE-01 — When does soft-contact state actually change optimal impedance?

**Research question.** During continuous finite-area probe contact with soft and heterogeneous environments, under which measurable combinations of contact-history timescale, local geometry/contact-area change, sliding rate, and material shift does a scalar memoryless force–indentation/rate representation cease to be decision-equivalent to a stateful representation for selecting impedance?

**Why this is the mature problem.** The user’s HC-upgrade hypothesis survives only after a material reframing. Existing work already covers HC-based learned optimal impedance (`j4KJ1EdUyYAJ`), soft-contact-aware ultrasound MPC (`QPuQ4sfxSTIJ`), deformable-contact-aware MPC (`ZDySGaNsMn8J`), low-order viscoelastic tissue identification plus force control (`iasK6kEBzIEJ`), real-time PFC (`_-WpL4QNulUJ`), and learned uncertainty-tightened force MPC (`9vRJMR2dgG4J`). The unresolved delta is therefore not “use a richer contact model,” but whether any added state is identifiable and changes the impedance decision or outcome under held-out continuous-contact shifts.

**Competing explanations.** A minimal relaxation/contact-area state may become necessary when material and motion timescales interact; robust wrench feedback or calibrated output uncertainty may instead absorb the mismatch; apparent model benefits may be calibration, sensing, geometry, or comparator artifacts; or detailed states may be non-identifiable and collapse to a smaller sufficient statistic.

**Decisive falsifier.** If matched memoryless or contact-model-agnostic baselines choose equivalent impedance and match stateful models on held-out transient wrench, constraint, contact-loss/slip, and independent scanning-quality outcomes—and the added states are not observable or reproducible—the richer contact-state hypothesis is rejected for that regime.

**Feasible probe.** A randomized repeated-measures phantom study spanning relaxation time, stiffness, thickness/curvature, sliding speed, dwell history, and withheld specimens, followed by a bounded ex-vivo transfer check. Equalize data, compute, objectives, and constraints across model-agnostic, HC-like, finite-area memoryless, and low-order stateful representations; test prediction, observability, selected impedance trajectories, and failure-inclusive closed-loop outcomes.

**Value under either answer.** A positive result yields a regime boundary and minimal sufficient contact state. A negative result prevents unjustified constitutive complexity and redirects work toward robust feedback, uncertainty calibration, sensing, and constraint design.

**Primary formal evidence.** `j4KJ1EdUyYAJ`, `QPuQ4sfxSTIJ`, `USER-TASE-2023-3282974`, `USER-TRO-2022-3216078`, `ZDySGaNsMn8J`, `_8NXx8-VkcgJ`, `USER-TUFFC-2011-1961`, `iasK6kEBzIEJ`, `Ubx3Xkv4y2kJ`, `_-WpL4QNulUJ`, `9vRJMR2dgG4J`, `0n2WntIcZ5MJ`.

**Current status.** Mature Candidate awaiting the independent Problem Quality Gate; not yet certified, novel, or human accepted.
