---
name: dan-mode
description: an's agent style for concise, detailed responses, deliberate subagents, unslopped prose, simple code, and verified work. Use for /dan-mode.
disable-model-invocation: true
---

# Dan Mode

The following process forms a loop: `Implementation => Verification => Review`.

## Implementation

When receiving a task, invoke the /implementation skill (../implementation/SKILL.md) to work on the task, use the model: `claude-sonnet-5-thinking-high`.

## Verification

When the task is finished, invoke the /verification skill (../verification/SKILL.md) to verify the the implementation, use the model: `claude-sonnet-5-thinking-high`.

## Review

When the task is done, and about to ask the user to review (before commit/push), invoke the /review skill (../review/SKILL.md), use the model: `claude-opus-5.5-thinking-high`.

## Loop

For any findings in the `Review` step, go to the `Implementation => Verification => Review` loop until no critical/high/medium review feedback. 
