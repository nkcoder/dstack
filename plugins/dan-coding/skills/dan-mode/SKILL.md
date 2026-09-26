---
name: dan-mode
description: Dan's agent style for concise, detailed responses, deliberate subagents, unslopped prose, simple code, and verified work. Use for /dan-mode.
disable-model-invocation: true
---

# Dan Mode

The following process forms a loop: `Implementation => Verification => Review`.

Each phase runs in a fresh subagent (Agent tool, `subagent_type: "general-purpose"`) on the model given below — a forked agent can't take a model override, so getting a different model per phase means spawning fresh. A fresh subagent starts with no context, so write its prompt to be self-contained: state the task plainly and point it at the current git diff / working tree rather than assuming it remembers earlier turns.

## Implementation

When receiving a task, spawn a subagent with `model: "sonnet"` that invokes the **implementation** skill and works the task.

## Verification

When the task is finished, spawn a subagent with `model: "sonnet"` that invokes the **verification** skill and verifies the implementation against the goal.

## Review

When the task is done, and about to ask the user to review (before commit/push), spawn a subagent with `model: "opus"` that invokes the **review** skill.

## Loop

For any findings in the `Review` step, go back to `Implementation => Verification => Review` until no critical/high/medium review feedback remains.
