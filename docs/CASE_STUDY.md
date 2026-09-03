# SaaSForge — Technical Case Study

## Problem

AI can generate code quickly, but code generation alone does not answer whether an idea is commercially sensible, technically coherent, or worth the implementation cost. SaaSForge addresses the larger workflow: discover candidate building blocks, evaluate them, combine compatible tools into a product recipe, apply an explicit feasibility verdict, and only then generate a structured scaffold.

## System Flow

1. **Discover** open-source tools from GitHub and HuggingFace.
2. **Evaluate** product/technical potential using structured scoring.
3. **Recipe** combines selected tools with explicit roles and an execution plan.
4. **Feasibility** returns `GO`, `NO_GO`, or `RISKY` with rationale and confidence.
5. **Scaffold** generates a manifest-first project structure and developer handoff.

## Engineering Decisions

### Explicit rejection is a feature

The feasibility layer can reject an idea. This is a deliberate boundary around generative AI: generation should not be the default outcome simply because generation is possible.

### Metadata-aware composition

Recipe selection uses repository metadata to control cost rather than blindly re-running full evaluations for every candidate tool.

### Manifest-first generation

The generator establishes the project file plan before writing individual files. This improves coherence and makes progress/failure visible at the file level.

### Partial failure instead of total failure

Each generated file is isolated. A failed file becomes a visible failure artifact while the rest of the scaffold continues.

### Handoff over illusion of completion

The generated `ZCODE_TASK.md` explicitly tells the next developer/agent what remains. This prevents the scaffold from being represented as a finished product.

## Controlled-Run Evidence

The supplied project record describes controlled production runs. The most portfolio-relevant examples are:

- **ContentFlow AI:** five-tool recipe; compatibility 82/100; feasibility verdict `RISKY` at 68% confidence.
- **KalamAI:** 19 generated files, approximately 6,400 lines, under five minutes, approximately $0.50 AI cost, and 95% per-file success for that run.

These are **controlled-run results**, not generalized benchmarks.

## Tradeoffs

- More feasibility analysis adds latency/cost before generation but can prevent expensive weak builds.
- Metadata-driven recipe selection reduces evaluation cost but does not replace deep technical compatibility analysis.
- File-by-file LLM generation improves isolation but creates many model calls and requires cross-file context discipline.
- Generated scaffolds accelerate implementation but still require engineering review, integration, tests, and deployment validation.

## Portfolio Boundary

This case study describes the engineering system without publishing the private production source. The goal is reviewable evidence, not source disclosure.
