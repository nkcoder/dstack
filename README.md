# AI

A personal collection of AI resources — skills, agents, MCP servers, etc. This is a living collection; more categories will be added as they're picked up.

This repo is a Claude Code plugin marketplace (`dstack`) with two plugins: `dan-coding` (everything below) and `dan-financial` (Buffett-style investment analysis).

## Installing

```
/plugin marketplace add nkcoder/dstack
/plugin install dan-coding@dstack
/plugin install dan-financial@dstack
```

## Update

```
/plugin marketplace update nkcoder/dstack
```

## dan-coding

[`plugins/dan-coding/`](plugins/dan-coding/) — software engineering skills, agents, and principles.

### Agents

- [`plugins/dan-coding/agents/dan-agent.md`](plugins/dan-coding/agents/dan-agent.md) — routing target for `/dan-mode`; reads the `dan-mode` skill in full before any work.
- [`plugins/dan-coding/agents/comment-sicko.md`](plugins/dan-coding/agents/comment-sicko.md) — deranged comment-hater that hunts down and deletes narration, banners, and workaround comments.

### Skills

- [`plugins/dan-coding/skills/architect/`](plugins/dan-coding/skills/architect/SKILL.md) — sketch types, signatures, and module structure before code, then stay in the loop while implementation fills in.
- [`plugins/dan-coding/skills/arena/`](plugins/dan-coding/skills/arena/SKILL.md) — spawn N parallel candidates at the same task, pick a base, graft the strongest parts of the losers into it.
- [`plugins/dan-coding/skills/automate-me/`](plugins/dan-coding/skills/automate-me/SKILL.md) — draft or revise a personal "-mode" skill capturing how the user works, via skill-creator + unslop.
- [`plugins/dan-coding/skills/blast-radius/`](plugins/dan-coding/skills/blast-radius/SKILL.md) — find what a change could break beyond the diff, and prove the one fact that makes it safe by running real code.
- [`plugins/dan-coding/skills/bro/`](plugins/dan-coding/skills/bro/SKILL.md) — restate the last message in plain human language, no jargon.
- [`plugins/dan-coding/skills/coding-best-practice/`](plugins/dan-coding/skills/coding-best-practice/SKILL.md) — language-agnostic coding guidelines covering restraint and design, distilled from classic engineering literature plus common LLM failure modes.
- [`plugins/dan-coding/skills/create-verification-skill/`](plugins/dan-coding/skills/create-verification-skill/SKILL.md) — generate a project-local verification skill that drives an app the way a user does.
- [`plugins/dan-coding/skills/dan-mode/`](plugins/dan-coding/skills/dan-mode/SKILL.md) — Dan's agent style: concise responses, deliberate subagents, unslopped prose, simple code, verified work.
- [`plugins/dan-coding/skills/figure-it-out/`](plugins/dan-coding/skills/figure-it-out/SKILL.md) — auditable playbook for large migrations or ambitious multi-part changes with no narrower playbook.
- [`plugins/dan-coding/skills/grilling/`](plugins/dan-coding/skills/grilling/SKILL.md) — grill the user relentlessly about a plan, decision, or idea to stress-test their thinking.
- [`plugins/dan-coding/skills/how/`](plugins/dan-coding/skills/how/SKILL.md) — explain subsystem architecture, runtime flow, and placement/ownership questions.
- [`plugins/dan-coding/skills/interrogate/`](plugins/dan-coding/skills/interrogate/SKILL.md) — multiple LLM reviewers challenge a change from independent angles.
- [`plugins/dan-coding/skills/maintain-verification-skill/`](plugins/dan-coding/skills/maintain-verification-skill/SKILL.md) — periodic audit that keeps a project's verification skill and feature map honest.
- [`plugins/dan-coding/skills/make-bot-ui/`](plugins/dan-coding/skills/make-bot-ui/SKILL.md) — build a custom UI that wakes a Grok Bot over a webhook, optionally exposed on Tailscale.
- [`plugins/dan-coding/skills/no-comments/`](plugins/dan-coding/skills/no-comments/SKILL.md) — spawn Comment Sicko, fix accepted findings, and encode claimed constraints instead of commenting them.
- [`plugins/dan-coding/skills/principle-*/`](plugins/dan-coding/skills/dan-mode/SKILL.md) — 22 single-concept leaf skills (`principle-fix-root-causes`, `principle-boundary-discipline`, `principle-laziness-protocol`, ...) referenced by name from `dan-mode` and other skills rather than invoked directly. See `dan-mode`'s Principles index (linked) for the full list with when-to-apply notes.
- [`plugins/dan-coding/skills/recall/`](plugins/dan-coding/skills/recall/SKILL.md) — reconstruct recent working context from chat history and shared state into a tight current-state brief.
- [`plugins/dan-coding/skills/reflect/`](plugins/dan-coding/skills/reflect/SKILL.md) — spawn three parallel review subagents over the active transcript and route learnings to concrete skill edits.
- [`plugins/dan-coding/skills/setup-dstack/`](plugins/dan-coding/skills/setup-dstack/SKILL.md) — configure which model each dstack role runs on, into the register the routed skills read.
- [`plugins/dan-coding/skills/show-me-your-work/`](plugins/dan-coding/skills/show-me-your-work/SKILL.md) — keep a reviewable decision trail (TSV log) for long-running or unattended work.
- [`plugins/dan-coding/skills/swarm/`](plugins/dan-coding/skills/swarm/SKILL.md) — fan out N parallel workers, drain them, return one report.
- [`plugins/dan-coding/skills/tdd/`](plugins/dan-coding/skills/tdd/SKILL.md) — write a failing test then make it pass, when explicitly requested or the bug has an obvious cheap test target.
- [`plugins/dan-coding/skills/teach/`](plugins/dan-coding/skills/teach/SKILL.md) — explain a body of work plainly by running `how` and `why` and weaving the results together.
- [`plugins/dan-coding/skills/technical-writing/`](plugins/dan-coding/skills/technical-writing/SKILL.md) — Diátaxis structure, Google developer style, and STE instruction rules for docs, RFCs, and PR descriptions.
- [`plugins/dan-coding/skills/typescript-best-practices/`](plugins/dan-coding/skills/typescript-best-practices/SKILL.md) — TypeScript best practices for any `.ts`/`.tsx` file.
- [`plugins/dan-coding/skills/unslop/`](plugins/dan-coding/skills/unslop/SKILL.md) — cut AI tells from any writing.
- [`plugins/dan-coding/skills/why/`](plugins/dan-coding/skills/why/SKILL.md) — discover available MCPs and query each evidence source in parallel for a cited read on design rationale and decisions.

## dan-financial

[`plugins/dan-financial/`](plugins/dan-financial/) — Buffett-style investment analysis.

### Skills

- [`plugins/dan-financial/skills/investment-buffett/`](plugins/dan-financial/skills/investment-buffett/SKILL.md) — Warren Buffett's investment thinking system for stock/company analysis and capital allocation.

# References

## Skills

- [agi-now/buffett-skills](https://github.com/agi-now/buffett-skills): Warren Buffett-style investment analysis via structured stock-analysis templates and reference material.
- [mukul975/Anthropic-Cybersecurity-Skills](https://github.com/mukul975/Anthropic-Cybersecurity-Skills):  818 structured cybersecurity skills mapped to MITRE ATT&CK and NIST CSF 2.0 for security workflows.

## Plugins

- [NeoLabHQ/ddd](https://github.com/NeoLabHQ/context-engineering-kit/tree/master/plugins/ddd): Bakes Clean Architecture, SOLID, and Domain-Driven Design patterns into the dev workflow via automated rules.

- [claude-plugins-official](https://github.com/anthropics/claude-plugins-official): Official, Anthropic-managed directory of high quality Claude Code Plugins.
## Stack

- [Cursor PStack](https://github.com/cursor/plugins/tree/main/pstack): 