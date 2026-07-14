---
title: "Context Compression Optimization (ACON) for Long-Horizon Agents"
summary: "ACON compresses an agent's observations and history by iteratively refining compression guidelines from failure analysis — gradient-free, no fine-tuning, 26–54% fewer peak tokens."
tags: [context-engineering, agents, strategies, compression, long-horizon]
sources:
  - url: "https://arxiv.org/abs/2510.00615"
    title: "ACON: Optimizing Context Compression for Long-horizon LLM Agents"
    accessed: 2026-07-12
updated: 2026-07-12
confidence: P0
---

# Context Compression Optimization (ACON) for Long-Horizon Agents

## Overview

**Agent Context Optimization (ACON)** is a compression framework from Minki Kang
and colleagues (Microsoft, ICML 2026) for long-horizon agents — agents that run
many steps and accumulate an ever-growing record of actions and observations.
Unbounded context growth creates two bottlenecks the paper names directly:
**prohibitive inference-memory cost** and **reasoning degradation** as relevant
signal drowns in irrelevant history (a concrete instance of the
[[failure-modes/how-contexts-fail]] "distraction" mode).

ACON is the "compress" bucket of the [[strategies/four-context-strategies]] done
rigorously: instead of a brittle summarization heuristic, it *optimizes* how it
compresses, and instead of fine-tuning the model, it optimizes in natural
language. That makes it applicable to proprietary and API-only models where
weight updates are impossible.

> Fidelity note: this article rests on the paper's arXiv abstract and
> bibliographic record (`extract` capture), not a full read of the PDF. Reference
> code is published at [github.com/microsoft/acon](https://github.com/microsoft/acon);
> benchmark internals live in the [full paper](https://arxiv.org/abs/2510.00615).

## Key Capabilities

- **Compresses both observations and history** — a unified framework for the two
  things that balloon in agentic runs: environment observations (tool outputs,
  pages, results) and the interaction history itself.
- **No fine-tuning required** — optimization happens in natural-language space, so
  ACON works with closed-source and large-scale LLMs that cannot be retrained.
- **Failure-driven guideline optimization** — the compression rule is not fixed;
  it is refined by analyzing cases where compression caused the agent to fail.
- **Distillable** — the optimized compressor can be distilled into smaller models
  to cut the overhead of running compression itself.

## How It Works

The distinctive idea is **compression guideline optimization in natural-language
space**, driven by failure analysis:

1. **Pair trajectories** — collect cases where the agent succeeds with *full*
   context but fails with the *compressed* context. That gap localizes what the
   compressor wrongly discarded.
2. **Analyze the failure** — a capable LLM reads the paired trajectories and
   diagnoses *why* the compressed context lost critical state.
3. **Update the guideline** — the natural-language compression guideline is
   revised so that the next round preserves the information that mattered.
4. **Distill (optional)** — once the guideline is good, distill the compressor
   into a smaller model to minimize computational overhead at runtime.

The loop is gradient-free: no weights change, only the prompt-level guideline
that tells the compressor what to keep and what to drop.

## Usage Examples

### Example: Taming observation bloat in a tool-using agent

An agent that scrapes pages or calls verbose APIs accumulates huge observations.
ACON compresses each observation as it lands, keeping the state the agent will
actually need — refining what "need" means whenever aggressive compression
later causes a failure.

### Example: Letting a small model run long-horizon tasks

Because compression mitigates context distraction, ACON lets smaller LMs stay
coherent over long runs. The abstract reports up to **46% performance
improvement** for smaller models by reducing distraction, and **26–54% lower
peak token usage** across AppWorld, OfficeBench, and Multi-objective QA — while
improving task success over existing compression baselines.

## Reported Results

- **26–54%** reduction in peak token usage across three benchmarks.
- Up to **46%** performance improvement for smaller models via reduced distraction.
- The optimized compressor is distilled into smaller models to cut compression
  overhead; the abstract does not quantify how much performance the distilled
  compressor retains.

(Figures are as stated in the abstract; see the paper for methodology.)

## Limitations

- **Needs a failure signal to optimize against** — guideline optimization depends
  on observing compressed-context failures; a task with no clear success/failure
  signal gives the optimizer little to learn from.
- **Compression can still drop the wrong thing** — the method reduces, but does
  not eliminate, the risk that a compressed observation omits information a later
  step needs; the optimization loop is the mitigation, not a guarantee.
- **Extract-fidelity coverage** — this article is grounded in the abstract; exact
  guideline formats, prompts, and per-benchmark numbers require the full paper.

## See Also

- [[strategies/four-context-strategies]] — ACON is the "compress" strategy made
  rigorous and self-tuning
- [[strategies/context-management-tactics]] — Breunig's summarization and pruning
  tactics, which ACON turns from static heuristics into an optimized guideline
- [[strategies/agentic-context-engineering]] — ACE, a sibling framework that also
  refines context in natural-language space from execution feedback, but to
  *grow* a playbook rather than *shrink* the history
- [[failure-modes/how-contexts-fail]] — "context distraction," the failure mode
  ACON's compression is designed to mitigate for long-horizon agents
- [[strategies/agentic-search-and-retrieval]] — retrieval loads less *into*
  context; ACON keeps less *in* it — complementary ways to bound the window
- [[agent-engineering/lessons-from-manus]] — Manus's hands-on compaction and
  context-management lessons that ACON formalizes, optimizes, and measures
