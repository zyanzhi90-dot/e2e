# Reader Batch 02 — E2E Test Continuation

**TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**

This directory contains an explicitly E2E-specific continuation produced after the recorded project-hook runtime blocker. It is not a formal ARIS `paper_reader` dispatch, contains no receipt or lifecycle event, and does not alter the admitted read-event manifest. The cards reflect local full-text scientific reading of exactly the five user-supplied PDFs; no web search was used.

Every result below has the status **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**.

| Result | Source type | Scientific scope | Status |
|---|---|---|---|
| `evidence-X3fLqXpiIBoJ.json` | Primary theory + small robot experiment | Passive state-feedback control of learned dynamical-system motions with selective damping and an energy tank | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-lLfdIFGp-oYJ.json` | Primary theory + simulation + single robot/human demonstration | MPC complementary input constrained by a passivity-energy state | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-sXrm1sT8KVIJ.json` | Primary theory + simulation + robot/phantom demonstration | Passivity filters that reshape diagonal variable-impedance profiles | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-ioYGUCT49JIJ.json` | Primary theory + 1-DOF benchtop experiment | Robust passivity under bounded motor-model uncertainty and frequency-limited passivity relaxation | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-d7xxRHNdSMAJ.json` | Primary simulation + small human-participant robot experiment | Joint design of Cartesian variable damping and redundancy resolution for cooperative writing | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |

## Scientific limitations shared across the batch

- The passivity and stability claims are conditional on each paper's chosen model, interaction port, environment class, and implementation assumptions. They are not interchangeable and are not comprehensive human-safety certificates.
- The experimental evidence is narrow: one plate-placement task, one Panda sawing demonstration, one robot/phantom needle task, one 1-DOF series-elastic testbed, and one five-participant writing study (with a three-participant subcomparison).
- Several implementations lack sampled-data, saturation, delay, contact-discontinuity, or uncertainty guarantees even when their continuous analytical model is passive.
- Comparative conclusions are task- and tuning-specific. The papers generally do not provide broad replication, preregistration, multi-robot validation, or inferential statistics beyond the limited writing-task tests.
- None of these five papers is a review; each card is treated as primary evidence but distinguishes formal guarantees, author-reported observations, and inference.

## Write boundary

Only this isolated directory is intended to contain batch-02 outputs. No file here should be interpreted as an admitted Evidence Card, a formal submission, or a replacement for an ARIS lifecycle artifact.
