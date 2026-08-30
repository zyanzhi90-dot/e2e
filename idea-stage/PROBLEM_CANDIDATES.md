# Problem Candidate Index

## P-SOFTSCAN-STATE-01 — Which additional contact information actually improves optimal impedance?

**Research question.** In continuous soft-contact tasks, compared with existing simple contact representations, under which practical operating conditions do additional contact information—contact area, local geometry, tangential force or moment, and contact history, separately or in combination—improve optimal impedance decisions and held-out closed-loop outcomes, and what is the minimal sufficient contact representation required to achieve each improvement?

**Why this is the mature problem.** Existing work already covers HC-based learned optimal impedance (`j4KJ1EdUyYAJ`), soft-contact-aware ultrasound MPC (`QPuQ4sfxSTIJ`), deformable-contact-aware MPC (`ZDySGaNsMn8J`), low-order viscoelastic tissue identification plus force control (`iasK6kEBzIEJ`), real-time PFC (`_-WpL4QNulUJ`), and learned uncertainty-tightened force MPC (`9vRJMR2dgG4J`). The unresolved delta is therefore not “use a richer contact model” or “add history state,” but which additional contact information changes the impedance decision or a closed-loop outcome in which practical regime, and how much of that information is minimally sufficient.

**Competing explanations.** Contact area or geometry may matter under curvature, thickness, or contact-patch change; tangential wrench information may matter near sliding, stick–slip, or moment-capacity limits; history may matter when material and motion timescales interact; robust wrench feedback or calibrated uncertainty may absorb these effects; or only a smaller latent statistic—or no extra information—may be reproducibly useful.

**Decisive falsifier.** For each added-information component and their combinations, if matched simple or contact-model-agnostic baselines choose equivalent impedance and match the richer representation on held-out transient wrench, constraint, contact-loss/slip, and independent scanning-quality outcomes, that component is not control-relevant in the tested regime. A claimed minimal representation must also be observable, reproducible, and incrementally predictive.

**Feasible probe.** A randomized repeated-measures phantom study spanning relaxation time, stiffness, thickness/curvature, contact-area change, sliding speed, direction, dwell history, and withheld specimens, followed by a bounded ex-vivo transfer check. Equalize data, compute, objectives, and constraints; use nested ablations that add area, geometry, tangential wrench, and history information separately and in justified combinations; compare selected impedance trajectories and failure-inclusive closed-loop outcomes rather than fit alone.

**Value under either answer.** A positive result yields an information-by-regime map and the minimal sufficient contact representation for each supported benefit. A negative or selective result prevents unjustified sensing and modeling complexity and redirects work toward robust feedback, uncertainty calibration, and constraint design.

**Primary formal evidence.** `j4KJ1EdUyYAJ`, `QPuQ4sfxSTIJ`, `USER-TASE-2023-3282974`, `USER-TRO-2022-3216078`, `ZDySGaNsMn8J`, `_8NXx8-VkcgJ`, `USER-TUFFC-2011-1961`, `iasK6kEBzIEJ`, `Ubx3Xkv4y2kJ`, `_-WpL4QNulUJ`, `9vRJMR2dgG4J`, `0n2WntIcZ5MJ`.

**Current status.** Revised Candidate awaiting the independent Problem Quality Gate; not yet recertified, re-audited for novelty, or human accepted.
