# Principles

Every change follows these. You write to them, and the reviewer checks against them. They hold only the choices a good engineer might make differently and the mistakes that keep coming back from review. General good practice isn't repeated here. Where the project's own conventions differ, the project wins.

## Scope

- A pull request stays under about 400 changed lines, not counting tests, lockfiles, and generated code. When a task will go over, plan the split before starting.
- Every changed line traces to the task. Mention unrelated problems instead of fixing them.
- Keep a refactor and a behavior change in separate steps.

## Design

- Business rules live in pure functions that take plain data and return plain data. Handlers, jobs, SQL, and UI call them and do the I/O. Pass in time, randomness, and config.
- Make invalid states impossible to build. A union of states (`Draft | Submitted | Paid`) beats a status string plus optional fields. Parse outside data into these types where it enters, then trust them.
- Expected failures like "not found" are values the caller has to handle, like a result union in TypeScript. In Python, raise a specific exception. Never hide a failure behind `null` or a default.
- Prefer duplication over the wrong abstraction. Merge on the third copy, or when you can show it's the same rule.
- Prefer a few deep functions over chains of tiny ones. Split only when a piece has its own nameable job.
- For a choice that's hard to reverse, like a data model, a public API, a storage engine, or a new service, compare at least two options with their costs and record the decision. Default to one deployable and to technology the project already runs.

## What can run twice or stop halfway

These are the bugs review sends back most often. Before writing the code, decide how it handles retries, replays, timeouts, concurrent runs, and a crash between two writes.

- A write that can repeat, from a retried request, a double click, or a redelivered message, carries an idempotency key.
- Replace check-then-write with a guarantee from the database, like a unique constraint, `ON CONFLICT`, or `UPDATE ... SET stock = stock - 1 WHERE stock > 0`.
- Don't make a network call inside a transaction. A write that must also send a message goes through an outbox table.
- Under a platform with its own rules, like a job queue, a durable workflow, or a serverless route, look up its retry, time, and transaction limits in its docs or type files. Don't work from memory.

## Changes others depend on

- Changes to an API, event, or schema are additive. Old clients keep working against the new server, and a migration works with both the old and the new code. Rename or drop in steps across releases.
- On a large table, use the migration form that doesn't lock it, like `CREATE INDEX CONCURRENTLY`.

## Security

- Authorize each record on the server, not only each route.
- Never build SQL, shell commands, HTML, or file paths by joining strings with input.
- Keep secrets out of code, logs, error messages, and anything shipped to the browser, including `NEXT_PUBLIC_*` and `VITE_*` variables. Auth tokens go in `httpOnly` cookies, not `localStorage`.

## UI

- Every data view has loading, empty, and error states. A failed request never loses what the user typed.
- Meet WCAG 2.2 AA. Use real elements, not `<div onClick>`. Everything works by keyboard, and focus moves into dialogs and to the new content after a route change.
- Build for phone width first.

## Tests

- A bug fix comes with a test that failed before the fix, for the bug's reason.
- Test the function production runs, not a copy, a wrapper, or a mock of it.
- Assert what a caller would see, against a literal value. A test that would still pass with every import returning `undefined` proves nothing.
- Use real dependencies where you can, like a test database on the production engine. Mock only what can't run locally.
- Never weaken an assertion or edit a test to fit wrong code. A flaky test is a bug, so don't rerun it until it passes.

## Language

- **TypeScript.** Don't silence the compiler with `any`, `as`, `!`, or `@ts-ignore`. Use string literal unions, not `enum`. End a `switch` on a union with a `never` check. A boolean parameter, or more than three parameters, becomes an options object.
- **Python.** Use frozen dataclasses or pydantic models instead of passing dicts around. Re-raise with `from e`.

## Comments

- Keep a comment only for a why the code can't show, like a vendor bug, a platform quirk, or a rejected approach someone would otherwise retry. Link the issue when there is one.
- License headers, doc comments on a public API, and tool directives stay. Everything else goes, including comments that narrate the code, commented-out code, step labels, changelog notes, and TODOs with no issue.
- A lint or type suppression that hides a correctness rule is a bug.

## Writing

All prose follows `unslop.md`. That covers replies, comments, docs, commit messages, and text users see.
