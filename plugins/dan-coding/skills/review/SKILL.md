---
name: review
description: "Review the current diff, or a PR number/branch/path target for correctness, reuse, simplification, performance, efficiency, cleanup, best practices etc."
disable-model-invocation: false
---

# Review

## Code Review

Run a comprehensive code review using the the following Claude skills:
- /code-review with high effort
- /security-review
- /simplify

Classify the findings as the following so that a following agent can use it as inputs directly for fixes:
- critical: must fix
- high: must fix
- medium: should fix
- low: nice to fix

## unslop

Use the `references/unslop.md` to edit text to remove AI patterns.

## Remove comments

Use the `references/no-comment.md` to simplify/remove comments.