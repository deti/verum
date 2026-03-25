# Verum — Plugin Architecture

## File tree

```
verum-plugin/
  .claude-plugin/plugin.json         — plugin manifest: name, version, hooks path
  config/hooks.json                  — Stop hook configuration
  custom/commands/
    verum-init.md                    — /verum-init command
    verum.md                         — /verum command
    verum-consult.md                 — /verum-consult command
  custom/skills/verum-update/
    SKILL.md                         — auto-update skill
    format-reference.md              — spec format reference used by skill
  templates/                         — component, index, invariants, decisions templates
  examples/vibe/                     — example project with populated specs
  {commands,skills,hooks,agents}/    — empty (.gitkeep); actual files are in custom/
```

## How the plugin loads

1. Claude Code reads `.claude-plugin/plugin.json`, which declares plugin name (`verum`), version (`0.1.0`), and hooks path (`config/hooks.json`).
2. Claude Code discovers commands in `custom/commands/` — each `.md` file with YAML frontmatter becomes a slash command. The frontmatter specifies `allowed-tools`.
3. Claude Code discovers skills in `custom/skills/` — each directory with a `SKILL.md` file becomes an auto-activating skill. The frontmatter specifies `allowed-tools` and a `description` used for activation matching.
4. Claude Code reads `config/hooks.json` and registers the Stop hook.

Templates in `templates/` and examples in `examples/` are not loaded by Claude Code directly. Commands and skills reference the format conventions described in `custom/skills/verum-update/format-reference.md`.

## Data flow

### /verum-init (scaffold)

```
Project files (read-only)
  → verum-init.md analyzes structure
  → creates verum/index.md, verum/components/*.md, verum/invariants.md, verum/decisions.md
```

Reads: top-level directories, README.md, CLAUDE.md, manifest files, source structure.
Creates: the entire `verum/` directory in the user's project.
Does not modify any existing files.

### /verum (review)

```
verum/*.md (read) + git history (read)
  → verum.md compares specs to recent changes
  → proposes edits → user confirms
  → edits verum/components/*.md, verum/index.md
```

Reads: all files in `verum/`, `git diff HEAD~5`, `git log -10`.
Modifies: spec files where drift is confirmed by the user.

### /verum-consult (advisory)

```
verum/*.md (read-only)
  → verum-consult.md reads specs for affected components + neighbors
  → produces structured consultation report (text output only)
```

Reads: files in `verum/` only. Does not read source code.
Modifies: nothing.

### verum-update skill (automatic)

```
Code changes (observed by Claude)
  → SKILL.md activation criteria matched
  → reads verum/index.md → identifies affected components
  → reads affected verum/components/*.md
  → applies surgical edits to affected sections
```

Activates: when Claude assesses that behavioral code changes occurred during the session.
Reads: `verum/index.md`, affected component specs, `format-reference.md`.
Modifies: affected spec files. May create new component specs.

### Stop hook (reminder)

```
Session ends
  → hooks.json Stop hook fires
  → Claude checks if source files changed but verum/ was not updated
  → prints reminder if specs may need updating
```

Reads: session change history (implicit).
Modifies: nothing. Outputs a text reminder.

## Component interaction

The three commands and the skill operate independently. They share only the `verum/` directory in the user's project as common state:

- **verum-update** and **/verum** both modify spec files, but verum-update is automatic (fires during coding) while /verum is manual (user-invoked review). They are complementary — verum-update handles in-session changes, /verum catches drift across commits.
- **Stop hook** does not modify specs. It checks whether verum-update already ran and, if not, reminds the user to run /verum.
- **/verum-consult** is read-only and does not interact with the other components beyond reading the same spec files.

Typical workflow: `/verum-init` → code with auto-update → Stop hook reminds if specs missed → `/verum` for thorough review → `/verum-consult` before new features.

## Configuration

Everything is hardcoded. There are no user-facing configuration options, environment variables, or settings files.

- Spec format: defined in `format-reference.md` (nine sections, heading levels, link format).
- Stop hook prompt: defined in `config/hooks.json`.
- Git history depth for `/verum` review: 5 commits (`HEAD~5`).
- Max components discovered by `/verum-init`: 10.
- Templates: defined in `templates/`.
