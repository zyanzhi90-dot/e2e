# Literature Reuse Audit v2

Date: 2026-08-13  
Run: `impedance-control-landscape-e2e`

## Reuse rule

The legacy run is an evidence source, not an accepted synthesis. Identity-verified metadata, registered local full text, completed read receipts, and accepted Evidence Cards may be reused. Legacy taxonomy, stage ordering, inclusion decisions, and Field Map claims must be re-evaluated under the narrowed impedance-control Research Brief.

## Baseline

- Deduplicated legacy candidates: 171.
- Accepted full-text Evidence Cards: 19.
- Legacy candidates requiring a new explicit screening record: 171; the old run predates the schema-v2 screening contract.
- Old Field Map: not directly reusable as a canonical conclusion; usable only as a hypothesis/checklist.

## Direct Evidence candidates for migration

The following accepted Evidence Cards do not require another full-text read. They will receive a new scope and priority decision; only their evidence-supported content may migrate.

- Classical formulation and implementation: Hogan Part II (1985), stable contact execution (1987), contact-transition stability (1997).
- Adaptive/robust/optimal development: direct adaptive impedance (1993), accuracy/robustness dilemma (2009), impedance learning in unknown environments (2014), optimal robot-environment impedance adaptation (2014), stability of variable impedance control (2016), adaptive VIC for dynamic force tracking (2018).
- Flexible/compliant implementation and passivity: passivity-based flexible-joint impedance control (2008), passivity preservation for VIC (2020).
- Learning-based progression: early learning impedance control (1998), learning VIC (2011), end-effector impedance as an RL action space (2019), contact-sensitive learning VIC (2020), IRL for force-related VIC (2021), composite-learning VIC with stability/passivity (2024), stable state-dependent learned VIC (2025).
- Review map: *Variable Impedance Control and Learning—A Review* (2020).

## Known gaps before supplemental search

- Hogan Part I, needed to ground the original theory rather than relying on implementation Part II alone.
- A complementary recent authoritative survey, especially the known 2019 tutorial survey, to reduce dependence on one review taxonomy.
- Major high-citation backbone candidates already present in the legacy corpus but lacking accepted Evidence Cards, including Cartesian impedance control for redundant/flexible-joint robots, target-dynamics force tracking in unknown environments, and early reinforcement-learning impedance control.
- Systematic 2021–2026 target-venue screening, with full text only for reviews, high-citation backbone, and representative/decision-critical frontier work.
- A defensible account of what follows early learning-based VIC: stability/passivity guarantees, model-based and data-efficient learning, contact-rich adaptation, and their unresolved generalization/safety limitations.

## Explicit non-reuse

- *A Unified Passivity Based Control Framework for Position, Torque and Impedance Control of Flexible Joint Robots* is excluded from the reading set by user decision. It may remain in the corpus with that exclusion reason but must not support a decision-grade claim.
- Peripheral teleoperation/haptics, legged/whole-body, biomechanical measurement, actuator-only, prosthetics, and generic interaction/RL papers are not retained merely because the legacy search found them. They require a direct impedance-control mechanism contribution.

## Duplication-avoidance decision

No broad 1984–2026 search will be repeated. The existing corpus supplies the historical candidate pool. Supplemental retrieval is limited to recent reviews, a few exact missing backbone identities, and venue-bounded 2021–2026 frontier searches. All candidates are deduplicated by DOI first and normalized title plus author second.
