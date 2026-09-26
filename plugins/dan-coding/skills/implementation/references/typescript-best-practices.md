# TypeScript best practices

Only the rules that change outcomes. The project's own config and conventions win over this file.

## Fit the project

- Use the project's package manager (check the lockfile), test runner, linter, and formatter. Don't add a dependency when a few lines of code will do.
- Keep `strict` on. Don't loosen `tsconfig` to make an error go away.

## Types

- No `any`. Use `unknown` and narrow it.
- Don't silence the compiler with `as`, `!`, `@ts-ignore`, or `@ts-expect-error`. Fix the type. The one exception is a wrong type in a third-party library, with a link to the upstream issue.
- Make illegal states unrepresentable. Use a discriminated union (`{ status: 'ok'; data: T } | { status: 'error'; error: E }`) instead of a bag of optional fields or boolean flags.
- Handle every case of a union with a `switch` that ends in a `never` check, so adding a case breaks the build where it isn't handled.
- Prefer string literal unions or `as const` objects over `enum`.
- Give exported functions explicit return types. Let inference handle locals.
- Use `readonly` and `const` by default. Don't mutate arguments.

## Parse at the boundary

Data from outside the program (HTTP, JSON, env vars, files, user input, `localStorage`) is `unknown` until parsed. Parse it once, where it enters, with the project's schema library (zod, valibot, or similar) into a typed value. Inside the program, trust the types and skip defensive checks.

## Errors and async

- Throw `Error` objects, never strings. Use a custom error class when callers need to tell errors apart.
- Catch only where you can handle the error or add context. Never catch and ignore. Type caught values as `unknown`.
- Await or return every promise. A floating promise loses its errors.
- Run independent async work with `Promise.all`, not `await` in a loop.
- Use `??` and `?.` rather than `||` when `0`, `''`, or `false` are valid values.

## Functions

- More than three parameters, or any boolean parameter, becomes a single options object with named fields.
- Keep side effects at the edges. Business logic should be pure functions that are easy to test.

## Performance

Measure first. The usual real problems are an `await` inside a loop, `array.includes` or `find` inside a loop (use a `Set` or `Map`), re-rendering or recomputing on every call when the input hasn't changed, and loading a whole large file when you could stream it.

## Testing

- Test through the public API, the way callers use the code.
- Prefer real implementations and in-memory fakes over mocks. Mock only what you can't run locally, like third-party network APIs.
- Control time and randomness by injecting them, not by patching globals.

## Checks to run

`tsc --noEmit` (or the project's typecheck script), the linter, and the tests.
