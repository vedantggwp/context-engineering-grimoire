---
title: "Context Management Tactics: Six Ways to Fix Your Context"
summary: "Drew Breunig's six tactics — RAG, tool loadout, quarantine, pruning, summarization, offloading — for mitigating context failures in agents."
tags: [context-engineering, agents, strategies, context-management]
sources:
  - url: "https://www.dbreunig.com/2025/06/26/how-to-fix-your-context.html"
    title: "How to Fix Your Context (Drew Breunig)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P1
---

# Context Management Tactics: Six Ways to Fix Your Context

## Overview

Drew Breunig's "How to Fix Your Context" is the practical companion to his earlier catalog of how long contexts fail. Where the first post named the failure modes — Context Poisoning (a hallucination or error makes it into the context and is repeatedly referenced), Context Distraction (a context grows so long the model over-focuses on it, neglecting training), Context Confusion (superfluous information is used to generate a low-quality response), and Context Clash (new information and tools conflict with other content in the prompt) — this post lays out the fixes.

The unifying theme is information management: "Everything in the context influences the response." Breunig frames it as the old adage "Garbage in, garbage out," and offers six tactics for dealing with the failures. The key insight running through all of them is that **context is not free** — every token influences the model's behavior, for better or worse, and the massive context windows of modern LLMs "are not an excuse to be sloppy with information management."

This article is the direct remedy to the failure modes; read it alongside [[failure-modes/how-contexts-fail]].

## Key Capabilities

- **RAG** — Selectively adding only relevant information to help the LLM generate a better response.
- **Tool Loadout** — Selecting only the relevant tool definitions to add to your context.
- **Context Quarantine** — Isolating contexts in their own dedicated threads, each used separately by one or more LLMs.
- **Context Pruning** — Removing irrelevant or unneeded information from the context.
- **Context Summarization** — Boiling down an accrued context into a condensed summary.
- **Context Offloading** — Storing information outside the LLM's context, usually via a tool that stores and manages the data.

## How It Works

### RAG

Retrieval-Augmented Generation is the act of selectively adding relevant information to help the LLM generate a better response. Breunig notes it's "very much alive": every time a model raises the context-window ante (the last big event being Llama 4 Scout's 10-million-token window), a "RAG is Dead" debate is born. But if you treat your context like a junk drawer, the junk will influence your response.

### Tool Loadout

Tool Loadout borrows a gaming term for the specific combination of abilities and equipment you select before a level — here, selecting the most relevant tools for a given task. The simplest approach is to apply RAG to your tool descriptions, storing them in a vector database and selecting the most relevant ones per prompt (the "RAG MCP" paper by Gan and Sun).

The numbers matter:

- Prompting DeepSeek-v3, selecting the right tools becomes critical past **30 tools** — above that, descriptions overlap and create confusion; beyond **100 tools** the model was virtually guaranteed to fail. Using RAG to select fewer than 30 tools yielded up to **3x better tool selection accuracy**.
- For smaller models, problems start earlier: the "Less is More" paper showed Llama 3.1 8b fails a benchmark given 46 tools but succeeds with only 19 — the issue is context confusion, not window limits. A dynamic LLM-powered tool recommender (feeding into tool RAG) improved Llama 3.1 8b performance by **44%**, and even when it didn't improve results it saved 18% power and 77% time.

### Context Quarantine

Context Quarantine isolates contexts in their own dedicated threads. Breaking a task into smaller, isolated jobs — each with its own context — keeps contexts from getting too long or picking up irrelevant content. The accessible example is Anthropic's multi-agent research system: subagents "facilitate compression by operating in parallel with their own context windows," each with separation of concerns (distinct tools, prompts, and exploration trajectories). Anthropic reported a multi-agent system with Claude Opus 4 as lead and Claude Sonnet 4 subagents outperformed single-agent Claude Opus 4 by **90.2%** on an internal research eval. The caveat: problems that require context-sharing among agents aren't suited to this tactic.

### Context Pruning

Context Pruning removes irrelevant or unneeded information as an agent accrues context from firing tools and assembling documents. It can be a task for the main LLM, a separate LLM-powered tool, or a dedicated pruner. Breunig highlights **Provence**, "an efficient and robust context pruner for Question Answering" — fast, accurate, only 1.75 GB, callable in a few lines. In his test it cut 95% of a Wikipedia article, leaving only the relevant subset. Pruning is a strong argument for maintaining a *structured* version of your context (in a dictionary), from which you compile a string before each LLM call, so main instructions and goals are preserved while document/history sections are pruned or summarized.

### Context Summarization

Context Summarization boils an accrued context into a condensed summary. It first appeared as a way to stay under the maximum context length — users manually asked a chatbot for a recap to paste into a new session. But as windows grew, builders found benefits beyond staying within limits: a growing context becomes distracting and causes the model to rely less on training (Context Distraction). The Pokémon-playing Gemini agent showed anything beyond **100,000 tokens** triggered a tendency to repeat actions from history rather than synthesize novel plans — highlighting the distinction between long-context for retrieval and long-context for multi-step generative reasoning. Summarization is easy to do but hard to perfect; knowing what to preserve is critical, and it's worth breaking out as its own LLM-powered stage so you can collect evaluation data.

### Context Offloading

Context Offloading stores information outside the LLM's context, via a tool that stores and manages the data. Breunig calls this his favorite tactic — "so simple you don't believe it will work." Anthropic's "think" tool is essentially a scratchpad; Breunig argues it should have been *called* scratchpad, since its function is a place for the model to write notes that don't cloud its context but are available for later reference. Pairing the "think" tool with a domain-specific prompt yielded up to a **54% improvement** against a benchmark for specialized agents. Anthropic identified three scenarios where offloading helps:

- **Tool output analysis** — when the model must carefully process previous tool output before acting and might need to backtrack.
- **Policy-heavy environments** — when the model must follow detailed guidelines and verify compliance.
- **Sequential decision making** — when each action builds on previous ones and mistakes are costly.

## Usage Examples

### Example: Curating a tool loadout past the 30-tool cliff

An agent with a large, growing set of integrations starts calling the wrong tools because descriptions overlap.

```
store tool descriptions in a vector DB
  → RAG-select < 30 tools relevant to the input prompt
  → shorter prompts, up to 3x better tool selection accuracy
```

### Example: Offloading with a scratchpad on a long tool chain

An agent performing a long chain of tool calls loses track of intermediate findings.

```
give the model a "think"/scratchpad tool
  → it writes notes/progress outside the working context
  → reads them back when needed (up to 54% gain on specialized-agent benchmark)
```

## Limitations

- **Quarantine doesn't fit shared-context problems** — Tasks that require context-sharing among multiple agents "aren't particularly suited to this tactic."
- **Summarization is hard to perfect** — Knowing what to preserve versus discard is critical and agent-specific; done poorly it drops important events or decisions.
- **Pruning and summarization need structure** — Getting reliable results argues for maintaining a structured version of your context rather than an opaque string.
- **Every tactic costs work** — These are levers an agent designer must actively deploy and evaluate; context management "is usually the hardest part of building an agent."

## See Also

- [[failure-modes/how-contexts-fail]] — the four failure modes (poisoning, distraction, confusion, clash) that these six tactics are the fix for
- [[strategies/four-context-strategies]] — LangChain's write/select/compress/isolate buckets, which these tactics map onto
- [[strategies/agentic-search-and-retrieval]] — a deeper treatment of RAG and just-in-time retrieval as a context tactic
- [[strategies/agentic-context-engineering]] — ACE, which turns summarization/offloading into a collapse-resistant evolving playbook
- [[strategies/context-compression-optimization]] — ACON, which turns static summarization/pruning heuristics into an optimized compression guideline
- [[strategies/multi-agent-context-isolation]] — the primary source for Context Quarantine — Anthropic's own account of the system this article cites secondhand
- [[strategies/agent-memory]] — Breunig's offloading and summarization tactics, viewed as memory operations
