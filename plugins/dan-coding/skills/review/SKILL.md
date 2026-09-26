---
name: review
description: Pre-commit review of the current change for bugs, security, comments, and prose, with every finding graded critical, high, medium, or low. Use when a change is implemented and verified and about to go to the user for commit, and as the third phase of dan-mode.
---

# Review

Review the current change: uncommitted edits plus any commits on this branch that aren't on the base branch (`main` unless the project says otherwise). Review finds and grades problems. Fixing them is implementation's job.

## Steps

1. **Code review.** Run `/code-review` at high effort. It checks correctness, reuse, simplification, and efficiency.
2. **Security review.** Run `/security-review` only when the diff touches a trust boundary. That means login or permissions, parsing or forwarding untrusted input, secrets or credentials, a new external call or dependency, file paths built from user input, or a change to what a client can reach. Decide this from the diff itself. If none apply, the whole answer is "security review skipped, no trust boundary in the diff."
3. **Design.** Check the diff against `../implementation/references/programming-principles.md`. Look hardest at names, side effects mixed into logic, mutation that could be a new value, business rules outside the domain model, bare primitives standing in for domain ideas, and one change that needed edits in unrelated places. Flag only what will cost the next person, not matters of taste. If the diff makes an architecture decision, also check it against `../implementation/references/architecture.md`, and check that the decision is written down with its options and tradeoffs. A missing record is a medium finding.
4. **Comments.** Check every comment in the diff against `references/no-comment.md`.
5. **Prose.** Check the prose in the diff, including docs, READMEs, user-facing messages, and comments, against `../dan-mode/references/unslop.md`.
6. **Confirm each finding.** Reviewers raise false alarms. Read the code each finding points at, and drop the ones that aren't real with a one-line reason.
7. **Grade and report** what's left.

## Grades

- **Critical.** Must fix. Data loss, a security hole, a crash or wrong result on a main path.
- **High.** Must fix. A real bug on a path that will run, or changed behavior with no proof it works.
- **Medium.** Should fix. It will cost the next person, for example duplicated logic, a confusing structure, dead code, a misleading name or comment, or AI-sounding prose.
- **Low.** Nice to fix. Style nits and matters of taste.

## Report format

One line per finding, most severe first, written so a fixer can act on it without rereading the review.

```
[high] src/cart.ts:42  Discount applied twice when coupon and sale overlap. Apply the larger one only.
```

End with the counts per grade and the security result.
