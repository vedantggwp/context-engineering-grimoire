# Overview

Context engineering is the discipline of filling an LLM's limited context window with
the right information, tools, and format at each step of a task — so that an agent can
plausibly solve it. As the sources in this wiki argue in near-unison (Philipp Schmid,
Anthropic, Simon Willison, LangChain), the field is shifting from *prompt* engineering
(crafting one clever static string) to *context* engineering (curating an entire dynamic,
stateful payload across many turns). The consensus framing, popularized by Tobi Lütke and
Andrej Karpathy, is that most agent failures today are not model failures but **context
failures** — and that the context window behaves like the RAM of an operating system:
finite working memory that must be deliberately managed.

This knowledge base covers five clusters. **Foundations** defines the discipline, contrasts
it with prompt engineering, and develops the context-window-as-working-memory mental model.
**Failure modes** catalogs how long and agentic contexts break: Drew Breunig's poisoning /
distraction / confusion / clash, and the Liu et al. "lost in the middle" U-shaped curve
showing models under-use information buried mid-context. **Strategies** gives the repeatable
fixes — LangChain's write/select/compress/isolate taxonomy, Breunig's six management tactics,
and Anthropic's just-in-time agentic retrieval. **Agent engineering** distills hard-won
practice: HumanLayer's 12-Factor Agents, the Manus team's rebuild lessons, and two open
templates (coleam00, davidkimai) that operationalize the ideas. **Evaluation** closes the
loop: without evals (Hamel Husain, Eugene Yan) you cannot tell whether any of the above
actually improved the system.

A note on provenance: this wiki was built deliberately from *messy* sources — practitioner
blogs, arXiv papers, and GitHub repos rather than a single clean documentation site. Only the
three GitHub READMEs captured at `full` (verbatim) fidelity; the remaining twelve sources are
`extract`-fidelity HTML extractions. Where an extract dropped a figure or table (notably the
arXiv plots), the articles say so rather than reconstruct numbers from memory. Read every
claim against the fidelity label on its source.
