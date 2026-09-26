# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`dstack` is a Claude Code **plugin marketplace** (`.claude-plugin/marketplace.json`) publishing two plugins:

- **`dan-coding`** (`plugins/dan-coding/`) — a minimal software engineering agent style built around a single Implementation => Verification => Review loop. Everything most work in this repo touches.
- **`dan-financial`** (`plugins/dan-financial/`) — a single skill, Warren Buffett-style investment analysis (`skills/investment-buffett/`).

There is no application to build or run; the "product" is Markdown skill/agent definitions. Users install via Claude Code itself, not a local script:

```
/plugin marketplace add nkcoder/dstack
/plugin install dan-coding@dstack
/plugin install dan-financial@dstack
```

(There is no `install.sh` — that symlink-based installer was removed when the repo converted to a marketplace. Don't recreate it; plugin install/update is Claude Code's job now.)

## Validating changes to skills/agents

There is no automated lint or test suite for the Markdown content — the prior `check-skills.mjs`/`check-plan.mjs` scripts and the `orch`/`watch-pr` Bun toolchain under `plugins/dan-coding/skills/dan-mode/scripts/` were removed as part of the September 2026 simplification down to the four-skill loop below. Don't recreate that tooling unless the user asks for it back.

Validate by hand instead: confirm each `SKILL.md`'s frontmatter (`name` matching its directory, non-empty `description`) is correct, and that every in-prose reference to a **bold-skill-name**, `/slash-command`, or `subagent_type` resolves to something that actually exists in this repo, or is a Claude Code built-in (`code-review`, `security-review`, `simplify`, `loop`, `run`, `skill-creator`, ...).

## Architecture

### Marketplace layout

`.claude-plugin/marketplace.json` lists each plugin's `name` and `source` (a relative path to a directory containing its own `.claude-plugin/plugin.json`). Adding a new plugin means creating `plugins/<name>/.claude-plugin/plugin.json` plus its `skills/`/`agents/` and registering it in the marketplace manifest's `plugins` array.

### Skills (`plugins/<plugin>/skills/*/SKILL.md`)

Each skill is a directory with a `SKILL.md` (YAML frontmatter: `name`, `description`, optionally `disable-model-invocation: true` to make it callable only by explicit `/name` and never auto-triggered) plus optional `references/` subdirectories. `description` is load-bearing: it's what a future Claude session matches against to decide whether to load the skill, so it must state concretely when to use it, not just what it is.

`dan-coding` has four skills, forming one loop:

- **`dan-mode`** (`disable-model-invocation: true`, reachable only via `/dan-mode` or the `dan-agent` agent) is the umbrella skill. Its `SKILL.md` states the loop — `Implementation => Verification => Review` — and, for each phase, spawns a fresh subagent on a specific model to run the corresponding skill below. It does no work itself; it only dispatches.
- **`implementation`** does the actual coding work (feature, bugfix, refactor, optimization, ...). Its `references/` hold `programming-principles.md` plus per-language best-practices files (`typescript-best-practices.md`, `python-best-practices.md`), loaded when the task matches that language.
- **`verification`** checks the implementation against the goal (unit/integration/throw-away e2e tests), invoked once implementation finishes.
- **`review`** runs before asking the user to look at the work (pre-commit/push). It delegates to Claude Code's own `/code-review`, `/security-review`, and `/simplify`, classifies findings by severity (critical/high/medium/low), and applies the unslop and comment-cleanup passes from its `references/`.

Findings at critical/high/medium severity from `review` send the loop back to `implementation`; `low` findings don't block.

`dan-financial` currently has one skill, `investment-buffett`, and no `dan-mode`-style routing layer.

### Agents (`plugins/<plugin>/agents/*.md`)

Standalone subagent definitions, same frontmatter shape as skills (`name`, `description`, optionally `is_background: true`). `dan-agent.md` is the routing target for `/dan-mode`: a thin pointer telling the subagent to read `dan-mode`'s `SKILL.md` in full before acting and run its loop — substituting a generic subagent type for it causes drift because the routing logic lives entirely in that `SKILL.md`, not in the agent file.

### Cross-referencing convention

Skill prose refers to other skills as **bold-skill-name** (matching the skill's `name:` frontmatter) and to slash commands as `/command`. There's no automated checker for this anymore (see "Validating changes" above), so double-check by hand when adding or renaming a skill, or when referencing something outside `dan-coding` (e.g. Claude Code's own `code-review`, `security-review`, `simplify`, which ship with Claude Code itself).
