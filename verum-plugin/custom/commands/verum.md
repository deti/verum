---
allowed-tools: Read, Glob, Grep, Edit, Bash(git diff:*, git log:*, git show:*, find:*)
---

# /verum

Review existing specs against recent code changes and propose surgical updates where specs have drifted.

Arguments: $ARGUMENTS (optional — component name or area to focus the review on)

## Step 1: Check for verum directory

Use Glob to check if `verum/index.md` exists.

If it does not exist:
- Tell the user: "No verum/ directory found. Run `/verum-init` to scaffold specs first."
- Stop. Do not continue.

## Step 2: Read specs

Read all files in `verum/` recursively:
- `verum/index.md`
- `verum/invariants.md`
- `verum/decisions.md`
- All files in `verum/components/`

If `$ARGUMENTS` is provided, narrow the scope:
- If it matches a component name, only read that component's spec file and `verum/index.md`.
- If it matches a section name (e.g., "behaviors", "accepts"), read all component specs but only flag drift in that section.
- If it matches a path or directory, try to identify which component spec covers that area and focus there.

## Step 3: Read recent changes

Run these git commands to understand what changed recently:
- `git diff --stat HEAD~5` — files changed in the last 5 commits.
- `git log --oneline -10` — recent commit messages for context.
- `git diff HEAD~5 -- <paths>` — actual diffs for files that are covered by verum specs.

Focus on source code changes, not spec file changes. Identify which components are affected by the recent changes.

## Step 4: Compare specs to code

For each component that has recent changes in its covered files:

1. Read the changed source files (or the relevant parts via git diff).
2. Compare against the component's spec sections:
   - **Accepts** — do the inputs still match? Were parameters added, removed, or renamed?
   - **Produces** — do the outputs still match? Were return values or side effects changed?
   - **Behaviors** — do the described behaviors still hold? Were new behaviors added or existing ones changed?
   - **Boundaries** — are the limits still accurate?
   - **Invariants** — do the invariants still hold?
   - **Depends on / Exposes to** — were dependency relationships changed?
3. Also check `verum/index.md`:
   - Were components added or removed from the project?
   - Does the component list still match reality?

Do NOT:
- Flag sections that have `<!-- needs review -->` placeholders — those are already known to be incomplete.
- Flag style differences or minor wording preferences — only flag factual drift.
- Remove or modify Decisions entries — those are historical record.
- Add new sections beyond the canonical set (Stories, Accepts, Produces, Behaviors, Boundaries, Invariants, Depends on, Exposes to, Decisions).

## Step 5: Report drift

If no drift is detected:
- Tell the user: "Specs are current. No drift detected in the last 5 commits."
- Stop.

If drift is detected, present each finding:

```
Drift detected:

1. verum/components/[component].md — [section]
   Spec says: [what the spec currently states]
   Code does: [what the code actually does now]
   Proposed edit: [specific change to make]

2. ...
```

Keep proposed edits minimal. Preserve the original author's voice and style. Only change what is factually wrong, not what could be phrased differently.

If `$ARGUMENTS` was provided and no drift was found in that scope:
- Tell the user: "No drift detected for [argument] in the last 5 commits."

## Step 6: Confirm and apply

After presenting all proposed changes, ask the user:
"Apply these changes? (all / pick / none)"

- **all** — apply every proposed edit using the Edit tool.
- **pick** — let the user choose which changes to apply by number.
- **none** — do nothing.

Apply changes as surgical edits to specific sections. Do NOT rewrite entire files. Use the Edit tool to replace only the drifted content within each section.

## Step 7: Summary

After applying changes (or if none were applied), print:

```
verum review complete:
  - Components checked: N
  - Drift found: N items
  - Changes applied: N items
```
