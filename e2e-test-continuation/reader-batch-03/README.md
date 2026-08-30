# Reader Batch 03 - E2E Test Continuation

**TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**

This directory is an explicitly E2E-specific continuation after the recorded project-hook runtime blocker. It is not a formal ARIS `paper_reader` dispatch, contains no attestation receipt or lifecycle event, and does not alter the admitted read-event manifest. These cards are based on local full-text scientific reading of exactly the five user-supplied PDFs. No web search was used.

Every result below is **TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED**.

| Result | Evidence type | Scientific scope | Status |
|---|---|---|---|
| `evidence-03NKTnfzQ6sJ.json` | Primary scalar theory + simulation + robot experiments | PD/adaptive feedforward impedance compensation for unknown rigid surfaces | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-B5eAiFMsuvgJ.json` | Primary stochastic-control formulation + simulation only | Risk-sensitive optimization of quadruped whole-body feedback gains under contact-location uncertainty | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-m1A_ZYZ-UQwJ.json` | Primary scalar theory + simulation + robot experiments | Traditional force-error adaptive impedance with delayed robust position compensation | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-JSUpYYWBjK0J.json` | Primary approximate theory + simulation + robot experiments | Dynamic scheduling of hybrid-impedance adaptation rate for deformable support | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |
| `evidence-vql7SZH2jUQJ.json` | Primary sampled passivity theory + robot experiments | Energy-bounded force updates for uncertain passive stiffness contacts | TEST-ONLY / NOT FORMALLY ATTESTED / NOT SUBMITTED |

## Scientific limitations shared across the batch

- Three force-tracking papers reduce contact to a scalar spring and rely on ideal or strongly regulated position tracking. Their gain bounds do not directly cover general mass-damper environments, coupled multi-axis contact, friction, separation, saturation, filtering, or arbitrary delays.
- The risk-sensitive quadruped study is simulation-only, locally optimal, covariance-dependent, and uses a predefined contact schedule. It provides no passivity or closed-loop contact-stability certificate.
- The energy-bounding paper gives the strongest sampled interaction certificate in this batch, but only if its experimentally identified, pose-dependent dissipation parameter is accurate or conservative and the environment is passive. Its stiff-contact force target is not maintained without a separate reference adjustment.
- Robot comparisons are mainly single plotted executions with scenario-specific tuning and no repetition counts, uncertainty intervals, or inferential statistics.
- Stability, convergence, passivity, force-tracking accuracy, low overshoot, and comprehensive human/workpiece safety are distinct claims. None of these papers establishes all of them simultaneously.

## Write boundary

Only this isolated directory is intended to contain batch-03 outputs. These files are not admitted Evidence Cards, formal submissions, receipts, or replacements for any ARIS lifecycle artifact.
