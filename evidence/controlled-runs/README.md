# SaaSForge — Controlled Run Evidence

The following runs are the concrete basis for the strongest quantitative claims used in this portfolio repository.

## OpenManus — evidence sensitivity

| Condition | Result |
|---|---|
| Evaluation without README evidence | **22/100 · `no_go`** |
| Same candidate after README retrieval | **73/100 · `go`** |

Interpretation: the same evaluation engine changed materially when the evidence available to it changed. This is evidence sensitivity, not a general accuracy benchmark.

## ContentFlow AI — veto independence

| Observation | Result |
|---|---|
| Tools assembled | 5 |
| Compatibility | **82/100** |
| Estimated margin | **89.2%** |
| Feasibility | **RISKY @ 68% confidence** |
| Main risks | competitor density + 15-week MVP timeline |

Interpretation: attractive economics did not force a positive verdict. The separate Feasibility layer surfaced proposal-level risks that remained material.

## KalamAI — scaffold generation

| Observation | Result |
|---|---|
| Files | **19** |
| Approx. lines | **~6,400** |
| Generation time | **<5 min** |
| Approx. AI cost | **~$0.50** |
| Per-file success | **95% in this run** |

Observed artifact categories included specialized backend services, Alembic migration, FastAPI router, React components, README and `ZCODE_TASK.md`.

Interpretation: a substantial structured developer artifact was generated. The observation does not imply that the scaffold was a finished production product or that the figures generalize to every run.

## Evidence discipline

These are intentionally described as **controlled runs**. Future broad claims should require a defined benchmark dataset, repeated runs, measurement methodology and retained artifacts.
