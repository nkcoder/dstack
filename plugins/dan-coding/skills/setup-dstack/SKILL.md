---
name: setup-dstack
description: "Configure which model each dstack role runs on. Detects the model slugs this session can actually spawn, then writes `~/.claude/rules/dstack-models.md`, the file the routed skills read. Use for /setup-dstack, \"configure dstack models\", or changing dstack's model choices."
disable-model-invocation: true
---

# Setup dstack

Write `~/.claude/rules/dstack-models.md`, the per-role model register that `how`, `why`, `arena`, `swarm`, `architect`, `interrogate`, `reflect`, and the dan-mode playbooks read before they spawn anything.

Nothing loads this file automatically. Each reader opens it by path and falls back to its own default when a role has no line, so a partial file is valid and an absent file is valid. That is why the filename and the role labels are a contract, not a preference. A label the readers do not look for is dead config.

## Steps

### 1. Detect available models

Enumerate the model slugs you can pass to an `Agent` call in this session. That is the dependable source. Never write a real slug you have not confirmed is spawnable, because an unresolvable slug sends the reader into its fallback path mid-task. If you cannot detect any, ask the user to paste the slugs they have access to. The aliases `inherit-parent` and `auto` are always valid and are never detected slugs.

### 2. Load current state

Read `~/.claude/rules/dstack-models.md` if it exists and treat its values as the current choices. Otherwise start from the defaults in the Roles table below. Those defaults are owned by the reading skills, so when one disagrees with this table, the reading skill wins and this table needs a fix.

### 3. Map and confirm

Show every role with its current value, flagging any real slug missing from the detected set. Ask whether to accept as-is or change specific roles, offering the detected slugs plus `inherit-parent` and `auto`. Prefer `AskUserQuestion` over free text.

A panel role takes a list, and one subagent runs per entry, so the list length sets the fan-out. Alias entries count toward it. `arena cross-judge pool` is the exception. Arena picks one entry from that list, preferring a family different from the parent's, so its length sets the choice set rather than a fan-out.

### 4. Validate

Every real slug must be in the detected set. `inherit-parent` and `auto` always pass. A panel role needs at least one entry. If a chosen slug is unavailable, stop and ask again rather than writing it.

### 5. Write the register

`mkdir -p ~/.claude/rules` first, since the directory does not exist on a fresh machine. Overwrite the whole file so re-runs converge on the same end state (**principle-make-operations-idempotent**).

One role per line as `label: value`. A comma separates entries of a panel list, so never put two roles on one line. Omit a role to leave it on the reading skill's default.

```
# dstack per-role model register. Read on demand by the routed skills.
# One role per line. Commas separate panel entries, never roles.
# `inherit-parent` or `auto` runs that role on the parent chat model (omit the Agent `model`).
# Delete a line to fall back to the reading skill's own default.
feature: claude-sonnet-5-thinking-high
refactoring: claude-sonnet-5-thinking-high
bug-fix: claude-opus-5-thinking-high
perf-issue: claude-opus-5-thinking-high
hillclimb: claude-opus-5-thinking-high
how-explorer: claude-sonnet-5-thinking-high
how-explainer: claude-opus-5-thinking-high
why-investigators: claude-sonnet-5-thinking-high
why-synthesizer: claude-opus-5-thinking-high
reflect-judgment: claude-opus-5-thinking-high
reflect-tooling: claude-sonnet-5-thinking-high
reflect-divergent: claude-opus-5-thinking-high
swarm workers: claude-sonnet-5-thinking-high
arena runners: claude-sonnet-5-thinking-high
arena cross-judge pool: claude-opus-5-thinking-high
architect runners: claude-opus-5-thinking-high, claude-sonnet-5-thinking-high, claude-haiku-4-5
interrogate reviewers: claude-opus-5-thinking-high, claude-sonnet-5-thinking-high
```

### 6. Prove a reader can find its role

Read the written file back and confirm every label you wrote appears exactly as its reader looks it up (**principle-prove-it-works**). A register the readers cannot match is worse than no register, because each reader silently takes its default and the user believes the configuration applied.

### 7. Confirm

Tell the user the path you wrote, which roles changed, and which stayed on their reading skill's default. Say that the readers pick it up on their next run, not that it applies globally, because nothing auto-loads it.

## Roles

Each label is spelled the way its reader looks it up. Hyphenated labels come from skills that name "your configured `<role>` model" in prose. Spaced labels come from skills that quote the label directly.

| Label | Read by | Default |
|---|---|---|
| `feature` | `skills/dan-mode/playbooks/feature.md` | `claude-sonnet-5-thinking-high` |
| `refactoring` | `skills/dan-mode/playbooks/refactoring.md` | `claude-sonnet-5-thinking-high` |
| `bug-fix` | `skills/dan-mode/playbooks/bug-fix.md` | `claude-opus-5-thinking-high` |
| `perf-issue` | `skills/dan-mode/playbooks/perf-issue.md` | `claude-opus-5-thinking-high` |
| `hillclimb` | `skills/dan-mode/playbooks/hillclimb.md` | `claude-opus-5-thinking-high` |
| `how-explorer` | **how** | `claude-sonnet-5-thinking-high` |
| `how-explainer` | **how** | `claude-opus-5-thinking-high` |
| `why-investigators` | **why** | `claude-sonnet-5-thinking-high` |
| `why-synthesizer` | **why** | `claude-opus-5-thinking-high` |
| `reflect-judgment` | **reflect**, for the judgment lens and the synthesizer | `claude-opus-5-thinking-high` |
| `reflect-tooling` | **reflect** | `claude-sonnet-5-thinking-high` |
| `reflect-divergent` | **reflect** | `claude-opus-5-thinking-high` |
| `swarm workers` | **swarm** | `claude-sonnet-5-thinking-high` |
| `arena runners` | **arena**, panel | one each on `claude-sonnet-5-thinking-high` |
| `arena cross-judge pool` | **arena**, one entry chosen | `claude-opus-5-thinking-high` |
| `architect runners` | **architect**, panel | `claude-opus-5-thinking-high`, `claude-sonnet-5-thinking-high` |
| `interrogate reviewers` | **interrogate**, panel sized by entry count | `claude-opus-5-thinking-high`, `claude-sonnet-5-thinking-high`|

Claude Code spawns Claude-family models only, so panel diversity comes from spreading entries across model tiers rather than across vendors.

A role that exists in a skill but not in this table is a gap in this skill. Add the row in the same PR that needs it, and keep the label identical to what the reader greps for.
