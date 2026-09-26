---
name: implementation
description: How to write or change code well, covering features, bug fixes, refactors, and performance work. Use when about to change code, and as the first phase of dan-mode.
---

# Implementation

Make the change correct first, then simple, then fast enough. Fast enough means measured, not guessed.

## Before you write code

1. **Know the goal.** Restate what done looks like as something you can check. "Add validation" becomes "invalid input X is rejected with error Y, valid input still works."
2. **Read the code you'll touch**, plus its callers and its tests. Match how the project already does things, including its style, libraries, error handling, and test layout.
3. **Name the data shape first.** Decide what the core types and structures are before writing logic. A good shape removes branches later.
4. **Pick the smallest change that fully solves it.** If there's a simpler approach than the one asked for, say so.

## Bugs, find the root cause

- Reproduce the bug first. If you can't reproduce it, you can't prove you fixed it.
- Keep asking why until you reach the cause. Fix it there.
- Don't add a guard that hides the symptom. A null check that stops a crash without explaining why the value was null is a symptom fix.
- Look for the same mistake elsewhere and fix every instance.
- When stuck, add logging or read the real error. Don't guess.
- If something breaks only after a restart, suspect stale state (caches, config, lock files) before code.

## Performance work

Measure before and after, on the same input. Keep a change only if the numbers say it helped. Common real wins are removing repeated work in a loop, replacing a list scan with a set or map lookup, batching I/O, and running independent async work in parallel.

## While you write

- Follow `references/programming-principles.md`. Read it at the start of any coding task.
- For TypeScript (`*.ts`, `*.tsx`), also follow `references/typescript-best-practices.md`.
- For Python (`*.py`), also follow `references/python-best-practices.md`.
- Every changed line should trace back to the task. Don't clean up unrelated code. Mention it instead.
- Remove anything your change made unused.
- Write comments clean as you go. Keep one only for a non-obvious why the code can't show.
- Don't add error handling, config options, or abstractions for cases that can't happen yet.

## Done when

The change does what the goal says, and you know exactly how you'll prove it in verification.
