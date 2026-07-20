---
title: "Context Rot: Performance Degrades With Input Length Alone"
summary: "Chroma tests 18 frontier models on deliberately trivial tasks and finds performance degrades as input grows — even far below the context limit, and even when the task itself never gets harder."
tags: [failure-modes, long-context, benchmarks, research]
sources:
  - url: "https://research.trychroma.com/context-rot"
    title: "Context Rot: How Increasing Input Tokens Impacts LLM Performance (Hong, Troynikov, Huber — Chroma)"
    accessed: 2026-07-20
updated: 2026-07-20
confidence: P0
---

# Context Rot: Performance Degrades With Input Length Alone

## Overview

Modern models advertise context windows in the millions of tokens, and they score
near-perfectly on Needle in a Haystack (NIAH). Chroma's technical report argues
that this combination has produced a false sense of security: NIAH "is
fundamentally a simple retrieval task" that "typically assesses direct lexical
matching, which may not be representative of flexible, semantically oriented
tasks."

The report's methodological move is what makes it load-bearing for context
engineering. Prior long-context benchmarks confound two variables — as input
grows, the task usually gets harder too. Chroma instead holds task complexity
fixed and varies only input length, "allowing us to directly measure the effect
of input length alone." Across 18 models and 194,480 LLM calls, performance still
degrades.

This is the empirical successor to [[failure-modes/lost-in-the-middle]]. Where
Liu et al. (2023) established that *position* within the context matters, Context
Rot establishes that *length itself* matters, on 2025-era frontier models, even
when nothing else changes.

The conclusion the authors draw is a direct argument for this wiki's subject:
"Whether relevant information is present in a model's context is not all that
matters; what matters more is how that information is presented."

> **Note on figures:** This article is written from an HTML extract. The report's
> plots were not captured — only their captions. Every claim below is drawn from
> the report's prose. Per-model performance curves, which live in the figures, are
> not reproduced here; read the source for them.

## Key Capabilities

- **Isolates input length as a variable** — task complexity held constant, so
  degradation cannot be attributed to harder problems.
- **Covers 18 models** including closed-source and open-weights, in both standard
  and thinking modes where available.
- **Tests four NIAH variables** the standard benchmark ignores: needle–question
  similarity, distractors, needle–haystack similarity, and haystack structure.
- **Adds two non-NIAH settings** — LongMemEval (conversational QA) and a trivial
  word-replication task.
- **Ships a replication codebase** at `github.com/chroma-core/context-rot`.

## How It Works

Each model is tested across 8 input lengths and 11 needle positions for every
combination of needle type, haystack topic, and haystack structure. Temperature
is 0 except where incompatible (o3) or discouraged (Qwen thinking mode). Qwen
models are extended from 32,768 to 131,072 tokens via YaRN. Outputs are graded by
an aligned GPT-4.1 judge. Refusals were negligible: 69 of 194,480 calls (0.035%).

### 1. Needle–question similarity

Needles and questions are scored by cosine similarity, averaged across five
embedding models for robustness. Finding: **"as needle-question similarity
decreases, model performance degrades more significantly with increasing input
length."** At short lengths, models handle even low-similarity pairs well — so the
failure is not intrinsic pair difficulty.

Notably, across 11 needle positions this specific task showed **"no notable
variation in performance"** by position. That is a meaningful contrast with
lost-in-the-middle rather than a contradiction of it: the two studies use
different tasks, and Chroma's point is that length degrades performance even where
position does not.

### 2. Distractors

Using a high-similarity needle (so the needle should be easy to find), the authors
add topically-related-but-wrong statements. **"Even a single distractor reduces
performance relative to the baseline (needle only), and adding four distractors
compounds this degradation further."** Impact is non-uniform — some distractors
hurt far more than others.

The model-family split is the practically useful part:

> "Claude models consistently exhibit the lowest hallucination rates.
> Specifically, Claude Sonnet 4 and Opus 4 are particularly conservative and tend
> to abstain when uncertain, explicitly stating that no answer can be found. In
> contrast, GPT models show the highest rates of hallucination, often generating
> confident but incorrect responses when distractors are present."

### 3. Needle–haystack similarity

Effect is **non-uniform**. In a Paul Graham essay haystack, arXiv needles
performed significantly better than PG-essay needles — models did better when the
needle did *not* blend in. In an arXiv haystack, the difference was minimal. The
authors explicitly decline to generalize: "Testing across only two topics is
insufficient to draw a generalizable conclusion."

### 4. Haystack structure (the counterintuitive one)

> "Surprisingly, we find that structural coherence consistently hurts model
> performance."

Shuffling the haystack's sentences — destroying logical flow while preserving
topic — **improved** performance, consistently, "across all 18 models and
needle-haystack configurations." The authors flag the mechanism as an open
interpretability question, out of scope for the report.

### 5. LongMemEval

A more realistic setting: conversational QA over chat history. 306 prompts
(filtered from LongMemEval_s, dropping 38 ambiguous ones), averaging ~113k tokens,
versus focused versions averaging ~300 tokens containing only the relevant parts.

"Across all models, we see significantly higher performance on focused prompts
compared to full prompts."

The report's explanation is the sharpest framing in the paper for practitioners:
the full-input condition silently makes the model do **two jobs in one call** —
find the relevant history (retrieval), then reason over it. The focused condition
requires only the second. Adding irrelevant context does not merely dilute; it
adds a task.

Thinking modes improve both conditions, but "we still see a performance gap
between the two input lengths even with full reasoning capabilities on the latest
models."

### 6. Repeated words

The floor case. Models are asked to replicate a string of repeated words with one
unique word inserted — a task a two-line program does perfectly every time. Even
here, "model performance becomes non-uniform as context length, encompassing both
input and output lengths, grows." Because the models are autoregressive, generated
output re-enters the input, so output length is context length too.

## Usage Examples

### Example: Stop treating the advertised window as usable capacity

The report's framing is that degradation is continuous, not a cliff at the window
boundary. Budget context by measured task performance, not by the spec sheet.

### Example: Retrieve, then reason — in separate steps

The LongMemEval result is a direct argument for the compress/select strategies in
[[strategies/four-context-strategies]]. If a focused ~300-token prompt beats a
~113k-token one containing the same answer, the retrieval step is doing real work
that the model should not be asked to do inline.

### Example: Audit for distractors, not just for recall

Recall-oriented retrieval that sweeps in topically-similar-but-wrong passages is
actively harmful — a single distractor measurably hurts. And the cost is
model-dependent: a GPT-family system will tend to answer confidently and wrongly
where a Claude-family system will tend to abstain. Both are failures, but they
need different guardrails.

## Limitations

- **Figures not captured** — the per-model degradation curves are the report's
  core evidence and live in plots this article could not read. Quantitative
  per-model claims are therefore absent here by design.
- **Two haystack topics only** — the authors themselves refuse to generalize the
  needle–haystack similarity result.
- **Point-in-time models** — 2025-era frontier models (Claude 4 family, GPT-4.1,
  Gemini 2.5, Qwen3, o3). The pattern is claimed across all 18; absolute numbers
  reflect those systems.
- **Deliberately simple tasks** — a strength for isolating length, but the authors
  note real applications "typically involve much greater complexity, implying that
  the influence of input length may be even more pronounced in practice." That
  extrapolation is asserted, not measured.
- **Mechanism unexplained** — why structural coherence hurts is left open.

## See Also

- [[failure-modes/lost-in-the-middle]] — the positional half of the same problem; this report is its length-isolating successor on frontier models
- [[failure-modes/how-contexts-fail]] — Breunig's Context Distraction named this qualitatively; Context Rot measures it
- [[strategies/four-context-strategies]] — select and compress are the direct responses to length-driven degradation
- [[strategies/context-compression-optimization]] — ACON attacks the same problem by shrinking what enters the window
- [[foundations/context-window-as-working-memory]] — why a large window is capacity, not usable capacity
- [[strategies/multi-agent-context-isolation]] — buying extra context windows as a response to length degradation
