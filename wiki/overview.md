# Context Engineering for LLM Agents — Overview

Context engineering is the discipline of filling an LLM's limited context window with
the right information, tools, and format at each step of a task — so that an agent can
plausibly solve it. As the sources in this wiki argue in near-unison (Philipp Schmid,
Anthropic, Simon Willison, LangChain), the field is shifting from *prompt* engineering
(crafting one clever static string) to *context* engineering (curating an entire dynamic,
stateful payload across many turns). The consensus framing, popularized by Tobi Lütke and
Andrej Karpathy, is that most agent failures today are not model failures but **context
failures** — and that the context window behaves like the RAM of an operating system:
finite working memory that must be deliberately managed.

The wiki's spine is [[strategies/four-context-strategies]] — LangChain's write / select /
compress / isolate taxonomy — and most articles here can be read as elaborations of one of
those four buckets. **Select** is developed in [[strategies/agentic-search-and-retrieval]],
where Anthropic's just-in-time retrieval argues for lightweight references resolved at
runtime rather than exhaustive pre-loading. **Compress** runs from Breunig's practical
tactics in [[strategies/context-management-tactics]] through ACON's optimized compression
guidelines. **Isolate** now has a production account in
[[strategies/multi-agent-context-isolation]] — Anthropic's Research system, which buys
extra context windows via subagents at roughly 15× the token cost, and states candidly
where the pattern does not apply. **Write** turns out to be the largest of the four: it
opens onto an entire research field, mapped in [[strategies/agent-memory]], whose 47-author
survey argues that the long/short-term vocabulary most practitioners use is inadequate to
the systems now being built.

Two developments give the wiki a sharper spine than it had. First, the failure-mode
literature has moved from qualitative to measured: Breunig named context distraction,
Liu et al. established the positional U-curve, and Chroma's Context Rot now isolates
*input length itself* as a degradation variable across 18 frontier models with task
complexity held constant — including the counterintuitive finding that logically coherent
haystacks hurt performance more than shuffled ones. Second, the frontier work has stopped
treating context as something assembled fresh each turn.
[[strategies/agentic-context-engineering]] (ACE) treats it as a durable playbook refined
from execution feedback, and the memory literature treats it as a first-class design layer.
The through-line: context is increasingly modeled as *state* rather than *input*, and the
open questions in this wiki are mostly about how to manage that state well.

A note on provenance: this wiki was built deliberately from *messy* sources — practitioner
blogs, arXiv papers, and GitHub repos rather than a single clean documentation site. Of the
twenty-one usable sources behind its eighteen articles, only the three GitHub READMEs were
captured at `full` (verbatim) fidelity; the remaining eighteen are `extract`-fidelity HTML
extractions. Four of those rest on arXiv **abstracts alone** (ACE, ACON, the memory survey,
and Context Engineering 2.0) and carry explicit fidelity warnings at the top of the article.
Where an extract dropped a figure or table, the articles say so rather than reconstruct
numbers from memory. Read every claim against the fidelity label on its source.

## Coverage

- **18 content articles**, 3 support pages
- **21 sources**, 138 cross-references
- **21,288 words** total
- **1 connected component** — every article is reachable from every other. The wiki
  fragmented into 3 components before the 2026-07-20 update; the memory and multi-agent
  articles closed the gaps.

Most-connected articles by centrality: `four-context-strategies` (1.00),
`agentic-search-and-retrieval` (0.95), `context-management-tactics` (0.90),
`agent-memory` (0.85), `agentic-context-engineering` (0.85).

> **Caution on centrality:** `agent-memory` is now the fourth most central article in the
> wiki while resting on an abstract-only capture. High centrality reflects how well a topic
> *connects*, not how well it is *sourced*. Do not read graph position as authority.

## Topic Clusters

All 18 articles form a single connected component. Within it, five thematic clusters:

- **Foundations** — `what-is-context-engineering`, `context-vs-prompt-engineering`,
  `context-window-as-working-memory`, `context-engineering-as-a-discipline`. Definitional
  work plus the RAM mental model. The newest member complicates the others by arguing the
  discipline predates the agent era by two decades.
- **Failure modes** — `how-contexts-fail`, `lost-in-the-middle`, `context-rot`. A
  progression from qualitative taxonomy → positional measurement → length-isolating
  measurement on frontier models.
- **Strategies** — `four-context-strategies`, `context-management-tactics`,
  `agentic-search-and-retrieval`, `agentic-context-engineering`,
  `context-compression-optimization`, `multi-agent-context-isolation`, `agent-memory`. The
  largest cluster and the wiki's center of gravity.
- **Agent engineering** — `twelve-factor-agents`, `lessons-from-manus`,
  `context-engineering-templates`. Hard-won practice; Manus's "file system as context" is
  the practitioner instance of what the memory literature formalizes.
- **Evaluation** — `evals-for-llm-systems`. The loop-closer, and the thinnest cluster.

## Open Questions

- **Does position matter or not?** `lost-in-the-middle` finds a strong U-shaped positional
  effect; `context-rot` finds "no notable variation" across 11 needle positions on its NIAH
  task. These are different tasks and different model generations, but the wiki does not
  currently reconcile them. Which effect dominates in practice, and under what conditions?
- **Why does structural coherence hurt?** Chroma's most surprising result — models perform
  better on shuffled haystacks than logically ordered ones — is left explicitly unexplained.
  If it holds, it has direct implications for how retrieved context should be ordered, which
  cuts against the reranking intuition in `lost-in-the-middle`.
- **Where is the boundary between agent memory and context engineering?** The memory survey
  claims to draw it explicitly. This wiki cannot say how, because only the abstract was read.
  The answer is load-bearing for this wiki's own scope.
- **Is ACE's playbook the same thing as experiential memory?** ACE and the memory survey
  appear to describe overlapping phenomena in non-overlapping vocabulary. Nobody has
  reconciled them here.
- **What is context engineering, formally?** Four sources give four informal definitions.
  `context-engineering-as-a-discipline` promises a systematic one that this wiki has not read.

## Coverage Gaps

- **Evaluation is thin** — one article against seven on strategies. There is no coverage of
  how to evaluate context-engineering choices specifically (as opposed to LLM systems
  generally): no A/B methodology for context changes, no context-specific regression
  testing, no cost/quality frontier analysis.
- **No coverage of cost and latency as first-class constraints.** Token cost appears
  incidentally (ACON's 26–54% reduction, multi-agent's 15×) but no article treats the
  economics of context engineering directly.
- **Nothing on structured output, tool schemas, or prompt caching** as context-engineering
  levers. Manus's KV-cache lesson is the only touchpoint, and it is one bullet.
- **Nothing on failure recovery** — what an agent should do when it detects its own context
  has degraded mid-task.
- **Four articles rest on abstracts alone.** ACE, ACON, `agent-memory`, and
  `context-engineering-as-a-discipline` are maps to their papers, not summaries of them.
  A higher-fidelity ingest run against the PDFs or HTML fulltext is the single
  highest-value improvement available to this wiki.
- **Forum and practitioner-discussion sources remain uncaptured.** Reddit and HN both failed
  or captured off-topic during the initial build; the wiki has no coverage of what
  practitioners argue about, only what they publish.
