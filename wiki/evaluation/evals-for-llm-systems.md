---
title: "Evals for LLM Systems"
summary: "Why evals are the starting point for any LLM system, Hamel's three levels of evaluation, evaluating RAG, and Eugene Yan's eval metrics and pitfalls."
tags: [evaluation, llm-systems, rag, testing]
sources:
  - url: "https://eugeneyan.com/writing/llm-patterns/"
    title: "Patterns for Building LLM-based Systems & Products (Eugene Yan)"
    accessed: 2026-07-10
  - url: "https://hamel.dev/blog/posts/evals/"
    title: "Your AI Product Needs Evals (Hamel Husain)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P1
---

# Evals for LLM Systems

## Overview

Evaluations ("evals") are a set of measurements used to assess a system's performance on a task. For any LLM-based system — and a "system" here can be made up of multiple components such as LLMs, prompt templates, retrieved context, and parameters like temperature — evals are what tell you whether a change helped or hurt. Without them, as Eugene Yan puts it, "we would be flying blind, or would have to visually inspect LLM outputs with each change."

Hamel Husain frames the stakes bluntly from consulting experience: "unsuccessful products almost always share a common root cause: a failure to create robust evaluation systems." Success with AI hinges on how fast you can iterate, and iterating fast requires three things — a way to evaluate quality, a way to debug issues (logging and inspecting data), and a way to change behavior (prompt engineering, fine-tuning, code). Many people focus only on the third, which keeps their product stuck at the demo stage.

For context engineering specifically, this is the feedback loop: the whole practice of assembling prompts, retrieved documents, and tool definitions is only "working" if you can measure it. Evals are how you know whether a context change actually improved the system or just moved the failures around.

## Key Capabilities

- **Measure performance and catch regressions** — a representative eval set lets you measure system changes at scale instead of eyeballing outputs each time.
- **Guide iteration** — task-specific evals guide prompt engineering, model selection, and fine-tuning; Yan calls this Eval Driven Development (EDD).
- **Unlock debugging and fine-tuning** — Hamel notes the infrastructure for evaluation (a searchable trace database, assertions, curated data) is nearly the same infrastructure you need for debugging and for generating fine-tuning data.
- **Reduce reliance on humans** — a strong LLM can serve as a reference-free judge, potentially removing the need for gold references or human labels on every output.

## How It Works

### Hamel's three levels of evaluation

Hamel organizes evaluation into three levels, ordered by cost (Level 3 > Level 2 > Level 1), which dictates how often you run each:

1. **Level 1 — Unit tests.** Assertions, like you'd write in pytest, that run fast and cheaply on every code change. You scope them by breaking your LLM's surface into features and scenarios. For Rechat's real-estate assistant "Lucy," the *Listing Finder* feature has scenarios like "one listing matches" (`len(listing_array) == 1`), "multiple match" (`> 1`), and "none match" (`== 0`). Generic assertions apply across features — e.g., a regex that asserts no internal UUID leaks into the response. You generate test cases to trigger each scenario, often synthetically with an LLM. Rechat has hundreds of these tests. Unlike traditional unit tests, you don't necessarily need a 100% pass rate — the pass rate is a product decision based on the failures you're willing to tolerate.

2. **Level 2 — Human & model eval.** After a solid Level 1 foundation, evaluate what assertions can't catch. The prerequisite is **logging traces** (the conversation/request flow). You must "remove ALL friction from looking at data," often by building a domain-specific viewing and labeling tool (Rechat's was built with Shiny for Python in under a day). Start by labeling examples simply as good or bad. Then you can add **automated evaluation with an LLM**: a more powerful model writes a critique and a binary good/bad outcome, and you align that judge to a human (Hamel iterates on this in a spreadsheet, comparing model vs. human labels). Track the correlation between model-based and human evaluation to decide how much to trust the automated judge — and note that raw agreement is misleading under class imbalance, so measure precision and recall separately.

3. **Level 3 — A/B testing.** Confirm the product actually drives the user behaviors and outcomes you want. A/B testing for LLMs isn't much different from other products, and this level is usually only appropriate for more mature products.

Hamel runs Level 1 on every code change, Level 2 on a set cadence, and Level 3 only after significant product changes.

### Eugene Yan's eval metrics and their pitfalls

Yan surveys the metric landscape and, crucially, its limits:

- **Benchmarks** like MMLU, HELM, EleutherAI's harness, and AlpacaEval measure broad capability, but the same model can score very differently depending on implementation details (prompt phrasing, tokenization, how the answer probability is computed). Even MMLU is sensitive enough that the QLoRA author concluded "do not work with/report or trust MMLU scores."
- **Reference metrics.** BLEU (precision-based, with a brevity penalty; common in translation) and ROUGE (recall-based; common in summarization) rely on n-gram overlap. BERTScore and MoverScore use contextualized embeddings and cosine similarity, so they can account for synonyms and paraphrasing that exact-match metrics miss.
- **Pitfalls.** These conventional metrics have (1) poor correlation with human judgments — BLEU and ROUGE have shown negative correlation with human fluency ratings and low correlation on creative/diverse tasks; (2) poor adaptability across tasks — n-gram metrics make little sense for abstractive summarization or dialogue, where an output can have zero overlap with the reference yet be a great response; and (3) poor reproducibility — high variance is reported across studies.
- **LLM-as-judge.** An emerging pattern uses a strong LLM as a reference-free evaluator. G-Eval applies an LLM with chain-of-thought and form-filling to score outputs and had high Spearman correlation with human judgments (0.514), outperforming prior methods. In the Vicuna/MT-Bench work, GPT-4's single-answer grading agreed with humans 85% of the time — higher than humans agreed with each other (81%). Known biases to mitigate: **position bias** (favoring the first response — swap order and evaluate twice), **verbosity bias** (favoring longer answers — keep compared responses similar in length), and **self-enhancement bias** (favoring its own outputs — GPT-4 favored itself with a 10% higher win rate, Claude-v1 with 25%; don't use the same model to judge its own output).

Both authors converge on a practical stance: **don't rely on generic, off-the-shelf benchmarks** to measure your product. Yan recommends starting from task-specific evals (prompt, context, expected output). Hamel puts it as "create an evaluation system specific to your problem." And both keep a place for human "vibe checks" — Yan quotes MosaicML's Jonathan Frankle: "The vibe-based eval cannot be underrated."

### Evaluating RAG

Because context-engineered systems so often rely on retrieval, the retrieval component needs its own evaluation. Hamel notes you can evaluate sub-components of your AI, like RAG, separately from the system as a whole (and points to Jason Liu's writing for depth). Yan grounds *why* RAG matters: it reduces hallucination by grounding the model on retrieved context, and it's cheaper to keep a retrieval index current than to continually pre-train a model. He also reports, from building Obsidian-Copilot, that **hybrid retrieval** (a traditional keyword/BM25 index plus embedding-based semantic search) works better than either alone — keyword search handles names, acronyms, and IDs where pure embedding search falls short, while semantic search handles synonyms and correlation that keyword search misses. Upstream retrieval quality flows through to end-to-end accuracy: DPR showed higher retrieval precision translates to higher QA accuracy.

## Usage Examples

### Example: Eval Driven Development for a context change

You tweak the retrieved-context assembly in a RAG pipeline. Rather than eyeballing a few outputs, run your Level 1 assertion suite on every change (fast, cheap), then periodically run Level 2 human/model eval on logged traces to catch quality issues assertions miss. Track results over time — Rechat visualizes test results in Metabase to see an error's prevalence before vs. after a fix. This is EDD: the eval set is the thing that tells you the context change worked.

### Example: Generating test cases and fine-tuning data from one engine

Hamel shows the same synthetic-generation prompt produces both test cases and fine-tuning data. Ask an LLM to "Write 50 different instructions a real estate agent can give to create contacts…," pair each with a lookup instruction, then assert the CRM returns exactly one result. Once you have an eval system, "you already have a robust data generation and curation engine" — your Level 1 and Level 2 checks filter undesirable synthetic data for fine-tuning.

## Limitations

- **Metrics can mislead** — classical metrics (BLEU, ROUGE) correlate poorly with human judgment and don't fit open-ended tasks; benchmark scores aren't comparable unless the implementation is identical down to prompts and tokenization.
- **LLM judges are biased** — position, verbosity, and self-enhancement biases require explicit mitigation, and agreement-as-a-metric is misleading under class imbalance (use precision and recall).
- **Human eval doesn't fully go away** — Hamel: "You are doing it wrong if you aren't looking at lots of data"; you can sample more over time but never stop entirely. A reasonable heuristic is to keep reading logs until you stop learning anything new.
- **Semantic caching and other shortcuts carry risk** — Yan warns that caching on semantic similarity can serve the wrong response (a summary of "Mission Impossible 2" for a "Mission Impossible 3" request); evals are what surface such failures.

## See Also

- [[failure-modes/how-contexts-fail]] — the failure modes evals are meant to catch (poisoning, distraction, confusion, clash)
- [[strategies/agentic-search-and-retrieval]] — the RAG and retrieval techniques whose quality evals measure
- [[foundations/what-is-context-engineering]] — why measuring a context-engineered system is the point of building evals
- [Your AI Product Needs Evals (Hamel Husain)](https://hamel.dev/blog/posts/evals/) — the full three-level playbook with code
- [[strategies/multi-agent-context-isolation]] — outcome-based evaluation applied to non-deterministic agent paths
