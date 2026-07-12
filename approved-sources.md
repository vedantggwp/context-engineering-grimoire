# Approved Sources: Context Engineering for LLM Agents
Approved: 2026-07-10
Total: 18 attempted / 15 usable
Note: Deliberately messy-source topic (blogs, papers, forum posts, GitHub) — NOT a docs site. Selected to test fetch-ladder generalization.

## Ingest Queue

| # | URL | Title | Type | Tier | Fidelity | Method | Status |
|---|-----|-------|------|------|----------|--------|--------|
| 1 | https://www.philschmid.de/context-engineering | The New Skill in AI is Context Engineering | article | P1 | extract | html-extract | ingested |
| 2 | https://blog.langchain.com/context-engineering-for-agents/ | Context Engineering for Agents (LangChain) | article | P0 | extract | html-extract | ingested |
| 3 | https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents | Effective Context Engineering for AI Agents | article | P0 | extract | html-extract | ingested |
| 4 | https://simonwillison.net/2025/Jun/27/context-engineering/ | Context Engineering (Simon Willison) | article | P1 | extract | html-extract | ingested |
| 5 | https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html | How Long Contexts Fail (Drew Breunig) | article | P1 | extract | html-extract | ingested |
| 6 | https://www.dbreunig.com/2025/06/26/how-to-fix-your-context.html | How to Fix Your Context (Drew Breunig) | article | P1 | extract | html-extract | ingested |
| 7 | https://rlancemartin.github.io/2025/06/23/context_engineering/ | Context Engineering (Lance Martin) | article | P1 | extract | html-extract | ingested |
| 8 | https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus | Lessons from Building Manus | article | P1 | extract | html-extract | ingested |
| 9 | https://github.com/humanlayer/12-factor-agents | 12-Factor Agents | repository | P0 | full | github-readme | ingested |
| 10 | https://github.com/coleam00/context-engineering-intro | Context Engineering Intro Template | repository | P1 | full | github-readme | ingested |
| 11 | https://github.com/davidkimai/Context-Engineering | Context-Engineering (davidkimai) | repository | P1 | full | github-readme | ingested |
| 12 | https://arxiv.org/html/2507.13334v1 | A Survey of Context Engineering for LLMs | paper | P0 | extract | html-extract | ingested |
| 13 | https://arxiv.org/html/2307.03172v1 | Lost in the Middle (Liu et al.) | paper | P0 | extract | html-extract | ingested |
| 14 | https://eugeneyan.com/writing/llm-patterns/ | Patterns for Building LLM Systems (Eugene Yan) | article | P1 | extract | html-extract | ingested |
| 15 | https://hamel.dev/blog/posts/evals/ | Your AI Product Needs Evals (Hamel Husain) | article | P1 | extract | html-extract | ingested |
| 16 | https://arxiv.org/html/2307.03172v3 | Lost in the Middle (v3 HTML) | paper | P0 | failed | failed | failed |
| 17 | https://www.reddit.com/r/LocalLLaMA/comments/1lm3wag/... | Reddit: context engineering thread | thread | P2 | failed | failed | failed |
| 18 | https://news.ycombinator.com/item?id=44427757 | HN thread | thread | P2 | extract | html-extract | rejected (off-topic capture) |

## Failure notes
- **#16** arXiv HTML fulltext for v3 returns no acceptable capture; the v1 HTML render exists and was used instead (#13). arXiv HTML availability is version-dependent.
- **#17** Reddit blocked at BOTH the research.js ladder and the WebFetch fallback rung. Forum content is the ladder's hard weak spot.
- **#18** HN item captured cleanly (`fidelity: extract`, 200) but the extracted text was an off-topic thread about C compilers. `extract` certifies faithfulness-to-page, not relevance-to-topic — curation still load-bearing. Rejected from corpus.
