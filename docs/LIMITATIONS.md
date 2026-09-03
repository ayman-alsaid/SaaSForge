# SaaSForge — Limitations & Boundaries

Strong engineering evidence includes the edges of what the evidence does **not** establish.

## 1. Recipe selection uses proxy signals

Candidate selection before assembly relies heavily on metadata such as license, stars, language and recency instead of full deep evaluation for every discovered candidate. This reduces cost/latency but can miss weaknesses a full evaluation might surface.

## 2. Hard gates require calibration

`margin <20% → NO_GO` is an explicit product-policy choice, not a universal economic law. A different organization, vertical or risk profile may require a different threshold. Hard gates become dangerous if their authority exceeds the quality of their calibration.

## 3. Controlled runs are not benchmarks

The OpenManus, ContentFlow and KalamAI results are bounded observations. They should not be interpreted as population-wide accuracy, latency, cost or success-rate guarantees.

## 4. Scaffold ≠ finished SaaS

Generated files are a starting codebase and developer handoff. Integration, review, security validation, product-specific testing and deployment work can remain. The repository intentionally does not call a scaffold an autonomously completed production product.

## 5. LLM reasoning remains probabilistic

Retries, structured parsing, repair and partial-state handling improve operational reliability but do not make model reasoning deterministic or infallible.

## 6. Evidence quality constrains evaluation quality

The 22→73 OpenManus contrast is useful precisely because it shows this limitation: weak/missing evidence can materially change a verdict. Better evidence ingestion is therefore part of decision quality, not merely an enrichment feature.

## 7. Generalization is architectural, not automatic

`Discover → Score → Assemble → Veto → Generate` can be adapted to other screening domains, but evidence sources, thresholds, regulations, safety rules and human oversight must be redesigned for each domain.

## 8. Public repository scope

This repository documents the engineering evidence; it does not publish the private production source code. Reviewers can evaluate architecture, decisions, controlled evidence and limitations, but cannot independently reproduce every production claim from this public repository alone.
