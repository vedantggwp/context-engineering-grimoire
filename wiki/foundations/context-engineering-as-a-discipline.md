---
title: "Context Engineering as a Twenty-Year Discipline"
summary: "Hua et al. argue context engineering is not a 2025 invention but a practice traceable to early-1990s HCI, evolving in phases set by machine intelligence — and propose a systematic definition on that basis."
tags: [foundations, history, definitions, research]
sources:
  - url: "https://arxiv.org/abs/2510.26493"
    title: "Context Engineering 2.0: The Context of Context Engineering (Hua, Ye, Fu, ... Liu)"
    accessed: 2026-07-20
updated: 2026-07-20
confidence: P0
---

# Context Engineering as a Twenty-Year Discipline

> **Fidelity warning.** This article rests on the arXiv **abstract and
> bibliographic record only** (CC BY 4.0). The paper's systematic definition, its
> historical phase breakdown, and its design considerations were not captured —
> only the fact that it offers them. Do not cite this article for the paper's
> actual definition.

## Overview

Most of this wiki's foundational sources — Schmid, Willison, LangChain, Anthropic
— treat context engineering as something that emerged in 2025 when practitioners
noticed that prompt engineering had stopped describing the job (see
[[foundations/context-vs-prompt-engineering]]). Hua et al. push back on the
chronology:

> "Although it is often regarded as a recent innovation of the agent era, we argue
> that related practices can be traced back more than twenty years."

Their claim is that the field has evolved since the early 1990s "through distinct
historical phases, each shaped by the intelligence level of machines: from early
human--computer interaction frameworks built around primitive computers, to
today's human--agent interaction paradigms driven by intelligent agents, and
potentially to human--level or superhuman intelligence in the future."

The organizing variable is worth stating plainly, because it is the paper's actual
contribution to how you think about the problem: **context engineering gets easier
as machines get smarter.** Early HCI systems needed context spelled out in rigid,
machine-legible structures because the machine could infer nothing. Agents can
infer a great deal. The discipline is not a fixed set of techniques but a moving
boundary — the work is whatever the machine still cannot infer for itself.

The central question the paper poses is likewise not LLM-specific: "How can
machines better understand our situations and purposes?"

## Key Capabilities

The paper states four contributions:

- **Situates** context engineering historically rather than treating it as new.
- **Provides a systematic definition** — the field's practitioner definitions
  (Karpathy's "filling the context window with just the right information for the
  next step," Anthropic's "curating and maintaining the optimal set of tokens")
  are useful but informal.
- **Outlines the historical and conceptual landscape** across phases.
- **Examines key design considerations for practice.**

It positions itself as "a stepping stone for a broader community effort toward
systematic context engineering in AI systems" — an agenda-setting paper, not a
results paper.

## How It Works

Not captured. The abstract announces a systematic definition and a phase
breakdown without stating either.

**A correction worth recording:** secondary commentary on this paper widely
attributes to it a framing of context engineering as *entropy reduction* —
transforming high-entropy human intentions into low-entropy machine-readable
forms. That phrase does **not** appear in the captured abstract. It may well be in
the paper's body, but this wiki cannot confirm it, and it is not repeated here as
the paper's claim. This is exactly the kind of plausible-sounding secondary
attribution that survives repetition without ever being checked.

## Usage Examples

Not available from this capture.

The one durable takeaway that survives at abstract fidelity is a framing
correction rather than a technique: if context engineering is a twenty-year
discipline rather than a 2025 discovery, then the HCI, ubiquitous-computing, and
context-aware-systems literatures are prior art, and the field's newer sources are
rediscovering vocabulary that already exists. That is a claim about where to look
for answers, and it is actionable on its own.

## Limitations

- **Abstract-only capture.** The definition — the paper's headline contribution —
  is unread.
- **Position paper.** Explicitly agenda-setting; contributes framing, not results.
- **The historical claim is an argument, not a finding.** Whether early-1990s HCI
  context modeling is genuinely continuous with LLM context engineering, or a
  suggestive analogy, cannot be assessed from the abstract.
- **v1 only**, submitted 30 Oct 2025, with no later revision at capture time.

## Open Questions

- What *is* the systematic definition, and does it subsume or contradict the
  Karpathy/Anthropic practitioner definitions this wiki currently uses?
- What are the historical phases, and where does the LLM era sit among them?
- Is the "entropy reduction" framing actually the paper's, or an artifact of
  secondary summarization?

*All three require a full read — a candidate for a higher-fidelity ingest run.*

## See Also

- [[foundations/what-is-context-engineering]] — the practitioner definitions this paper aims to formalize
- [[foundations/context-vs-prompt-engineering]] — the 2025-origin framing this paper complicates
- [[foundations/context-window-as-working-memory]] — the mental model that dominates the LLM-era phase
- [[strategies/agent-memory]] — another 2025–26 survey attempting to impose order on a fragmented vocabulary
