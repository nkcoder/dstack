# Database

Read this when the task touches queries, schema, migrations, or transactions. Use the project's ORM and migration tool. Data ownership across services is covered in `architecture.md` section 6.

## 1. Schema

- Let the database enforce what it can, with NOT NULL, foreign keys, unique constraints, and check constraints. Checks that live only in code get bypassed by other code paths, scripts, and races.
- Pick precise types. Store timestamps in UTC with a time zone type. Store money as integer minor units or a decimal, never a float. Use an enum or a check constraint for a fixed set of values.
- Every table has a primary key.
- Name tables and columns in the domain's words, the same as the code.

## 2. Queries

- Always pass values as parameters. Never build SQL by joining strings with input. When a column or sort direction comes from input, pick it from an allowlist.
- Index what you filter, join, and sort on. For any new query on a table that can grow, check the plan with `EXPLAIN`, or `EXPLAIN ANALYZE` on realistic data. A full table scan on a large table is a bug.
- Never run one query per row. ORMs hide this. Loading a list and then touching a relation inside a loop fires a query for every item. Use eager loading, a join, or one batched `IN` query.
- Every query that returns a list has a limit. Paginate with a cursor on an indexed column.
- Select the columns you need, not every column.
- Aggregate in the database (`COUNT`, `SUM`, `GROUP BY`) instead of loading rows into memory to add them up.

## 3. Transactions and concurrency

Assume two requests for the same record arrive at the same moment.

- Put writes that must succeed or fail together in one transaction. Keep transactions short, and never make a network call (HTTP, email, publishing to a queue) inside one.
- Replace check-then-write in code with a guarantee from the database. "If the email doesn't exist, insert it" becomes a unique constraint plus handling the conflict, or `INSERT ... ON CONFLICT`.
- Change counters and balances in one atomic statement, like `UPDATE items SET stock = stock - 1 WHERE id = $1 AND stock > 0`. Reading the value, changing it in code, and writing it back loses updates.
- When two people can edit the same record, use a version column. Check it on update, and return a conflict (409) when it changed.
- Lock rows explicitly (`SELECT ... FOR UPDATE`) only when you must, and always lock in the same order to avoid deadlocks.
- When a write must also publish an event or send a message, write the message to an outbox table in the same transaction and send it afterward. Otherwise the write can succeed while the message fails, or the other way round.

## 4. Migrations

- Every schema change is a checked-in migration in the project's tool. Never change a shared database by hand.
- The old code keeps running during a deploy, so a migration must work with both the code before it and the code after it. Rename or drop in steps. Add the new column, write to both, backfill, move reads over, and drop the old one in a later release.
- Watch for locks on large tables. On some databases, adding an index, a constraint, or a column with a default locks the table while it runs. Use the safe form, like `CREATE INDEX CONCURRENTLY` in Postgres, or adding a constraint as `NOT VALID` and validating it afterward.
- Backfill large tables in batches, separately from the schema change.
- Know how to roll back. Write the down migration when the tool supports it. If the change can't be undone, say so in the shipping notes, with the reason.
- Never edit a migration that has already run on a shared database. Add a new one.

## 5. Data safety

- Destructive operations on real data, like drop, delete, truncate, or a mass update, need Dan's approval and a backup or another way back.
- Keep personal data to what the feature needs, know which columns hold it, and keep it out of logs.
- Test and seed data never come from production copies that contain personal data.

## Verify

- Run each new migration forward on a fresh database and on a copy with realistic data. If it has a down migration, run that too, then forward again.
- Run `EXPLAIN` on every new or changed query against a table that can grow, and confirm it uses an index.
- Test concurrency rules for real. Fire two conflicting requests at the same time, like two sign-ups with the same email, and check the database ends up correct.
- Count the queries a list endpoint or page makes. The count shouldn't grow with the number of rows.

## Review

Look hardest at migrations that break the running code or lock a large table, queries with no index or no limit, one query per row, and read-then-write races. A migration that breaks running code or locks a large table, and a race that can corrupt data, are each at least high.
