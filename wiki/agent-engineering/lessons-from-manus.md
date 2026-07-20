---
title: "Lessons from Building Manus"
summary: "Six practical context-engineering lessons the Manus team learned rebuilding their agent framework four times, from KV-cache design to keeping failures in context."
tags: [agent-engineering, context-engineering, kv-cache, agents, memory]
sources:
  - url: "https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus"
    title: "Context Engineering for AI Agents: Lessons from Building Manus"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P1
---

# Lessons from Building Manus

## Overview

This is a field report by Yichao 'Peak' Ji on the context-engineering choices behind Manus. At the start of the project the team faced "a key decision: should we train an end-to-end agentic model... or build an agent on top of the in-context learning abilities of frontier models?" They chose context engineering because "this allows us to ship improvements in hours instead of weeks" and keeps the product "orthogonal to the underlying models: If model progress is the rising tide, we want Manus to be the boat, not the pillar stuck to the seabed."

The post is explicit that this is hard-won and empirical: "we've rebuilt our agent framework four times, each time after discovering a better way to shape context." They call this "manual process of architecture searching, prompt fiddling, and empirical guesswork" **"Stochastic Graduate Descent" (SGD)**. The lessons are "the local optima we arrived at through our own 'SGD'," learned "through repeated rewrites, dead ends, and real-world testing across millions of users."

## Key Capabilities

- **Design Around the KV-Cache** — Optimize the single metric the author would pick if forced to choose one: the KV-cache hit rate, which "directly affects both latency and cost."
- **Mask, Don't Remove** — Manage a growing tool space by masking token logits instead of dynamically adding/removing tool definitions.
- **Use the File System as Context** — Treat the file system as "the ultimate context": unlimited, persistent, and operable by the agent as externalized memory.
- **Manipulate Attention Through Recitation** — Rewrite a `todo.md` so objectives stay in recent attention and the agent avoids drifting off-topic.
- **Keep the Wrong Stuff In** — Leave failed actions and their observations in context so the model adapts instead of repeating mistakes.
- **Don't Get Few-Shotted** — Add controlled variation so the agent doesn't imitate a repetitive pattern into a rut.

## How It Works

Each lesson maps to a concrete practice (names are the post's own section headings):

1. **Design Around the KV-Cache.** Agent context "grows with every step, while the output... remains relatively short" — in Manus "the average input-to-output token ratio is around 100:1." Because "contexts with identical prefixes can take advantage of KV-cache," the author reports that with Claude Sonnet "cached input tokens cost 0.30 USD/MTok, while uncached ones cost 3 USD/MTok — a 10x difference." Practices: (1) "Keep your prompt prefix stable" — even a single-token change like a per-second timestamp "kills your cache hit rate"; (2) "Make your context append-only" with deterministic serialization; (3) "Mark cache breakpoints explicitly when needed."

2. **Mask, Don't Remove.** As tools multiply (and "the recent popularity of MCP only adds fuel to the fire"), "avoid dynamically adding or removing tools mid-iteration." Tool definitions "live near the front of the context," so changing them invalidates the KV-cache and can leave prior actions referring to undefined tools, causing "schema violations or hallucinated actions." Instead Manus uses "a context-aware state machine" that "masks the token logits during decoding" — leveraging response prefill modes (Auto, Required, Specified) and consistent name prefixes like `browser_` and `shell_`.

3. **Use the File System as Context.** Even 128K-token windows fall short: "Observations can be huge," "model performance tends to degrade beyond a certain context length," and "long inputs are expensive." Because "you can't reliably predict which observation might become critical ten steps later," aggressive compression is risky. Manus treats the file system as "structured, externalized memory," and keeps compression "restorable" — e.g., dropping a web page's content "as long as the URL is preserved."

4. **Manipulate Attention Through Recitation.** A typical Manus task "requires around 50 tool calls on average," making it "vulnerable to drifting off-topic or forgetting earlier goals." By "constantly rewriting the todo list, Manus is reciting its objectives into the end of the context," which "pushes the global plan into the model's recent attention span, avoiding 'lost-in-the-middle' issues."

5. **Keep the Wrong Stuff In.** "Agents make mistakes. That's not a bug — it's reality." The common impulse is to "clean up the trace, retry the action, or reset," but "erasing failure removes evidence. And without evidence, the model can't adapt." Leaving "the wrong turns in the context" lets the model see "a failed action — and the resulting observation or stack trace" and shift "its prior away from similar actions." The author calls "error recovery... one of the clearest indicators of true agentic behavior."

6. **Don't Get Few-Shotted.** "Language models are excellent mimics" — a context "full of similar past action-observation pairs" makes the model follow that pattern "even when it's no longer optimal." The fix is "to increase diversity": "small amounts of structured variation in actions and observations — different serialization templates, alternate phrasing, minor noise in order or formatting."

## Usage Examples

### Example: Reviewing a batch of resumes

The post's own example for the few-shot trap: "when using Manus to help review a batch of 20 resumes, the agent often falls into a rhythm — repeating similar actions simply because that's what it sees in the context. This leads to drift, overgeneralization, or sometimes hallucination." Introducing "controlled randomness" in formatting "helps break the pattern and tweaks the model's attention."

### Example: Restorable compression via the file system

When an observation is a huge web page or PDF, Manus can drop the body from the context while preserving the URL or sandbox path, so context length shrinks "without permanently losing information" — the content can be re-read on demand.

## Limitations

- **Not universal truth** — The author is explicit: "None of what we've shared here is universal truth — but these are the patterns that worked for us."
- **Empirical, not principled** — The framework was "rebuilt four times" via "Stochastic Graduate Descent," described as "architecture searching, prompt fiddling, and empirical guesswork" — "not elegant, but it works."
- **Provider/framework dependent** — Several practices (manual cache breakpoints, response prefill / logit masking, prefix caching via vLLM) depend on what "model providers or inference frameworks support."

## See Also

- [[agent-engineering/twelve-factor-agents]] — a complementary principles-based take, including compacting errors into context
- [[failure-modes/lost-in-the-middle]] — the attention failure recitation is designed to avoid
- [[strategies/context-management-tactics]] — compression, externalized memory, and other context-shaping tactics
- [[strategies/agentic-context-engineering]] — ACE, a formal framework for the evolving agent memory Manus builds by hand
- [[strategies/context-compression-optimization]] — ACON, a rigorous, measured take on the compression Manus does by hand
- [[strategies/four-context-strategies]] — the write/select/compress/isolate taxonomy these field lessons instantiate
- [Context Engineering for AI Agents: Lessons from Building Manus](https://manus.im/blog/Context-Engineering-for-AI-Agents-Lessons-from-Building-Manus) — the full post
- [[strategies/agent-memory]] — the research taxonomy that names what Manus's file-system-as-context is an instance of
