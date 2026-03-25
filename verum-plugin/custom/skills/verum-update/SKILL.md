---
description: Update verum behavioral specifications after code changes that affect component behavior, interfaces, boundaries, or invariants
allowed-tools: Read, Glob, Grep, Edit, Write
---

# verum-update

After completing a task that changes application behavior, update the verum specs to match.

## When to activate

This skill applies when code changes affect:
- How a component behaves (new behavior, changed behavior, removed behavior)
- What a component accepts (new parameters, changed types, removed inputs)
- What a component produces (new outputs, changed return values, new side effects)
- Component boundaries (new limits, changed constraints, new rejection conditions)
- Component invariants (properties that must hold changed or new ones added)
- Dependencies between components (new dependency, removed dependency, changed interface)

## When NOT to activate

Do NOT update specs for:
- Typo fixes or formatting changes in source code
- Dependency version bumps that don't change behavior
- Test-only changes (new tests, test refactors)
- Documentation-only changes (README, comments, docstrings)
- Internal refactors that don't change external behavior (renaming private variables, extracting private helpers)
- Performance optimizations that don't change observable behavior

## Procedure

1. Check if `verum/` exists. If not, skip — specs haven't been initialized for this project.

2. Read `verum/index.md` to understand the component map.

3. Identify which component specs are affected by the changes just made. Match changed files to components based on the project structure and component descriptions.

4. Read each affected component spec file.

5. Apply surgical updates to the affected sections only:
   - **Accepts** — add new inputs, update changed ones, remove deleted ones.
   - **Produces** — add new outputs, update changed ones, remove deleted ones.
   - **Behaviors** — add new behavior subsections, update descriptions of changed behaviors, remove behaviors that no longer exist.
   - **Boundaries** — add new limits or constraints, update changed ones.
   - **Invariants** — add new invariants, update or remove invalidated ones.
   - **Depends on / Exposes to** — update dependency links when component relationships change.
   - **Decisions** — add a new decision entry when a non-obvious implementation choice was made. Use today's date.

6. Do NOT touch sections unaffected by the change. Do NOT rewrite existing prose — only modify what the code change invalidates.

7. If a new component was created that has no spec file, create one in `verum/components/` following the format in `format-reference.md`. Also add it to the component list in `verum/index.md`.

8. If a component was deleted, remove its spec file and its entry in `verum/index.md`.

9. Follow the formatting conventions in [format-reference.md](format-reference.md).

10. Commit spec changes in the same commit as the code changes — they are part of the same logical unit of work.

## Examples

**Behavioral change — update specs:**
- Adding a rate limit to an API endpoint → update Boundaries, possibly Behaviors
- Adding a new parameter to a function's public interface → update Accepts
- Changing what events a component emits → update Produces
- Adding a new API endpoint to a service → add to Behaviors, Accepts, Produces

**Not a behavioral change — skip:**
- Renaming an internal variable → no spec update
- Adding a unit test → no spec update
- Fixing a typo in a log message → no spec update
- Bumping a library version without API changes → no spec update
- Extracting a private method for readability → no spec update
