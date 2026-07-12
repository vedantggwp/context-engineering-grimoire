---
title: "The Four Context Strategies: Write, Select, Compress, Isolate"
summary: "LangChain's four buckets for agent context engineering — write, select, compress, and isolate context — with concrete examples of each."
tags: [context-engineering, agents, strategies, langgraph]
sources:
  - url: "https://blog.langchain.com/context-engineering-for-agents/"
    title: "Context Engineering for Agents (LangChain)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P0
---

# The Four Context Strategies: Write, Select, Compress, Isolate

## Overview

Agents need context to perform tasks. LangChain defines context engineering as "the art and science of filling the context window with just the right information at each step of an agent's trajectory." Because agents interleave LLM calls and tool calls — often for long-running tasks — they accumulate feedback over many turns and can use a large number of tokens. This can exceed the size of the context window, balloon cost and latency, or degrade agent performance.

To organize the response to this problem, LangChain groups common strategies for agent context engineering into four buckets: **write, select, compress, and isolate**. Each bucket is drawn from a review of popular agent products and papers. The framing borrows Andrej Karpathy's analogy that LLMs are like a new kind of operating system: the LLM is the CPU and its context window is the RAM, a working memory with limited capacity that must be curated — the role context engineering plays.

This matters because, as Cognition put it, context engineering "is effectively the #1 job of engineers building AI agents," and Anthropic notes that agents "often engage in conversations spanning hundreds of turns, requiring careful context management strategies."

## Key Capabilities

- **Write context** — saving information outside the context window (scratchpads, memories) so it persists and is available to the agent later.
- **Select context** — pulling the right information into the context window at each step (from scratchpads, memories, tools, and knowledge via RAG).
- **Compress context** — retaining only the tokens required to perform a task, via summarization or trimming.
- **Isolate context** — splitting context up across sub-agents, sandboxed environments, or state objects to keep each context window focused.

## How It Works

The four strategies address the same underlying problem — a finite context window filling with accumulated tool feedback — from four different angles.

### 1. Write Context

Writing context means saving it outside the context window to help an agent perform a task.

- **Scratchpads** — Note-taking that persists information while an agent works on a task. Anthropic's multi-agent researcher illustrates this: the LeadResearcher saves its plan to memory to persist context, "since if the context window exceeds 200,000 tokens it will be truncated and it is important to retain the plan." Scratchpads can be a tool call that writes to a file, or a field in a runtime state object that persists during the session.
- **Memories** — Where scratchpads help within a single session, memories let agents remember across many sessions. Reflexion introduced reflection after each agent turn, re-using self-generated memories; Generative Agents synthesized memories periodically from past feedback. These concepts reached products like ChatGPT, Cursor, and Windsurf, which auto-generate long-term memories from user-agent interactions.

### 2. Select Context

Selecting context means pulling it into the context window to help an agent perform a task.

- **Scratchpad** — Selection depends on implementation: a tool-based scratchpad is read via a tool call; a state-based scratchpad lets the developer choose which parts of state to expose each step.
- **Memories** — Agents select few-shot examples (episodic memories), instructions (procedural memories), or facts (semantic memories). Some agents always pull a narrow set of files into context — Claude Code uses CLAUDE.md; Cursor and Windsurf use rules files. Larger memory collections use embeddings and/or knowledge graphs for indexing, but selection remains challenging. Simon Willison shared an example of selection gone wrong: ChatGPT fetched his location from memories and unexpectedly injected it into a requested image, making users feel the context window "no longer belongs to them."
- **Tools** — Agents can become overloaded when given too many tools with overlapping descriptions, causing confusion about which to use. One approach is to apply RAG to tool descriptions to fetch only the most relevant tools; recent papers show this improves tool selection accuracy by 3-fold.
- **Knowledge (RAG)** — A rich and central context engineering challenge. Varun from Windsurf notes that indexing code is not the same as context retrieval: as a codebase grows, embedding search becomes unreliable, so production code agents rely on a combination of grep/file search, knowledge-graph-based retrieval, and a re-ranking step.

### 3. Compress Context

Compressing context involves retaining only the tokens required to perform a task.

- **Context Summarization** — Interactions can span hundreds of turns with token-heavy tool calls. Claude Code runs "auto-compact" after you exceed 95% of the context window, summarizing the full trajectory. Summarization can be recursive or hierarchical, and can be applied at specific points — post-processing token-heavy tool calls, or at agent-agent boundaries during knowledge hand-off (Cognition uses a fine-tuned model for this).
- **Context Trimming** — Whereas summarization uses an LLM to distill context, trimming filters or "prunes" it using hard-coded heuristics like removing older messages from a list. LangChain cites Provence, a trained context pruner for Question-Answering.

### 4. Isolate Context

Isolating context involves splitting it up to help an agent perform a task.

- **Multi-agent** — Split context across sub-agents, each with its own tools, instructions, and context window (a motivation for OpenAI's Swarm library was separation of concerns). Anthropic's multi-agent researcher found many agents with isolated contexts outperformed a single agent, because each subagent's window is allocated to a narrower sub-task, operating "in parallel with their own context windows." Challenges include token use (up to 15× more than chat), the need for careful prompt engineering, and coordination.
- **Context Isolation with Environments** — HuggingFace's deep researcher uses a CodeAgent that outputs code containing tool calls, then runs it in a sandbox. Context (e.g., return values) is passed back to the LLM, isolating token-heavy objects (images, audio) as variables in the environment.
- **State** — An agent's runtime state object can isolate context: a state schema can have fields context is written to, exposing one field (e.g., `messages`) to the LLM each turn while isolating information in other fields.

## Usage Examples

### Example: Persisting a plan with a scratchpad (Write)

An agent facing a long-running research task risks losing its plan when the context window is truncated.

```
LeadResearcher: think through approach → save plan to Memory
  (context window may exceed 200,000 tokens and truncate;
   the persisted plan survives)
```

### Example: Trimming tools with RAG (Select)

An agent equipped with a large tool library keeps picking the wrong tool because descriptions overlap.

```
apply RAG over tool descriptions
  → fetch only the most relevant tools for the current task
  → up to 3x better tool selection accuracy (per cited papers)
```

## Limitations

- **Memory selection is hard** — Pulling the *right* memory from a large collection is unreliable; undesired retrieval (like ChatGPT injecting a location into an image) erodes user trust in the context window.
- **Summarization can lose critical detail** — If specific events or decisions must be captured, summarization may drop them; Cognition found this required a fine-tuned model.
- **Multi-agent isolation is expensive** — It can consume up to 15× more tokens than chat and demands careful prompt engineering and coordination of sub-agents.
- **These are patterns, not guarantees** — LangChain recommends looking at your data, tracking token usage, and testing whether a given context engineering change actually helps or hurts agent performance before adopting it.

## See Also

- [[foundations/what-is-context-engineering]] — the definition and mental model these four strategies operationalize
- [[strategies/context-management-tactics]] — Drew Breunig's six tactics, a parallel taxonomy that maps closely onto write/select/compress/isolate
- [[strategies/agentic-search-and-retrieval]] — a deeper look at the "select" bucket: fetching knowledge on demand
- [[failure-modes/how-contexts-fail]] — the failure modes (poisoning, distraction, confusion, clash) these strategies are designed to mitigate
