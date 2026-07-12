---
title: "Agentic Search and Just-in-Time Retrieval"
summary: "How agents fetch context on demand at runtime — Anthropic's just-in-time retrieval and agentic search vs. pre-loading, plus select-context strategies."
tags: [context-engineering, agents, retrieval, rag, agentic-search]
sources:
  - url: "https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents"
    title: "Effective Context Engineering for AI Agents (Anthropic)"
    accessed: 2026-07-10
  - url: "https://rlancemartin.github.io/2025/06/23/context_engineering/"
    title: "Context Engineering (Lance Martin)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P0
---

# Agentic Search and Just-in-Time Retrieval

## Overview

A central design question in context engineering is *when* an agent fetches its data: pre-load everything up front, or retrieve on demand as the task unfolds. Anthropic frames a shift in how engineers think about designing context for agents. Many AI-native applications employ embedding-based, pre-inference-time retrieval to surface important context. As the field moves toward more agentic approaches, teams increasingly augment these systems with **"just in time"** context strategies.

Rather than pre-processing all relevant data up front, agents built with the just-in-time approach maintain lightweight identifiers — file paths, stored queries, web links — and use these references to dynamically load data into context at runtime using tools. This mirrors human cognition: we don't memorize entire corpuses; we use external organization and indexing systems like file systems, inboxes, and bookmarks to retrieve relevant information on demand.

Lance Martin's treatment of "Select Context" describes the mechanics on the retrieval side — how agents pull scratchpads, memories, tools, and knowledge into the window. Together these sources describe how an agent assembles the right context piece by piece instead of drowning in an exhaustive but potentially irrelevant dump.

## Key Capabilities

- **Just-in-time retrieval** — Load data at runtime via lightweight references rather than pre-computing and pre-loading everything.
- **Agentic search** — Let the model autonomously use tools in a loop to navigate and retrieve data, recovering from errors as models become more capable.
- **Progressive disclosure** — Incrementally discover relevant context through exploration, where each interaction yields signals that inform the next decision.
- **Metadata-driven refinement** — Use folder hierarchies, naming conventions, file sizes, and timestamps as signals for how and when to use information.
- **Hybrid retrieval** — Combine some up-front data (for speed) with autonomous exploration (for freshness) at the agent's discretion.
- **Select-context mechanics (Lance Martin)** — Pull scratchpads, memories, tools, and knowledge into the window with fine-grained control.

## How It Works

### Just-in-time context (Anthropic)

Anthropic defines agents, following Simon Willison, as "LLMs autonomously using tools in a loop." As underlying models get more capable, agent autonomy can scale — smarter models can independently navigate nuanced problem spaces and recover from errors.

In the just-in-time approach, the agent holds lightweight identifiers and loads data at runtime:

1. **Maintain references, not payloads** — file paths, stored queries, web links.
2. **Load on demand with tools** — Claude Code performs complex data analysis over large databases by writing targeted queries, storing results, and using Bash commands like `head` and `tail` to analyze large volumes of data without ever loading full data objects into context.
3. **Read the metadata** — The presence of `test_utils.py` in a tests folder implies a different purpose than the same filename in `src/core_logic/`. Folder hierarchies, naming conventions, and timestamps are signals that help agents understand how and when to use information.
4. **Assemble understanding through progressive disclosure** — Each interaction yields context that informs the next decision: file sizes suggest complexity, naming conventions hint at purpose, timestamps proxy relevance. The agent maintains only what's necessary in working memory, keeping itself focused on relevant subsets.

The trade-off is explicit: runtime exploration is **slower** than retrieving pre-computed data, and it requires thoughtful engineering so the LLM has the right tools and heuristics. Without proper guidance, an agent can waste context by misusing tools, chasing dead-ends, or failing to identify key information.

### The hybrid strategy

The most effective agents may retrieve some data up front for speed and pursue further autonomous exploration at their discretion; the right level of autonomy depends on the task. Claude Code is a hybrid example: CLAUDE.md files are dropped into context up front, while primitives like `glob` and `grep` let it retrieve files just-in-time — bypassing stale indexing and complex syntax trees. Anthropic suggests the hybrid strategy suits contexts with less dynamic content, such as legal or finance work, and closes with the guidance that "do the simplest thing that works" will likely remain the best advice.

### Select-context mechanics (Lance Martin)

Lance Martin groups agent context engineering into write, select, compress, and isolate, and the **select** bucket is the retrieval side of this article:

- **Scratchpad** — Selection depends on implementation: a tool-based scratchpad is read by a tool call; a state-based one lets the developer expose chosen parts of state each step.
- **Memories** — Agents select episodic memories (few-shot examples), procedural memories (instructions), or semantic memories (facts). Narrow always-on files are common — Claude Code uses CLAUDE.md; Cursor and Windsurf use rules files. Larger collections use embeddings and/or knowledge graphs, though selection remains hard (Simon Willison's example of ChatGPT injecting his location into an image).
- **Tools** — Too many tools with overlapping descriptions cause confusion; applying RAG to tool descriptions fetches the most relevant tools by semantic similarity and can improve tool selection accuracy 3-fold.
- **Knowledge (RAG)** — Code agents are among the best examples of RAG in production. Varun from Windsurf notes indexing code is not the same as context retrieval: embedding search becomes unreliable as a codebase grows, so teams combine grep/file search, knowledge-graph retrieval, and a re-ranking step that orders context by relevance.

### Why on-demand retrieval matters: the attention budget

Anthropic grounds the case for careful retrieval in a hard constraint. Studies on needle-in-a-haystack benchmarks uncovered **context rot**: as the number of tokens increases, the model's ability to accurately recall information decreases. LLMs have an "attention budget" that every new token depletes, stemming from the transformer architecture — every token attends to every other token, giving n² pairwise relationships for n tokens. This creates a performance gradient rather than a hard cliff, and it's why good context engineering means finding "the smallest possible set of high-signal tokens that maximize the likelihood of some desired outcome."

## Usage Examples

### Example: Analyzing a large database without loading it (just-in-time)

An agent must analyze a database far larger than its context window.

```
hold a reference (query / file path), not the data
  → write a targeted query, store results
  → use head / tail (Bash) to inspect large volumes
  → never load full data objects into context
```

### Example: Hybrid retrieval in a coding agent

An agent needs both stable project conventions and fresh, current file contents.

```
up front:  drop CLAUDE.md into context (stable conventions)
at runtime: glob / grep to fetch specific files just-in-time
  → avoids stale indexing and complex syntax trees
```

## Limitations

- **Runtime exploration is slower** — Just-in-time retrieval trades speed for freshness; pre-computed retrieval is faster when data is static.
- **Requires opinionated engineering** — Without the right tools and heuristics, an agent wastes context misusing tools, chasing dead-ends, or missing key information.
- **Embedding search degrades at scale** — As a codebase grows, embedding search becomes an unreliable retrieval heuristic; production systems must combine grep/file search, knowledge-graph retrieval, and re-ranking.
- **Memory selection is unreliable** — Selecting the right item from a large memory collection is hard, and undesired retrieval erodes user trust.
- **Simplicity often wins** — Anthropic's standing advice is "do the simplest thing that works"; smarter models require less prescriptive retrieval engineering over time.

## See Also

- [[strategies/four-context-strategies]] — the write/select/compress/isolate framing; this article expands the "select" bucket
- [[strategies/context-management-tactics]] — RAG and offloading as concrete tactics that complement agentic retrieval
- [[foundations/what-is-context-engineering]] — the definition and finite-attention-budget rationale behind retrieving on demand
- [[failure-modes/how-contexts-fail]] — context rot and the failure modes that pre-loading everything invites
- [[strategies/agentic-context-engineering]] — ACE, the "write"-side counterpart that evolves a curated context playbook from execution feedback
- [[strategies/context-compression-optimization]] — ACON, the compression counterpart: shrink what you keep rather than fetch on demand
