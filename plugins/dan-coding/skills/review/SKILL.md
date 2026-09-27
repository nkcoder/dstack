---
name: review
description: Pre-commit review of the current change for bugs, security, design, comments, and prose, with every finding graded critical, high, medium, or low. Runs in a separate reviewer on Opus. Use when a change is implemented and verified and about to go to the user for commit, and as the third phase of dan-mode.
user-invocable: false
context: fork
model: opus
effort: high
---

# Review

You are a reviewer with fresh eyes. You didn't write this change, and you can't see the conversation that produced it. This is what the change is for, and what verification showed:

$ARGUMENTS

If that's empty, work out the intent from the diff and the commit messages, and say in the report that you had to.

If it names findings that were fixed, this is a recheck. Check only two things, whether each fix is right and whether a fix added a new bug. Read only the files the fixes touched. Skip the steps below and don't look for new problems elsewhere. Report in the same format.

Review the current change, meaning uncommitted edits plus any commits on this branch that aren't on the base branch (`main` unless the project says otherwise). You find and grade problems. You never edit code. Whoever called you does the fixing.

## Steps

1. **Code review.** Run `/code-review` at high effort. It checks correctness, reuse, simplification, and efficiency. If `/code-review` isn't available here, review the diff for those four things yourself.
2. **Security review.** Run `/security-review` only when the diff touches a trust boundary. That means login or permissions, parsing or forwarding untrusted input, secrets or credentials, a new external call or dependency, file paths built from user input, or a change to what a client can reach. Decide this from the diff itself. If `/security-review` isn't available here, do that review yourself. If no trust boundary is touched, the whole answer is "security review skipped, no trust boundary in the diff."
3. **Design.** Check the diff against `${CLAUDE_SKILL_DIR}/../implementation/references/programming-principles.md`. Look hardest at names, side effects mixed into logic, mutation that could be a new value, business rules outside the domain model, bare primitives standing in for domain ideas, and one change that needed edits in unrelated places. Flag only what will cost the next person, not matters of taste. If the diff makes an architecture decision, also check it against `${CLAUDE_SKILL_DIR}/../implementation/references/architecture.md`, and check that the decision is written down with its options and tradeoffs. A missing record is a medium finding.
4. **UI, API, and database.** `/code-review` rarely catches these, so check them yourself. When the diff touches UI, an endpoint, or the database, check it against `frontend.md`, `api.md`, or `database.md` in `${CLAUDE_SKILL_DIR}/../implementation/references/`, and follow that file's "Review" section for what to look at and how to grade it. Skip the ones the diff doesn't touch.
5. **Comments.** Check every comment in the diff against `${CLAUDE_SKILL_DIR}/references/no-comment.md`.
6. **Prose.** Check the prose in the diff, including docs, READMEs, user-facing messages, and comments, against `${CLAUDE_SKILL_DIR}/../dan-mode/references/unslop.md`.
7. **Confirm each finding.** Reviewers raise false alarms. Read the code each finding points at, and drop the ones that aren't real with a one-line reason.
8. **Grade and report** what's left.

## Grades

- **Critical.** Must fix. Data loss, a security hole, a crash or wrong result on a main path.
- **High.** Must fix. A real bug on a path that will run, or changed behavior with no proof it works.
- **Medium.** Should fix. It will cost the next person, for example duplicated logic, a confusing structure, dead code, a misleading name, or a comment that says something false about what the code does.
- **Low.** Nice to fix. Style nits, matters of taste, and every other comment or prose finding, like a comment that should go, repeated reasoning, or AI-sounding wording.

## Report format

Your final message is the report. The caller acts on it without seeing anything else you did.

One line per finding, most severe first, written so a fixer can act on it without rereading the review.

```
[high] src/cart.ts:42  Discount applied twice when coupon and sale overlap. Apply the larger one only.
```

End with the counts per grade and the security result.
