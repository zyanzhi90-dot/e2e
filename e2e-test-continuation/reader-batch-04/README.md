# Reader batch 04 - E2E test continuation

**TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**

This directory contains an isolated E2E-specific continuation after the recorded project-hook discovery blocker. The cards are genuine local full-text scientific readings, but they are not formal ARIS `paper_reader` outputs, were not submitted to formal state, have no manufactured receipt or lifecycle event, and must not be counted as a canonical Harness pass.

No web search was used. Each supplied PDF was read through local PDF text extraction, and each SHA-256 digest was checked against the user-supplied binding before card creation.

## Results

| Result | Binding | Scientific evidence level | Test status |
|---|---|---|---|
| `evidence-grfxImawk1MJ.json` | `39e41cc972bd44679392e73fe5073916` / `85ef8da4d6fd9a1f67fd2af0afa32d0dcfa585546908c60533c7d236bf12a8be` | Conditional stability/dissipativity analysis plus two-link simulations; no hardware | **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED** |
| `evidence-GkLovpurDJwJ.json` | `44e90e266fd24befa91615934b11d53c` / `fb205ecf3d6bda309dcdb708b49f736d6e5283b18de999e6a8b9398c75efff00` | Bounded-estimation/model-reaching analysis plus Franka hardware experiments | **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED** |
| `evidence-5WHAHKnl3fYJ.json` | `939498bf84d841dc8b561e1271b413dc` / `da5a79ede8701777f68dc7f504c8daed3ec2b17d2bb0ed20ae7c69f2e1a28c65` | Learned-model/CEM simulations and simplified Franka hardware tasks; no stability theorem | **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED** |
| `evidence-_Li1FYysbT8J.json` | `d65b553e469d47c78765aa4db7b20e31` / `8fc536b4d97b68ec0150bef32055375f6255cfb4dfede46af3f2570565f40b10` | Model-free PI2 derivation and two robot simulations; no hardware/contact validation | **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED** |
| `evidence-W_DvVyMz0QAJ.json` | `2d7d9bf03251466aa6e310a0c88d174c` / `3065887a09e2ac25bd90e75973a3433df4ed07255aa44e67c71bd292b38def56` | Flexible-joint GAS/passivity analysis plus simulation and Baxter demonstrations | **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED** |

## Cross-paper scientific limitations

- Analytical guarantees are conditional on each paper's robot/contact model, gain inequalities, smoothness, sensing, and implementation assumptions; they are not interchangeable safety certificates.
- The UDE and PI2 papers provide simulation evidence only.
- The unified model-reaching experiments assume decoupled Kelvin-Voigt single contact and known uncertainty bounds, and explicitly do not preserve passivity under arbitrary target-matrix variation.
- Deep MPVIC explicitly lacks a stability guarantee, omits constraints in the evaluated CEM controller, cannot directly handle discontinuous contact, and runs hardware planning at 5 Hz.
- The flexible-joint paper's Baxter experiments use its more model- and state-dependent full-state controller, not a clean experimental test of the minimally model-based motor-side variant.
- Hardware studies are limited demonstrations without repeated-trial uncertainty intervals or statistical tests; simulation comparisons also use selected tasks, weights, bounds, and baselines.

All cards distinguish author-reported outcomes from inferred failure mechanisms and retain assumptions and boundary conditions in dedicated fields.

**TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**
