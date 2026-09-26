# Comment check

A comment earns its place only when it explains a non-obvious why that the code can't show. Everything else goes. Report each comment that should go as a medium finding. Implementation deletes it.

## Keep

- Legal or license headers.
- A why forced by something outside our control, like a vendor bug, a platform quirk, or a protocol rule. Include a link to the issue when there is one.
- Doc comments that define a public API.
- `// prettier-ignore` and similar formatter directives.

## Remove

- Comments that say what the code does. Rename or restructure the code until it says it.
- Commented-out code. Git has the history.
- Banners, section dividers, and step labels like `// Step 1: load config`.
- Changelog notes like "added for X" or "fixed bug Y". Those belong in the commit message.
- TODOs with no linked issue.

## Look closer before judging

- **A long justification for our own code.** If a comment needs a paragraph to defend a workaround in code we own, the code is the problem. Report the symbol as a medium finding so implementation can reshape it.
- **Lint and type suppressions** (`eslint-disable`, `@ts-ignore`, `@ts-expect-error`, `# type: ignore`, `# noqa`). Look up the rule. If it protects correctness or safety, report it as a high finding and fix the code. A suppression for a pure style rule can stay.
- **Warnings like "IMPORTANT" or "do not remove".** Read the nearby code and its callers to see if the claim is true today. If it's true and comes from something outside our control, keep it. If it's about our own code, report it as a finding, since the code should make the constraint impossible to break.

When you're unsure whether a comment fits the keep list, flag it for removal.
