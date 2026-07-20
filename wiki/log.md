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

## 2026-07-20 — Update run (automated)
- sources-added: 4
- articles-changed: 19
- connections-made: 5
- Added Chroma Context Rot → failure-modes/context-rot, Anthropic multi-agent research system → strategies/multi-agent-context-isolation, Memory in the Age of AI Agents (arXiv 2512.13564) → strategies/agent-memory, Context Engineering 2.0 (arXiv 2510.26493) → foundations/context-engineering-as-a-discipline. All four extract-fidelity.
- Content graph went from 3 connected components to 1 — memory and multi-agent isolation were the missing bridges. Links 80 → 138, density 0.294 → 0.329.
- Repaired 18 one-directional backlinks; accepted 5 mined connections, rejected 1 (appended to _config/update.md exclusions), left 14 unjudged rather than filling to cap.
- Rewrote wiki/overview.md to the full compile spec — it previously lacked Open Questions and Coverage Gaps, which starved delta scout of gap-driven angles.
- Two new articles rest on arXiv abstracts alone and carry explicit fidelity warnings. Four wiki articles are now abstract-fidelity.
- Claim audit: 0 unsupported / 0 review across 18 articles. Two plausible-but-unverifiable figures from search summaries were discarded rather than written.
- Freshness: 18 fresh / 0 aging / 0 stale. No stale verification (verify_stale: false); no checked: dates bumped.
