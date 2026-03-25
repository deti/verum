---
allowed-tools: Read, Glob, Grep
description: Consult verum specs before building a feature
---

# /verum-consult

Consult verum specs before building a feature. Read-only — this command never modifies any files.

Arguments: $ARGUMENTS (required — description of the feature or change to consult on)

## Step 1: Validate arguments

If `$ARGUMENTS` is empty or not provided:
- Ask the user: "What feature or change are you planning? Describe it so I can consult the specs."
- Wait for their response before continuing.

## Step 2: Check for verum directory

Use Glob to check if `verum/index.md` exists.

If it does not exist:
- Tell the user: "No verum/ directory found. Run `/verum-init` to scaffold specs first."
- Stop. Do not continue.

## Step 3: Read project index

Read `verum/index.md` to understand the project structure and component list.

## Step 4: Identify affected components

Based on the feature description in `$ARGUMENTS` and the component list from `index.md`:
- Identify which components are likely affected by the proposed change.
- Read each affected component's spec file from `verum/components/`.

## Step 5: Load adjacent specs

For each affected component, read its "Depends on" and "Exposes to" sections.
- Follow the links to load adjacent component specs — one level deep only.
- Do NOT follow links from the adjacent specs to their neighbors. Stop at one hop.

Also read `verum/invariants.md` and `verum/decisions.md` for system-wide constraints.

## Step 6: Produce consultation report

Using only the spec content (do NOT read source code files), produce a structured report:

### Affected components

List each component that the proposed change touches or interacts with. For each, state why it is relevant to this feature.

### Relevant behaviors

List existing behaviors (from the Behaviors sections of affected specs) that interact with the proposed change. Quote or paraphrase the behavior and name the component it belongs to.

### Boundaries to respect

List boundaries and limits (from Boundaries sections) that constrain how this feature can be implemented. Include boundaries from adjacent components if the feature crosses component boundaries.

### Invariants at risk

List invariants (from both component-level and system-level invariants) that the proposed change might violate or that must be carefully preserved. Explain why each is relevant.

### Relevant decisions

List past decisions (from component-level and system-level Decisions sections) that inform or constrain the approach. Include the date and reasoning.

### Suggested approach

Based on the spec constraints above, suggest how to implement the feature in a way that respects existing behaviors, boundaries, and invariants. This is architectural guidance, not implementation steps or task lists.

### Spec changes needed

List which spec files and sections will need updating after the feature is implemented. Be specific — name the file, the section, and what kind of update is needed (e.g., "add new behavior", "update accepts list", "add boundary").

## Rules

- Do NOT modify any files. This command is strictly read-only and advisory.
- Do NOT read source code files. Work from specs only. The point is to validate that specs are sufficient for planning.
- Do NOT generate implementation code or task lists.
- Do NOT suggest changes to the spec format.
- If specs have `<!-- needs review -->` placeholders in sections relevant to the consultation, note this as a gap: "The [section] of [component] has not been filled in yet — this should be reviewed before implementing."
