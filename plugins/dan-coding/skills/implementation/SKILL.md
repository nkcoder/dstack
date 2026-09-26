---
name: implementation
description: How to write or change code well, covering features, bug fixes, refactors, and performance work. Use when about to change code, and as the first phase of dan-mode.
user-invocable: false
---

# Implementation

Make the change correct first, then simple, then fast enough. Fast enough means measured, not guessed.

## Before you write code

1. **Know the goal.** Restate what done looks like as something you can check. "Add validation" becomes "invalid input X is rejected with error Y, valid input still works."
2. **Read the code you'll touch**, plus its callers and its tests. Match how the project already does things, including its style, libraries, error handling, and test layout.
3. **Name the data shape first.** Decide what the core types and structures are before writing logic, using the domain's own words. A good shape removes branches later. See "Model the domain" in the principles.
4. **Decide where the side effects go.** Keep the logic in pure functions and push I/O to the edges. See "Prefer a functional style" in the principles.
5. **Pick the smallest change that fully solves it.** If there's a simpler approach than the one asked for, say so.
6. **If the task is about architecture, read `references/architecture.md` first.** That means it adds a service, module, or datastore, changes how components talk to each other, picks a technology that's hard to swap, changes a public API, event, or schema, or has explicit scale, reliability, or security goals. Skip it for everything else.

## Bugs, find the root cause

- Reproduce the bug first. If you can't reproduce it, you can't prove you fixed it.
- Keep asking why until you reach the cause. Fix it there.
- Don't add a guard that hides the symptom. A null check that stops a crash without explaining why the value was null is a symptom fix.
- Look for the same mistake elsewhere and fix every instance.
- When stuck, add logging or read the real error. Don't guess.
- If something breaks only after a restart, suspect stale state (caches, config, lock files) before code.

## Refactoring, change structure without changing behavior

1. Make sure tests cover the behavior you're about to restructure. If they don't, first write tests that pin down what the code does today, even the odd parts.
2. Take one small, named step at a time, like rename, extract function, inline, move, or replace a repeated `switch` with a lookup table.
3. Run the tests after every step. If they fail, undo the step instead of debugging forward.
4. Use tool-driven edits (LSP rename, a codemod) over hand edits across files, so no call site is missed.
5. Never mix a refactor with a behavior change in the same step. When a task needs both, refactor first, verify, then change behavior.

## Performance work

Measure before and after, on the same input. Keep a change only if the numbers say it helped. Common real wins are removing repeated work in a loop, replacing a list scan with a set or map lookup, batching I/O, and running independent async work in parallel.

## While you write

- Follow `references/programming-principles.md`. Read it at the start of any coding task.
- For TypeScript (`*.ts`, `*.tsx`), also follow `references/typescript-best-practices.md`.
- For Python (`*.py`), also follow `references/python-best-practices.md`.
- When the task touches UI code (components, pages, styles, client state), also follow `references/frontend.md`.
- When the task adds or changes an endpoint, or client code that calls one, also follow `references/api.md`.
- When the task touches queries, schema, migrations, or transactions, also follow `references/database.md`.
- Every changed line should trace back to the task. Don't clean up unrelated code. Mention it instead.
- Remove anything your change made unused.
- Write comments clean as you go. Keep one only for a non-obvious why the code can't show.
- Don't add error handling, config options, or abstractions for cases that can't happen yet.

## Done when

The change does what the goal says, and you know exactly how you'll prove it in verification. Docs that describe the changed behavior are updated too, like the README, API docs, changelog, and `.env.example`.
