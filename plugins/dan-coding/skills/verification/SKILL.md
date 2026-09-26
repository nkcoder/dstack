---
name: verification
description: Prove a code change actually works by running it, not by reading it. Covers regression tests for bugs, behavior tests, and throwaway end-to-end checks. Use after implementing a change and before calling it done, and as the second phase of dan-mode.
---

# Verification

Prove the change does what was asked, by checking the real thing. "It compiles" and "it looks right" are not proof.

## Steps

1. **Restate what done looks like.** Take the goal from implementation and list the behaviors that must be true, including the edge cases that matter.
2. **Run the project's existing checks.** Typecheck, lint, and the tests near the code you changed. Find the commands in `package.json`, `Makefile`, `pyproject.toml`, the README, or `CLAUDE.md`.
3. **For a bug, show it failing first.** Write a test that reproduces the bug. Run it before the fix and confirm it fails for the right reason. Then confirm it passes after the fix. If a test would be expensive or brittle, use the closest cheap check instead, like a script or a command that reproduces it. Say why you skipped the test.
4. **For new or changed behavior, add tests.** Cover the behaviors from step 1. Follow the rules in the next section.
5. **Exercise the real path once.** Run the thing the way a user would. Call the endpoint, run the CLI command, load the page, or run the script on real input. For a UI or an app, use the **run** skill or drive a browser. Put throwaway end-to-end scripts in the scratchpad, and delete them after unless they're worth keeping as tests.
6. **Follow the data all the way through.** Check the output, the saved file, the database row, or the log line, not just the exit code.
7. **Run the full test suite once** before handing off to review.

## Test behavior, not implementation

A test calls the code the way its users do and asserts what they would observe, against a literal expected value.

Before keeping a test, ask whether it would still pass if every function it imports returned `undefined`. If yes, it can't catch a bug. Rewrite the assertion or delete the test.

These shapes fail that check.

- **Weak assertions.** Only `toBeDefined`, `toBeTruthy`, `not.toThrow`, or `assert result`.
- **Mock-only assertions.** Only checking that a mock was called. Assert what it was called with, or the state after the call.
- **Self-referential.** The expected value comes from the code under test, like `expect(f(a)).toBe(f(a))`.
- **Constant pins.** Restating a config value or constant, like `expect(MAX_RETRIES).toBe(3)`. Test the code that uses the constant instead.
- **Fixture checks fixture.** The test asserts on data it built itself, and the code under test never runs.

A good test looks like `expect(slugify('Hello, World!')).toBe('hello-world')`.

Never change a test just to match a wrong implementation, and never weaken an assertion to get green. If a test fails, the code is wrong until you've proven the test is.

## When a check fails

Treat it as a bug and find the root cause. Go back to implementation and fix it there, then run this skill again. Don't retry flaky tests until they pass. A flaky test is a bug too, either in the code or in the test.

## Report

- The commands you ran and what they showed.
- For a bug, the failing-before and passing-after evidence.
- Anything you could not verify, and why. Don't claim what you didn't check.
