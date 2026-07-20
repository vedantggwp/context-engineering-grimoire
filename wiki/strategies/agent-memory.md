---
title: "Agent Memory: Forms, Functions, Dynamics"
summary: "A 47-author survey argues long/short-term is the wrong taxonomy for agent memory, and proposes forms (token-level, parametric, latent) × functions (factual, experiential, working) × dynamics (formation, evolution, retrieval)."
tags: [strategies, memory, agents, research, survey]
sources:
  - url: "https://arxiv.org/abs/2512.13564"
    title: "Memory in the Age of AI Agents (Hu, Liu, Yue, Zhang et al.)"
    accessed: 2026-07-20
updated: 2026-07-20
confidence: P0
---

# Agent Memory: Forms, Functions, Dynamics

> **Fidelity warning.** This article rests on the arXiv **abstract and
> bibliographic record only** (CC BY 4.0). The survey's body — its taxonomy
> tables, benchmark compilation, and framework comparisons — was not captured.
> What follows is the survey's stated organizing scheme and its stated claims
> about the field. It is deliberately not a summary of the survey's findings,
> because those were not read. Treat this as a map to the source, not a
> substitute for it.

## Overview

Memory is the part of context engineering this wiki has otherwise not covered.
The "write" strategy in [[strategies/four-context-strategies]] — persist context
outside the window — is the entry point to an entire research field, and this
survey is that field's most recent consolidation attempt (47 authors, December
2025, revised January 2026).

Its motivating claim is that the field has become incoherent:

> "Existing works that fall under the umbrella of agent memory often differ
> substantially in their motivations, implementations, and evaluation protocols,
> while the proliferation of loosely defined memory terminologies has further
> obscured conceptual clarity."

And, pointedly for anyone reasoning about agent memory with borrowed
vocabulary: **"Traditional taxonomies such as long/short-term memory have proven
insufficient to capture the diversity of contemporary agent memory systems."**

The survey also does something directly useful for this wiki's scope: it "begins
by clearly delineating the scope of agent memory and distinguishing it from
related concepts such as LLM memory, retrieval augmented generation (RAG), and
context engineering." Agent memory and context engineering are adjacent, not
identical — a distinction worth having drawn explicitly by someone.

## Key Capabilities

The survey proposes three orthogonal lenses in place of the long/short-term
split:

- **Forms** — how memory is physically realized. Three dominant realizations:
  **token-level**, **parametric**, and **latent** memory.
- **Functions** — what the memory is *for*. A finer-grained taxonomy
  distinguishing **factual**, **experiential**, and **working** memory.
- **Dynamics** — how memory changes over time: how it is **formed**, **evolved**,
  and **retrieved**.

It additionally compiles "a comprehensive summary of memory benchmarks and
open-source frameworks," and names five research frontiers: memory automation,
reinforcement learning integration, multimodal memory, multi-agent memory, and
trustworthiness.

## How It Works

Not captured. The abstract states the taxonomy but not its contents — which
systems fall under which form, how the benchmarks compare, or what the survey
concludes about any of them. Reading the full paper is the only way to get this.

What can be said from the abstract is why the three-lens framing is a better fit
for context-engineering work than long/short-term. Long/short-term conflates two
independent questions — *where does this live* and *what is it for*. A summary
buffer and a vector store are both "long-term" but differ completely in form; a
scratchpad and a retrieved fact are both "in the window" but differ completely in
function. Separating forms from functions from dynamics lets you ask the three
questions independently, which is how the design decisions actually present
themselves when building.

The survey's stated ambition is "rethinking memory as a first-class primitive in
the design of future agentic intelligence" — i.e. not as an add-on to a context
window, but as a design layer of its own.

## Usage Examples

Not available from this capture. The survey is positioned as a reference work;
its benchmark and framework compilations are the practical payload, and neither
was read.

## Limitations

- **Abstract-only capture.** The single largest limitation, and it is this
  article's, not the survey's. Every substantive claim in the survey is
  unavailable here.
- **Survey, not result.** Even fully read, this consolidates others' work; it
  reports no new experiments.
- **Taxonomy proposals are contested by nature.** A 47-author consolidation is a
  strong signal of where the field is converging, not proof that the framing is
  correct.
- **Fast-moving area.** The survey itself names memory automation and RL
  integration as open frontiers, which is a fair warning that specifics date
  quickly.

## Open Questions

- What actually falls under token-level vs. parametric vs. latent memory, and how
  do their cost/fidelity trade-offs compare?
- Which memory benchmarks does the survey compile, and do any of them measure the
  thing practitioners care about (does memory improve task success)?
- How does the survey draw the boundary between agent memory and context
  engineering? That boundary is directly load-bearing for this wiki's scope.

*Resolving these requires a full read of the paper — a good candidate for a
future ingest run at higher fidelity.*

## See Also

- [[strategies/four-context-strategies]] — "write" is the strategy this field elaborates
- [[strategies/multi-agent-context-isolation]] — Anthropic's lead agent persists its plan to memory precisely because the window truncates
- [[strategies/agentic-search-and-retrieval]] — retrieval is one of the survey's three memory dynamics
- [[foundations/context-window-as-working-memory]] — the RAM analogy this literature formalizes
- [[strategies/agentic-context-engineering]] — ACE's self-improving playbook is experiential memory by another name
- [[foundations/context-engineering-as-a-discipline]] — the companion survey arguing the discipline predates the agent era
- [[agent-engineering/lessons-from-manus]] — Manus's "file system as context" is externalized token-level memory, built by hand
- [[strategies/context-management-tactics]] — offloading and summarization are memory formation and evolution under another name
