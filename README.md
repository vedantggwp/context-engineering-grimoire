# Context Engineering — a living grimoire

A knowledge base about **context engineering for LLM agents** that maintains itself.
Built with [Grimoire](https://github.com/vedantggwp/grimoire); every week a scheduled
run scouts for new sources, proposes updates, and opens a pull request — the wiki
compounds instead of rotting. Browse the PR history to watch it think.

## What's inside

- **[`wiki/`](wiki/)** — compiled, cross-referenced articles with backlinks and freshness tiers
- **[`raw/`](raw/)** — the preserved source captures behind every article, each labeled with
  capture fidelity (`full` = verbatim, `extract` = partial). Articles never look more
  trustworthy than their sources: degraded capture is visibly labeled, all the way into
  query responses.
- **[`site/`](site/)** — a generated study frontend (reading, graph, search, quiz, gaps views)
- **[`approved-sources.md`](approved-sources.md)** — the curated source list with confidence scores
- **[`_config/update.md`](_config/update.md)** — the standing editorial policy the weekly
  update run obeys; changes ship as PRs, never direct commits

## Query it from your AI tools

This wiki ships with an MCP server. With [Grimoire](https://github.com/vedantggwp/grimoire)
installed, point any MCP client (Claude Desktop, Claude Code, …) at this directory and your
agent can query the knowledge base — and it will say "not covered" rather than guess when a
question falls outside the wiki.

## Provenance note

Source captures are honest about their limits: docs sites and GitHub sources are preserved
verbatim; blog and paper captures are typically `extract` fidelity (labeled as such). Raw
files retain author and source URL; excerpts stay within quotation bounds — if you're an
author who'd prefer different handling, open an issue.

---

*Maintained by [Grimoire](https://github.com/vedantggwp/grimoire)'s update engine under a
PR-gated policy. Human-reviewed before every merge.*
