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

Each skill is a directory with a `SKILL.md` (YAML frontmatter: `name`, `description`, optionally `disable-model-invocation: true` to make it callable only by explicit `/name` and never auto-triggered, or `user-invocable: false` for the opposite — hidden from the `/` menu but still auto-triggerable by Claude and callable from another skill's own flow) plus optional `references/` subdirectories. `description` is load-bearing: it's what a future Claude session matches against to decide whether to load the skill, so it must state concretely when to use it, not just what it is.

`dan-coding` has four skills that form one loop, and no agent files. Implement and verify run inline in the user's session, on the session model (the user's default is Sonnet 5 at high effort), because a fresh subagent would lose the conversation context. Review is the one exception. Its frontmatter sets `context: fork`, `model: opus`, `effort: high`, so it runs in a separate Opus reviewer whose only real input is the diff plus a short goal summary that `dan-mode` passes as `$ARGUMENTS`. Because the fork starts fresh, review refers to other files through `${CLAUDE_SKILL_DIR}`, never bare relative paths. Don't put `model` on `implementation` or `verification`. The prompt cache is per model, so an inline switch makes the new model reread the whole conversation, and those skills can also trigger outside dan-mode and silently change the user's model.

- **`dan-mode`** (`disable-model-invocation: true`, so only `/dan-mode` starts it) runs `implementation`, then `verification`, then `review`, and fixes critical, high, and medium findings. Only a critical or high finding triggers a recheck, which covers just the fixes. There are two review rounds at most, and a hard stop after that. Review fixes that need new machinery go back to the user first, because in practice each round's bugs came from code the previous round's fix added. The other three carry `user-invocable: false` so they don't show up in the `/` menu as standalone commands, but they still auto-trigger by description and stay callable from within dan-mode's own flow. It stops before commit. When a session taught something lasting, its last step runs `/claude-md-management:revise-claude-md` from the separate official `claude-md-management` plugin, and lists suggested lines in the final reply instead when that plugin isn't installed. It also owns the reply and comment rules and when to ask the user versus decide. Its `references/unslop.md` is the full list of prose patterns to avoid, and `review` points at it too.
- **`implementation`** covers how to write the change, including root-causing bugs. Its `references/` hold `programming-principles.md`, always read, plus short `typescript-best-practices.md` and `python-best-practices.md`, read only for that language, `architecture.md`, read only when the task is architectural (new service, module, or datastore, component boundaries, hard-to-swap technology, public contracts, explicit quality goals), `frontend.md`, read only when the task touches UI code, `api.md`, read only when the task adds or changes an endpoint or the client code calling it, and `database.md`, read only when the task touches queries, schema, migrations, or transactions. Keep the language files short and limited to rules that change outcomes. They load on every coding task.
- **`verification`** proves the change works by running it. It covers failing-first regression tests for bugs, the test-behavior-not-implementation rule, breaking the code to confirm each new test can fail, and exercising the real path once. `dan-mode` gates review on that evidence, and `review` grades missing proof as high, because in practice skipping this phase was what sent high findings back from review.
- **`review`** runs Claude Code's own `/code-review` at high effort, runs `/security-review` only when the diff touches a trust boundary, and checks design (against `implementation`'s principles file), UI, API, and database changes (against the matching `implementation` reference), comments (`references/no-comment.md`), and prose. It confirms and grades each finding and never edits code. Fixes happen back in `implementation`.

Area references that load only on demand (`frontend.md`, `api.md`, `database.md`) end with their own "Verify" and "Review" sections. `verification` and `review` hold only one pointer line to them, so a task that doesn't touch that area never loads its checks. Follow the same pattern when adding a new area reference, and don't copy area checklists back into `verification` or `review`, since those load on every task.

`dan-financial` currently has one skill, `investment-buffett`, and no `dan-mode`-style routing layer.

### Cross-referencing convention

Skill prose refers to other skills as **bold-skill-name** (matching the skill's `name:` frontmatter) and to slash commands as `/command`. There's no automated checker for this anymore (see "Validating changes" above), so double-check by hand when adding or renaming a skill, or when referencing something outside `dan-coding` (e.g. Claude Code's own `code-review`, `security-review`, `simplify`, which ship with Claude Code itself).
