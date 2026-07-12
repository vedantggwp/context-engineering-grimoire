---
title: "Lost in the Middle: How Models Use Long Contexts"
summary: "Liu et al. find a U-shaped curve: models best use information at the very start or end of the input and degrade sharply when it sits in the middle."
tags: [failure-modes, long-context, retrieval, research]
sources:
  - url: "https://arxiv.org/html/2307.03172v1"
    title: "Lost in the Middle: How Language Models Use Long Contexts (Liu et al.)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P0
---

# Lost in the Middle: How Models Use Long Contexts

## Overview

Language models can accept long contexts as input, but relatively little was known about how well they actually *use* longer context. Liu et al. (Stanford, UC Berkeley, Samaya AI) analyze model performance on two tasks that require identifying relevant information within the input context: multi-document question answering and synthetic key-value retrieval.

Their central finding: performance is often highest when relevant information occurs at the **beginning or end** of the input context, and degrades significantly when models must access relevant information in the **middle** of long contexts. Plotted against the position of the relevant information, performance traces a distinctive **U-shaped curve**. Furthermore, performance substantially decreases as the input context grows longer — even for models explicitly built for long contexts.

This matters directly for context engineering: where you place information in a prompt affects whether the model can use it, and simply adding more documents does not reliably help. The result held across open models (MPT-30B-Instruct, LongChat-13B 16K) and closed models (OpenAI's GPT-3.5-Turbo and its 16K variant, Anthropic's claude-1.3 and claude-1.3-100k), and even GPT-4 (higher absolute performance but the same U-shape).

## Key Capabilities

- **Establishes the U-shaped position effect** — models retrieve and use information at the very start or very end of their input far better than information in the middle.
- **Shows degradation with length** — averaged across positions, performance steadily decreases as the input context grows longer, on both tasks.
- **Shows extended-context models aren't necessarily better** — a model and its extended-context counterpart perform nearly identically when the input fits in both windows.
- **Connects to a psychological analog** — the U-shape mirrors the serial-position effect, where humans best recall the first and last items in a list.

> **Note on figures:** This article is written from an HTML extract of the paper. The paper's plots (Figures 1, 5, 6, 10, 11, 14, etc.) were not captured as images; only the numeric values stated in the surrounding text are used below. Where a value was not in the extract, the effect is described qualitatively.

## How It Works

The authors run controlled experiments, varying two things independently: the length of the input context and the position of the relevant information within it.

1. **Multi-document QA.** The model receives a question and *k* documents (Wikipedia passages), where exactly one document contains the answer and *k*−1 are "distractor" documents that do not. Context length is modulated by adding or removing distractor documents (they test 10, 20, and 30 documents). Position is modulated by re-ordering documents so the answer-bearing one sits at the start, middle, or end. Data comes from NaturalQuestions-Open; distractors are retrieved with Contriever. Accuracy judges whether any correct answer appears in the output.

2. **Synthetic key-value retrieval.** A minimal testbed stripped of natural-language semantics: the input is a JSON object of *k* key-value pairs (keys and values are random 128-bit UUIDs), and the model must return the value for a specified key. They test 75, 140, and 300 key-value pairs. The same U-shaped curve appears; many models struggle to retrieve a matching token from the middle. (claude-1.3 and claude-1.3-100k are near-perfect across lengths; others struggle, especially at 140+ pairs.)

Reported numbers from the extract:

- With the relevant document in the **middle**, GPT-3.5-Turbo's multi-document QA performance drops below its **closed-book** performance (56.1%) — i.e., worse than giving it no documents at all. Its performance can drop by **more than 20%** from peak to nadir.
- GPT-3.5-Turbo / GPT-3.5-Turbo (16K) had the highest closed-book (**55%**) and oracle (**88%**) performance among the models.
- GPT-3.5-Turbo's lowest score in the 20-document setting was **52.9%** (answer positioned 10th of 20). GPT-3.5-Turbo (16K) scored **49.5%** in the 30-document setting (answer 10th of 30), showing the extended-context model still degraded.
- On open-domain QA (retriever-reader on NaturalQuestions-Open), reader performance saturates long before retriever recall does: using more than 20 retrieved documents only marginally improved performance (~**1.5%** for GPT-3.5-Turbo, ~**1%** for Claude) while increasing latency and cost.

Closed-book vs. oracle accuracy from the paper's Table 1:

```
Model                 Closed-Book   Oracle
LongChat-13B (16K)    35.0%         83.35%
MPT-30B-Instruct      31.5%         81.9%
GPT-3.5-Turbo         56.1%         88.3
GPT-3.5-Turbo (16K)   56.0%         88.6
Claude                48.3%         76.1%
Claude (100K)         48.2%         76.4%
```

### Why does this happen?

The authors run preliminary investigations into three factors:

- **Architecture.** Encoder-decoder models (Flan-T5-XXL, Flan-UL2) are relatively robust to position *when evaluated within their training-time sequence length*, but exhibit the U-shape on sequences longer than seen in training. The bidirectional encoder may let them estimate relative document importance better.
- **Query-aware contextualization.** Placing the query both before and after the data makes decoder-only models perform the synthetic key-value task perfectly (GPT-3.5-Turbo 16K reaches perfect performance at 300 pairs, vs. a 45.6% low without it), but only minimally changes the multi-document QA trend.
- **Instruction tuning.** Even the base model MPT-30B (before instruction tuning) shows the U-shaped curve, so instruction tuning alone doesn't cause it. The effect complements prior findings of a recency bias toward the end of the context; here models can also use the beginning.

## Usage Examples

### Example: Reranking retrieved documents

Because models use information best at the start or end, the paper suggests effective reranking of retrieved documents — pushing the most relevant content closer to the start of the input context — as a promising direction for improving retriever-reader systems. Don't assume a relevant document buried in the middle of many retrieved passages will be used.

### Example: Truncating the retrieved list

Since reader performance saturates long before retriever recall (more than 20 documents barely helped), returning fewer documents when appropriate (ranked-list truncation) can preserve accuracy while cutting the input length, latency, and cost. More context is not always better.

## Limitations

- **The "why" investigations are preliminary** — the architecture, query-aware contextualization, and instruction-tuning analyses are explicitly framed as preliminary, not definitive causal explanations.
- **Task-specific findings** — results come from multi-document QA and synthetic key-value retrieval; the open-domain QA case study notes the marginal value of added context is downstream-task-specific.
- **Point-in-time models** — the study evaluates 2023-era models (GPT-3.5-Turbo, claude-1.3, MPT-30B, LongChat-13B, GPT-4 on a subset); absolute numbers reflect those systems, though the U-shaped pattern held even for the strongest model tested.
- **Some values not captured** — this article draws only on numbers present in the text extract; the full per-position curves live in figures that were not captured.

## See Also

- [[failure-modes/how-contexts-fail]] — Drew Breunig's four failure modes, including Context Distraction (degradation as context grows), which this paper measures empirically
- [[strategies/agentic-search-and-retrieval]] — retrieval and reranking strategies that respond to the position effect
- [[foundations/context-window-as-working-memory]] — why filling the window isn't the same as using it well
