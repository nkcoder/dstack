# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

`dstack` is a Claude Code **plugin marketplace** (`.claude-plugin/marketplace.json`) publishing two plugins:

- **`dan-coding`** (`plugins/dan-coding/`) — a coding mode built around one loop of ask, implement and verify, review, fix, and hand back. Everything most work in this repo touches.
- **`dan-financial`** (`plugins/dan-financial/`) — a single skill, Warren Buffett-style investment analysis (`skills/investment-buffett/`).

There is no application to build or run; the "product" is Markdown skill/agent definitions. Users install via Claude Code itself, not a local script:

```
/plugin marketplace add nkcoder/dstack
/plugin install dan-coding@dstack
/plugin install dan-financial@dstack
```

(There is no `install.sh` — that symlink-based installer was removed when the repo converted to a marketplace. Don't recreate it; plugin install/update is Claude Code's job now.)

## Validating changes to skills/agents

There is no automated lint or test suite for the Markdown content — the prior `check-skills.mjs`/`check-plan.mjs` scripts and the `orch`/`watch-pr` Bun toolchain under `plugins/dan-coding/skills/dan-mode/scripts/` were removed in the September 2026 simplification, and the October 2026 pass cut `dan-coding` to the two skills below. Don't recreate that tooling unless the user asks for it back.

Validate by hand instead: confirm each `SKILL.md`'s frontmatter (`name` matching its directory, non-empty `description`) is correct, and that every in-prose reference to a **bold-skill-name**, `/slash-command`, or `subagent_type` resolves to something that actually exists in this repo, or is a Claude Code built-in (`code-review`, `security-review`, `simplify`, `loop`, `run`, `skill-creator`, ...).

## Architecture

### Marketplace layout

`.claude-plugin/marketplace.json` lists each plugin's `name` and `source` (a relative path to a directory containing its own `.claude-plugin/plugin.json`). Adding a new plugin means creating `plugins/<name>/.claude-plugin/plugin.json` plus its `skills/`/`agents/` and registering it in the marketplace manifest's `plugins` array.

### Skills (`plugins/<plugin>/skills/*/SKILL.md`)

Each skill is a directory with a `SKILL.md` (YAML frontmatter: `name`, `description`, optionally `disable-model-invocation: true` to make it callable only by explicit `/name` and never auto-triggered, or `user-invocable: false` for the opposite — hidden from the `/` menu but still auto-triggerable by Claude and callable from another skill's own flow) plus optional `references/` subdirectories. `description` is load-bearing: it's what a future Claude session matches against to decide whether to load the skill, so it must state concretely when to use it, not just what it is.

`dan-coding` has two skills and no agent files.

- **`dan-mode`** (`disable-model-invocation: true`, so only `/dan-mode` starts it) holds the whole loop. It asks only what needs the user, implements and verifies inline on the session model, loads `review`, fixes every critical and high finding plus small, clearly right medium or low ones, rechecks once only after a critical or high, and stops before commit. Review fixes that need new machinery go back to the user first, because in practice each round's bugs came from code the previous round's fix added. Implement and verify stay inline because a fresh subagent would lose the conversation context. They used to be separate skills, and a session skipped loading them, so they now live in dan-mode's own text where there's no load to skip. Don't put `model` on dan-mode. The prompt cache is per model, so a switch makes the new model reread the whole conversation. When a session taught something lasting, its last step runs `/claude-md-management:revise-claude-md` from the separate official `claude-md-management` plugin, or lists suggested lines in the final reply when that plugin isn't installed.
- **`review`** (`user-invocable: false`, `context: fork`, `model: opus`, `effort: high`) runs in a separate reviewer whose only input is the diff plus a short goal and evidence summary that dan-mode passes as `$ARGUMENTS`. Its description says to use it only from dan-mode, so it doesn't fire on its own in other sessions. It runs `/code-review`, runs `/security-review` only when the diff touches a trust boundary, checks the diff against dan-mode's `principles.md` and `unslop.md`, and checks the test evidence. Because the fork starts fresh, it refers to those files through `${CLAUDE_SKILL_DIR}`, never bare relative paths. It grades findings and never edits code.

`dan-mode/references/principles.md` is the one mandatory rules file for code, and `unslop.md` is the one for prose. Both sides read the same two files. They hold only choices the model wouldn't make by default and mistakes review keeps sending back. Don't add general good practice the model already knows or aims for, like textbook design principles or per-area checklists. A rule earns a line only when it changes outcomes. The old per-area references (frontend, API, database, architecture, TypeScript, Python) and the comment checklist were folded into `principles.md` in October 2026 for that reason. Don't bring them back as separate files unless the user asks.

`dan-financial` currently has one skill, `investment-buffett`, and no `dan-mode`-style routing layer.

### Cross-referencing convention

Skill prose refers to other skills as **bold-skill-name** (matching the skill's `name:` frontmatter) and to slash commands as `/command`. There's no automated checker for this anymore (see "Validating changes" above), so double-check by hand when adding or renaming a skill, or when referencing something outside `dan-coding` (e.g. Claude Code's own `code-review`, `security-review`, `simplify`, which ship with Claude Code itself).
