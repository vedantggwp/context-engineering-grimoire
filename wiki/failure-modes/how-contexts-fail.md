---
title: "How Contexts Fail: Four Failure Modes"
summary: "Drew Breunig's four ways long contexts break agents: poisoning, distraction, confusion, and clash — why bigger context windows create new failures."
tags: [failure-modes, context-management, agents]
sources:
  - url: "https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html"
    title: "How Long Contexts Fail (Drew Breunig)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P1
---

# How Contexts Fail: Four Failure Modes

## Overview

As frontier model context windows have grown — many now supporting up to 1 million tokens — a tempting assumption spread: with a large enough window, you can simply throw everything a model might need into a prompt (tools, documents, instructions) and let the model sort it out. Long contexts kneecapped RAG enthusiasm, enabled MCP hype, and fueled enthusiasm for agents.

But in reality, longer contexts do not generate better responses. Overloading a context can cause agents and applications to fail in surprising ways. Drew Breunig catalogs four distinct failure modes: contexts can become **poisoned**, **distracting**, **confusing**, or **conflicting**. These failures hit agents hardest, because agents operate in exactly the scenarios where contexts balloon — gathering information from multiple sources, making sequential tool calls, engaging in multi-turn reasoning, and accumulating extensive histories.

Anyone building agents needs to recognize these modes, because they explain why an agent that works in a short demo degrades as its context grows.

## Key Capabilities

- **Context Poisoning** — a hallucination or error makes it into the context, where it is repeatedly referenced, causing the model to fixate on impossible or irrelevant goals.
- **Context Distraction** — a context grows so long the model over-focuses on it, neglecting what it learned during training and repeating past actions rather than synthesizing new plans.
- **Context Confusion** — superfluous content in the context is used by the model to generate a low-quality response, such as calling irrelevant tools.
- **Context Clash** — parts of the context directly conflict with each other, derailing the model's reasoning.

## How It Works

### Context Poisoning

Context Poisoning is when a hallucination or other error makes it into the context, where it is repeatedly referenced.

The DeepMind team called this out in the Gemini 2.5 technical report, describing the Gemini agent playing Pokémon. As the report put it, "many parts of the context (goals, summary) are 'poisoned' with misinformation about the game state, which can often take a very long time to undo. As a result, the model can become fixated on achieving impossible or irrelevant goals." When the "goals" section of its context was poisoned, the agent developed nonsensical strategies and repeated behaviors in pursuit of a goal that could not be met.

### Context Distraction

Context Distraction is when a context grows so long that the model over-focuses on the context, neglecting what it learned during training.

As context accumulates during an agentic workflow, it can become distracting rather than helpful. The Pokémon-playing Gemini agent again illustrated this: as the context grew significantly beyond 100k tokens, the agent showed "a tendency toward favoring repeating actions from its vast history rather than synthesizing novel plans." This highlights a distinction between long-context for retrieval and long-context for multi-step, generative reasoning.

For smaller models, the distraction ceiling is much lower. A Databricks study found that model correctness began to fall around 32k tokens for Llama 3.1 405b, and earlier for smaller models. Breunig's takeaway: if you're not doing summarization or fact retrieval, be wary of your chosen model's distraction ceiling — the useful working length is often far below the advertised window.

### Context Confusion

Context Confusion is when superfluous content in the context is used by the model to generate a low-quality response.

The clearest example is tool overload. The Berkeley Function-Calling Leaderboard, a tool-use benchmark now on its third version, shows that every model performs worse when provided with more than one tool. The Berkeley team also designed scenarios where none of the provided functions are relevant, expecting the model to make no function call — yet all models occasionally call tools that aren't relevant, and the problem worsens as models get smaller.

A striking example comes from a paper evaluating small-model performance on the GeoEngine benchmark, which features 46 different tools. When the team gave a quantized Llama 3.1 8b a query with all 46 tools, it failed — even though the context was well within the 16k context window. But when they gave the model only 19 tools, it succeeded. The lesson: if you put something in the context, the model has to pay attention to it, even when it's irrelevant.

### Context Clash

Context Clash is when you accrue new information and tools in your context that conflict with other information already there. It is a more problematic version of Context Confusion — the bad context isn't merely irrelevant, it directly conflicts with other information in the prompt.

A Microsoft and Salesforce team documented this by taking prompts from multiple benchmarks and "sharding" their information across multiple prompts, simulating a multi-step chat exchange rather than one fully-specified prompt. The sharded prompts yielded dramatically worse results, with an average drop of 39%. OpenAI's o3 dropped from 98.1 to 64.1.

The cause: the assembled context contains early attempts by the model to answer before it had all the information. These incorrect early answers remain present and influence the final answer. As the team wrote, "LLMs often make assumptions in early turns and prematurely attempt to generate final solutions, on which they overly rely… when LLMs take a wrong turn in a conversation, they get lost and do not recover." For agents that assemble context from documents, tool calls, and other models tasked with subproblems, this diverse context has a real chance of disagreeing with itself.

## Usage Examples

### Example: Diagnosing a fixated agent

An agent keeps pursuing a goal that can't be achieved and repeats the same behaviors. Check whether an earlier hallucination entered a persistent part of the context (a goals or summary section). This is Context Poisoning — the error is being re-referenced every turn, so it must be removed from the context, not merely argued against.

### Example: An agent that stops making new plans

A long-running agent starts repeating actions from its history rather than devising novel strategies, and this begins well before the context window is full. This is Context Distraction. Note the model and its distraction ceiling (correctness fell around 32k tokens for Llama 3.1 405b, earlier for smaller models) and reduce accumulated history unless the task is summarization or fact retrieval.

### Example: Too many tools

An agent connected to many MCP tools calls irrelevant ones or produces low-quality output despite fitting inside the context window. This is Context Confusion. The GeoEngine result — failure with 46 tools, success with 19 — suggests cutting the number of tools presented at once.

## Limitations

- **The article names the failure modes but defers fixes** — Breunig states solutions (dynamically loading tools, spinning up context quarantines) are covered in a follow-up post, "How to Fix Your Context." This article is diagnostic, not prescriptive.
- **Some evidence is anecdotal** — the Gemini Pokémon observations are described in the source as anecdotal, and the distraction ceiling varies by model, so specific thresholds shouldn't be treated as universal constants.
- **Larger, reasoning models are improving** — the source notes large models, especially reasoning models, are getting better at ignoring superfluous context, so the severity of confusion and distraction depends on the model in use.

## See Also

- [[failure-modes/lost-in-the-middle]] — a related, measured degradation where models under-use information positioned in the middle of long contexts
- [[strategies/four-context-strategies]] — approaches for managing context to avoid these failure modes
- [[strategies/context-management-tactics]] — concrete tactics for keeping contexts lean and coherent
- [[strategies/agentic-context-engineering]] — ACE, designed to prevent "context collapse," a close cousin of these failures
- [[strategies/context-compression-optimization]] — ACON, which compresses history to mitigate "context distraction" in long-horizon agents
- [How to Fix Your Context (Drew Breunig)](https://www.dbreunig.com/2025/06/26/how-to-fix-your-context.html) — the follow-up covering mitigations
