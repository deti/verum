# Verum

Living behavioral specifications that accrete during development. Verum maintains a structured description of what your system currently does — not what it should do, not what it used to do. Specs are created once, then updated automatically as Claude Code makes behavioral changes to your code.

## Installation

From a local clone of this repo:

```sh
claude --plugin-dir ./verum-plugin
```

Persistent install (copies the plugin into your Claude Code plugins directory):

```sh
cp -r verum-plugin ~/.claude/plugins/verum
```

After either method, the three commands (`/verum-init`, `/verum`, `/verum-consult`) and the `verum-update` skill are available in any Claude Code session.

## Quick start

```
/verum-init
```

This scaffolds a `verum/` directory with an `index.md`, component stubs in `verum/components/`, and empty `invariants.md` and `decisions.md`. Review the generated files and fill in placeholder sections marked with `<!-- needs review -->`.

From here, three mechanisms keep specs current:

1. The `verum-update` skill fires automatically when you make behavioral code changes.
2. Run `/verum` periodically to catch drift the auto-update missed.
3. Run `/verum-consult <feature description>` before building a feature to check specs for constraints.

## Commands

### /verum-init

Scaffold the `verum/` directory and generate seed specs from codebase analysis.

**Arguments:** None used currently.

**Allowed tools:** `Read`, `Glob`, `Grep`, `Write`, `Bash(find:*, wc:*, head:*)`

**What it does:**

1. Checks if `verum/index.md` already exists. Refuses to overwrite.
2. Reads top-level directory listing, `README.md`, `CLAUDE.md`, entry points, and manifest files (`package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, etc.).
3. Extracts project name, description, major components (up to 10), and system invariants.
4. Creates `verum/index.md`, `verum/invariants.md`, `verum/decisions.md`, and one stub file per component in `verum/components/`.
5. Prints a summary of what was created.

**Files created:** `verum/index.md`, `verum/invariants.md`, `verum/decisions.md`, `verum/components/<name>.md` (one per discovered component).

**Example:**

```
> /verum-init

verum/ initialized:
  - verum/index.md — project overview with 4 components
  - verum/components/ — 4 component stubs created
  - verum/invariants.md — system invariants (empty, needs review)
  - verum/decisions.md — architecture decisions (empty, needs review)

Review the generated specs and fill in details. Component stubs marked
with <!-- needs review --> need human input.
```

### /verum

Review specs against recent code changes and propose surgical updates.

**Arguments:** Optional — a component name, section name, or file path to narrow scope.

**Allowed tools:** `Read`, `Glob`, `Grep`, `Edit`, `Bash(git diff:*, git log:*, git show:*, find:*)`

**What it does:**

1. Checks for `verum/index.md`. If missing, suggests `/verum-init`.
2. Reads all spec files (or a subset if an argument narrows scope).
3. Runs `git diff --stat HEAD~5` and `git log --oneline -10` to identify recent source code changes.
4. Compares changed source files against their component specs, checking: Accepts, Produces, Behaviors, Boundaries, Invariants, and Depends on / Exposes to.
5. Skips sections with `<!-- needs review -->` placeholders — those are already known incomplete.
6. Reports each drift finding with what the spec says vs. what the code does, plus a proposed edit.
7. Asks the user to confirm: apply all, pick specific changes, or apply none.
8. Applies confirmed changes as surgical edits to specific sections.

**Files read:** All files in `verum/`. Recent git history.
**Files modified:** Spec files where drift is found and user confirms changes.

**Example:**

```
> /verum
> /verum task-router
> /verum behaviors
```

### /verum-consult

Consult specs before building a feature. Read-only — never modifies files.

**Arguments:** Required — description of the feature or change to consult on.

**Allowed tools:** `Read`, `Glob`, `Grep`

**What it does:**

1. Validates that an argument was provided. Asks for one if missing.
2. Checks for `verum/index.md`. If missing, suggests `/verum-init`.
3. Reads `verum/index.md` and identifies affected component specs.
4. Reads affected component specs and their one-hop neighbors (via Depends on / Exposes to links).
5. Reads `verum/invariants.md` and `verum/decisions.md`.
6. Produces a structured report with these sections: Affected components, Relevant behaviors, Boundaries to respect, Invariants at risk, Relevant decisions, Suggested approach, Spec changes needed.

Does NOT read source code files. Works from specs only. Does NOT generate implementation code.

**Files read:** Files in `verum/` only.
**Files modified:** None.

**Example:**

```
> /verum-consult add priority scheduling to the task router
```

## Spec format

Every component spec has exactly these sections:

| Section | What belongs there |
|---|---|
| **Stories** | Narrative paragraphs (with `###` headings) describing why this component exists. Not acceptance criteria. |
| **Accepts** | Every input: `- **name** (required/optional, type hint) — description`. Natural language type hints, not code types. |
| **Produces** | Every output: `- **name** (type hint) — description`. No required/optional marker. |
| **Behaviors** | Named scenarios with `###` headings and prose. What happens, not how. |
| **Boundaries** | Bullet list of hard limits, rejection conditions, edge cases. |
| **Invariants** | Falsifiable statements that must always hold. |
| **Depends on** | `- [Component](component-name.md) — what it gets`. Relative Markdown links. |
| **Exposes to** | Same format as Depends on. |
| **Decisions** | `- **YYYY-MM-DD: Title.** Reasoning.` Bold date prefix. Never remove entries. |

The top-level description uses `>` blockquote syntax. No YAML frontmatter in spec files. No additional sections beyond these nine.

System-level files: `index.md` (component list, system invariants, architecture decisions), `invariants.md` (cross-cutting invariants with subsections: Data Integrity, Communication, Identity and Access), `decisions.md` (system-wide decisions).

See `custom/skills/verum-update/format-reference.md` for the complete format reference. See `examples/vibe/` for an example project with populated specs.

## Auto-update behavior

The `verum-update` skill (defined in `custom/skills/verum-update/SKILL.md`) activates automatically when code changes affect component behavior, interfaces, boundaries, or invariants.

**Activates for:** new/changed behaviors, added/changed/removed inputs or outputs, boundary changes, invariant changes, dependency changes between components.

**Does not activate for:** typo fixes, dependency version bumps, test-only changes, documentation changes, internal refactors that don't change external behavior, performance optimizations.

**What it does:** reads `verum/index.md`, identifies affected components, reads their spec files, applies surgical edits to only the affected sections, and creates new component specs for new components.

## Stop hook behavior

A Stop hook (configured in `config/hooks.json`) runs when a Claude Code session ends. It checks whether behavioral source code was modified during the session. If code in `src/`, `lib/`, `app/`, or similar directories changed but no files in `verum/` were updated, it prints:

> Verum specs may need updating for the changes made this session. Run /verum to review.

If `verum/` files were already updated, or if changes were non-behavioral (formatting, tests, docs, dependencies), the hook says nothing.

The hook runs as a `prompt` type hook, meaning Claude evaluates the condition at session end.

## Limitations

- **Auto-update is probabilistic.** The `verum-update` skill triggers based on Claude's assessment of whether changes are behavioral. It catches roughly 80% of relevant changes. Run `/verum` to catch the rest.
- **No formal spec-code validation.** There is no mechanical check that specs match code. Specs are maintained by convention — Claude updates them, but nothing enforces correctness.
- **No multi-session conflict handling.** If two Claude Code sessions modify the same component's specs concurrently, the last writer wins. There is no merge or conflict resolution.
- **No spec versioning beyond git.** Spec history is whatever git tracks. There is no separate versioning scheme.
- **Stop hook uses a prompt evaluation.** The hook relies on Claude assessing which files changed and whether changes were behavioral. It may miss nuances or produce false positives.
- **Component discovery is approximate.** `/verum-init` infers components from directory structure and manifests. It may miss components or create spurious ones.

## Non-features

Verum does NOT:

- **Generate code from specs.** Specs describe what code does; they do not produce code.
- **Run tests.** Specs are not test cases and do not execute.
- **Validate that code matches specs mechanically.** There is no automated verification beyond Claude's judgment.
- **Work outside Claude Code.** Verum is a Claude Code plugin. It has no CLI, no API, no standalone mode.
- **Support non-Markdown formats.** Specs are Markdown files with a fixed section structure. No JSON, YAML, or other formats.
- **Enforce spec updates.** The stop hook reminds but does not block. The auto-update skill is best-effort.

## License

MIT
