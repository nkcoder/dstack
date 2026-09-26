# Python best practices

Only the rules that change outcomes. The project's own config and conventions win over this file. Assumes Python 3.10 or newer.

## Fit the project

- Use the project's tools. Check `pyproject.toml` for the package manager (uv, poetry, pip), linter and formatter (usually ruff), type checker (mypy or pyright), and test runner (usually pytest).
- Don't add a dependency when a few lines of code will do.

## Types

- Type hint every public function's parameters and return value. Use modern syntax, like `list[str]` and `X | None`.
- Use `dataclass(frozen=True)` or a pydantic model instead of passing dicts around. `data["user"]["id"]` scattered through the code is a bug waiting to happen.
- Use `Enum` or `Literal` for a fixed set of values, not bare strings.
- Use `match` for branching on the shape of data.

## Parse at the boundary

Data from outside the program (HTTP, JSON, env vars, files, user input) gets parsed once, where it enters, into a typed object (pydantic or a dataclass with a constructor that validates). Inside the program, trust the types and skip defensive checks.

## Errors

- Catch specific exceptions. Never write bare `except:` or `except Exception: pass`.
- Catch only where you can handle the error or add context. When re-raising as a different type, use `raise NewError(...) from e`.
- Define your own exception classes for domain errors that callers need to tell apart.

## Common traps

- Never use a mutable default argument like `def f(items=[])`. Use `None` and create the list inside.
- Use `with` for files, locks, and connections.
- Use `pathlib.Path` instead of `os.path` string handling.
- Use `logging`, not `print`, in library and service code.
- Use `zip(..., strict=True)` when the lengths must match.
- Keep imports at the top of the file, absolute, and free of side effects.
- In `async` code, never call blocking I/O like `requests` or `time.sleep`. Use async libraries, or run the blocking call in a thread.

## Performance

Measure first with `cProfile` or `py-spy`. The usual real problems are membership tests on a list inside a loop (use a `set` or `dict`), building strings with `+=` in a loop (use `"".join`), loading a whole large file when you could iterate it, and making I/O calls one at a time that could be batched or run concurrently.

## Testing

- Use pytest with plain `assert`. Use fixtures for setup and `parametrize` for tables of cases.
- Test through the public functions, the way callers use the code.
- Prefer real dependencies (`tmp_path`, a test database of the same engine as production) over mocks. Mock only what you can't run locally, like third-party network APIs.
- Control time and randomness by passing them in, not by patching globals.

## Checks to run

The linter (`ruff check`), the formatter check (`ruff format --check`), the type checker, and the tests.
