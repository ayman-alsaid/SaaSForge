# SaaSForge — Architecture

## System thesis

SaaSForge separates **candidate discovery**, **evidence evaluation**, **proposal assembly**, **proposal veto**, and **artifact generation** into explicit responsibilities. The separation is intentional: each stage has different incentives and failure modes.

## Topology

```text
Browser / Next.js 15
        │
        │ HTTPS + JWT
        ▼
FastAPI application
 ├─ Discovery service ───────────────► GitHub Search / HuggingFace
 ├─ Evaluation API ─┐
 ├─ Recipe API ─────┼────► Redis broker ─► Celery workers ─► AIClient ─► OpenRouter
 ├─ Scaffold API ───┘                       │
 ├─ Feasibility logic                       └─ shared scaffold volume
 └─ Credits / ownership
        │
        ▼
PostgreSQL 16 + pgvector
```

## Stage responsibilities

### Discover
Captures external candidates and metadata. Upserts by source/repository identity, fetches/caches README evidence, records freshness/license/community signals, and supports deterministic categorization/trend logic.

### Evaluate
Produces structured SaaS-readiness analysis across five weighted dimensions. Quick evaluation intentionally uses a cheaper subset; full evaluation runs asynchronously.

### Recipe
Discovers/filter candidates, selects 3–5 components, assigns explicit roles, constructs commercial packaging and execution phases, persists links, then hands the proposal to Feasibility.

### Feasibility
A separate judgment boundary. It consumes the assembled proposal and evaluates market saturation, economics, build time and worst-case exposure. Hard gates such as margin `<20%` are not averaged away by narrative scores.

### Scaffold
Designs a manifest first, generates product-specific files independently, records per-file failures, produces `ZCODE_TASK.md`, packages a ZIP and exposes owner-scoped download.

## Async lifecycle

```text
client POST
   │
   ├─ validate ownership / credits
   ├─ create working row
   ├─ append ledger debit
   └─ dispatch Celery task
             │
             ├─ AIClient calls / external work
             ├─ progress persisted
             └─ terminal state: done / done_partial / failed

client polls GET until terminal state
```

Broker dispatch failure is handled explicitly so a debit cannot disappear into a job that never started.

## AI reliability boundary

AIClient centralizes provider interaction: retries, backoff, structured parsing/repair and the `partial` flag. This prevents each engine from inventing different semantics for malformed/degraded model responses.

## Data responsibilities

Domain tables documented in the source material include tools, tool evaluations, recipes, recipe-tool role links, scaffolds, trend signals, watchlists and reports. Alembic manages schema evolution. pgvector is available within the PostgreSQL layer rather than requiring a separate vector database.

## Trust boundaries

- JWT authentication at the API boundary.
- User-scoped resources enforce ownership.
- Database and Redis remain private infrastructure dependencies.
- Public-facing services sit behind reverse-proxy/TLS infrastructure.
- Secrets are environment-managed and excluded from the public evidence repository.
- Generated scaffold downloads are owner-scoped.

## Why this architecture matters

The main boundary is conceptual as much as technical:

```text
PROPOSER                     JUDGE
Recipe ───────────────────► Feasibility
“Can I assemble this?”       “Should this survive?”
```

Keeping these roles separate is the project's central engineering decision.
