---
title: "What Is Context Engineering"
summary: "The discipline of filling an LLM's limited context window with the right information, tools, and format at each step so an agent can plausibly solve its task."
tags: [context-engineering, foundations, llm-agents, definition]
sources:
  - url: "https://www.philschmid.de/context-engineering"
    title: "The New Skill in AI is Context Engineering (Philipp Schmid)"
    accessed: 2026-07-10
  - url: "https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents"
    title: "Effective Context Engineering for AI Agents (Anthropic)"
    accessed: 2026-07-10
  - url: "https://simonwillison.net/2025/Jun/27/context-engineering/"
    title: "Context Engineering (Simon Willison)"
    accessed: 2026-07-10
  - url: "https://arxiv.org/html/2507.13334v1"
    title: "A Survey of Context Engineering for Large Language Models (arXiv 2507.13334)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P0
---

# What Is Context Engineering

## Overview

Context engineering is the emerging discipline of curating everything an LLM sees before it generates a response, so that a task becomes "plausibly solvable by the LLM." Shopify CEO Tobi Lütke framed the term as "the art of providing all the context for the task to be plausibly solvable by the LLM," and Andrej Karpathy amplified it, describing it as the "delicate art and science of filling the context window with just the right information for the next step" (per Simon Willison's and Philipp Schmid's write-ups).

The shift matters most for agents. As Schmid puts it, with the rise of agents it becomes more important what information we load into the model's "limited working memory," and "the main thing that determines whether an Agent succeeds or fails is the quality of the context you give it. Most agent failures are not model failures anymore, they are context failures." Anthropic frames context engineering as "the natural progression of prompt engineering" — the set of strategies for curating and maintaining the optimal set of tokens (information) during LLM inference, including all the information that lands there outside of the prompts.

The arXiv *Survey of Context Engineering for LLMs* formalizes it as a discipline that "transcends simple prompt design to encompass the systematic optimization of information payloads for LLMs." Where prompt engineering treated the context as a single static string (`C = prompt`), context engineering re-conceptualizes context as a dynamically structured assembly of components, `C = A(c1, c2, …, cn)`, orchestrated by an assembly function.

## Key Capabilities

- **A system, not a string** — Per Schmid, context is not a static prompt template but the output of a system that runs before the main LLM call, assembling the right inputs on the fly.
- **Dynamic and task-tailored** — Context is created on the fly for the immediate task: calendar data for one request, emails or a web search for another (Schmid).
- **Right information and tools, right format, right time** — The core job is to ensure the model isn't missing crucial details ("Garbage In, Garbage Out"), providing both knowledge and capabilities only when needed, and in a digestible format — a concise summary beats a raw data dump, a clear tool schema beats a vague instruction (Schmid).
- **Token optimization under constraints** — Anthropic frames the engineering problem as "optimizing the utility of those tokens against the inherent constraints of LLMs," finding "the smallest possible set of high-signal tokens that maximize the likelihood of some desired outcome."

## How It Works

### The anatomy of context

Context is "everything the model sees before it generates a response" (Schmid). Schmid decomposes it into these components:

1. **Instructions / System Prompt** — initial instructions defining model behavior; can include examples and rules.
2. **User Prompt** — the immediate task or question from the user.
3. **State / History (short-term memory)** — the current conversation, including prior user and model turns.
4. **Long-Term Memory** — persistent knowledge across many prior conversations: learned preferences, summaries of past projects, remembered facts.
5. **Retrieved Information (RAG)** — external, up-to-date knowledge from documents, databases, or APIs.
6. **Available Tools** — definitions of functions or built-in tools the model can call (e.g., `check_inventory`, `send_email`).
7. **Structured Output** — definitions of the response format, e.g., a JSON object.

Anthropic's guidance across these components (system prompts, tools, examples, message history) is to keep context "informative, yet tight." System prompts should hit the right "altitude" — the Goldilocks zone between brittle hardcoded if-else logic and vague high-level guidance. Tools should be self-contained, token-efficient, and unambiguous; Anthropic warns that bloated tool sets covering too much functionality are one of the most common failure modes. For examples (few-shot prompting), it recommends a curated set of diverse, canonical examples rather than stuffing in every edge case.

The survey maps the same components to formal technical domains: system instructions (`c_instr`), external knowledge (`c_know`, e.g., RAG), tool definitions (`c_tools`), persistent memory (`c_mem`), dynamic world/agent state (`c_state`), and the user's immediate query (`c_query`). It defines context engineering as the optimization problem of finding the context-generating functions that maximize expected output quality, subject to the hard constraint that the assembled context cannot exceed the model's context length limit (`|C| ≤ L_max`).

### A definition

Schmid offers a working definition:

```
Context Engineering is the discipline of designing and building dynamic systems
that provide the right information and tools, in the right format, at the right time,
to give an LLM everything it needs to accomplish a task.
```

## Usage Examples

### Example: The scheduling assistant (cheap demo vs. magical agent)

Schmid illustrates the payoff with an assistant asked to reply to: "Hey, just checking if you're around for a quick sync tomorrow."

A "cheap demo" agent sees only the user's request. Its code works — it calls an LLM and gets a response — but the output is robotic: "Thank you for your message. Tomorrow works for me. May I ask what time you had in mind?"

A "magical" agent gathers context before calling the LLM: the user's calendar (showing they are fully booked), past emails with the sender (to match tone), the contact list (to identify a key partner), and tools like `send_invite`. The result:

```
Hey Jim! Tomorrow's packed on my end, back-to-back all day.
Thursday AM free if that works for you? Sent an invite, lmk if it works.
```

The difference, per Schmid, "isn't in a smarter model or a more clever algorithm. It's in providing the right context for the right task."

### Example: Just-in-time retrieval in Claude Code

Anthropic describes a "just in time" strategy where an agent maintains lightweight identifiers (file paths, queries, links) and loads data into context at runtime rather than pre-processing everything up front. Claude Code uses this to analyze large databases: the model writes targeted queries, stores results, and uses Bash commands like `head` and `tail` to inspect large data volumes without ever loading full objects into context — mirroring how humans use file systems and bookmarks instead of memorizing entire corpuses.

## Limitations

- **Runtime exploration is slower** — Anthropic notes that just-in-time retrieval trades speed for flexibility: exploring at runtime is slower than retrieving pre-computed data, and without proper guidance an agent can waste context by misusing tools or chasing dead-ends.
- **Attention is a finite budget** — Per Anthropic, LLMs have an "attention budget" that every new token depletes; context must be treated as a finite resource with diminishing marginal returns, so more context is not automatically better.
- **Format and curation are hard** — Schmid stresses that how information is presented matters (concise summary over raw dump), and Anthropic notes bloated tool sets and over-stuffed examples actively degrade performance. Good curation is "much easier said than done."

## See Also

- [[foundations/context-vs-prompt-engineering]] — how context engineering broadens and supersedes prompt engineering
- [[foundations/context-window-as-working-memory]] — the finite working-memory analogy that makes curation necessary
- [[strategies/four-context-strategies]] — write, select, compress, and isolate as concrete tactics
- [[failure-modes/how-contexts-fail]] — the context failures that context engineering exists to prevent
- [Effective Context Engineering for AI Agents (Anthropic)](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) — Anthropic's practical anatomy and long-horizon techniques
