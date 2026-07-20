---
title: "The Context Window as Working Memory"
summary: "Karpathy's analogy of the LLM as an operating system and its context window as RAM — a finite working memory whose limited capacity makes context curation essential."
tags: [context-engineering, foundations, context-window, working-memory, analogy]
sources:
  - url: "https://rlancemartin.github.io/2025/06/23/context_engineering/"
    title: "Context Engineering (Lance Martin)"
    accessed: 2026-07-10
  - url: "https://arxiv.org/html/2507.13334v1"
    title: "A Survey of Context Engineering for Large Language Models (arXiv 2507.13334)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P0
---

# The Context Window as Working Memory

## Overview

A useful mental model for context engineering treats the LLM's context window as its working memory. Lance Martin relays Andrej Karpathy's framing: "LLMs are like a new kind of operating system. The LLM is like the CPU and its context window is like the RAM, serving as the model's working memory." Just as RAM has limited capacity, the LLM context window has limited capacity to hold the various sources of context an agent needs — and just as an operating system curates what fits into RAM, context engineering plays the same curating role.

This analogy explains why context engineering exists as a discipline. Martin defines it as "the art and science of filling the context window with just the right information at each step of an agent's trajectory" (echoing Karpathy's line about "just the right information for the next step"). The arXiv *Survey of Context Engineering for LLMs* corroborates the working-memory mapping: "Short-term memory in LLMs operates through the context window, serving as working memory maintaining immediate access to previously processed tokens," implemented through key-value caches that disappear when a session terminates.

## Key Capabilities

- **A unifying analogy** — The OS/RAM framing (Martin, via Karpathy) gives engineers an intuitive model: the context window is scarce, shared working memory that must be actively managed, not an unlimited scratch space.
- **A basis for memory hierarchy** — The survey classifies memory temporally into sensory memory (input prompts), short-term memory (immediate context processing), and long-term memory (external databases or dedicated structures), with short-term memory being the context window itself.
- **A driver for curation strategy** — Because working memory is finite, agents must decide at each step what to keep in context, motivating the write / select / compress / isolate strategies Martin catalogs.

## How It Works

### The operating-system analogy

Martin lays out the mapping directly (crediting Karpathy):

1. **LLM ≈ CPU** — the model is the processing unit.
2. **Context window ≈ RAM** — the context window is the model's working memory, holding what the model can act on right now.
3. **Context engineering ≈ the OS's memory management** — "just as an operating system curates what fits into a CPU's RAM, 'context engineering' plays a similar role."

The survey extends the same OS metaphor into implementation: OS-inspired hierarchical memory systems like MemGPT "page information between limited context windows (main memory) and external storage, similar to traditional operating systems," and PagedAttention is "inspired by virtual memory and paging techniques in operating systems."

### Finite capacity and why curation is required

The pressure that makes curation necessary comes from finite capacity. Martin notes that RAM, and therefore the context window, has "limited capacity to handle various sources of context." In agents this bites hard: "long-running tasks and accumulating feedback from tool calls mean that agents often utilize a large number of tokens. This can cause numerous problems: it can exceed the size of the context window, balloon cost / latency, or degrade agent performance." He cites Cognition's claim that context engineering "is effectively the #1 job of engineers building AI agents," and Anthropic's observation that agents "often engage in conversations spanning hundreds of turns, requiring careful context management strategies."

The survey grounds the same constraint architecturally. LLMs "face fundamental constraints in context management stemming from finite context window sizes," and traditional transformer architectures "experience quadratic computational complexity growth as sequence length increases, making processing extremely long texts prohibitively expensive." Simply enlarging the window does not solve the problem: it surfaces the "lost-in-the-middle" phenomenon, where models "struggle to access information positioned in middle sections of long contexts," and risks both context window overflow (models "forget" prior context after exceeding limits) and context collapse (enlarged windows cause models to fail to distinguish between different conversational contexts). The survey also notes LLMs "inherently process each interaction independently," so working memory in the context window is ephemeral and must be explicitly managed.

### The types of context to manage

Martin frames context engineering as an umbrella spanning a few context types that all compete for this finite working memory:

```
- Instructions – prompts, memories, few-shot examples, tool descriptions, etc.
- Knowledge    – facts, memories, etc.
- Tools        – feedback from tool calls
```

## Usage Examples

### Example: Persisting a plan outside working memory

Because the context window is limited RAM, agents offload important information to external storage. Martin cites Anthropic's multi-agent researcher: "The LeadResearcher begins by thinking through the approach and saving its plan to Memory to persist the context, since if the context window exceeds 200,000 tokens it will be truncated and it is important to retain the plan." The plan is written outside working memory precisely because working memory will be overrun.

### Example: Auto-compaction when RAM fills up

When working memory approaches capacity, it can be compressed. Martin points to Claude Code, which "runs 'auto-compact' after you exceed 95% of the context window and it will summarize the full trajectory of user-agent interactions" — the working-memory equivalent of reclaiming RAM by condensing what it holds.

## Limitations

- **The analogy is a model, not a mechanism** — Martin presents OS/RAM as an intuition pump. The survey shows the underlying reality is messier: attention is quadratic in sequence length, and capacity limits interact with positional biases like lost-in-the-middle rather than behaving like clean byte-addressable RAM.
- **Bigger windows don't remove the constraint** — Per the survey, extending context windows to millions of tokens still leaves models struggling to reason over extended contexts, especially when relevant information sits in the middle, so curation remains necessary even as capacity grows.
- **Working memory is ephemeral** — The survey notes short-term memory (KV caches, hidden states) exists only within a single session and disappears when the session ends, so anything durable must be pushed to long-term memory outside the window.

## See Also

- [[foundations/what-is-context-engineering]] — the discipline this analogy motivates
- [[strategies/four-context-strategies]] — write, select, compress, isolate as ways to manage finite working memory
- [[failure-modes/lost-in-the-middle]] — the positional degradation that finite windows expose
- [[strategies/context-management-tactics]] — compaction and offloading when working memory fills
- [Context Engineering (Lance Martin)](https://rlancemartin.github.io/2025/06/23/context_engineering/) — the source of the OS/RAM framing and the four-bucket taxonomy
- [[failure-modes/context-rot]] — evidence that window size is capacity, not usable capacity
- [[strategies/agent-memory]] — the literature that formalizes what sits outside the window
- [[foundations/context-engineering-as-a-discipline]] — where the RAM-analogy era sits in a twenty-year history
- [[strategies/agentic-search-and-retrieval]] — why fetching on demand beats preloading: the window is RAM, not disk
