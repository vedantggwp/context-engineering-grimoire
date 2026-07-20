---
title: "Multi-Agent Context Isolation"
summary: "Anthropic's production account of the isolate strategy: subagents with separate context windows explore in parallel and return condensed findings, buying capacity at roughly 15× the token cost."
tags: [strategies, agents, multi-agent, isolation, architecture]
sources:
  - url: "https://www.anthropic.com/engineering/multi-agent-research-system"
    title: "How we built our multi-agent research system (Anthropic Engineering)"
    accessed: 2026-07-20
updated: 2026-07-20
confidence: P0
---

# Multi-Agent Context Isolation

## Overview

"Isolate" is the fourth strategy in LangChain's write/select/compress/isolate
taxonomy (see [[strategies/four-context-strategies]]), and it is the one most
often described abstractly. Anthropic's engineering post on their Research feature
is the canonical production account of what isolation actually costs and buys.

The framing is explicitly a context-engineering one:

> "The essence of search is compression: distilling insights from a vast corpus.
> Subagents facilitate compression by operating in parallel with their own context
> windows, exploring different aspects of the question simultaneously before
> condensing the most important tokens for the lead research agent."

The key insight is that a subagent is not primarily a specialization mechanism —
it is a **context window you get to spend and then throw away**. The subagent may
burn tens of thousands of tokens exploring; only its distilled findings enter the
lead agent's context. Separation of concerns ("distinct tools, prompts, and
exploration trajectories") is a second-order benefit that "reduces path
dependency."

## Key Capabilities

- **Buys context capacity through parallelism** — multiple independent windows
  rather than one window that fills up.
- **Measured gain**: a system with Claude Opus 4 leading and Claude Sonnet 4
  subagents "outperformed single-agent Claude Opus 4 by 90.2% on our internal
  research eval."
- **Explains why it works**: on BrowseComp, "three factors explained 95% of the
  performance variance... token usage by itself explains 80% of the variance,"
  with tool-call count and model choice as the others.
- **States its own cost**: "agents typically use about 4× more tokens than chat
  interactions, and multi-agent systems use about 15× more tokens than chats."
- **States where it does not apply** — an unusually candid negative result for a
  vendor engineering post.

## How It Works

An orchestrator-worker pattern. A LeadResearcher agent analyzes the query,
develops a strategy, and spawns subagents that explore different aspects in
parallel. Each subagent searches independently, uses interleaved thinking to
evaluate tool results, and returns findings. The lead synthesizes and decides
whether more research is needed. A final CitationAgent attributes claims to
sources.

One detail is a context-engineering tell in itself: the lead agent saves its plan
to Memory "since if the context window exceeds 200,000 tokens it will be truncated
and it is important to retain the plan." That is the **write** strategy operating
underneath the **isolate** strategy — persisting state outside the window because
the window is not durable. See [[strategies/agent-memory]].

The post contrasts this with static RAG: "Traditional approaches using Retrieval
Augmented Generation (RAG) use static retrieval... In contrast, our architecture
uses a multi-step search that dynamically finds relevant information, adapts to
new findings, and analyzes results." This is the same argument made in
[[strategies/agentic-search-and-retrieval]].

### Delegation is the hard part

The failure modes are specific and worth internalizing, because they are what
isolation costs you: the lead agent must transfer intent across a context
boundary, and that boundary is lossy.

> "Each subagent needs an objective, an output format, guidance on the tools and
> sources to use, and clear task boundaries. Without detailed task descriptions,
> agents duplicate work, leave gaps, or fail to find necessary information."

The concrete example: the instruction 'research the semiconductor shortage' was
vague enough that one subagent explored the 2021 automotive chip crisis while two
others duplicated work on 2025 supply chains. Early versions also produced
"spawning 50 subagents for simple queries, scouring the web endlessly for
nonexistent sources, and distracting each other with excessive updates."

The fix was explicit effort-scaling rules embedded in the prompt: "Simple
fact-finding requires just 1 agent with 3-10 tool calls, direct comparisons might
need 2-4 subagents with 10-15 calls each, and complex research might use more than
10 subagents with clearly divided responsibilities."

### Other stated lessons

- **Tool design gates everything.** "An agent searching the web for context that
  only exists in Slack is doomed from the start." With MCP servers, agents
  "encounter unseen tools with descriptions of wildly varying quality."
- **Start wide, then narrow.** "Agents often default to overly long, specific
  queries that return few results."
- **Thinking as a scratchpad.** Extended thinking for lead-agent planning;
  interleaved thinking in subagents after tool results.
- **Parallelism at two levels** — lead spawns 3-5 subagents in parallel; subagents
  use 3+ tools in parallel. Together these "cut research time by up to 90% for
  complex queries."
- **Agents can improve their own tooling.** A tool-testing agent that rewrote a
  flawed MCP tool description produced "a 40% decrease in task completion time for
  future agents using the new description."

### Evaluating an isolated-context system

Path-based evaluation breaks: "Even with identical starting points, agents might
take completely different valid paths to reach their goal." The post's evaluation
advice aligns closely with [[evaluation/evals-for-llm-systems]]:

- Start immediately with ~20 queries. "A prompt tweak might boost success rates
  from 30% to 80%. With effect sizes this large, you can spot changes with just a
  few test cases."
- LLM-as-judge against a rubric (factual accuracy, citation accuracy,
  completeness, source quality, tool efficiency). "A single LLM call with a single
  prompt outputting scores from 0.0-1.0 and a pass-fail grade was the most
  consistent."
- Human evaluation still catches what automation misses — testers found early
  agents "consistently chose SEO-optimized content farms over authoritative but
  less highly-ranked sources like academic PDFs or personal blogs."

## Usage Examples

### Example: Decide whether isolation is worth 15× tokens

The post is explicit that this is an economic question: multi-agent systems
"require tasks where the value of the task is high enough to pay for the increased
performance." Breadth-first research over many independent directions qualifies.

### Example: Write subagent prompts as contracts, not requests

Objective, output format, tool guidance, task boundaries. The semiconductor
example shows that under-specification does not produce a vague answer — it
produces *duplicated work and silent gaps*, which are much harder to detect.

## Limitations

- **Not for tightly-coupled work.** Stated directly: "some domains that require
  all agents to share the same context or involve many dependencies between agents
  are not a good fit... most coding tasks involve fewer truly parallelizable tasks
  than research, and LLM agents are not yet great at coordinating and delegating
  to other agents in real time."
- **Synchronous bottleneck.** As built, "the lead agent can't steer subagents,
  subagents can't coordinate, and the entire system can be blocked while waiting
  for a single subagent to finish searching."
- **Emergent behavior resists testing.** "Small changes to the lead agent can
  unpredictably change how subagents behave."
- **Errors compound across turns.** "One step failing can cause agents to explore
  entirely different trajectories." Mitigations are resume-from-failure plus
  "deterministic safeguards like retry logic and regular checkpoints."
- **Vendor-internal evaluation.** The 90.2% figure is from Anthropic's own
  internal research eval, on Anthropic's own models. It is not independently
  replicated.
- **Point-in-time.** Written June 2025 against Claude 4-era models.

## See Also

- [[strategies/four-context-strategies]] — this is the "isolate" strategy in production
- [[strategies/agent-memory]] — the lead agent's plan-persistence is the "write" strategy underneath
- [[strategies/agentic-search-and-retrieval]] — the dynamic-retrieval argument this post makes against static RAG
- [[agent-engineering/twelve-factor-agents]] — overlapping principles on owning control flow and context construction
- [[evaluation/evals-for-llm-systems]] — the outcome-based evaluation stance this post adopts
- [[failure-modes/context-rot]] — the length degradation that makes buying extra windows worthwhile
- [[strategies/context-management-tactics]] — Breunig names this tactic Context Quarantine and situates it among five others
- [[strategies/context-compression-optimization]] — ACON reaches the same goal by optimizing compression instead of buying extra windows
