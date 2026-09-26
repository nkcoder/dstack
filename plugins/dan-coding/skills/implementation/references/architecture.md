# Architecture

Read this only when the task is about architecture. That means it adds a service, module, or datastore, changes how components talk to each other, picks a technology that's hard to swap, changes a public API, event, or data schema, or comes with explicit goals for scale, reliability, or security. For design inside a single module, `programming-principles.md` is enough.

Sources are Just Enough Software Architecture (Fairbanks), Fundamentals of Software Architecture (Richards and Ford), Software Architecture in Practice (Bass, Clements, Kazman), and Clean Architecture (Martin). Gaps are filled from Designing Data-Intensive Applications (Kleppmann), Release It! (Nygard), Building Evolutionary Architectures (Ford, Parsons, Kua), and a few well-known laws and essays named where they're used.

## 1. Spend design effort in proportion to risk (Just Enough)

- Name the top risks first. What could make this fail? Load, data loss, a boundary in the wrong place, an external API you don't understand yet. If no risk is worth naming, do the minimum design and move on.
- Ask how hard the decision is to reverse. Easy to undo, like an internal function shape or a library behind your own interface, means decide quickly and pick the simple option. Hard to undo, like a data model, a public API, a storage engine, a service split, or a contract with another team, means slow down and write out the options.
- When a risk is uncertain, build a small prototype to settle it instead of debating it.

## 2. Make quality goals concrete (Software Architecture in Practice)

"Fast", "scalable", and "reliable" can't be checked. Turn each into a scenario with a trigger, a condition, and a number. For example, "when 1,000 users check out per second, 95% of checkouts finish in under 300ms."

Pick the two or three qualities that matter most for this system (Fundamentals calls them architecture characteristics). You can't maximize all of them, and each one you add costs complexity. If the goals are unknown and the choice depends on them, ask.

## 3. Every choice is a tradeoff (Fundamentals)

- For each real decision, list at least two options and what each one costs. If you can't name a downside, you don't understand the option yet.
- Why matters more than how. Record the reason, because the next person can read the how from the code.
- Prefer the simplest option that meets the goals from section 2.

## 4. Dependencies point toward the business rules (Clean Architecture)

- Business rules don't depend on frameworks, databases, UI, or transport. Those details depend on the business rules. This is the same idea as the functional core and the infrastructure-free domain in `programming-principles.md`.
- Where the domain needs something from outside, like storage or email, define a small interface the domain owns, and implement it in an adapter at the edge.
- Create an interface only at a real boundary, or when there's a second implementation. A test fake counts. Not every class needs one.
- Treat frameworks as details. Keep them at the edge so an upgrade or swap doesn't reach the business rules.

## 5. Start with one well-divided application

- The default is one deployable with strong module boundaries inside it. Split into separate services only for a concrete reason, like scaling one part independently, separate teams deploying on their own schedules, or different security or reliability needs. (Fowler, "Monolith First")
- Draw boundaries around parts of the domain (bounded contexts), not around technical layers like "controllers" and "repositories".
- System boundaries end up matching team boundaries (Conway's law). Put service boundaries where ownership boundaries already are, or will be.

## 6. Data outlives code (Designing Data-Intensive Applications)

- Each piece of data has exactly one owner that writes it. Everyone else reads through the owner's API or events, never its tables.
- Choose consistency on purpose. Money and inventory usually need strong consistency. Search indexes, analytics, and notifications can usually lag.
- Change a live schema in steps (expand, then contract). Add the new shape, write to both, move reads over, backfill, then remove the old shape. Never ship a breaking schema change in one deploy.
- Choose storage by how the data is read and written, not by habit or fashion.

## 7. The network is not reliable (Release It!, DDIA)

Any call that leaves the process can fail, be slow, or happen twice.

- Put a timeout on every remote call.
- Retry only operations that are safe to repeat, with backoff and jitter. Make writes safe to repeat with idempotency keys, because messages are usually delivered at least once.
- Stop one failure from spreading. Use circuit breakers, separate resource pools per dependency, and bounded queues that push back instead of growing forever.
- Decide what the user sees when a dependency is down, before it happens.

## 8. Design for running it, not just building it

- **Observability.** Use structured logs with a request ID, metrics for the goals from section 2, and traces across services. You can't meet a latency goal you don't measure.
- **Safe releases.** Changes deploy in a backward-compatible way and can be rolled back. Use feature flags for risky behavior, and keep config separate from code.
- **Security from the start.** Name the trust boundaries, grant each component the least access it needs, keep secrets out of code, and validate input at every boundary.
- **Cost.** Cost is a quality like any other. Estimate it for anything that scales with usage.

## 9. Contracts are hard to change (Hyrum's law)

Every observable behavior of a public API, event, file format, or shared schema will be depended on by someone. Keep contracts small. Make changes additive. Version them when a breaking change is unavoidable, and give consumers a migration path before removing anything.

## 10. Choose boring technology (McKinley, "Choose Boring Technology")

- Default to what the project already runs and what's well understood. Every new database, queue, or framework costs operations work and learning for as long as it lives. Spend novelty only where it's the product's real advantage.
- Use a well-maintained dependency for problems that aren't your core, like auth, payments, or crypto. Build what is your core.

## 11. Evolve it, and enforce it (Building Evolutionary Architectures)

- Big rewrites fail more often than they succeed. Replace a system piece by piece. Route one path at a time to the new implementation, run old and new side by side, and compare results (the strangler fig pattern).
- A rule that lives only in a document erodes. Turn important rules into automated checks that run in CI. Examples are dependency rules (dependency-cruiser or eslint-plugin-boundaries for TypeScript, import-linter for Python, ArchUnit for Java) and performance budgets.

## Record the decision

Write a short record in the final reply. If the project keeps architecture decision records (look for `docs/adr/` or similar), write it there too, in the project's format.

- **Decision.** One sentence.
- **Context.** The goal and the main risk.
- **Options.** Each option considered and what it costs.
- **Choice.** Which one, and why.
- **Revisit when.** What would make this the wrong choice later.

Five to ten lines is enough.
