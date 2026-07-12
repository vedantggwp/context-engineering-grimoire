---
title: "Context Engineering Templates"
summary: "Two open-source templates that operationalize context engineering: coleam00's PRP/CLAUDE.md workflow and davidkimai's atoms-to-fields biological progression."
tags: [agent-engineering, context-engineering, templates, prp, open-source]
sources:
  - url: "https://github.com/coleam00/context-engineering-intro"
    title: "Context Engineering Intro Template (coleam00)"
    accessed: 2026-07-10
  - url: "https://github.com/davidkimai/Context-Engineering"
    title: "Context-Engineering (davidkimai)"
    accessed: 2026-07-10
updated: 2026-07-10
confidence: P1
---

# Context Engineering Templates

## Overview

Context engineering as a discipline has open-source templates that turn the idea into a repeatable workflow. This article covers two of them.

**coleam00's Context Engineering Template** is "A comprehensive template for getting started with Context Engineering - the discipline of engineering context for AI coding assistants so they have the information necessary to get the job done end to end." Its headline claim: "Context Engineering is 10x better than prompt engineering and 100x better than vibe coding." It frames the shift as: prompt engineering is "Like giving someone a sticky note," while context engineering is "Like writing a full screenplay with all the details."

**davidkimai's Context-Engineering** is "A frontier, first-principles handbook for moving beyond prompt engineering to the wider discipline of context design, orchestration, and optimization." It anchors on Karpathy's definition — "Context engineering is the delicate art and science of filling the context window with just the right information for the next step" — and organizes the whole field around a biological metaphor progressing from atoms to neural/semantic fields.

Together they show two operationalization styles: coleam00 is a hands-on project workflow for AI coding assistants; davidkimai is a progressive curriculum grounded in research.

## Key Capabilities

- **PRP workflow (coleam00)** — Generate and execute a "Product Requirements Prompt," a "comprehensive implementation blueprint," via `/generate-prp` and `/execute-prp`.
- **CLAUDE.md global rules (coleam00)** — A file of "project-wide rules that the AI assistant will follow in every conversation."
- **Examples folder (coleam00)** — A "critical" `examples/` folder, since "AI coding assistants perform much better when they can see patterns to follow."
- **Biological metaphor progression (davidkimai)** — A first-principles ladder: `atoms → molecules → cells → organs → neural systems → neural & semantic field theory`.
- **Research-grounded framing (davidkimai)** — Ties each level to papers (a 1400-paper survey, IBM Zurich cognitive tools, Context Rot, and more).

## How It Works

### coleam00: the PRP / CLAUDE.md / examples / INITIAL workflow

The quick-start sequence is:

1. Clone the template.
2. "Set up your project rules" by editing `CLAUDE.md` — which covers "Project awareness," "Code structure," "Testing requirements," "Style conventions," and "Documentation standards."
3. "Add examples" — "Place relevant code examples in the examples/ folder."
4. "Create your initial feature request" by editing `INITIAL.md`, which has four sections: `FEATURE`, `EXAMPLES`, `DOCUMENTATION`, and `OTHER CONSIDERATIONS`.
5. "Generate a comprehensive PRP" with `/generate-prp INITIAL.md`.
6. "Execute the PRP to implement your feature" with `/execute-prp PRPs/your-feature-name.md`.

A **PRP (Product Requirements Prompt)** is an "implementation blueprint" that includes "Complete context and documentation," "Implementation steps with validation," "Error handling patterns," and "Test requirements." They are "similar to PRDs... but are crafted more specifically to instruct an AI coding assistant." `/generate-prp` runs a Research Phase, Documentation Gathering, Blueprint Creation, and a Quality Check that "Scores confidence level (1-10)." `/execute-prp` then loads context, plans "using TodoWrite," executes, validates ("Runs tests and linting"), and iterates.

The template's stated rationale: "Most agent failures aren't model failures - they're context failures," and validation loops make the system "Self-Correcting."

### davidkimai: the atoms → fields biological progression

This repo "provides a progressive, first-principles approach to context engineering, built around a biological metaphor":

```
atoms → molecules → cells → organs → neural systems → neural & semantic field theory
  │        │         │         │             │                         │
single    few-     memory +   multi-   cognitive tools +     context = fields +
prompt    shot     agents     agents   operating systems     persistence & resonance
```

Each stage maps to a familiar real-world parallel: atoms are "Like: Basic prompt engineering," molecules "Like: Few-shot learning," cells "Like: Conversational chatbots," organs "Like: Multi-agent systems," neural systems "Like: ReAct / Chain-of-Thought," and neural fields "Like: Semantic field theory." The repo layers these into four levels — Level 1 Basic Context Engineering, Level 2 Field Theory, Level 3 Protocol System, and Level 4 Meta-Recursion.

It adopts a "Karpathy + 3Blue1Brown Inspired Style" with six principles: "First principles," "Iterative add-on – add only what the model demonstrably lacks," "Measure everything," "Delete ruthlessly – pruning beats padding," "Code > slides," and "Visualize everything." Its definition of context engineering (from the 1400-paper survey) is that "Context is the complete information payload provided to a LLM at inference time, encompassing all structured informational components that the model needs to plausibly accomplish a given task."

## Usage Examples

### Example: Writing an effective INITIAL.md (coleam00)

The template contrasts a weak vs. strong feature description. Weak: "Build a web scraper." Strong: "Build an async web scraper using BeautifulSoup that extracts product data from e-commerce sites, handles rate limiting, and stores results in PostgreSQL." Its best practices stress being explicit ("Don't assume the AI knows your preferences"), providing comprehensive examples ("Show both what to do AND what not to do"), and using validation gates ("PRPs include test commands that must pass").

### Example: A learning path through the metaphor (davidkimai)

The repo's quick start walks a learner from `00_foundations/01_atoms_prompting.md` ("Understand why prompts alone often underperform"), to a minimal runnable example, to copy-paste templates in `20_templates/`, to a complete `30_examples/00_toy_chatbot/` "with context management" — mirroring the atoms → organs climb.

## Limitations

- **coleam00 excludes RAG/tools for now** — "This template doesn't focus on RAG and tools with context engineering because I have a LOT more in store for that soon."
- **coleam00 is coding-assistant specific** — It is scoped to "AI coding assistants" (Claude Code slash commands, CLAUDE.md), not agents in general.
- **davidkimai reaches speculative frontier levels** — Later stages invoke "Quantum Semantics," "Neural Field Theory," and "Meta-Recursion," which the repo frames as frontier/"Under Construction" rather than settled practice ("Comprehensive Course Under Construction").
- **Abstraction has a cost** — The repo itself quotes "Abstraction is the cost of generalization," flagging that the higher metaphor levels trade concreteness for generality.

## See Also

- [[foundations/context-vs-prompt-engineering]] — the prompt-vs-context distinction both templates build on
- [[foundations/what-is-context-engineering]] — the discipline these templates operationalize
- [[agent-engineering/twelve-factor-agents]] — another practitioner framework for context-aware agents
- [Context Engineering Template (coleam00)](https://github.com/coleam00/context-engineering-intro) — the PRP workflow template
- [Context-Engineering (davidkimai)](https://github.com/davidkimai/Context-Engineering) — the first-principles handbook
