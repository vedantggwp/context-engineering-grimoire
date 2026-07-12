---
title: "Context Engineering vs. Prompt Engineering"
summary: "How context engineering broadens prompt engineering — from crafting one static string to curating an entire dynamic, stateful information payload across multiple turns."
tags: [context-engineering, prompt-engineering, foundations, terminology]
sources:
  - url: "https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents"
    title: "Effective Context Engineering for AI Agents (Anthropic)"
    accessed: 2026-07-10
  - url: "https://simonwillison.net/2025/Jun/27/context-engineering/"
    title: "Context Engineering (Simon Willison)"
    accessed: 2026-07-10
  - url: "https://www.philschmid.de/context-engineering"
    title: "The New Skill in AI is Context Engineering (Philipp Schmid)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P0
---

# Context Engineering vs. Prompt Engineering

## Overview

"Context engineering" is gaining traction as a broader successor to "prompt engineering." Where prompt engineering focuses on crafting the perfect set of instructions in a single text string, context engineering asks the broader question of what configuration of context — across system instructions, tools, message history, external data, and memory — is most likely to produce the desired behavior. Anthropic states its position directly: "we view context engineering as the natural progression of prompt engineering."

The two are not opposites. Prompt engineering is best understood as a subset of context engineering: writing effective instructions (especially system prompts) remains one component of the larger job of curating everything in the context window. What changed is the scope of the problem, driven by the shift from one-shot chat interactions to agents that operate over many turns and long time horizons.

## Key Capabilities

- **Broader unit of design** — Per Schmid, prompt engineering focuses on "crafting the perfect set of instructions in a single text string," while context engineering is "far broader," designing dynamic systems that assemble information and tools.
- **A subset relationship** — Anthropic defines prompt engineering as "methods for writing and organizing LLM instructions for optimal outcomes" and context engineering as "the set of strategies for curating and maintaining the optimal set of tokens (information) during LLM inference, including all the other information that may land there outside of the prompts."
- **Iterative vs. discrete** — Anthropic contrasts "the discrete task of writing a prompt" with context engineering, which "is iterative and the curation phase happens each time we decide what to pass to the model."

## How It Works

### Anthropic's framing of the shift

Anthropic traces the transition to a change in what AI engineering actually involves:

1. **The prompt-era baseline** — "In the early days of engineering with LLMs, prompting was the biggest component of AI engineering work," because most use cases outside everyday chat needed prompts optimized for one-shot classification or text generation. The primary focus was how to write effective prompts, particularly system prompts.
2. **The move to agents** — "As we move towards engineering more capable agents that operate over multiple turns of inference and longer time horizons, we need strategies for managing the entire context state (system instructions, tools, Model Context Protocol (MCP), external data, message history, etc)."
3. **Cyclical refinement** — "An agent running in a loop generates more and more data that could be relevant for the next turn of inference, and this information must be cyclically refined." Context engineering is "the art and science of curating what will go into the limited context window from that constantly evolving universe of possible information."

Anthropic summarizes the reframing: building with language models is "becoming less about finding the right words and phrases for your prompts, and more about answering the broader question of 'what configuration of context is most likely to generate our model's desired behavior?'"

### Why the term has sticking power (Simon Willison)

Simon Willison argues the term will last, and roots his reasoning in how definitions are actually adopted:

- He had "spoken favorably of prompt engineering" in the past, hoping the term could capture the inherent complexity of constructing reliable prompts.
- But, he notes, "most people's inferred definition is that it's a laughably pretentious term for typing things into a chatbot."
- His conclusion: "It turns out that inferred definitions are the ones that stick. I think the inferred definition of 'context engineering' is likely to be much closer to the intended meaning." Because the word "context" naturally implies the broader informational payload, casual observers infer roughly the right meaning — unlike "prompt," which people associate with short throwaway task descriptions.

Willison anchors the term in Tobi Lütke's definition ("the art of providing all the context for the task to be plausibly solvable by the LLM") and Karpathy's amplification, which explicitly lists what fills the context window: "task descriptions and explanations, few shot examples, RAG, related (possibly multimodal) data, tools, state and history, compacting." Karpathy's own framing captures the superset relationship: people associate prompts with the short task descriptions of day-to-day use, whereas "in every industrial-strength LLM app, context engineering is the delicate art and science of filling the context window with just the right information for the next step."

## Usage Examples

### Example: When prompt engineering is enough

For everyday chat interactions and one-shot tasks — classification, a single text generation — the prompt is nearly the whole job. Anthropic notes these "required prompts optimized for one-shot classification or text generation tasks," where writing an effective system prompt is the dominant concern. Here, prompt engineering and context engineering largely coincide.

### Example: When you need context engineering

A multi-turn agent doing a codebase migration or a research project cannot be steered by one static string. Following Anthropic, the engineer must manage the entire evolving context state — system instructions, tool definitions, MCP servers, retrieved external data, and a growing message history — and re-curate what to pass to the model on each turn. Prompt engineering still contributes (the system prompt must sit at the right "altitude"), but it is now one lever among many.

## Limitations

- **Not a replacement, a superset** — The sources treat prompt engineering as a still-valid component, not a discarded practice. Anthropic calls context engineering the "natural progression" of prompt engineering, and prompt quality (especially the system prompt) remains part of the work.
- **Terminology is still settling** — Willison presents the sticking power of "context engineering" as a prediction ("I think this one may have sticking power"), grounded in how inferred definitions spread, not as a settled fact.
- **Curation adds complexity** — Moving from a discrete prompt to iterative, per-turn curation (Anthropic) means more moving parts to design, evaluate, and debug than a single prompt string.

## See Also

- [[foundations/what-is-context-engineering]] — the full definition and anatomy of context
- [[foundations/context-window-as-working-memory]] — why finite context forces curation rather than one-shot prompting
- [[strategies/context-management-tactics]] — the per-turn curation practices that distinguish context from prompt work
- [Context Engineering (Simon Willison)](https://simonwillison.net/2025/Jun/27/context-engineering/) — the argument for why the term will stick
