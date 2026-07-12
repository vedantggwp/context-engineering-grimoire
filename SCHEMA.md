# SCHEMA.md

> Conventions, taxonomy, and structure for this Grimoire wiki.
> Every stage reads this file. Edit it to reshape the entire knowledge base.

---

## Domain

topic: "Context Engineering for LLM Agents"
scope:
  in: "How to fill an agent's context window with the right information at each step — foundations, failure modes, strategies (write/select/compress/isolate), agent-engineering principles, retrieval/memory, and evaluation."
  out: "Model training, fine-tuning internals, prompt-engineering tricks unrelated to context assembly, and vendor-specific API reference."
audience: "Senior engineers building LLM agents who need a working mental model plus concrete, source-grounded tactics."
taxonomy: "defined"

---

## Taxonomy

| Directory | Description | Example Articles |
|-----------|-------------|-----------------|
| `foundations` | What context engineering is, why it matters, how it differs from prompting | what-is-context-engineering, context-vs-prompt-engineering |
| `failure-modes` | The ways long/agentic contexts degrade model performance | how-contexts-fail, lost-in-the-middle |
| `strategies` | Repeatable tactics for managing the context window | four-context-strategies, context-management-tactics, agentic-search |
| `agent-engineering` | Principles and lessons for building reliable context-driven agents | twelve-factor-agents, lessons-from-manus, context-engineering-templates |
| `evaluation` | Measuring whether a context-engineered system actually works | evals-for-llm-systems |

---

## Cross-Reference Format

Link between wiki articles using double-bracket notation:

```
[[topic/slug]]
```

- `topic` matches a taxonomy directory name from the table above
- `slug` is the article filename without `.md`

Backlinks are bidirectional. When article A references article B, the compile
stage ensures article B's "See Also" section links back to article A.

---

## Raw Source Naming

Raw sources are stored in `raw/` and named by convention:

```
raw/{topic}/{YYYY-MM-DD}-{slug}.md
```

Raw files are immutable once written. All interpretation happens in `wiki/`.

---

## Confidence Tiers

| Tier | Meaning |
|------|---------|
| P0 | Primary/authoritative source (Anthropic engineering, LangChain, peer-style survey/paper) |
| P1 | Strong practitioner source (named-author blog, well-known repo) |
| P2 | Supporting/contextual source |

## Fidelity

Every raw source carries `fidelity: full | extract | failed`.
- `full` — verbatim capture (GitHub README raw / content-negotiated markdown).
- `extract` — deterministic HTML extraction (blogs, arXiv HTML, forums). Faithful
  to the page but model/parser-mediated; may drop figures, tables, or sidebars.
- `failed` — no acceptable capture; never compiled into an article.

Articles inherit the **lowest** fidelity among their sources. This wiki is a
deliberately messy-source test: most articles rest on `extract` captures.
