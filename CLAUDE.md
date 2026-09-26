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

`dan-coding` has four skills that form one loop. There are no agents. The loop runs inline in the user's session on purpose, because a fresh subagent per phase loses the conversation context and costs far more tokens. Don't reintroduce per-phase subagents without the user asking.

- **`dan-mode`** (`disable-model-invocation: true`, so only `/dan-mode` starts it) runs `implementation`, then `verification`, then `review`, and repeats until no critical, high, or medium findings remain, capped at three review rounds. It stops before commit. It also owns the reply and comment rules and when to ask the user versus decide. Its `references/unslop.md` is the full list of prose patterns to avoid, and `review` points at it too.
- **`implementation`** covers how to write the change, including root-causing bugs. Its `references/` hold `programming-principles.md`, always read, plus short `typescript-best-practices.md` and `python-best-practices.md`, read only for that language, and `architecture.md`, read only when the task is architectural (new service, module, or datastore, component boundaries, hard-to-swap technology, public contracts, explicit quality goals). Keep the language files short and limited to rules that change outcomes. They load on every coding task.
- **`verification`** proves the change works by running it. It covers failing-first regression tests for bugs, the test-behavior-not-implementation rule, and exercising the real path once.
- **`review`** runs Claude Code's own `/code-review` at high effort, runs `/security-review` only when the diff touches a trust boundary, and checks design (against `implementation`'s principles file), comments (`references/no-comment.md`), and prose. It confirms and grades each finding and never edits code. Fixes happen back in `implementation`.

`dan-financial` currently has one skill, `investment-buffett`, and no `dan-mode`-style routing layer.

### Cross-referencing convention

Skill prose refers to other skills as **bold-skill-name** (matching the skill's `name:` frontmatter) and to slash commands as `/command`. There's no automated checker for this anymore (see "Validating changes" above), so double-check by hand when adding or renaming a skill, or when referencing something outside `dan-coding` (e.g. Claude Code's own `code-review`, `security-review`, `simplify`, which ship with Claude Code itself).
