<div align="center">

# SaaSForge

### From raw opportunity to evidence-based decision — then to a working scaffold.

![Decision Pipeline](https://img.shields.io/badge/Discover_%E2%86%92_Evaluate_%E2%86%92_Recipe_%E2%86%92_Feasibility_%E2%86%92_Scaffold-5B6F8A?style=flat-square)
![Evidence Anchored](https://img.shields.io/badge/Evidence--Anchored_Scoring-547C78?style=flat-square)
![Hard Veto](https://img.shields.io/badge/Hard_Feasibility_Veto-75658A?style=flat-square)
![Controlled Run](https://img.shields.io/badge/Controlled_Run-19_files_%C2%B7_~6%2C400_lines_%C2%B7_%3C5_min-667F91?style=flat-square)
![Portfolio](https://img.shields.io/badge/Engineering_Evidence-6F7782?style=flat-square)

**SaaSForge is not an “AI startup idea generator.”** It is a decision-support and product-engineering pipeline designed around a harder question:

> **Can an AI system propose something compelling — and still be structurally capable of rejecting its own proposal?**

[Live Product](https://saasforge.agentcraft.info) · [Architecture](docs/ARCHITECTURE.md) · [Technical Case Study](docs/CASE_STUDY.md) · [Verification](docs/TESTING_AND_VERIFICATION.md) · [Limitations](docs/LIMITATIONS.md)

</div>

---

## Why this project exists

Product discovery often combines several different judgments in one informal process: search for candidate technologies, decide which are credible, imagine how they could form a product, estimate commercial viability, and finally start building. The problem is not only that this takes time. The reasoning is often inconsistent, difficult to audit, and vulnerable to optimism once a promising narrative has formed.

SaaSForge turns that process into an explicit five-stage system:

```text
External corpus
     │
     ▼
 DISCOVER ──► EVALUATE ──► RECIPE ──► FEASIBILITY ──► SCAFFOLD
 GitHub/HF     evidence      assemble     independent      codebase
 candidates    scoring       3–5 tools    veto layer       + handoff
```

The engineering thesis is deliberately stronger than “use an LLM to generate ideas”:

> **Proposal generation and proposal approval should not be the same decision.**

The Recipe stage is rewarded for finding a coherent product. The Feasibility stage is designed to be skeptical of that product. Some constraints are weighted judgments; others are hard gates that a persuasive narrative cannot offset.

---

## The five-stage pipeline

| Stage | Purpose | Engineering behavior |
|---|---|---|
| **1 · Discover** | Find reusable open-source building blocks | Searches GitHub and HuggingFace, captures metadata, categorizes, deduplicates, tracks freshness and trend signals |
| **2 · Evaluate** | Decide whether an individual tool is commercially useful | 5 weighted dimensions: Market 30, Technical 25, Conversion 20, Differentiation 15, License 10 |
| **3 · Recipe** | Assemble 3–5 candidates into one coherent product | Assigns pipeline roles, compatibility, commercial packaging, execution plan and MVP estimates |
| **4 · Feasibility** | Challenge the assembled proposal | Separate judgment layer: market saturation, unit economics, build time and worst-case exposure; emits GO / NO_GO / RISKY |
| **5 · Scaffold** | Turn an approved recipe into a developer starting point | Manifest-first generation, file-by-file isolation, ZIP packaging and `ZCODE_TASK.md` handoff |

Slow AI stages run through Celery/Redis. Credit-consuming actions are metered. Hard failures trigger refunds. Partial AI responses remain explicitly partial rather than being silently promoted to clean successes.

---

## What makes SaaSForge technically interesting

### 1. A judge that is separate from the proposer

The most important architectural decision is the separation between **Recipe** and **Feasibility**.

A simpler design would ask one model call to assemble a product and then “also judge whether it is viable.” SaaSForge deliberately does not do that. The system that makes the proposal should not be trusted to grade its own work using the same optimistic reasoning that made the proposal coherent.

Feasibility therefore runs as its own meta-layer. It evaluates:

- market saturation and competitor density;
- unit economics;
- build-time exposure;
- bounded worst-case risk;
- explicit key risks and opportunities;
- a final GO / NO_GO / RISKY verdict with confidence and advice.

And one rule is intentionally non-negotiable:

```text
margin < 20%  →  automatic NO_GO
```

That condition is a **hard gate**, not merely a low score that strong performance elsewhere can compensate for.

### 2. Evidence changes the verdict

A controlled evaluation demonstrates why the scoring layer is described as evidence-anchored.

The same OpenManus candidate was evaluated twice:

| Input evidence | Result |
|---|---:|
| README unavailable | **22/100 · `no_go`** |
| README fetched and included | **73/100 · `go`** |

Same candidate. Same evaluation engine. Different available evidence. Different verdict.

This does **not** establish general benchmark accuracy. It is a controlled demonstration that the engine's judgment responds materially to evidence instead of merely repeating an optimistic prior.

### 3. Healthy economics do not guarantee approval

A ContentFlow AI recipe assembled five tools with an **82/100 compatibility score** and an estimated **89.2% margin**. Yet the separate Feasibility layer returned:

> **RISKY · 68% confidence**

The reason was not unit economics. It was competitor density and a 15-week MVP timeline.

That run matters because it demonstrates the intended independence of the judgment layer: a commercially attractive number did not force a positive verdict when other risks remained material.

### 4. Scaffold generation produces an artifact, not another recommendation

For an Arabic TTS recipe (KalamAI), one controlled run generated:

- **19 files**;
- approximately **6,400 lines**;
- in **under 5 minutes**;
- for approximately **$0.50 of AI cost**;
- with **95% per-file success** in that run.

The output included four specialized backend services, an Alembic migration with upgrade/downgrade paths, a five-endpoint FastAPI router, React components, a project README, and `ZCODE_TASK.md` for developer/AI-agent handoff.

These numbers describe **one controlled run**, not a generalized performance benchmark. A generated scaffold is a developer starting point — not a claim that a production SaaS product was autonomously completed.

---

## Inside the engines

### Discover — structured corpus acquisition

Discovery searches GitHub and HuggingFace Spaces in parallel and persists identity, repository metadata, community signals, language, license, README content, freshness and source-specific metrics. Results are upserted by `(source, repo_id)` so repeated searches refresh evidence rather than duplicating entities.

The engine also supports deterministic category assignment, star-velocity ranking, spike detection and watchlists. These are deliberately separated from LLM reasoning where deterministic logic is sufficient.

### Evaluate — five-dimensional SaaS readiness

Full evaluation produces structured analysis across:

| Dimension | Weight | Questions |
|---|---:|---|
| Market | 30 | Is there demand, paid competition and willingness to pay? |
| Technical | 25 | Is this a product, library or experiment? How maintainable and compatible is it? |
| Conversion | 20 | What must be built around it? What are likely MVP/API/GPU costs? |
| Differentiation | 15 | What paid value can exist beyond the free project? |
| License | 10 | Can it be commercialized safely under the detected license? |

Quick evaluation intentionally covers only the cheaper market + technical path; full evaluation runs asynchronously and returns recommendation, reasoning, strengths, weaknesses and structured per-dimension analysis.

### Recipe — coherent assembly rather than a flat shortlist

Recipe generation does not simply rank tools. It assigns selected candidates roles such as `core_engine`, `data_collection`, `processing`, `analysis`, `output` and `integration`, then constructs a product proposal with compatibility, execution phases, pricing, cost/user, margin and MVP timing.

Candidate selection is deliberately metadata-driven before assembly rather than running expensive full evaluation across every discovered candidate. That saves significant cost and latency, but it is a real trade-off documented in [Limitations](docs/LIMITATIONS.md).

### Feasibility — weighted checks plus hard gates

```text
                    RECIPE
                      │
        ┌─────────────┼─────────────┬──────────────┐
        ▼             ▼             ▼              ▼
     MARKET        ECONOMICS      BUILD         WORST CASE
   saturation       margin         time          exposure
        │             │             │              │
        └─────────────┴──────┬──────┴──────────────┘
                             ▼
                  GO / NO_GO / RISKY
                   + confidence + rationale
```

The distinction between **must-meet constraints** and **weighted judgment** is the core design idea. It prevents a strong narrative from “averaging away” a structurally unacceptable number.

### Scaffold — manifest first, files second

The generator first designs a complete manifest, then generates each product-specific file independently. File isolation means one generation failure is recorded and replaced with an explicit placeholder while the rest of the run continues. The backend and worker share a scaffold volume for ZIP generation and owner-only download.

`ZCODE_TASK.md` turns generation into a handoff protocol: what exists, what remains, priorities, protected template areas and measurable success criteria.

---

## Reliability engineering around the LLM

Every model-backed stage routes through a shared `AIClient` abstraction rather than embedding provider calls throughout the application.

Key behaviors include:

- three attempts per call;
- stricter JSON-only retry behavior;
- exponential backoff for 429/5xx responses;
- structured repair/extraction as a final fallback;
- explicit `partial` state when expected data is missing or repaired;
- persisted `done_partial` rather than false clean success;
- model selection through configuration rather than application rewrites.

The important property is not the retry count. It is **honest degradation**: downstream code can distinguish “complete,” “partial,” and “failed.”

---

## Credits and concurrency

The credit subsystem uses an **append-only ledger**: balance is derived from ledger entries rather than treated as an authoritative mutable number.

Balance-sensitive operations use row-level locking (`SELECT ... FOR UPDATE`) to protect concurrent checks/refills from double-spend and double-refill races. Failure policy is explicit:

- hard failure after retries → full refund;
- partial but usable result → preserved and visibly marked, no automatic refund;
- broker dispatch failure → job marked failed and credit refunded.

This is a small subsystem, but it is representative of the broader engineering philosophy: state transitions should remain auditable even when external AI infrastructure behaves imperfectly.

---

## Architecture

```text
┌─────────────────────────────┐
│ Next.js 15 / TypeScript UI  │
└──────────────┬──────────────┘
               │ HTTPS / JWT
┌──────────────▼──────────────┐
│ FastAPI application         │
│ discovery · evaluation      │
│ recipe · feasibility        │
│ scaffold · credits          │
└───────┬───────────┬─────────┘
        │           │
        ▼           ▼
 PostgreSQL      Redis
 + pgvector      broker
        ▲           │
        │           ▼
        │       Celery workers
        │           │
        └───────────┼───────────────┐
                    ▼               ▼
                AIClient       External corpus
                OpenRouter     GitHub / HF
```

See [ARCHITECTURE.md](docs/ARCHITECTURE.md) for responsibilities, trust boundaries, asynchronous execution and data flow.

---

## Verification map

| Claim | Evidence status | Evidence |
|---|---|---|
| Five-stage pipeline implemented | **IMPLEMENTED** | System design and project documentation |
| OpenManus evidence sensitivity | **VERIFIED IN A CONTROLLED RUN** | 22/100 without README → 73/100 with README |
| ContentFlow independent feasibility judgment | **VERIFIED IN A CONTROLLED RUN** | 82 compatibility; 89.2% margin; RISKY @ 68% |
| KalamAI scaffold output | **VERIFIED IN A CONTROLLED RUN** | 19 files; ~6,400 lines; <5 min; ~$0.50; 95% per-file success |
| Generated scaffold = production-ready finished SaaS | **NOT CLAIMED** | Scaffold remains a starting artifact requiring review/integration |
| Controlled-run figures generalize to every recipe | **NOT YET VALIDATED** | No broad benchmark dataset is claimed |

The repository intentionally uses the strongest wording the evidence supports — not the strongest wording a product story would benefit from.

---

## Security and operational boundaries

The documented system includes JWT access/refresh authentication, owner-scoped evaluations/recipes/scaffolds, private database/Redis networking, localhost-bound service ports behind a reverse proxy, split environment configuration, and secret exclusion from source control.

The public repository does **not** publish the proprietary production implementation. It exists to make architecture, engineering decisions, verification and limitations reviewable without exposing private source or credentials.

---

## The architecture beyond SaaS ideation

The deeper reusable pattern is:

```text
Discover → Score → Assemble → Veto → Generate
```

This can be useful wherever a system must screen a large corpus, build a coherent candidate proposal and then protect the final decision from persuasive-but-disqualifying inputs. The content research around SaaSForge explores structural parallels in VC screening, real-estate feasibility, R&D stage-gate reviews, M&A screening and grant allocation.

That does **not** mean thresholds or domain rules transfer automatically. A margin threshold appropriate to SaaS evaluation is not a universal decision rule. What generalizes is the architecture: **weighted evidence plus domain-specific hard gates, with proposal and veto separated.**

This distinction matters because “generalizable architecture” is an engineering claim; “interchangeable domains” would be an unjustified product claim.

---

## What I built vs. what I used

The underlying LLM provides reasoning and generation. The engineering work is the system around that capability:

- corpus discovery and evidence capture;
- weighted evaluation schema;
- role-based multi-tool assembly;
- independent feasibility/veto architecture;
- deterministic economic gates;
- async orchestration and lifecycle states;
- honest partial-result handling;
- append-only credits and concurrency protection;
- manifest-first scaffold generation;
- per-file failure isolation;
- developer handoff protocol.

The contribution is not “an LLM can write code.” It is the architecture that decides **what deserves to be generated, what must be rejected, what evidence supports the decision, and how degradation is represented honestly.**

---

## Technology

`Next.js 15` · `TypeScript` · `FastAPI` · `SQLAlchemy 2` · `PostgreSQL 16` · `pgvector` · `Alembic` · `Celery` · `Redis` · `OpenRouter` · `Docker Compose`

---

## Engineering evidence index

- **[Technical Case Study](docs/CASE_STUDY.md)** — problem, design decisions, alternatives, trade-offs, verification, lessons and generalization.
- **[Architecture](docs/ARCHITECTURE.md)** — topology, stage responsibilities, async flow, trust boundaries and state.
- **[Testing & Verification](docs/TESTING_AND_VERIFICATION.md)** — controlled evidence and claim classification.
- **[Controlled Runs](evidence/controlled-runs/README.md)** — the concrete runs used in public claims.
- **[Limitations](docs/LIMITATIONS.md)** — what the current evidence does not establish.
- **[Portfolio Notice](PORTFOLIO_NOTICE.md)** — public-review scope and private-source boundary.

---

## Review scope

The production source code for SaaSForge is maintained privately and is not distributed through this repository. This repository is an **Engineering Evidence / Technical Case Study**: it is intended to give senior engineers, technical leaders and potential collaborators enough information to assess the architecture, decisions, verification discipline and product-engineering depth without publishing proprietary implementation details or credentials.

**Built by Ayman Alsaid · AgentCraft**

[agentcraft.info](https://agentcraft.info) · [contact@agentcraft.info](mailto:contact@agentcraft.info)
