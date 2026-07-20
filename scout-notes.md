# Scout Notes

Running log of scouting decisions for this grimoire. Delta runs (from
`/grimoire:update`) append their additions here.

## Deduplication

- 2026-07-20 update run: delta scout surfaced ~55 raw candidates across 8 angles;
  1 dropped by cross-run dedup (anthropic.com/engineering/effective-context-engineering,
  already ingested as #3); ~50 dropped as aggregators, mirrors, or SEO restatements
  of primary sources. 4 primaries scored. Notable: the delta angles that paid off
  were corpus-gap angles (memory, multi-agent isolation, context rot), not temporal
  ones — "what's new since 2026-07-12" returned almost entirely secondary commentary.
- 2026-07-12 update run: delta scout surfaced ~10 temporal candidates; 0 dropped
  by cross-run dedup (none matched `knownUrls` — all were genuinely absent from
  the corpus).

## Additions

- Added by update run 2026-07-20: https://research.trychroma.com/context-rot
  (Context Rot, score 26/P0 — the wiki's only long-context degradation source was
  Liu et al. 2023; this is the 18-frontier-model successor),
  https://www.anthropic.com/engineering/multi-agent-research-system (score 26/P0 —
  the "isolate" strategy was covered only abstractly via the LangChain taxonomy),
  https://arxiv.org/abs/2512.13564 (Memory survey, score 26/P0 — the corpus had
  zero memory coverage), https://arxiv.org/abs/2510.26493 (Context Engineering 2.0,
  score 23/P0 — reframes the discipline's history and gives it a formal definition).
- Added by update run 2026-07-12: https://arxiv.org/abs/2510.04618 (ACE — Agentic
  Context Engineering, score 26/P0), https://arxiv.org/abs/2510.00615 (ACON —
  context compression optimization, score 24/P0). Both are ICLR/ICML 2026 papers
  that predate the initial 2026-07-10 build but were not in the original curation;
  scored high on uniqueness/authority/depth, honestly moderate on recency.
