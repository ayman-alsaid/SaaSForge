# SaaSForge — Testing & Verification

This document separates implementation claims from controlled-run evidence. The figures below are not presented as generalized benchmarks.

## Controlled evidence 1 — evidence sensitivity

**Candidate:** OpenManus

- Evaluation without README evidence: **22/100 · `no_go`**.
- Evaluation after README retrieval: **73/100 · `go`**.
- The richer run produced a named competitor map and cost/MVP analysis.

**Classification:** VERIFIED IN A CONTROLLED RUN.

**What it supports:** the evaluation responds materially to available evidence.

**What it does not support:** statistical scoring accuracy across repositories.

## Controlled evidence 2 — independent feasibility judgment

**Recipe:** ContentFlow AI

- 5 tools assembled into roles.
- Compatibility: **82/100**.
- Estimated margin: **89.2%**.
- Feasibility verdict: **RISKY @ 68% confidence**.
- Material concerns: competitor density and 15-week MVP timeline.

**Classification:** VERIFIED IN A CONTROLLED RUN.

**What it supports:** the Feasibility layer can return a cautious verdict even when economics/tool fit appear attractive.

## Controlled evidence 3 — scaffold artifact

**Recipe:** KalamAI / Arabic TTS

- **19 files**.
- approximately **6,400 lines**.
- **<5 minutes**.
- approximately **$0.50 AI cost**.
- **95% per-file success in that run**.
- Included specialized backend services, Alembic migration, FastAPI router, React components, README and `ZCODE_TASK.md`.

**Classification:** VERIFIED IN A CONTROLLED RUN.

**What it supports:** the generator produced a substantial structured scaffold artifact in the observed run.

**What it does not support:** universal speed/cost/reliability or production readiness of every generated scaffold.

## Verification vocabulary

- IMPLEMENTED — documented capability exists in the system.
- TESTED — supported by explicit tests when test evidence is available.
- VERIFIED IN A CONTROLLED RUN — observed in a bounded documented execution.
- DEPLOYED — running in a deployment environment when directly supported.
- MEASURED — measured across a defined methodology/sample.
- ESTIMATED — calculated or projected, not measured broadly.
- SCAFFOLDED — generated structure exists but is not equivalent to a finished feature/product.
- PLANNED — intended future work.
- NOT YET VALIDATED — insufficient evidence for a stronger claim.

## Public-claim rule

A controlled run is evidence of that run. It is not silently promoted into a benchmark. A scaffold is evidence of generation, not evidence of a finished production application.
