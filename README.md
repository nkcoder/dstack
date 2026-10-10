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

[`plugins/dan-coding/`](plugins/dan-coding/) is a coding mode built on one loop. It asks only the questions that need you, implements and verifies the change, has a separate reviewer check it, fixes what matters, and stops before commit. Start it with `/dan-mode`.

Implement and verify run on your session's model, and review runs in a separate Opus reviewer at high effort. The intended setup is Sonnet 5.5 at high effort as your default. Set it once with `/model` and `/effort high`, pressing Enter each time to save it. On Bedrock or Google Cloud, use the full model ID, like `/model claude-sonnet-5-5`.

Optional: install `claude-md-management` from the official marketplace. When a session teaches something worth keeping, dan-mode runs its `/claude-md-management:revise-claude-md` to propose CLAUDE.md additions for your approval. Without it, dan-mode lists the suggestions in its final reply.

### Skills

- [`dan-mode`](plugins/dan-coding/skills/dan-mode/SKILL.md) runs the whole loop in the current session and stops before commit. Its two references are the mandatory rules, [`principles.md`](plugins/dan-coding/skills/dan-mode/references/principles.md) for code and [`unslop.md`](plugins/dan-coding/skills/dan-mode/references/unslop.md) for prose. Both list only what the model wouldn't do by default.
- [`review`](plugins/dan-coding/skills/review/SKILL.md) runs `/code-review`, runs `/security-review` when the diff touches a trust boundary, checks the test evidence and both rules files, and grades each finding.

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