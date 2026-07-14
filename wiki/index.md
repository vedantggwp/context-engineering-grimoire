# Wiki Index

> Master catalog of all articles. Updated during the compile stage.

Last updated: 2026-07-12
Total articles: 14

---

## Foundations

| Article | Summary | Updated |
|---------|---------|---------|
| [[foundations/what-is-context-engineering]] | The discipline of filling an LLM's limited context window with the right information, tools, and format at each step so an agent can plausibly solve its task. | 2026-07-10 |
| [[foundations/context-vs-prompt-engineering]] | How context engineering broadens prompt engineering — from crafting one static string to curating an entire dynamic, stateful information payload across turns. | 2026-07-10 |
| [[foundations/context-window-as-working-memory]] | Karpathy's analogy of the LLM as an operating system and its context window as RAM — a finite working memory whose limited capacity makes curation essential. | 2026-07-10 |

## Failure Modes

| Article | Summary | Updated |
|---------|---------|---------|
| [[failure-modes/how-contexts-fail]] | Drew Breunig's four ways long contexts break agents: poisoning, distraction, confusion, and clash. | 2026-07-10 |
| [[failure-modes/lost-in-the-middle]] | Liu et al.'s U-shaped curve: models best use information at the start or end of the input and degrade when it sits in the middle. | 2026-07-10 |

## Strategies

| Article | Summary | Updated |
|---------|---------|---------|
| [[strategies/four-context-strategies]] | LangChain's four buckets — write, select, compress, and isolate context — with concrete examples of each. | 2026-07-10 |
| [[strategies/context-management-tactics]] | Drew Breunig's six tactics — RAG, tool loadout, quarantine, pruning, summarization, offloading — for mitigating context failures. | 2026-07-10 |
| [[strategies/agentic-search-and-retrieval]] | How agents fetch context on demand — Anthropic's just-in-time retrieval and agentic search vs. pre-loading. | 2026-07-10 |
| [[strategies/agentic-context-engineering]] | ACE treats an agent's context as a self-improving playbook, refined from execution feedback via generation, reflection, and curation — no weight updates. | 2026-07-12 |
| [[strategies/context-compression-optimization]] | ACON compresses agent observations and history by optimizing compression guidelines from failure analysis — gradient-free, 26–54% fewer peak tokens. | 2026-07-12 |

## Agent Engineering

| Article | Summary | Updated |
|---------|---------|---------|
| [[agent-engineering/twelve-factor-agents]] | HumanLayer's 12 principles for reliable LLM software, several directly about owning and shaping the agent's context. | 2026-07-10 |
| [[agent-engineering/lessons-from-manus]] | Six practical context-engineering lessons from rebuilding the Manus agent framework, from KV-cache design to keeping failures in context. | 2026-07-10 |
| [[agent-engineering/context-engineering-templates]] | Two open templates operationalizing context engineering: coleam00's PRP/CLAUDE.md workflow and davidkimai's atoms-to-fields progression. | 2026-07-10 |

## Evaluation

| Article | Summary | Updated |
|---------|---------|---------|
| [[evaluation/evals-for-llm-systems]] | Why evals are the starting point, Hamel's three levels of evaluation, evaluating RAG, and Eugene Yan's metrics and pitfalls. | 2026-07-10 |

---

## Coverage Summary

| Topic | Articles | Avg. Confidence | Last Updated |
|-------|----------|-----------------|--------------|
| Foundations | 3 | P0 | 2026-07-10 |
| Failure Modes | 2 | P0–P1 | 2026-07-10 |
| Strategies | 5 | P0–P1 | 2026-07-12 |
| Agent Engineering | 3 | P0–P1 | 2026-07-10 |
| Evaluation | 1 | P1 | 2026-07-10 |

## Source Fidelity

This wiki is a deliberately messy-source knowledge base. Of 17 usable raw captures,
**3 are `full` (verbatim GitHub READMEs)** and **14 are `extract` (HTML extraction of
blogs and arXiv papers)**. Twelve of fourteen articles therefore rest on `extract`-fidelity
sources — see each article's frontmatter and the coverage-gaps report.
