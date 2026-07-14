# Ingest Log

## 2026-07-10 — Batch ingest: Context Engineering for LLM Agents (15 sources)

Deliberately messy-source corpus (blogs, papers, forum posts, GitHub) to test fetch-ladder
generalization off docs-sites.

- Sources fetched via `node dist/research.js fetch` (verbatim-first ladder).
- Fidelity distribution: 3 `full` (github-readme), 12 `extract` (html-extract), 3 `failed`/rejected (2 blocked, 1 off-topic).
- Actions: Created 12 articles across foundations, failure-modes, strategies, agent-engineering, evaluation.

### Captured (usable)
- foundations: the-new-skill-context-engineering (extract), effective-context-engineering-anthropic (extract), context-engineering-simonwillison (extract), survey-of-context-engineering (extract)
- failure-modes: how-long-contexts-fail (extract), lost-in-the-middle (extract)
- strategies: context-engineering-for-agents-langchain (extract), how-to-fix-your-context (extract), context-engineering-lance-martin (extract)
- agent-engineering: twelve-factor-agents (**full**), lessons-from-building-manus (extract), context-engineering-intro-template (**full**), context-engineering-davidkimai (**full**)
- evaluation: patterns-for-llm-systems (extract), your-ai-product-needs-evals (extract)

### Failed / rejected
- arXiv 2307.03172 **v3** HTML — `failed` (no acceptable capture; used v1 HTML instead).
- Reddit r/LocalLLaMA thread — `failed` at both research.js ladder and WebFetch fallback (forum block).
- HN item 44427757 — captured `extract` (200) but content was an off-topic C-compiler thread; rejected. `extract` certifies faithfulness-to-page, not relevance-to-topic.

## 2026-07-12 — Update run (automated)
- sources-added: 2
- articles-changed: 8
- connections-made: 5
- Added ACE (arXiv 2510.04618) → strategies/agentic-context-engineering and ACON (arXiv 2510.00615) → strategies/context-compression-optimization, both extract-fidelity from freely-licensed arXiv abstracts.
- Wove both into the graph: 5 new cross-references; links 52 → 80, graph density 0.248 → 0.294.
- Rejected 2 marginal connection candidates (appended to _config/update.md exclusions).
- Freshness: 14 fresh / 0 aging / 0 stale. No stale verification (verify_stale: false).
