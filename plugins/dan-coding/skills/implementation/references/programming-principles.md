# Programming principles

How to shape code in any language or paradigm. Treat these as lenses, not a checklist, and don't cite them in code comments. When a principle makes the code worse in a specific case, the specific case wins. For trivial tasks, use judgment.

Each section names its main source. The sources are Clean Code (Martin), The Pragmatic Programmer (Hunt and Thomas), Refactoring (Fowler), Domain-Driven Design (Evans), A Philosophy of Software Design (Ousterhout), and Tidy First? (Beck).

## 1. Think before coding

- State your assumptions.
- If you're unsure about a fact (how the code behaves, what a call returns), find out by reading or running it. Don't ask.
- If you're unsure about intent, scope, or a product choice, ask.
- If there are several readings and the choice is small, pick one and say which. If it's big, ask.
- If a simpler approach exists, say so. Push back when warranted.

## 2. Build only what's needed

- **Readable beats clever** (Clean Code). A trick that saves three lines but takes a minute to understand is a bad trade.
- **YAGNI.** No features, hooks, config options, or layers for a need that doesn't exist yet.
- **Make it work, make it right, then make it fast.** Optimize only what measurement shows is slow.
- No error handling or flexibility for cases that can't happen.
- If you wrote 200 lines and it could be 50, rewrite it. Ask whether a senior engineer would call it overcomplicated.

## 3. Touch only what you must

- Every changed line should trace back to the task.
- Don't improve adjacent code, comments, or formatting. Match the existing style, even if you'd do it differently.
- Remove what your change made unused. Mention other dead code, but don't delete it unless asked.

## 4. Name things for what they mean (Clean Code, DDD)

- A name says what something is or does, not how it's built. `unpaidInvoices`, not `filteredList` or `data2`.
- Use the words the users and the business use. If the product calls it a booking, the code doesn't call it a reservation.
- Use one word per idea across the codebase. Don't mix `fetch`, `get`, and `load` for the same thing.
- Booleans read as yes-or-no questions, like `isExpired` or `hasAccess`.
- Match length to reach. A short name is fine in a three-line function. Anything exported gets a full, clear name.
- If a name needs a comment to explain it, change the name. If you can't name something, you don't yet know what it does, and that's a design problem.

## 5. Prefer a functional style, in any language

Most modern languages have borrowed these ideas because they mean fewer side effects, and code with fewer side effects is easier to test and to reason about. Use them whether or not the language is "functional".

- **Immutable by default.** Build new values instead of changing existing ones. Use `const`, `readonly`, `final`, frozen dataclasses, and similar. Mutation is fine for a local variable no one else can see, or on a hot path that measurement proves needs it.
- **Pure functions for logic.** The output depends only on the inputs, and the function changes nothing outside itself. No hidden reads of the clock, environment, globals, or database. Pass time, randomness, and config in as arguments.
- **Functional core, imperative shell.** Keep side effects (database, network, files, logging, the clock) at the edges. The core takes plain data and returns plain data or a decision. The shell reads, calls the core, and writes the result. The core then needs no mocks to test.
- **Transform data in pipelines.** Use `map`, `filter`, `reduce`, and comprehensions when they read more clearly than a loop that mutates an accumulator. When a plain loop is clearer, use the loop.
- **Pass behavior as functions.** A function parameter often replaces a class hierarchy or a one-method strategy class.
- **Expected failures are values, where the language supports it.** For "not found" or "invalid input", return a result the caller must handle, like a union type in TypeScript or `Result` in Rust. Where exceptions are the idiom, as in Python, use them. Either way, don't hide failures by returning `null` or a default.
- **Handle every input the type allows.** A function that crashes on some valid input of its declared type is a trap.
- **Don't force FP ceremony on a language that fights it.** No hand-rolled monads, point-free chains, or deep recursion in Python. The goal is fewer side effects, not FP vocabulary.

## 6. Model the domain (DDD)

- Put business rules in the domain model, not in handlers, controllers, SQL, or UI code.
- Wrap meaningful primitives in small types, like `Money`, `EmailAddress`, or `OrderId`, instead of bare `number` and `string`. Validate once when the value is created, then trust it everywhere.
- Make invalid states impossible to build. A constructor that rejects bad input beats validation scattered across callers. Model states as a union (`Draft | Submitted | Paid`) instead of a status string plus optional fields.
- Keep the domain free of infrastructure. Domain code doesn't import the database, HTTP, or framework code. Adapters at the edge translate. This is the functional core from section 5, seen from the domain side.
- Objects that must stay consistent together form one unit (an aggregate). Change them through one entry point and save them in one transaction.
- In a large system the same word can mean different things in different areas, like "customer" in billing versus support. Give each area its own model and translate where they meet (bounded contexts). Don't build one giant shared model.
- Scale this to the problem. A simple CRUD screen doesn't need aggregates. Use the heavy parts only where the business rules are complex.

## 7. Shape modules around responsibility

- **Single responsibility.** A module has one reason to change. If describing what a class does needs "and", it's two classes.
- **Separation of concerns.** Parsing, persistence, formatting, and business rules each live in one place, not smeared across layers.
- **Orthogonality** (Pragmatic Programmer). Changing one thing shouldn't force changes in unrelated things. Pick a likely change, like swapping the database, adding a field, or renaming a label, and count the modules you'd touch. Anything beyond the module that owns that concern is hidden coupling.
- **High cohesion, low coupling.** Things that change together live together. Things that don't depend on each other don't know about each other.
- **Open for extension.** Prefer designs where new behavior is added (a new case, a new implementation) over edits scattered through working code.

## 8. Prefer depth over fragmentation (Ousterhout)

Clean Code is right that a function should do one thing. Its push for tiny functions is not. Chains of four-line functions force the reader to open five places to follow one flow. The best modules are deep, with a simple interface hiding real work.

- Split a function when a piece has its own nameable job or is reused, not to hit a line count.
- A function is too small if you have to open its siblings to understand what it does.
- A function is too big if it mixes unrelated jobs or you can't say what it does in one sentence.

## 9. Mind your boundaries

- **Law of Demeter.** Call methods on things you own or were handed. Don't reach through `a.getB().getC().doThing()`. Chains like that leak structure and couple you to it.
- **Composition over inheritance.** Use inheritance only for a real "is a" relationship whose shared behavior must stay in sync. Otherwise compose.
- **Command-query separation.** A function either does something or answers something, not both. Functions that mutate and return are a common source of surprise bugs.
- **Crash early** (Pragmatic Programmer). When something impossible happens, fail right there with a clear error. Returning a default lets bad data travel and makes the real bug hard to find. Parse outside input at the boundary so the inside never sees it raw.

## 10. Duplicate deliberately (Pragmatic Programmer)

- **DRY is about knowledge, not text.** Each fact or rule is defined in one place. Two pieces of code that look alike but represent different rules are not duplication. Merging them creates a shared abstraction that fights you when they diverge. Prefer duplication over the wrong abstraction, and wait for a third occurrence or clear proof it's the same rule.
- **Optimize for deletion.** Favor structures that let you delete or replace a piece cleanly over reuse you don't need yet.

## 11. Keep it maintainable (Refactoring, Tidy First)

- Write for the person or agent who edits this next, under time pressure, without your context.
- Leave code you touched a little cleaner than you found it, within the limits of section 3.
- When a change is hard to make, first refactor so it's easy, then make the easy change. Keep the two as separate steps.
- Refactor when you see these signs in code you're changing. The same rule appears in several places. One small change needs edits in many files. The same `switch` on a type repeats in several places. A function uses another object's data more than its own. Bare primitives stand in for domain ideas (see section 6). A parameter list keeps growing.

## 12. Define success, then loop until verified

Turn each task into a goal you can check.

- "Add validation" becomes "tests for invalid inputs pass, and valid inputs still work."
- "Fix the bug" becomes "a test that reproduces it fails before the fix and passes after."
- "Refactor X" becomes "the same tests pass before and after, unchanged."

For multi-step work, write a short plan where each step ends in a check.
