---
title: "12-Factor Agents"
summary: "HumanLayer's 12 engineering principles for building reliable LLM-powered software, several of which are directly about owning and shaping the agent's context."
tags: [agent-engineering, context-engineering, reliability, design-patterns]
sources:
  - url: "https://github.com/humanlayer/12-factor-agents"
    title: "12-Factor Agents (HumanLayer)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P0
---

# 12-Factor Agents

## Overview

12-Factor Agents is a set of principles from Dex (HumanLayer) for building "LLM-powered software that is actually good enough to put in the hands of production customers." It is written "in the spirit of [12 Factor Apps](https://12factor.net/)."

The author's premise comes from having "tried every agent framework out there" and talking to "a lot of really strong founders" — most of whom "are rolling the stack themselves." He was "surprised to find that most of the products out there billing themselves as 'AI Agents' are not all that agentic," being "mostly deterministic code, with LLM steps sprinkled in at just the right points." His conclusion: "Agents, at least the good ones... are comprised of mostly just software."

The guide argues that even if LLMs "continue to get exponentially more powerful, there will be core engineering techniques that make LLM-powered software more reliable, more scalable, and easier to maintain." The recommended adoption path is incremental: "take small, modular concepts from agent building, and incorporate them into their existing product" rather than doing "a greenfield rewrite" on top of a framework. Several of the twelve factors are directly about context engineering — the README even points context-engineering readers to "Jump straight to factor 3."

## Key Capabilities

- **Own your context window (Factor 3)** — Rather than delegating context assembly to a framework, you control exactly what the model sees, which the guide flags as the core context-engineering factor.
- **Compact errors into the context window (Factor 9)** — Keep error information in context, compacted, so the agent can recover instead of losing the trace.
- **Unify execution state and business state (Factor 5)** — Collapse the separate notions of "how the program is running" and "what the business data is" into one representation.
- **Own your prompts (Factor 2)** — Treat prompts as first-class code you control, not as opaque strings hidden inside a framework.

## How It Works

The guide first frames agents as a loop. "With agents you've got this loop consisting of 3 steps":

1. LLM determines the next step in the workflow, outputting structured json ("tool calling")
2. Deterministic code executes the tool call
3. The result is appended to the context window
4. Repeat until the next step is determined to be "done"

```python
initial_event = {"message": "..."}
context = [initial_event]
while True:
  next_step = await llm.determine_next_step(context)
  context.append(next_step)

  if (next_step.intent === "done"):
    return next_step.final_answer

  result = await execute_step(next_step)
  context.append(result)
```

Against that loop, the guide lays out **The 12 Factors** (exact names from "The Short Version: The 12 Factors"):

1. **Factor 1: Natural Language to Tool Calls**
2. **Factor 2: Own your prompts**
3. **Factor 3: Own your context window**
4. **Factor 4: Tools are just structured outputs**
5. **Factor 5: Unify execution state and business state**
6. **Factor 6: Launch/Pause/Resume with simple APIs**
7. **Factor 7: Contact humans with tool calls**
8. **Factor 8: Own your control flow**
9. **Factor 9: Compact Errors into Context Window**
10. **Factor 10: Small, Focused Agents**
11. **Factor 11: Trigger from anywhere, meet users where they are**
12. **Factor 12: Make your agent a stateless reducer**

An honorable mention is also listed: **Factor 13: Pre-fetch all the context you might need**.

## Usage Examples

### Example: Context-engineering factors

The factors most about context engineering are the ones that govern what enters the context window and how it is shaped:

- **Factor 3 — Own your context window**: the README explicitly directs context-engineering readers here ("Looking for Context Engineering? Jump straight to factor 3").
- **Factor 9 — Compact Errors into Context Window**: keep failures in context in a compacted form so the agent can adapt.
- **Factor 5 — Unify execution state and business state**: one state representation instead of two, simplifying what the loop appends.
- **Factor 2 — Own your prompts**: prompts are code you control.

### Example: Incremental adoption

The guide's recommended usage is not "adopt a framework wholesale." Instead: "the fastest way I've seen for builders to get high-quality AI software in the hands of customers is to take small, modular concepts from agent building, and incorporate them into their existing product." The factors "can be defined and applied by most skilled software engineers, even if they don't have an AI background."

## Limitations

- **Not a framework** — The guide is a set of principles, not a runtime. The author notes he even "ignored all our own advice and built a framework for running distributed agents in kubernetes," underscoring that these are patterns to apply, not a drop-in library.
- **The naive agent loop "doesn't quite work"** — The guide states plainly that the "here's your prompt, here's a bag of tools, loop until you hit the goal" pattern "just doesn't work as well as we want it to," which is the motivation for the factors rather than a solved outcome.
- **The 80% wall** — The described builder journey reaches a "70-80% quality bar" and then finds "80% isn't good enough for most customer-facing features," with getting past it requiring reverse-engineering framework internals.

## See Also

- [[agent-engineering/lessons-from-manus]] — a parallel field-tested account of context engineering for agents, including keeping errors in context
- [[agent-engineering/context-engineering-templates]] — open templates that operationalize similar own-your-context practices
- [[foundations/what-is-context-engineering]] — the broader discipline these factors sit inside
- [12-Factor Agents (HumanLayer)](https://github.com/humanlayer/12-factor-agents) — the full guide with per-factor deep dives
