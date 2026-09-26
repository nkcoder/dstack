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

[`plugins/dan-coding/`](plugins/dan-coding/) is a coding mode built on one loop, implement, verify, review. Start it with `/dan-mode`.

### Skills

- [`dan-mode`](plugins/dan-coding/skills/dan-mode/SKILL.md) runs the loop in the current session, fixes critical, high, and medium review findings, and stops before commit. It also sets the tone for replies and code comments, which is plain and jargon-free.
- [`implementation`](plugins/dan-coding/skills/implementation/SKILL.md) covers how to write the change, including finding the root cause of bugs, with short TypeScript and Python references.
- [`verification`](plugins/dan-coding/skills/verification/SKILL.md) proves the change works by running it, with failing-first tests for bugs and tests that check behavior rather than implementation.
- [`review`](plugins/dan-coding/skills/review/SKILL.md) runs `/code-review`, runs `/security-review` when the diff touches a trust boundary, checks comments and prose, and grades each finding.

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