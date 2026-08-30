# Literature Rerun v2 Changelog

## 2026-08-13

- Formally returned the accepted landscape to `QUERY_PLANNING` through the scope Human Gate's `request_revision` path, based on the user's instruction to rerun the landscape under the revised Harness.
- Added `LITERATURE_REUSE_AUDIT_V2.md`: 171 legacy candidates retained as a discovery baseline; 19 accepted Evidence Cards designated for scope-aware reuse rather than duplicate reading; the old Field Map is treated only as a hypothesis.
- Added `QUERY_PLAN_V2_REUSE_FIRST.json`: recent-review first, then missing high-citation backbone, then 2021–2026 target-venue frontier coverage, with explicit saturation criteria and title/abstract screening for every deduplicated candidate.
- Kept the user-excluded unified passivity framework outside the reading set.
- Harness migration fix: accepted Evidence Cards can serve as `FULL_TEXT` screening proof after rollback, while a new explicit out-of-scope decision is not overwritten by legacy evidence reconciliation. Regression tests pass.
- Completed all 28 executable Query Plan v2 searches; three historical exact-title lookups that could not be completed online were closed against identity-verified legacy records rather than repeated provider calls.
- Re-screened the merged 442-record corpus under the revised impedance-control-only brief: 19 legacy decision-grade papers were migrated, 91 additional records were selected for full-text reading, 94 were retained for discovery/abstract-level mapping, 236 were excluded as irrelevant, and 2 were deduplicated.
- Automatically downloaded and read two available arXiv frontier papers: `Safe and Optimal Variable Impedance Control via Certified Reinforcement Learning` and `Deep Model Predictive Variable Impedance Control`. Both Evidence Cards passed validation and were accepted canonically.
- Consolidated the remaining 89 publisher-restricted full texts into one formal `HUMAN_SEARCH_REQUIRED` batch, in accordance with the approved access policy. No per-publisher-page probing was performed.
- Added `FULLTEXT_DOWNLOAD_BATCH_V2.md`, ordered as reviews/field maps, high-citation mechanism backbone, and recent elite-venue frontier. The explicitly rejected unified passivity paper is not in the batch.
