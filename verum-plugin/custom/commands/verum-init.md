---
allowed-tools: Read, Glob, Grep, Write, Bash(find:*, wc:*, head:*)
---

# /verum-init

Scaffold the `verum/` spec directory for the current project and generate seed specs from codebase analysis.

Arguments: $ARGUMENTS (optional — unused currently, reserved for future component filters)

## Step 1: Check for existing verum directory

Use Glob to check if `verum/index.md` exists in the current working directory.

If it exists:
- Tell the user: "verum/ already exists. To avoid overwriting existing specs, this command will not re-initialize. Review your specs in verum/ or delete the directory to start fresh."
- Stop. Do not continue to further steps.

## Step 2: Analyze the project

Gather information about the project. Be fast and approximate — do not read every source file.

1. Read the top-level directory listing (use Bash with `find . -maxdepth 1 -type f` and `find . -maxdepth 1 -type d`).
2. Read `README.md` if it exists.
3. Read `CLAUDE.md` if it exists.
4. Read the main entry point if one is obvious (e.g., `src/index.ts`, `main.py`, `lib/main.rb`, `cmd/main.go`, `Cargo.toml`, `package.json`, `setup.py`, `pyproject.toml`).
5. Use Glob to discover the top-level structure: `src/**` or `lib/**` or `packages/**` or `services/**` — whichever exist. Only go one or two levels deep.
6. If a `package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, or similar manifest exists, read it to understand dependencies and project metadata.

From this analysis, extract:
- **Project name** — from manifest, README heading, or directory name (in that order of preference).
- **Project description** — from manifest or README first paragraph.
- **Major components** — directories or modules that represent distinct areas of responsibility. Look for: top-level directories under `src/`, `lib/`, `packages/`, `services/`, `apps/`; or standalone directories with their own manifest. A component is a cohesive unit — a service, module, subsystem, or bounded context.
- **System invariants** — any obvious cross-cutting properties mentioned in README or CLAUDE.md (e.g., "all API responses use JSON", "no runtime dependencies").

Limit discovered components to 10 maximum. If more exist, pick the 10 most significant and note the rest in `index.md`.

## Step 3: Create the directory structure

Create these directories and files:
```
verum/
  index.md
  components/
  invariants.md
  decisions.md
```

## Step 4: Generate verum/index.md

Use the index template structure (# heading, > blockquote description, ## Components, ## System Invariants, ## Architecture Decisions).

Fill in:
- `# [Project Name]` — from analysis.
- `> [description]` — one line from analysis.
- `## Components` — list each discovered component as `- [Name](components/component-name.md) — [what it does]`. Use lowercase-hyphenated filenames.
- `## System Invariants` — list any discovered invariants. If none were found, write a single placeholder: `- <!-- Add system-wide invariants here -->`.
- `## Architecture Decisions` — leave with a single placeholder: `- <!-- Add architecture decisions here -->`.

Do not include HTML template comments from the template file. The generated index should be clean.

## Step 5: Generate component stubs

For each component listed in `index.md`, create a file in `verum/components/` using the component template structure.

Fill in what can be inferred from the codebase analysis:
- `# [Component Name]` — human-readable name.
- `> [purpose]` — one-line description inferred from directory contents, README mentions, or file names.
- `## Stories` — leave one placeholder story: `### [Story title]` / `[Needs review]` with `<!-- needs review -->`.
- `## Accepts` — if entry points or APIs were discovered, list obvious inputs. Otherwise placeholder with `<!-- needs review -->`.
- `## Produces` — if outputs are obvious, list them. Otherwise placeholder with `<!-- needs review -->`.
- `## Behaviors` — leave placeholder with `<!-- needs review -->`.
- `## Boundaries` — leave placeholder with `<!-- needs review -->`.
- `## Invariants` — leave placeholder with `<!-- needs review -->`.
- `## Depends on` — if dependencies between components are obvious, list them. Otherwise placeholder with `<!-- needs review -->`.
- `## Exposes to` — placeholder with `<!-- needs review -->`.
- `## Decisions` — leave empty placeholder with `<!-- needs review -->`.

Keep stubs short. Do not attempt to reverse-engineer full specs from source code. The init command produces scaffolding, not finished documentation.

## Step 6: Generate invariants.md and decisions.md

For `verum/invariants.md`:
- Use the invariants template structure.
- Replace `[Project Name]` with the actual project name.
- Leave section contents as placeholders with `<!-- needs review -->`.

For `verum/decisions.md`:
- Use the decisions template structure.
- Replace `[Project Name]` with the actual project name.
- Leave content as placeholder with `<!-- needs review -->`.

## Step 7: Print summary

Print a summary for the user:

```
verum/ initialized:
  - verum/index.md — project overview with N components
  - verum/components/ — N component stubs created
  - verum/invariants.md — system invariants (empty, needs review)
  - verum/decisions.md — architecture decisions (empty, needs review)

Review the generated specs and fill in details. Component stubs marked
with <!-- needs review --> need human input.
```

Replace N with actual counts.
