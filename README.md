# SaaSForge — Engineering Evidence

<p align="center">
  <img src="https://img.shields.io/badge/SaaSForge-AI%20Product%20Infrastructure-5B78A6?style=flat-square" alt="SaaSForge" />
  <img src="https://img.shields.io/badge/Pipeline-Discover%20→%20Evaluate%20→%20Recipe-6F86B5?style=flat-square" alt="Pipeline" />
  <img src="https://img.shields.io/badge/Decision%20Gate-GO%20%7C%20NO__GO%20%7C%20RISKY-6F927F?style=flat-square" alt="Decision Gate" />
  <img src="https://img.shields.io/badge/Evidence-Controlled%20Runs-8A78A8?style=flat-square" alt="Controlled Runs" />
</p>

> **From product idea to feasibility-checked project scaffold.**

SaaSForge is a five-stage AI research and product-generation pipeline:

**Discover → Evaluate → Recipe → Feasibility → Scaffold**

This public repository is an **Engineering Evidence / Technical Case Study** for the private production system. It documents architecture, engineering decisions, controlled-run results, operational boundaries, and known limitations without publishing proprietary production source.

## Current Status

**DEPLOYED / Working system**, based on the project evidence supplied for portfolio preparation. Individual capabilities and numeric claims are classified separately below; deployment does not automatically validate every claim.

**Live product:** https://saasforge.agentcraft.info  
**Portfolio:** https://agentcraft.info

## Engineering Thesis

Generating code is not the hard part of AI-assisted product creation. The harder problem is deciding **what deserves to be built**, combining compatible building blocks, rejecting weak economics or feasibility, and handing a developer a coherent starting point rather than disconnected snippets.

SaaSForge therefore puts an explicit feasibility gate before generation and isolates generation failures file-by-file.

## Pipeline

```text
GitHub / HuggingFace
        │
        ▼
   1. Discover
        │
        ▼
   2. Evaluate
        │
        ▼
     3. Recipe
        │
        ▼
  4. Feasibility
   GO / NO_GO / RISKY
        │
        ▼
    5. Scaffold
 project files + ZCODE_TASK.md
```

## Evidence Summary

| Claim | Status | Evidence boundary |
|---|---|---|
| Five-stage Discover → Evaluate → Recipe → Feasibility → Scaffold pipeline exists | **IMPLEMENTED** | Supported by the supplied project architecture and API/operations documentation. |
| Feasibility produces explicit `GO` / `NO_GO` / `RISKY` verdicts | **IMPLEMENTED** | Supported by project documentation and controlled-run examples. |
| ContentFlow AI returned `RISKY` at 68% confidence | **VERIFIED IN A CONTROLLED RUN** | Reported in supplied run evidence; a single run, not a benchmark. |
| KalamAI scaffold generated 19 files and ~6,400 lines in under 5 minutes at ~$0.50 AI cost | **VERIFIED IN A CONTROLLED RUN** | Reported for one controlled production run; not generalized performance. |
| KalamAI generation had 95% per-file success | **VERIFIED IN A CONTROLLED RUN** | Applies to the documented run only. |
| Generated scaffold is a complete production product | **NOT YET VALIDATED** | A generated scaffold is explicitly not treated as a completed product. |
| Public evidence repository contains the production source | **NOT APPLICABLE** | Production source remains private by portfolio design. |

## Key Engineering Decisions

### 1. Feasibility before generation

The system does not assume that a technically composable idea is commercially or operationally worth building. The feasibility layer evaluates technical risk, unit economics, market conditions, and implementation burden before generation.

### 2. Manifest-first scaffold generation

Before generating files, SaaSForge designs the file manifest and responsibilities. Generation then proceeds file-by-file with project context and cross-file awareness.

### 3. Per-file failure isolation

A failed generation step does not abort the entire scaffold. The failed file is recorded and replaced by an explicit error placeholder while the remaining generation continues.

### 4. Developer handoff as a first-class artifact

`ZCODE_TASK.md` captures what was generated, source-tool roles, remaining implementation work, priorities, protected template areas, and success criteria. The scaffold is treated as the beginning of implementation—not proof that implementation is finished.

### 5. Cost-bearing work behind explicit gates

Evaluation, recipe generation, feasibility, and scaffold generation are accounted for through a credit model, while heavy generation work is executed asynchronously.

## Architecture

```text
Frontend
   │
   ▼
FastAPI API ───────────────► PostgreSQL + pgvector
   │
   ├──────────────► Redis / Celery workers
   │                       │
   │                       ▼
   │                  AIClient layer
   │                       │
   ▼                       ▼
Discovery sources      OpenRouter models
GitHub / HuggingFace
```

Operational documentation supplied for the project describes Dockerized database, Redis, backend, worker, and frontend services with reverse-proxy/TLS infrastructure.

## Verification Notes

The strongest supplied evidence is a set of controlled end-to-end runs, including:

- OpenManus discovery/evaluation behavior;
- ContentFlow AI recipe + feasibility verdict;
- KalamAI scaffold generation.

These results are intentionally labeled **VERIFIED IN A CONTROLLED RUN**, not **MEASURED benchmarks**, because the supplied evidence establishes specific runs rather than a repeatable performance study across workloads.

## Security and Ownership Boundaries

The production implementation is private. Public evidence should not expose credentials, environment values, proprietary source, or sensitive infrastructure details. Owner-only access is documented for evaluation, recipe, and scaffold artifacts in the production system.

Legacy repository links, identity, and email addresses from older project documentation are not carried into this evidence repository.

## Known Limitations

- A scaffold is not equivalent to a finished or production-ready application.
- Single controlled-run generation time/cost/success values must not be generalized into benchmarks.
- Template-inherited capabilities should be distinguished from SaaSForge-specific implementation when detailed evidence is published.
- Claims about backups, monitoring, restore behavior, billing, and other inherited infrastructure should be independently re-verified before being elevated as SaaSForge-specific evidence.
- Any current production metrics should be re-measured before publication if they are presented as current measurements.

## Repository Map

- [`docs/CASE_STUDY.md`](docs/CASE_STUDY.md) — engineering narrative and tradeoffs.
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — system boundaries and pipeline.
- [`docs/TESTING_AND_VERIFICATION.md`](docs/TESTING_AND_VERIFICATION.md) — controlled-run evidence classification.
- [`docs/LIMITATIONS.md`](docs/LIMITATIONS.md) — known evidence and product boundaries.
- [`evidence/controlled-runs/README.md`](evidence/controlled-runs/README.md) — controlled-run evidence index.
- [`PORTFOLIO_NOTICE.md`](PORTFOLIO_NOTICE.md) — source and usage boundary.

## Review Path

**AgentCraft project page → Engineering Evidence repository → Architecture & decisions → Controlled-run evidence → Live product**

---

**Ayman Alsaid** · Senior AI / Product Engineer  
AgentCraft · https://agentcraft.info · contact@agentcraft.info
