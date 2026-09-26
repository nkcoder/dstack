# Programming Principles

Guidelines for how to approach a coding task and how to shape the resulting code — independent of language or paradigm. These are lenses to look at a task through, not a checklist to satisfy mechanically or cite in comments. Apply judgment: a principle that makes code worse in a specific case should lose to the specific case.

For trivial tasks, use judgment.

## 1. Think before coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly.
- If you're unsure about a fact (how the code behaves, what a call returns), find out by reading or running it. Don't ask.
- If you're unsure about intent, scope, or a product choice, ask.
- If multiple interpretations exist and the choice is small, pick one and say which. If it's big, ask.
- If a simpler approach exists, say so. Push back when warranted.

## 2. Build only what's needed

**Minimum code that solves the problem. Nothing speculative.**

- **KISS** — the version that's easiest to read and trace usually beats the "clever" one.
- **YAGNI** — no features, hooks, config options, or layers for a need that doesn't exist yet.
- **Avoid premature optimization** — make it work, make it right, then make it fast — and only optimize what profiling actually shows is slow.
- No "flexibility" or error handling for scenarios that can't happen.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Touch only what you must

**Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that *your* changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: every changed line should trace directly to the request.

## 4. Shape modules around responsibility, not convenience

- **Single Responsibility** — a module should have one reason to change. If you find yourself describing what a class does with "and," it's two classes.
- **Separation of Concerns** — a concern (parsing, persistence, formatting, business rules) should live in one place, not be smeared across layers.
- **Maximize cohesion, minimize coupling** — things that change together should live together; things that don't depend on each other shouldn't need to know about each other.
- **Open/Closed** — prefer designs where new behavior is *added* (a new case, a new implementation of an interface) rather than requiring edits scattered through existing, working code.

## 5. Prefer depth over fragmentation

Clean Code popularized extremely small functions ("if it's more than 4 lines, extract it"); Ousterhout's *A Philosophy of Software Design* pushed back hard, arguing that the best modules are **deep** — a simple interface hiding real functionality — and that shallow modules (tiny interface-to-logic ratio) force readers to jump across many pieces to see one flow. Both are reacting to real failure modes. The synthesis that holds up:

- Split a function when it has a name-able sub-responsibility or is reused, not to hit a line-count target.
- A function/module is too small if understanding it requires opening three siblings to reconstruct the one thing it does.
- A function/module is too big if it mixes unrelated responsibilities (see §4) or you can't name what it does in one sentence.
- Comments explaining *why* (a non-obvious constraint, a rejected alternative) are fine and often better than decomposing further just to avoid writing one — contra Clean Code's "comments are a failure," which hasn't aged well.

## 6. Mind your boundaries

- **Law of Demeter** ("don't talk to strangers") — call methods on things you own or were handed directly; don't reach through `a.getB().getC().doThing()`. Chains like that leak internal structure and tighten coupling you didn't mean to create.
- **Composition over inheritance** — reach for inheritance only for a genuine "is-a" relationship with shared behavior that should stay in sync; reach for composition ("has-a"/"uses-a") otherwise. Inheritance is the tighter coupling of the two — it binds you to a base class's internals across versions.
- **Command-Query Separation** — a method either does something (command) or answers something (query), not both. `list.pop()`-style methods that mutate *and* return are the classic violation and a common source of surprising bugs.

## 7. Duplicate deliberately

- **DRY** still holds for *knowledge* — one fact, one rule, one place it's defined. But don't rush to unify two pieces of code just because they currently look similar; if they represent different concerns that happen to coincide today, a shared abstraction will fight you the moment they diverge. Sandi Metz's framing has aged well here: **prefer duplication over the wrong abstraction.** Wait for a third occurrence, or clear evidence it's the same *rule*, before extracting.
- **Optimize for deletion** — favor structures (small modules, few incoming dependents, minimal shared state) that let you delete or replace a piece cleanly, over ones optimized for reuse you don't yet need.

## 8. Maintenance is a continuous, separated activity

- **Code for the maintainer** — you're writing for the person (or agent) who edits this code next under time pressure, without your current context.
- **Boy Scout Rule** — leave touched code slightly cleaner than you found it — but keep that opportunistic (see §3), not a license for unrelated rewrites.
- **Tidy first** (Kent Beck) — when a change requires both restructuring code *and* changing behavior, do them as separate steps: tidy (rename, extract, reorder — no behavior change) then change. Mixing them makes both harder to review and to revert.

## 9. Define success criteria. Loop until verified.

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

## 10. Paradigm note: state and mutation

Regardless of language, prefer immutable data and pure functions (no hidden side effects, same input → same output) where the language and performance budget allow it — this is *Functional Programming in Scala*'s core lesson, and it generalizes past Scala: it's why `const` in JS, `final` in Java, and Rust's ownership model all exist. Reach for mutation and shared state deliberately, where it buys real simplicity or performance, not as a default.

