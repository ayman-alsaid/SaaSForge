# SaaSForge — Technical Case Study

## The question behind the project

Most AI product-building tools optimize for producing a convincing answer to **“what could we build?”** SaaSForge was designed around a second question that is intentionally harder: **“should this be built at all?”**

That distinction drives the architecture. The system is not a single prompt that searches, proposes and approves its own output. It is a staged decision-support pipeline in which proposal generation and proposal judgment have different responsibilities and different failure modes.

## Problem

A typical early product decision combines research, technical evaluation, commercial reasoning and implementation planning in one informal workflow. The judgment is expensive, inconsistent and difficult to audit. Once a coherent idea has been assembled, confirmation bias also becomes a structural risk: the same evidence used to make the proposal sound good can influence the decision about whether it should proceed.

SaaSForge turns this into five explicit stages: Discover, Evaluate, Recipe, Feasibility and Scaffold.

## Design goals

1. Ground evaluation in captured evidence rather than model prior alone.
2. Separate optimistic assembly from skeptical judgment.
3. Encode genuinely disqualifying conditions as hard gates, not soft weights.
4. Preserve partial/degraded AI results honestly.
5. Keep expensive work asynchronous and metered.
6. Generate a concrete developer artifact only after the proposal has passed through the decision pipeline.

## Decision 1 — separate Feasibility from Recipe

**Alternative:** ask the Recipe call to also decide whether its own proposal is viable.

**Rejected because:** the proposer is structurally rewarded for coherence. A self-assessment risks reusing the same optimistic reasoning rather than challenging it.

**Chosen design:** Feasibility consumes the assembled proposal as a separate stage and evaluates market saturation, unit economics, build time and worst-case exposure.

**Trade-off:** extra latency and AI cost in exchange for a more independent decision boundary.

### Controlled evidence

ContentFlow AI had five selected tools, compatibility 82/100 and estimated margin 89.2%. The Feasibility stage nevertheless returned **RISKY at 68% confidence**, identifying competitor density and a 15-week MVP as material risks. This is a controlled demonstration of the intended separation; it is not a claim that the system replaces expert business diligence.

## Decision 2 — hard gate vs. weighted economics

The system treats margin below 20% as an automatic `NO_GO` rather than another feature in a holistic score.

**Why:** a weighted system permits strong narrative or scores elsewhere to compensate for a structurally unacceptable economic condition. A hard gate does not.

**Trade-off:** the rule is deliberately blunt. Threshold calibration is domain- and strategy-specific and should not be mistaken for a universal law.

## Decision 3 — evidence-sensitive evaluation

Full evaluation scores Market (30), Technical (25), Conversion (20), Differentiation (15) and License (10). The structured result is used downstream rather than being only presentation text.

A controlled contrast with OpenManus produced **22/100 `no_go` without README evidence** and **73/100 `go` after the README was fetched**. This supports the narrower claim that available evidence materially affects the evaluation. It does not establish statistical accuracy across arbitrary repositories.

## Decision 4 — metadata-driven Recipe candidate selection

**Alternative:** run full evaluation over every one of 15–20 discovered candidates before assembly.

**Rejected because:** full evaluation is the expensive path. Exhaustive evaluation would significantly increase credits and latency before the system even knows which 3–5 tools will be used.

**Chosen design:** deterministic filters and metadata (license, stars, language, freshness) narrow/rank candidates; the assembled product is then challenged at proposal level.

**Trade-off:** metadata is weaker than a deep evaluation and can miss subtle technical weaknesses. This is documented rather than hidden.

## Decision 5 — manifest-first scaffold generation

The generator designs the file manifest before writing product-specific files. Each file is generated independently with project context and neighboring paths. A failed file receives an explicit placeholder and the run continues.

The handoff artifact, `ZCODE_TASK.md`, records what was generated, remaining work, priorities, protected template areas and success criteria.

### Controlled evidence

A KalamAI Arabic TTS recipe produced **19 files / ~6,400 lines / <5 minutes / ~$0.50**, with **95% per-file success in that run**. The output included specialized services, an Alembic migration, FastAPI routes, React components and the handoff document.

This proves a concrete scaffold artifact was produced in that controlled run. It does not prove every generated scaffold is production-ready or that those cost/time figures generalize.

## Decision 6 — honest partial failure

All model calls pass through a shared AIClient with retries, backoff and repair fallback. A repaired or incomplete response is marked `partial` and persisted as `done_partial` rather than quietly relabeled as success.

The distinction matters because downstream decision quality depends on input quality. A system that hides degraded inputs destroys its own auditability.

## Decision 7 — append-only credit accounting

Credits are represented as ledger entries; balance is derived rather than treated as an authoritative mutable field. Row-level locking protects balance/refill operations from concurrent mutation races. Hard failures refund credits; partial usable results remain recorded as partial.

This design favors auditability and correctness over the minimal implementation of a mutable balance column.

## What I built vs. what I used

OpenRouter-hosted models provide underlying reasoning/generation. A shared SaaS foundation provides common platform capabilities. SaaSForge-specific engineering is the decision pipeline around those capabilities: discovery, evidence model, evaluation schema, assembly, veto architecture, orchestration, failure semantics and scaffold workflow.

The core contribution is therefore not “LLM generates code.” It is a system that decides **what to inspect, what to believe, what to assemble, what to veto, and only then what to generate.**

## Verification summary

| Evidence | Classification |
|---|---|
| Five-stage workflow | IMPLEMENTED |
| OpenManus 22 → 73 evidence contrast | VERIFIED IN A CONTROLLED RUN |
| ContentFlow RISKY @ 68% despite 89.2% margin | VERIFIED IN A CONTROLLED RUN |
| KalamAI 19 files / ~6,400 lines / <5 min / ~$0.50 | VERIFIED IN A CONTROLLED RUN |
| Broad performance/accuracy benchmark | NOT YET VALIDATED |
| Generated scaffold as finished production product | NOT CLAIMED |

## Generalization

The structural abstraction is `Discover → Score → Assemble → Veto → Generate`. Research content around the project explores parallels to VC screening, real-estate feasibility, R&D stage-gate review, M&A target screening and grant allocation.

What transfers is the discipline of evidence-based scoring plus domain-specific hard gates. The actual thresholds, evidence sources, safety requirements and human review rules do not transfer automatically.

## Lesson

The strongest lesson is that **decision-support reliability is not only a scoring problem; it is a boundary-design problem.** A sophisticated score is less useful if the system cannot preserve a hard constraint, distinguish degraded evidence, or keep the judge independent from the proposer.
