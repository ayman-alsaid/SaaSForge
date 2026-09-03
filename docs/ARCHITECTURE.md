# Architecture

## Core Components

- **Frontend:** project discovery, evaluation, recipe, feasibility, scaffold progress/download flows.
- **FastAPI backend:** API/auth/business orchestration.
- **PostgreSQL + pgvector:** persisted tools, evaluations, recipes, scaffolds, and related state.
- **Redis + Celery:** asynchronous heavy work.
- **AIClient:** shared abstraction for model-backed evaluation/generation operations.
- **Discovery integrations:** GitHub and HuggingFace sources.

## Control Boundary

The central control boundary is:

```text
candidate idea
   ↓
evaluation
   ↓
recipe
   ↓
FEASIBILITY VERDICT
   ├─ NO_GO → stop/rework
   ├─ RISKY → explicit risk decision
   └─ GO → scaffold generation
```

The LLM contributes analysis and generation, but the product workflow preserves explicit gates and status artifacts around those operations.

## Async Boundary

Long-running generation is moved to background workers. The UI polls progress and receives file-level completion/failure state instead of blocking an HTTP request for the entire generation run.

## Failure Boundary

Scaffold generation is isolated per file. A single file failure is recorded without destroying the entire run.
